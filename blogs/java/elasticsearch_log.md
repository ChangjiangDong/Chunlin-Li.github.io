使用 ElasticSearch 处理日志信息
==========================

业务中产生了大量的日志信息, 这些日志是进行下一步数据统计分析的原料. 以原始的方式将日志存在文件中, 离线用脚本去定时做数据的统计分析已经无法
满足需求了. 最主要的问题有这么几个: 1 无法适应不断变化的统计需求; 2 对于比较复杂的统计的开发成本较高.

针对这个问题, 决定尝试引入 ElasticSearch 来解决. 其实这是个基于全文检索工具 Lucene 的搜索引擎. 而事实上我们要处理的是日志中数据的统计
问题. 但其实不矛盾, ES 提供了很多比较方便的 API, 对我们来说, count 和 aggregation 应该是最有用的.

学习使用的过程中遇到了问题和疑惑, 踩了点小坑, 同时也发现网上 ES 的中文资料并不是很多. 那我在这里也将我近期学到的一点知识分享出来. 

首先, 对于最基本的了解可以去看看 github 上翻译的权威指南: 地址 [Elasticsearch 权威指南（中文版）](http://es.xiaoleilu.com/)  

去官网下载 ES(我用的版本的 2.2.0), 只依赖 JDK, 解压后配置好 JAVA_HOME 的环境变量就可以直接启动了.  

ES 的一大特点就是非常易于入门使用, 没有复杂的配置部署过程. 但是其实它有非常多的配置, 如果要应用在规模较大的生产环境中, 还是需要仔细学习研究
并调试才能得到比较好的性能和满足需要的功能.

在我们的使用场景下, 主要关注的有这么几点: 

* 日志的索引速度. 必须保证 ES 插入文档的速度小于我们日志的生成速度, 还得留有一定余量.
* 数据查询速度. 不能查询一个数据半天算不出结果.
* 数据占用的空间不能过大.
* 有高效的查询方式来满足业务上的需求.

-------------------------

索引速度方面可以通过以下方式优化: 

* 日志数据需要是 JSON 格式的字符串, 对于其中不是 JSON 的部分, 如果需要支持查询, 则要自己进行格式转换.
* 日志中基本上不会有需要全文模糊检索的东西. `_all` 因此将字段关闭.
* 日志中的数据一般都是准确值, 不需要分词. 通过设置索引的 dynamic mapping 将 string 类型的 analyze 关掉.
* 如果不需要 ES 保存日志原文, 可以将 `_source` 关闭. 但关闭这个会导致部分功能不能使用. 如果原文不是特别大, 尽量不要关闭这个选项.
* 日志 JSON 中把一些不会用于搜索的字段删掉, 修改数据嵌套结构, 尽量让其扁平化.


-------------------------------------

#### 关于 JVM 和 elasticsearch.yml 的设置

生产环境常常遇到大内存, 大磁盘的环境. 需要对 JVM 和 elasticsearch 的相关参数进行谨慎的调优, 才能尽可能的发挥出它的性能来.

**内存**

* 默认的 HeapSize 是 1G, 所以如果要上线一定要记得改 `ES_HEAP_SIZE` 参数, 这是在 `elasticsearch/bin/elastsicsearch.in.sh` 中设置的
* HEAP Size 最大不要超过 32G. 另外, 这个 ES_HEAP_SIZE 设置的只是 ES 的内存, 事实上, 其后的 Lucene 实例也需要内存的, 所以通常来说 ES_HEAP_SIZE 也不要超过 50% 的物理内存
* 如果可以, 尽量关闭 swap, 或者打开 ES 的 `bootstrap.mlockall: true` 选项. 可能涉及一些权限问题, 通过查google或日志的提示进行设置修改.


**磁盘**

* 在 `elasticsearch.yml` 中可以设置 `path.data` 参数, 将多块磁盘都添加到其中.

**scripting**

ES 中支持多种脚本语言, 脚本可以在查询, 更新, 聚合等操作时使用, 详情见[官方文档](https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-scripting.html)   

需要注意的是, 处于安全性考虑, 不是所有场合都可以使用所有类型的脚本语言. 因此, 如果有对 scripting 的特殊需要, 则需在 elasticsearch.yml 中进行相应的配置.    
官方的文档里的介绍还算详细.可以仔细研究学习一下. 

另外, 对于 update 操作中对 script 的使用, 还可以看这篇[文档](https://www.elastic.co/guide/en/elasticsearch/reference/current/docs-update.html)


#### Index 的 settings

* 我们的日志数据不需要备份, 因为还会有一份压缩归档的.因此关闭 replica, 即将 `number_of_replicas` 设置为 0.
* 根据测试发现 shard 的数量会对索引速度有一定影响, 太少(比如1个)则索引的非常慢, 太多(比如20个)性能也不会有线性增长, 反而可能会因为太多的 lucene 实例导致冗余的 meta 信息以及处理效率的降低, 最后根据简单的测试, 决定 `number_of_shards` 的值保持默认的 5. 
* `refresh_interval` 选项决定多久将临时索引块(segment)的变化信息刷入到主索引中以便可以让最新的数据被搜索到. 默认 1s 但对我们来说没有什么必要, 一些文章说在批量写入数据的时候将其关闭(设置为 -1),写入完成后再打开. 我们采取了一种居中的策略, 将其设置为一个较长的值 (30s), 因为我们对日志的实时性要求并不是很高, 而日志信息又是分多个进程源源不断的写入, 多加一个开关会使逻辑变得较为复杂.

#### ES 中的动态映射

主要手段是配置 template

另外 ES 默认的 字段类型的动态映射也很重要, template 中的大部分配置可能都还是基于字段的动态映射. 少数需要调整的字段可以单独设置.

dynamic template 中的几个很有用的属性是: `match_mapping_type` `match` `path_match`

需要注意, 多个动态模板是放在一个数组中的, 模板在数组中的先后顺序会影响最终一个字段的映射结果. 由于是从第一个模板开始尝试映射, 因此应该将宽松的映射条件放在后面, 而把较为严格的映射条件放在前面.


#### ES 中的字段

每个文档都由各个字段组成. 直接保存的原始数据是放在 _source 字段下的.

`_timestamp`, `_id`, `_index`, `_type`, `_all`等. 这些都是自动生成的字段. 其中 `_id _index _type` 是必须的字段. 而不是全文检索的情况下建议将 `_all` 关闭. `_timestamp` 如果开启, 会在创建和更新文档的时候自动更新该时间戳. 对我们来说不需要.


这里给出我们的 index 设置: 

```
{
  "template": "*",
  "order": 0,
  "settings": {
    "index": {
      "number_of_shards": 5,
      "number_of_replicas": 0,
      "refresh_interval": "30s"
    },
    "persistent": {
      "indices.store.throttle.max_bytes_per_sec" : "100mb"
    },
    "transient" : {
      "indices.store.throttle.type" : "none"
    }
  },
  "mappings": {
    "_default_": {
      "_all": {
        "enabled": false
      },
      "_timestamp": {
        "enabled": false
      },
      "date_detection": false,
      "numeric_detection": false,
      "dynamic_templates": [
        {
          "timestamp": {
            "match_pattern": "regex",
            "match": ".*Time",
            "mapping": {
              "type": "date",
              "format": "strict_date_time||epoch_millis",
              "norms": {
                "enabled": false
              }
            }
          }
        },
        {
          "number2double": {
            "match_pattern": "regex",
            "match": "doubleNumber_[uvc]",
            "mapping": {
              "type": "double",
              "norms": {
                "enabled": false
              }
            }
          }
        },
        {
          "raw_string": {
            "match_mapping_type": "string",
            "mapping": {
              "type": "string",
              "index": "not_analyzed",
              "ignore_above": 255,
              "norms": {
                "enabled": false
              }
            }
          }
        }
      ]
    }
  }
}
```

#### ES 的 bulk API

生产环境中数据如果一条一条插入, 效率会低的无法想象. 因此是一定要使用 bulk api 进行批量处理的. 

对于如何设定一批的大小, 从性能角度来说, 调用一次 `_bulk` 发送的一批数据大致在 10MB 左右比较合适. 比如倘若每行数据有 1KB 大小, 那么一批数据大概在 10000 行左右可以达到比较好的性能. 此外, 可能还需要考虑数据生成的速度, 比如如果要攒够1W行数据需要1分钟, 而你期望一行数据最晚30s内就要写入ES, 那么可能还得适度缩小批次的大小才行.

另外, 有些情况下我们希望数据尽可能不要损失, 那么可能会对插入失败的数据进行重试或进行其他处理. `_bulk` api 的返回结果给了我们进行有针对性的错误处理的可能. 

如果 `_bulk` api 成功响应了, 那么 http 返回码就会是 200, 返回内容是一个 JSON 字符串. 其首层有几个比较重要的值: 

* `errors:true/false`: 表明这一批数据处理是否有出错的. 如果是 false 那就说明全部都成功了. 反之说明里面有错误的.
* `items:[{},{}]`: 这个数据里的元素数和你发送给 bulk 的操作数是一致的, 并且一一对应. 通过检查这个巨大的数据, 可以找出所有出错的下标, 并且错误信息中会有错误类型和 reason 信息, 可以据此对错误进行分类统计和处理. 比如我们的业务中会对 create 失败的操作直接输出到一个单独的日志中, 而 update 失败的操作会挑出来修改参数后重试一次, 若再次失败才输出到单独的日志. 对于输出到日志的失败操作还可以再次使用另一个工具脚本进行离线的数据导入.
 
