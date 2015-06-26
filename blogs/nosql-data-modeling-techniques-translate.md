使用 MongoDB 的过程中发现, NoSQL 的数据建模是发挥其性能的关键点.
在网上发现一篇介绍 NoSQL 数据建模技术的文章, NOSQL DATA MODELING. 
学习后觉得不错, 简单翻译了一下, 供大家学习参考.
本人英语水平很烂, 大家英文水平还行的就尽量阅读原文.

[原文在这里.](https://highlyscalable.wordpress.com/2012/03/01/nosql-data-modeling-techniques/ "NoSQL Data Modeling Techniques") 打不开的同学请采用正确的上网姿势.


----------------------------------

NOSQL DATA MODELING TECHNIQUES
===========

NoSQL databases are often compared by various non-functional criteria, such as scalability, performance, and consistency. This aspect of NoSQL is well-studied both in practice and theory because specific non-functional properties are often the main justification for NoSQL usage and fundamental results on distributed systems like the CAP theorem apply well to NoSQL systems.  At the same time, NoSQL data modeling is not so well studied and lacks the systematic theory found in relational databases. In this article I provide a short comparison of NoSQL system families from the data modeling point of view and digest several common modeling techniques.
> NoSQL 数据库的 扩展性, 性能, 以及一致性等经常被人们拿来进行分析比较, 这方面无论是实践还是理论, 都已经有了较多的研究, 因为这些方面是对于 NoSQL 的基本性能和 CAP 理论应用的比较好. 但是数据模型方面还缺乏研究, 以及系统性的理论来与传统的关系型数据库抗衡. 这篇文章中对 NoSQL 发展中的数据模型进行了比较, 并提供了一些通用的数据建模方式

I would like to thank Daniel Kirkdorffer who reviewed the article and cleaned up the grammar.

To  explore data modeling techniques, we have to start with a more or less systematic view of NoSQL data models that preferably reveals trends and interconnections. The following figure depicts imaginary “evolution” of the major NoSQL system families, namely, Key-Value stores, BigTable-style databases, Document databases, Full Text Search Engines, and Graph databases:
> 先从 NoSQL 的数据模型发展趋势和其中的联系开始. 下图展示了 NoSQL 家族数据模型的"进化". 

![NoSQL Data Models](http://img.blog.csdn.net/20150420133048578)

First, we should note that SQL and relational model in general were designed long time ago to interact with the end user. This user-oriented nature had vast implications:
> 很久以前, SQL 和关系模型就被设计出来, 作为与终端用户的接口. 用户导向对其有着重大影响.















- The end user is often interested in aggregated reporting information, not in separate data items, and SQL pays a lot of attention to this aspect.
- No one can expect human users to explicitly control concurrency, integrity, consistency, or data type validity. That’s why SQL pays a lot of attention to transactional guaranties, schemas, and referential integrity.

> 最终用户通常希望得到整合起来的数据报告, 而不是一堆分散的数据项. SQL 主要就在这方面下功夫.
> 没人希望用户显式的控制数据并发性, 完整性, 一致性, 或者是数据类型的验证, 因此, SQL 主攻事务, 数据表模式, 整合数据引用

On the other hand, it turned out that software applications are not so often interested in in-database aggregation and able to control, at least in many cases, integrity and validity themselves. Besides this, elimination of these features had an extremely important influence on the performance and scalability of the stores. And this was where a new evolution of data models began:
> 另一方面, 人们发现软件程序其实并不是特别关注数据库里数据是否整合存储, 以及是否需要进行数据完整性和可用性的验证. 因此, 消除这些特性会对存储有特别重要的性能和扩展性的影响. 这就是数据模型革命的开端


- Key-Value storage is a very simplistic, but very powerful model. Many techniques that are described below are perfectly applicable to this model.
- One of the most significant shortcomings of the Key-Value model is a poor applicability to cases that require processing of key ranges. Ordered Key-Value model overcomes this limitation and significantly improves aggregation capabilities.
- Ordered Key-Value model is very powerful, but it does not provide any framework for value modeling. In general, value modeling can be done by an application, but BigTable-style databases go further and model values as a map-of-maps-of-maps, namely, column families, columns, and timestamped versions.
- Document databases advance the BigTable model offering two significant improvements. The first one is values with schemes of arbitrary complexity, not just a map-of-maps. The second one is database-managed indexes, at least in some implementations. Full Text Search Engines can be considered a related species in the sense that they also offer flexible schema and automatic indexes. The main difference is that Document database group indexes by field names, as opposed to Search Engines that group indexes by field values. It is also worth noting that some Key-Value stores like Oracle Coherence gradually move towards Document databases via addition of indexes and in-database entry processors.
- Finally, Graph data models can be considered as a side branch of evolution that origins from the Ordered Key-Value models. Graph databases allow one model business entities very transparently (this depends on that), but hierarchical modeling techniques make other data models very competitive in this area too. Graph databases are related to Document databases because many implementations allow one model a value as a map or document.

> Key-Value 存储是一种非常简单但却很强大的存储模型, 许多技术都可以完美应用于这种数据模型.
> 这种模型的一个严重的缺点就是,  对于要查寻的是一个范围内的 key 的时候, 这种模型就无法很好适用了. 有序的 Key-Value 模型克服了这个限制, 并大幅改进了其功能.
> 有序 Key-Value 模型无法提供 Value 的建模框架, 因此, 值的模型需要由程序来维护. BigTable 模型在这方面迈进了一大步,  它将层层嵌套的 Map 作为值模型, 
> 文档数据模型又在 BigTable 的基础上, 做出了两个重大的改进. 一, 它支持任意复杂度的数据值模型, 而不仅仅是嵌套 Map. 二, 数据库可以管理索引(在某些实现中), 全文检索引擎就可以看作是一种类似的东西, 它支持灵活的模型和自动化的索引. 其中的不同在于文档数据库以字段名分组管理索引, 而搜索引擎是按照字段值管理索引. 值得注意的是, 一些 Key-Value 存储, 比如 Oracle Coherence 通过增加索引和数据库内的数据处理器, 逐步向文档数据库的阵营靠拢.
> 最后, 图数据模型可以被看作是一个有序 Key-Value 模型的进化分支,  .....  草, 后面那句看不懂.... 谁帮我翻译一下.    图书据库和文档数据库类似, 因为许多实现方式允许以 Map 或者文档作为数据模型的值

## General Notes on NoSQL Data Modeling

The rest of this article describes concrete data modeling techniques and patterns. As a preface, I would like to provide a few general notes on NoSQL data modeling:
> 接下来会具体讲解数据模型, 在这里先简单介绍以下:

- NoSQL data modeling often starts from the application-specific queries as opposed to relational modeling:
 - Relational modeling is typically driven by the structure of available data. The main design theme is  “What answers do I have?” 
 - NoSQL data modeling is typically driven by application-specific access patterns, i.e. the types of queries to be supported. The main design theme is “What questions do I have?”  
- NoSQL data modeling often requires a deeper understanding of data structures and algorithms than relational database modeling does. In this article I describe several well-known data structures that are not specific for NoSQL, but are very useful in practical NoSQL modeling.
- Data duplication and denormalization are first-class citizens.
- Relational databases are not very convenient for hierarchical or graph-like data modeling and processing. Graph databases are obviously a perfect solution for this area, but actually most of NoSQL solutions are surprisingly strong for such problems. That is why the current article devotes a separate section to hierarchical data modeling.

 > NoSQL 数据建模通常是先从程序的特定查询开始, 这和关系型建模不同
> 关系型建模通常是由数据的组成结构驱动的. 核心设计思想是: 我手里都有些什么? 
> NoSQL 数据建模通常由程序对数据的访问方式驱动, 也就是用建模去满足程序的查询需求. 核心的设计思想是: 我需要解决什么问题?
> NoSQL 数据建模通常会对数据结构以及算法的理解有着更高的要求. 本文会讲到几个非常著名的数据结构, 他们虽不是 NoSQL 所特有的, 但是对于 NoSQL 建模的实践非常有帮助.
> 数据冗余和反范式化是 NoSQL 建模最重要的方法.
> 关系数据库对于层级或者图状的数据进行建模和处理会非常不方便. 图数据库是解决这种问题的最理想的工具, 但是事实上, 多数 NoSQL 解决方案都能很好的处理这个问题. 这就是为什么这篇文章将层级数据建模独立出一个章节来.

Although data modeling techniques are basically implementation agnostic, this is a list of the particular systems that I had in mind while working on this article:
- Key-Value Stores: Oracle Coherence, Redis, Kyoto Cabinet
- BigTable-style Databases: Apache HBase, Apache Cassandra
- Document Databases: MongoDB, CouchDB
- Full Text Search Engines: Apache Lucene, Apache Solr
- Graph Databases: neo4j, FlockDB

> 尽管数据建模技术通常是难于直接直接实现,  但我能想到的以下系统中, 都可以实践本文中的建模技术:
> 这个列表就不翻译了, 自己看把.

## Conceptual Techniques

This section is devoted to the basic principles of NoSQL data modeling.

> 这部分重要介绍 NoSQL 数据建模中的基本原则

### (1) Denormalization

Denormalization can be defined as the copying of the same data into multiple documents or tables in order to simplify/optimize query processing or to fit the user’s data into a particular data model. Most techniques described in this article leverage denormalization in one or another form.

> 反范式化可以这样来定义: 通过复制一些数据到不同的文档中或着数据表中, 来简化或优化查询过程, 或是用于保证用户所需的特定数据模型.  这篇文章中用到的多数技术, 都或多或少的利用了反范式化的思想

In general, denormalization is helpful for the following trade-offs:

- Query data volume or IO per query VS total data volume. Using denormalization one can group all data that is needed to process a query in one place. This often means that for different query flows the same data will be accessed in different combinations. Hence we need to duplicate data, which increases total data volume.
- Processing complexity VS total data volume. Modeling-time normalization and consequent query-time joins obviously increase complexity of the query processor, especially in distributed systems. Denormalization allow one to store data in a query-friendly structure to simplify query processing.

> 通常来说, 反范式化的思想在以下这些权衡中会有所帮助:
> - 查询数据量或每个查询的 IO VS 全部数据容量. 使用反范式化, 我们可以在一个查询中, 从一个位置查到所有我们需要处理的数据. 这通常意味着对于不同的查询, 同样的数据可能会以不同的形式被访问到. 因此我们需要数据冗余, 这将增加整体的数据量.
> - 数据存取的复杂度 VS 数据总量. 建模时的范式化和由此导致的查询中的 join 操作将明显的增加查询操作的复杂度, 尤其是在分布式系统中. 反范式化可以让数据存储结构变得"查询友好", 以简化查询操作.

**Applicability**: Key-Value Stores, Document Databases, BigTable-style Databases

### (2) Aggregates

All major genres of NoSQL provide soft schema capabilities in one way or another:

- Key-Value Stores and Graph Databases typically do not place constraints on values, so values can be comprised of arbitrary format. It is also possible to vary a number of records for one business entity by using composite keys. For example, a user account can be modeled as a set of entries with composite keys like UserID_name, UserID_email, UserID_messages and so on. If a user has no email or messages then a corresponding entry is not recorded.
- BigTable models support soft schema via a variable set of columns within a column family and a variable number of versions for one cell.
- Document databases are inherently schema-less, although some of them allow one to validate incoming data using a user-defined schema.

> 所有主流 NoSQL 都会以某种方式提供软模式的功能:
> - Key-Value 存储和 图数据库 通常不会对 value 进行限制, 所以 value 可以是任意构成形式. 比如, 一个用户的账户信息可以构建为一个保存了UserID_name, UserID_email, UserID_messages等条目的集合. 如果用户没有 email 或者 messages, 相应的条目就不需要记录了. 
> - BigTable 提供软模式: 一个 列簇中包含多个列, 每个列中又有各种其他类型的信息.
> - 文档数据库继承了无模式的特性, 然而其中的某些也允许用户通过自定义的模式来验证输入的数据.

Soft schema allows one to form classes of entities with complex internal structures (nested entities) and to vary the structure of particular entities.This feature provides two major facilities:

- Minimization of one-to-many relationships by means of nested entities and, consequently, reduction of joins.
- Masking of “technical” differences between business entities and modeling of heterogeneous business entities using one collection of documents or one table.

> 软模式可以让人们通过复杂的内部结构(比如嵌套)来组织不同的实体, 也可以改变结构来满足特定的实体. 该特性提供了两方面的便利:
> - 对于一对多的数据关系, 可以使用嵌套实体的方式来简化, 以避免大量使用 joins.
> - 通过使用一个集合(表), 封装了 业务实体 和 异化业务实体模型之间的不同. (这句我特么看不懂你造么?!)

These facilities are illustrated in the figure below. This figure depicts modeling of a product entity for an eCommerce business domain. Initially, we can say that all products have an ID, Price, and Description. Next, we discover that different types of products have different attributes like Author for Book or Length for Jeans. Some of these attributes have a one-to-many or many-to-many nature like Tracks in Music Albums. Next, it is possible that some entities can not be modeled using fixed types at all. For example, Jeans attributes are not consistent across brands and specific for each manufacturer. It is possible to overcome all these issues in a relational normalized data model, but solutions are far from elegant. Soft schema allows one to use a single Aggregate (product) that can model all types of products and their attributes:

> 下图阐明了上面所说优势. 该图描绘了一个电子商务产品实体模型. 最初, 所有的产品都有一个 ID, 价格 和描述. 接下来我们发现不同的产品类型有着不同的特性, 比如书记的作者或是牛仔裤的长度. 其中某些属性有着一对多或者多对一的数据性质, 比如音乐专辑中的曲目. 然后, 有些产品无法使用某种固定的类型进行建模. 比如, 牛仔裤的属性可能会随着不同的品牌或产地的不同而不同.  在关系型数据库中我们有可能通过范式化的数据模型解决所有这些问题, 但解决方案远远谈不上优雅. 而软模式可以在一个单独的集合中处理好所有这些产品以及他们各种各样的属性.

![Entity Aggregation](http://img.blog.csdn.net/20150420133143131)

Embedding with denormalization can greatly impact updates both in performance and consistency, so special attention should be paid to update flows.

> 内嵌的反范式化可以极大的提高数据更新的性能和一致性. 因此对于数据的更新需要特别留意. ( 这两句我又看醉了... 到底想说什么意思... 是褒是贬呢? )

**Applicability**: Key-Value Stores, Document Databases, BigTable-style Databases

### (3) Application Side Joins

Joins are rarely supported in NoSQL solutions. As a consequence of the “question-oriented” NoSQL nature, joins are often handled at design time as opposed to relational models where joins are handled at query execution time. Query time joins almost always mean a performance penalty, but in many cases one can avoid joins using Denormalization and Aggregates, i.e. embedding nested entities. Of course, in many cases joins are inevitable and should be handled by an application. The major use cases are:

- Many to many relationships are often modeled by links and require joins.
- Aggregates are often inapplicable when entity internals are the subject of frequent modifications. It is usually better to keep a record that something happened and join the records at query time as opposed to changing a value . For example, a messaging system can be modeled as a User entity that contains nested Message entities. But if messages are often appended, it may be better to extract Messages as independent entities and join them to the User at query time: 
![这里写图片描述](http://img.blog.csdn.net/20150420133225173)

> 很少有 NoSQL 数据库会支持 Joins. "问题导向"是 NoSQL 的天性, 因此, 数据连结通常是在设计阶段来解决和处理的, 而关系型数据库却是在查询执行时来处理的. 查询时使用 joins 连结数据意味着较高的性能代价, 但在多数情况下, 使用反范式化和数据聚合可以避免查询时使用 join. 因此在多数情况下, 虽然真正的数据连结是无法避免的, 但对于 NoSQL 来说, 它是在应用程序中进行处理的. 主要的使用场景如下: 
>  - 多对多关系通常需要使用链接建模, 并在应用程序中将其连结起来.
>  - 当数据实体内部有需要频繁修改的数据时, 聚合并不适用. 更好的方式是使用独立的记录, 并在查询时在程序代码中将其进行链接, 而不是每次都去改变它的值. 例如: 一个消息系统中, 可以让一个用户实体中嵌套多条消息实体, 但是如果需要经常追加新的消息进去, 更好的做法是将消息抽出来作为独立的实体, 只在查询的时候才将用户与消息在程序内连结起来.
**Applicability**: Key-Value Stores, Document Databases, BigTable-style Databases, Graph Databases

## General Modeling Techniques

In this section we discuss general modeling techniques that applicable to a variety of NoSQL implementations.
> 这部分我们讨论一下可以应用到各种NoSQL数据库的通用建模技术.

### (4) Atomic Aggregates

Many, although not all, NoSQL solutions have limited transaction support. In some cases one can achieve transactional behavior using distributed locks or application-managed MVCC, but it is common to model data using an Aggregates technique to guarantee some of the ACID properties.

One of the reasons why powerful transactional machinery is an inevitable part of the relational databases is that normalized data typically require multi-place updates. On the other hand, Aggregates allow one to store a single business entity as one document, row or key-value pair and update it atomically:
![Atomic Aggregates](http://img.blog.csdn.net/20150420133410729)
> 绝大多数 NoSQL 数据库对事务的支持很有限. 在一些场景下, 可以使用分布的锁机制 或者由应用管理的 MVCC 来实现事务性操作. 但是更常用的方式是, 通过适用聚合技术来在一定程度上保证 ACID 特性.
> 为什么强大的事务机制对于关系型数据库是不可或缺的呢? 其中一个重要原因就是 范式化的数据通常需要在多个表中进行写操作. 而对于 NoSQL 来说, 数据聚合使得一个业务实体中的所有数据都存储在一个文档, 记录, 或者是键值对中, 而这种情况下是可以进行原子化的更新的.















Of course, Atomic Aggregates as a data modeling technique is not a complete transactional solution, but if the store provides certain guaranties of atomicity, locks, or test-and-set instructions then Atomic Aggregates can be applicable.
> 当然, 原子化聚合作为一项数据建模技术, 并不能完全解决事务问题, 但是, 如果数据库还提供了一些手段, 诸如原子操作, 锁, 以及 test-and-set 指令来保证原子性, 那么这种技术就是可以适用的了.

**Applicability**: Key-Value Stores, Document Databases, BigTable-style Databases

### (5) Enumerable Keys

Perhaps the greatest benefit of an unordered Key-Value data model is that entries can be partitioned across multiple servers by just hashing the key. Sorting makes things more complex, but sometimes an application is able to take some advantages of ordered keys even if storage doesn’t offer such a feature. Let’s consider the modeling of email messages as an example:

1. Some NoSQL stores provide atomic counters that allow one to generate sequential IDs. In this case one can store messages using userID_messageID as a composite key. If the latest message ID is known, it is possible to traverse previous messages. It is also possible to traverse preceding and succeeding messages for any given message ID.
2. Messages can be grouped into buckets, for example, daily buckets. This allows one to traverse a mail box backward or forward starting from any specified date or the current date.

> 也许, 无序的 Key-Value 数据模型最大的好处就是它可以很方便的被拆分存储在许多不同的数据库服务器上, 仅需要对 key 进行哈希操作即可. 排序使得事情变得复杂起来, 有时一个应用能够利用有序的 key , 哪怕数据库并不支持这个特性. 我们可以通过 Email 消息数据模型来看一下:
> 1. 一些 NoSQL 数据库提供原子计数器, 可以生成顺序的 ID. 我们可以使用 userID_messageID 的复合建来存储消息. 如果我们知道了最近的 messageID, 那我们就可以依次遍历上一条消息. 对于任意给出的 messageID, 我们也可以双向任意遍历.
>2. 消息可以划分成组, 存放在相应的存储区中, 比如, 以天作为存储区. 这样又可以在上述基础上按照日期进行数据遍历. 

**Applicability**: Key-Value Stores

### (6) Dimensionality Reduction

Dimensionality Reduction is a technique that allows one to map multidimensional data to a Key-Value model or to other non-multidimensional models.

Traditional geographic information systems use some variation of a Quadtree or R-Tree for indexes. These structures need to be updated in-place and are expensive to manipulate when data volumes are large. An alternative approach is to traverse the 2D structure and flatten it into a plain list of entries. One well known example of this technique is a Geohash. A Geohash uses a Z-like scan to fill 2D space and each move is encoded as 0 or 1 depending on direction. Bits for longitude and latitude moves are interleaved as well as moves. The encoding process is illustrated in the figure below, where black and red bits stand for longitude and latitude, respectively:
![Geohash Index](http://img.blog.csdn.net/20150420133319024)
> 降维是一种将多维度数据映射到 Key-Value 模型或者其他非多维模型的技术.
> 传统的地理信息系统使用 Quadtree 或 R-Tree 的变种来实现索引. 这种结构需要进行原地更新操作, 当数据量很大的时候, 数据操作的性能开销很大. 另一种方法是遍历 2D 结构, 并将其扁平化, 存储到一个最常见的列表中. 这种技术的一个著名的例子就是地理哈希. 地理哈希使用 Z型扫描模拟 2D 空间, 每一种移动方向都可以使用 0和1进行编码. 当扫描执行时, 用于描述移动的经度比特和维度比特交错排列. 扫描和编码过程如上图所示, 编码中的黑色和红色数字分别代表经度和维度.

An important feature of a Geohash is its ability to estimate distance between regions using bit-wise code proximity, as is shown in the figure. Geohash encoding allows one to store geographical information using plain data models, like sorted key values preserving spatial relationships. The Dimensionality Reduction technique for BigTable was described in . More information about Geohashes and other related techniques can be found in [6.2] and [6.3].
> 地理哈希的一个重要特性, 是它能够根据它的比特编码对不同区域之间的距离进行估算. 地理哈希编码支持将地理信息以一种类似于有序键值的简单模型进行存储, 并且能够保留数据之间的空间关系. 在 BigTable 中的这种降维技术的实现细节, 以及更多的关于地理哈希和其他相关技术的信息, 请参阅文末参考引用的 [6.2] 和 [6.3]





**Applicability**: Key-Value Stores, Document Databases, BigTable-style Databases

### (7) Index Table

Index Table is a very straightforward technique that allows one to take advantage of indexes in stores that do not support indexes internally. The most important class of such stores is the BigTable-style database. The idea is to create and maintain a special table with keys that follow the access pattern. For example, there is a master table that stores user accounts that can be accessed by user ID. A query that retrieves all users by a specified city can be supported by means of an additional table where city is a key:
> 索引表是一种非常直接的技术, 他可以在并不支持内部索引的数据库中利用索引原理进行带索引的存储. 这种数据库中最重要的一类就是类似 BigTable 这样的数据库. 索引表的核心思想就是创建并维护一个特殊的表, 该表中的 key 要符合数据访问的模式. 比如, 有一个存储了用户账户的主表, 可以通过 userID 进行访问. 要通过一个查询获取指定城市的所有用户, 这种查询可以通过增加一个额外的表来实现, 这个表中是以城市作为 key, 该城市的 userID 作为 key 对应的值.

![Index Table Example](http://img.blog.csdn.net/20150420133541974)

An Index table can be updated for each update of the master table or in batch mode. Either way, it results in an additional performance penalty and become a consistency issue.

Index Table can be considered as an analog of materialized views in relational databases.
> 主表的每一次更新都将同时更新索引表. 当然, 这将给数据操作带来额外的性能开销, 并且可能引入一致性的问题.
> 索引表可以被看作是关系型数据库的很形象的模拟.

**Applicability**: BigTable-style Databases

### (8) Composite Key Index

Composite key is a very generic technique, but it is extremely beneficial when a store with ordered keys is used. Composite keys in conjunction with secondary sorting allows one to build a kind of multidimensional index which is fundamentally similar to the previously described Dimensionality Reduction technique. For example, let’s take a set of records where each record is a user statistic. If we are going to aggregate these statistics by a region the user came from, we can use keys in a format (State:City:UserID) that allow us to iterate over records for a particular state or city if that store supports the selection of key ranges by a partial key match (as BigTable-style systems do):
> 复合键是一种非常通用的技术, 但是当它应用在有序键上时, 将显得更加强大. 复合键与次级排序键结合, 可以产生多维索引的效果, 有点类似于前一节提到的降维技术. 
> 例如: 在一个保存用户统计信息的表中, 会保存用户的位置信息, 我们可以使用 State:City:UserID 的格式来作为 key, 这样我们可以通过 key 的特定部分来查询特定的 州或城市的所有用户统计信息, 当然, 需要数据库支持 key 值的部分匹配.
```sql
SELECT Values WHERE state="CA:*"
SELECT Values WHERE city="CA:San Francisco*"
```
![Composite Key Index](http://img.blog.csdn.net/20150420133614032)

**Applicability**: BigTable-style Databases













### (9) Aggregation with Composite Keys

Composite keys may be used not only for indexing, but for different types of grouping. Let’s consider an example. There is a huge array of log records with information about internet users and their visits from different sites (click stream). The goal is to count the number of unique users for each site. This is similar to the following SQL query:

```SELECT count(distinct(user_id)) FROM clicks GROUP BY site```

We can model this situation using composite keys with a UserID prefix:
> 复合 Key 不仅可以作为索引使用, 也可以用于对不同类型的数据进行分组. 例如: 有一个很大的日志数组, 用于记录用户从不同网站的访问.  目的是能够统计出任意用户所有访问的次数. 这和以下这句 SQL 语句实现的功能类似.
> ```SELECT count(distinct(user_id)) FROM clicks GROUP BY site```
> 所有类似这种情景, 我们都可以使用 userID 作为 key 的前缀.

![Counting Unique Users using Composite Keys](http://img.blog.csdn.net/20150420133518458)

The idea is to keep all records for one user collocated, so it is possible to fetch such a frame into memory (one user can not produce too many events) and to eliminate site duplicates using hash table or whatever. An alternative technique is to have one entry for one user and append sites to this entry as events arrive. Nevertheless, entry modification is generally less efficient than entry insertion in the majority of implementations.
> 这种想法主要是要将一个用户的所有相关记录都至于同等地位, 这样就可以将这样一组数据载入内存中 ( 前提是用户不会产生过多的数据 ) 并使用一些特定的工具进行去重. 另一种技术是一个用户仅有一条记录, 每次访问都对数据进行追加. 然而, 对于绝大多数数据库来说, 记录的修改相对插入来说效率要低一些.

**Applicability**: Ordered Key-Value Stores, BigTable-style Databases

### (10) Inverted Search – Direct Aggregation

This technique is more a data processing pattern, rather than data modeling. Nevertheless, data models are also impacted by usage of this pattern. The main idea of this technique is to use an index to find data that meets a criteria, but aggregate data using original representation or full scans. Let’s consider an example. There are a number of log records with information about internet users and their visits from different sites (*click stream*). Let assume that each record contains user ID, categories this user belongs to (Men, Women, Bloggers, etc), city this user came from, and visited site. The goal is to describe the audience that meet some criteria (site, city, etc) in terms of unique users for each category that occurs in this audience (i.e. in the set of users that meet the criteria).
> 现在要说的技术是一种数据处理模式, 而不是数据建模技术. 然而, 这种技术的使用将会影响到数据模型.   这就是使用索引查找与条件匹配的数据, 而简单的聚合数据模型则需要适用传统的表达式或进行全表扫描.
>  让我们来看一个例子: 有一个用于存放用户访问记录的表. 我们假设每个记录都包含 user ID, 用户的分类信息, 用户的所属城市, 以及所访问的网站. 我们的目的是要统计符合特定查询条件的独立用户数.

It is quite clear that a search of users that meet the criteria can be efficiently done using inverted indexes like *{Category -> [user IDs]}* or *{Site -> [user IDs]}*. Using such indexes, one can intersect or unify corresponding user IDs (this can be done very efficiently if user IDs are stored as sorted lists or bit sets) and obtain an audience. But describing an audience which is similar to an aggregation query like


```SELECT count(distinct(user_id)) ... GROUP BY category```
cannot be handled efficiently using an inverted index if the number of categories is big. To cope with this, one can build a direct index of the form *{UserID -> [Categories]}* and iterate over it in order to build a final report. This schema is depicted below:

> 很明显, 使用反转索引可以高效率的查询出每个分类的用户. 比如  *{Category -> [user IDs]}* or *{Site -> [user IDs]}* 这样的索引,  可以适用交错的方式, 利用这些索引找到满足条件的用户. ( 如果 userID 是用有序 List 或 Bit Set , 则可以大幅提高性能 )
> 如果一个分类下的 userID 数量太多, 这种方式就无法高效的处理了. 我们可以通过创建直接索引来应对这种情况. 索引形式为 *{UserID -> [Categories]}*, 然后通过有序的遍历来搜集我们需要的数据. 

![Counting Unique Users using Inverse and Direct Indexes](http://img.blog.csdn.net/20150420133554338)

And as a final note, we should take into account that random retrieval of records for each user ID in the audience can be inefficient. One can grapple with this problem by leveraging batch query processing. This means that some number of user sets can be precomputed (for different criteria) and then all reports for this batch of audiences can be computed in one full scan of direct or inverse index.
> 最后需要注意, 为每一个 userID 随机取回数据记录会是低效率的. 应该使用批量查询来处理这个问题. 这意味着一些用户数据集可以被提前计算, 所有的数据在一次完整的扫描中就都可以被查询出来.

**Applicability**: Key-Value Stores, BigTable-style Databases, Document Databases







## Hierarchy Modeling Techniques

### (11) Tree Aggregation

Trees or even arbitrary graphs (with the aid of denormalization) can be modeled as a single record or document.

- This techniques is efficient when the tree is accessed at once (for example, an entire tree of blog comments is fetched to show a page with a post).
- Search and arbitrary access to the entries may be problematic.
- Updates are inefficient in most NoSQL implementations (as compared to independent nodes).
> 树, 或者任意图都可以被建模为单条记录或文档.
> - 当整个树一次性全部被访问的时候, 这种方式会比较高效. ( 比如, 作为博客评论的一整个树将一次性被取出显示在帖子页面上 )
> - 搜索操作, 或任意性的访问内部数据将会遇到麻烦
> - 在大多数 NoSQL 实现中, 适用这种方式进行数据更新效率较低( 相比于直接访问独立节点 ).

 ![Tree Aggregation](http://img.blog.csdn.net/20150420133620484)

**Applicability**: Key-Value Stores, Document Databases








### (12) Adjacency Lists

Adjacency Lists are a straightforward way of graph modeling – each node is modeled as an independent record that contains arrays of direct ancestors or descendants. It allows one to search for nodes by identifiers of their parents or children and, of course, to traverse a graph by doing one hop per query. This approach is usually inefficient for getting an entire subtree for a given node, for deep or wide traversals.
> 邻接表是一种直接的图状建模方式 - 每一个节点都是一条独立的记录, 其中包含了一个存储着直接相邻的父子节点的数组.  我们可以通过他们的父子节点标识符来搜索节点. 事实上也就是遍历一个图, 每一次查询就是一跳. 使用这种方法获取指定节点的一整个子树通常都是低效的, 无论是广度还是深度遍历. 

**Applicability**: Key-Value Stores, Document Databases

### (13) Materialized Paths

Materialized Paths is a technique that helps to avoid recursive traversals of tree-like structures. This technique can be considered as a kind of denormalization. The idea is to attribute each node by identifiers of all its parents or children, so that it is possible to determine all descendants or predecessors of the node without traversal:
> 物化路径是一种用于在树状结构中避免进行递归遍历的技术. 可以将其看作是一种反范式化方法.  

 ![Materialized Paths for eShop Category Hierarchy](http://img.blog.csdn.net/20150420133809457)

This technique is especially helpful for Full Text Search Engines because it allows one to convert hierarchical structures into flat documents. One can see in the figure above that all products or subcategories within the Men’s Shoes category can be retrieved using a short query which is simply a category name.

Materialized Paths can be stored as a set of IDs or as a single string of concatenated IDs. The latter option allows one to search for nodes that meet a certain partial path criteria using regular expressions. This option is illustrated in the figure below (path includes node itself):
> 这项技术对于全文搜索引擎非常有用， 因为它可以将层级化的结构转换为扁平化的文档。  上图中， Men's Shoes 类别下的所有产品和子类别可以仅仅通过一条简单的使用类别名称的查询而取得。
> 物化路径可以存储为一个ID的集合， 也可以存储为串联的ID。 后者可以支持正则表达式， 来查询匹配特定路径特征的所有节点， 如下图所示：

 ![Query Materialized Paths using RegExp](http://img.blog.csdn.net/20150420133708610)

**Applicability**: Key-Value Stores, Document Databases, Search Engines
















### (14) Nested Sets

Nested sets is a standard technique for modeling tree-like structures. It is widely used in relational databases, but it is perfectly applicable to Key-Value Stores and Document Databases. The idea is to store the leafs of the tree in an array and to map each non-leaf node to a range of leafs using start and end indexes, as is shown in the figure below:
> 嵌套集合是一种对树状结构建模的常规技术。 它在关系型数据库中广泛使用， 但它也可以在 Key-Value 数据库及文档型数据库中完美适用。其思想是： 将所有的叶子节点存储在一个数组中， 将所有非叶子节点利用数组中对应叶子节点的起止索引， 映射到数组上的一个区段， 如下图：

![Modeling of eCommerce Catalog using Nested Sets](http://img.blog.csdn.net/20150420133734163)

This structure is pretty efficient for immutable data because it has a small memory footprint and allows one to fetch all leafs for a given node without traversals. Nevertheless, inserts and updates are quite costly because the addition of one leaf causes an extensive update of indexes.
> 这种结构对于不可变的数据来说非常高效， 因为它使用的内存很小， 并且可以以O(1)复杂度取出任意节点下的所有叶子节点。 然而对于插入和更新操作来说， 开销会非常大， 因为增加每增加一个叶子节点， 都要对索引进行扩展和更新。


**Applicability**: Key-Value Stores, Document Databases


















### (15) Nested Documents Flattening: Numbered Field Names

Search Engines typically work with flat documents, i.e. each document is a flat list of fields and values. The goal of data modeling is to map business entities to plain documents and this can be challenging if the entities have a complex internal structure. One typical challenge mapping documents with a hierarchical structure, i.e. documents with nested documents inside. Let’s consider the following example:
> 搜索引擎通常使用扁平的文档， 比如每个文档都是一个由字段和值组成的 list。 这种数据模型的目的在于将业务实体映射到扁平的文档中。 这回引起人们的质疑， 担心是否会导致业务实体的内部结构变得很复杂。 一个典型的挑战来自于将文档映射为层级结构， 比如在文档内部使用嵌套文档。 让我们来看一下下面的例子：

![Nested Documents Problem](http://img.blog.csdn.net/20150420133815269)

Each business entity is some kind of resume. It contains a person’s name and a list of his or her skills with a skill level. An obvious way to model such an entity is to create a plain document with Skill and Level fields. This model allows one to search for a person by skill or by level, but queries that combine both fields are liable to result in false matches, as depicted in the figure above.

One way to overcome this issue was suggested in [4.6]. The main idea of this technique is to index each skill and corresponding level as a dedicated pair of fields Skill_i and Level_i, and to search for all these pairs simultaneously (where the number of OR-ed terms in a query is as high as the maximum number of skills for one person):
> 业务实体是简历， 其中包含了一个人的姓名和他的技能列表， 以及对应技能的级别。 对这种实体的显而易见的建模方式就是使用扁平的文档， 其中包含 Skill 和 Level 字段。这种模型可以用来按照技能或者技能级别来搜索一个人， 但是如果需要联合两个字段一起查询， 将可能导致匹配失败， 正如上图所示。
> 参考文献【4.6】提供了一种解决这种问题的方法。 这种方法的思想是索引每一种技能和其对应的级别， 并将它们作为一个特有的分组 Skill_i 和 Level_i, 这样就可以同步的去搜索所有成对的数据（查询语句中的 OR 的数据将会和这个人所拥有的技能数量一样多）：

![Nested Document Modeling using Numbered Field Names](http://img.blog.csdn.net/20150420133959749)

This approach is not really scalable because query complexity grows rapidly as a function of the number of nested structures.
> 这种方法并不利于扩展， 因为查询的复杂度将会随着嵌套结构的数量快速增长。

**Applicability**: Search Engines

### (16) Nested Documents Flattening: Proximity Queries

The problem with nested documents can be solved using another technique that were also described in [4.6]. The idea is to use proximity queries that limit the acceptable distance between words in the document. In the figure below, all skills and levels are indexed in one field, namely, SkillAndLevel, and the query indicates that the words “Excellent” and “Poetry” should follow one another:

![Nested Document Modeling using Proximity Queries](http://img.blog.csdn.net/20150420134031854)

[4.3] describes a success story for this technique used on top of Solr.
> 嵌套文档的问题可以通过另外一种技术解决, 可以参考[4.6]. 其主要思想是使用临近查询, 将在文档中词汇查询距离限制在一个可以接受的范围内. 如下图所示, 所有的技能和对应等级都在一个字段字段中, 称为 SkillAndLevel. 查询结果显示单词 "Excellent" 和 "Poetry" 出现的顺序不对.
> 参考 [4.3] 讲述的一个该技术再 Solr 上的成功应用案例.
**Applicability**: Search Engines

### (17) Batch Graph Processing

Graph databases like neo4j are exceptionally good for exploring the neighborhood of a given node or exploring relationships between two or a few nodes. Nevertheless, global processing of large graphs is not very efficient because general purpose graph databases do not scale well. Distributed graph processing can be done using MapReduce and the Message Passing pattern that was described, for example, in one of my previous articles. This approach makes Key-Value stores, Document databases, and BigTable-style databases suitable for processing large graphs.
> 类似 neo4j 的图数据库, 对于查找指定节点的相邻节点或查询几个节点之间的关系时, 表现的特别棒. 然而对于一个很大的图进行全局性的处理时, 效率并不是非常高, 因为通用图数据库无法很好的进行扩展. 分布式图操作可以使用 MapReduce 和消息传递模式, 这在我之前的一篇文章中有提到, 这种方法可以再 Key-Value型, 文档型, 以及 BigTable 型的数据库中实现对大型图的操作.
**Applicability**: Key-Value Stores, Document Databases, BigTable-style Databases

## References

Finally, I provide a list of useful links related to NoSQL data modeling:
> 最后, 提供一些关于 NoSQL 数据建模有用的链接.

1. Key-Value Stores:
http://www.devshed.com/c/a/MySQL/Database-Design-Using-KeyValue-Tables/
http://antirez.com/post/Sorting-in-key-value-data-model.html
http://stackoverflow.com/questions/3554169/difference-between-document-based-and-key-value-based-databases
http://dbmsmusings.blogspot.com/2010/03/distinguishing-two-major-types-of_29.html
2. BigTable-style Databases:
http://www.slideshare.net/ebenhewitt/cassandra-datamodel-4985524
http://www.slideshare.net/mattdennis/cassandra-data-modeling
http://nosql.mypopescu.com/post/17419074362/cassandra-data-modeling-examples-with-matthew-f-dennis
http://s-expressions.com/2009/03/08/hbase-on-designing-schemas-for-column-oriented-data-stores/
http://jimbojw.com/wiki/index.php?title=Understanding_Hbase_and_BigTable
3. Document Databases:
http://www.slideshare.net/mongodb/mongodb-schema-design-richard-kreuters-mongo-berlin-preso
http://www.michaelhamrah.com/blog/2011/08/data-modeling-at-scale-mongodb-mongoid-callbacks-and-denormalizing-data-for-efficiency/
http://seancribbs.com/tech/2009/09/28/modeling-a-tree-in-a-document-database/
http://www.mongodb.org/display/DOCS/Schema+Design
http://www.mongodb.org/display/DOCS/Trees+in+MongoDB
http://blog.fiesta.cc/post/11319522700/walkthrough-mongodb-data-modeling
4. Full Text Search Engines:
http://www.searchworkings.org/blog/-/blogs/query-time-joining-in-lucene
http://www.lucidimagination.com/devzone/technical-articles/solr-and-rdbms-basics-designing-your-application-best-both
http://blog.griddynamics.com/2011/07/solr-experience-search-parent-child.html
http://www.lucidimagination.com/blog/2009/07/18/the-spanquery/
http://blog.mgm-tp.com/2011/03/non-standard-ways-of-using-lucene/
http://www.slideshare.net/MarkHarwood/proposal-for-nested-document-support-in-lucene
http://mysolr.com/tips/denormalized-data-structure/
http://sujitpal.blogspot.com/2010/10/denormalizing-maps-with-lucene-payloads.html
http://java.dzone.com/articles/hibernate-search-mapping-entit
5. Graph Databases:
http://docs.neo4j.org/chunked/stable/tutorial-comparing-models.html
http://blog.neo4j.org/2010/03/modeling-categories-in-graph-database.html
http://skillsmatter.com/podcast/nosql/graph-modelling
http://www.umiacs.umd.edu/~jimmylin/publications/Lin_Schatz_MLG2010.pdf
6. Demensionality Reduction:
http://www.slideshare.net/mmalone/scaling-gis-data-in-nonrelational-data-stores
http://blog.notdot.net/2009/11/Damn-Cool-Algorithms-Spatial-indexing-with-Quadtrees-and-Hilbert-Curves
http://www.trisis.co.uk/blog/?p=1287










> Written with [StackEdit](https://stackedit.io/).