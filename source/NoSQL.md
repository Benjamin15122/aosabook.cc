# 13. NoSQL生态系统 #

> Unlike most of the other projects in this book, NoSQL is not a tool, but an ecosystem composed of several complimentary and competing tools. The tools branded with the NoSQL monicker provide an alternative to SQL-based relational database systems for storing data. To understand NoSQL, we have to understand the space of available tools, and see how the design of each one explores the space of data storage possibilities.

与本书中的其他项目不同的是，NoSQL并不是指一个工具，而是一个生态系统，这个生态系统由多个互补和竞争的工具组成。这些被称为NoSQL的工具在存储数据上提供了一种与关系型数据库系统截然不同的方式。为了理解NoSQL，我们需要先理解现有的NoSQL系统，看看它们是如何探索出数据存储的新的方式的，

> If you are considering using a NoSQL storage system, you should first understand the wide space of options that NoSQL systems span. NoSQL systems do away with many of the traditional comforts of relational database systems, and operations which were typically encapsulated behind the system boundary of a database are now left to application designers. This requires you to take on the hat of a systems architect, which requires a more in-depth understanding of how such systems are built.

如果你正在考虑选用一个NoSQL系统，你首先应该知道这里面有很大的选择空间。NoSQL系统舍弃了很多传统关系型数据库的便利之处，并且将原本在数据库中实现的操作丢给了应用层。这需要你对系统架构有更深入的了解，

## 13.1. “NoSQL”意味着什么 ##

> In defining the space of NoSQL, let's first take a stab at defining the name. Taken literally, a NoSQL system presents a query interface to the user that is not SQL. The NoSQL community generally takes a more inclusive view, suggesting that NoSQL systems provide alternatives to traditional relational databases, and allow developers to design projects which use Not Only a SQL interface. In some cases, you might replace a relational database with a NoSQL alternative, and in others you will employ a mix-and-match approach to different problems you encounter in application development.

在阐明NoSQL的时候，我们需要先解读一下“NoSQL”这个名字，字面上来看，一个NoSQL系统向用户提供了不同于SQL的接口。在NoSQL社区里，NoSQL被赋予了更广的含义“Not Only SQL”，提供了传统关系型数据库之外的另一种选择。在应用开发中，有时候你可能会用NoSQL来替代关系型数据库，有时候你会同时使用关系型数据库和费关系型数据库来解决实际中遇到的问题。

> Before diving into the world of NoSQL, let's explore the cases where SQL and the relational model suit your needs, and others where a NoSQL system might be a better fit.

在踏入NoSQL的大门前，我们先来看看有哪些情况适合使用关系型数据库，哪些适合使用NoSQL。

### 13.1.1. SQL和关系模型 ###

> SQL is a declarative language for querying data. A declarative language is one in which a programmer specifies what they want the system to do, rather than procedurally defining how the system should do it. A few examples include: find the record for employee 39, project out only the employee name and phone number from their entire record, filter employee records to those that work in accounting, count the employees in each department, or join the data from the employees table with the managers table.

SQL是一种声明式语言，声明式语言是指，编写程序的时候，程序员只描述他需要系统做什么，而不是通过编程告诉系统如何去做。比如，查找39号员工的信息，返回这个员工的姓名和电话号码；筛出在会计部门工作的员工的信息；计算每个部门各有多少人；或者对员工表和经理表做连接操作，

> To a first approximation, SQL allows you to ask these questions without thinking about how the data is laid out on disk, which indices to use to access the data, or what algorithms to use to process the data. A significant architectural component of most relational databases is a query optimizer, which decides which of the many logically equivalent query plans to execute to most quickly answer a query. These optimizers are often better than the average database user, but sometimes they do not have enough information or have too simple a model of the system in order to generate the most efficient execution.

简单来说，我们用SQL查询数据的时候，不需要考虑数据在磁盘中是如何存储的，或者使用哪种索引来查询数据，或者使用什么算法来处理数据。大多数关系数据库的一个重要结构组件是查询优化器，通过查询优化器来决定采用什么方案最快地处理一个查询语句。查询优化器通常比一般用户更聪明，但是有时候由于没有充足的信息或者系统模型过于简单，也会导致查询优化器不能得出最有效的操作方式，

> Relational databases, which are the most common databases used in practice, follow the relational data model. In this model, different real-world entities are stored in different tables. For example, all employees might be stored in an Employees table, and all departments might be stored in a Departments table. Each row of a table has various properties stored in columns. For example, employees might have an employee id, salary, birth date, and first/last names. Each of these properties will be stored in a column of the Employees table.

现在实际应用最广的数据库系统——关系型数据库系统，采用的是关系型数据模型，在这个模型中，不同类型的实体被存储在不同的表里，比如，所有的员工信息存在一个员工表里，所有的部门信息存在一个部门表里。每一行数据包含多个属性，比如员工表里可能包含了员工号，员工薪资，生日以及姓名，这里每一项信息对应员工表中的某一列。

> The relational model goes hand-in-hand with SQL. Simple SQL queries, such as filters, retrieve all records whose field matches some test (e.g., employeeid = 3, or salary > $20000). More complex constructs cause the database to do some extra work, such as joining data from multiple tables (e.g., what is the name of the department in which employee 3 works?). Other complex constructs such as aggregates (e.g., what is the average salary of my employees?) can lead to full-table scans.

关系型模型与SQL是紧密想连的。简单的SQL查询操作，比如查询符合某个条件的所有行（例：employeeid = 3, 或者 salary > $20000）。更复杂一些的任务会让数据库做一些额外的工作，比如跨表的联合查询（例：查出3号员工工作的部门名称是什么）。其他一些复杂的查询，比如统计操作（例：算出所有员工的平均工资），可能会导致全表扫描。

> The relational data model defines highly structured entities with strict relationships between them. Querying this model with SQL allows complex data traversals without too much custom development. The complexity of such modeling and querying has its limits, though:

  * Complexity leads to unpredictability. SQL's expressiveness makes it challenging to reason about the cost of each query, and thus the cost of a workload. While simpler query languages might complicate application logic, they make it easier to provision data storage systems, which only respond to simple requests.
  * There are many ways to model a problem. The relational data model is strict: the schema assigned to each table specifies the data in each row. If we are storing less structured data, or rows with more variance in the columns they store, the relational model may be needlessly restrictive. Similarly, application developers might not find the relational model perfect for modeling every kind of data. For example, a lot of application logic is written in object-oriented languages and includes high-level concepts such as lists, queues, and sets, and some programmers would like their persistence layer to model this.
  * If the data grows past the capacity of one server, then the tables in the database will have to be partitioned across computers. To avoid JOINs having to cross the network in order to get data in different tables, we will have to denormalize it. Denormalization stores all of the data from different tables that one might want to look up at once in a single place. This makes our database look like a key-lookup storage system, leaving us wondering what other data models might better suit the data.

关系型的数据模型定义了高度结构化的数据，并且这些数据之间有着严格的关系。使用SQL对关系型数据库进行查询允许对数据进行复杂的遍历操作而不需要太多定制开发，尽管如此，这种复杂的模型和查询方式导致关系型数据库有其局限性：

  * 复杂导致不确定性。SQL的形式导致很难去确定一个查询的代价或者造成的工作负载。同时，如果使用简单的查询语言可能使得应用的逻辑更复杂，但是这样，数据库只需要响应简单的请求数据库的内部结构可以简化，
  * 对一个问题建模有很多种方式，其中关系型数据模型是非常严格的一种：表结构的定义规定了表中每一行数据的存储内容。如果我们存储没有那么结构化的数据，或者对每一行数据的要求比较灵活，那么关系型数据模型就太过严格了。类似地，应用的开发人员可能会认为关系型数据模型不够完美，比如，很多应用程序是用面向对象的语言写的，包含有高层次的抽象，数据可能是以列表、队列或集合的形式存储的，程序员们当然希望他们的数据存储层也能和应用层的数据模型一致，
  * 当数据量增长到一台服务器已经不能容纳时，我们需要将数据库中的表分布到不同的机器上。为了避免通过网络获取数据来进行表连接操作，我们不得不设计反范式的数据库，把可能需要一次性查询到的数据存储在一起，这样做使得我们的系统变得像一个键查询系统，于是我们开始思考，是否有其它更适合的数据模型。

> It's generally not wise to discard many years of design considerations arbitrarily. When you consider storing your data in a database, consider SQL and the relational model, which are backed by decades of research and development, offer rich modeling capabilities, and provide easy-to-understand guarantees about complex operations. NoSQL is a good option when you have a specific problem, such as large amounts of data, a massive workload, or a difficult data modeling decision for which SQL and relational databases might not have been optimized.

通常来说，武断地舍弃多年以来的设计思路是不明智的。当你要把数据存到一个数据库里时，可以考虑选用SQL和关系型数据模型，这些是数十年研究和开发的成果，提供了丰富的数据模型，在执行复杂操作上提供了简单易理解的保证。而当你遇到一个特殊的问题，可能涉及到大数据、高负载、或者你的数据结构在SQL和关系型数据模型下很难得到优化，NoSQL可能是更好的选择。
### 13.1.2. NoSQL灵感来源 ###

> The NoSQL movement finds much of its inspiration in papers from the research community. While many papers are at the core of design decisions in NoSQL systems, two stand out in particular.

NoSQL运动很多是受到了相关研究论文的启发，很多论文都被应用在NoSQL系统中，其中两篇尤为突出。

> Google's BigTable [  http://www.aosabook.org/en/bib1.html#bib:bigtable |CDG+06  ] presents an interesting data model, which facilitates sorted storage of multi-column historical data. Data is distributed to multiple servers using a hierarchical range-based partitioning scheme, and data is updated with strict consistency (a concept that we will eventually define in  http://www.aosabook.org/en/nosql.html#sec.nosql.consistency |Section 13.5 ).

Google 的 BigTable [CDG+06] 描述了一种有趣的数据模型，有序存储，在列中存储了数据的历史版本，数据按范围分区分布在多个服务器上，并且数据更新追求强一致性（我们会在13.5节中提到这个概念）。

> Amazon's Dynamo [  http://www.aosabook.org/en/bib1.html#bib:amazon:dynamo |DHJ+07  ] uses a different key-oriented distributed datastore. Dynamo's data model is simpler, mapping keys to application-specific blobs of data. The partitioning model is more resilient to failure, but accomplishes that goal through a looser data consistency approach called eventual consistency.

Amazon 的 Dynamo [DHJ+07] 使用了一种面向键的分布式数据存储方式，Dynamo 的数据模型更加简单，将键映射到数据块，它的数据分片模型具有更强的容灾性，因为使用一种松散的数据一致性：最终一致性。

> We will dig into each of these concepts in more detail, but it is important to understand that many of them can be mixed and matched. Some NoSQL systems such as HBase((http://hbase.apache.org/)) sticks closely to the BigTable design. Another NoSQL system named Voldemort((http://project-voldemort.com/)) replicates many of Dynamo's features. Still other NoSQL projects such as Cassandra((http://cassandra.apache.org/)) have taken some features from BigTable (its data model) and others from Dynamo (its partitioning and consistency schemes).

接下来我们会深入探讨这些设计思想，同时还要知道，有些设计思想是可以混搭使用的。有些NoSQL系统，比如像HBase，他们在设计上更接近BigTable，而Voldemort系统有很多Dynamo的特点。同时还有像Cassandra这种两种设计都采用的系统（它的数据模型和BigTable类似，分片策略和一致性机制和Dynamo类似）。

### 13.1.3. 特征和考虑因素 ###

> NoSQL systems part ways with the hefty SQL standard and offer simpler but piecemeal solutions for architecting storage solutions. These systems were built with the belief that in simplifying how a database operates over data, an architect can better predict the performance of a query. In many NoSQL systems, complex query logic is left to the application, resulting in a data store with more predictable query performance because of the lack of variability in queries

NoSQL系统舍弃了一些SQL标准中的功能，取而代之的是一些简单灵活的功能。NoSQL的思想是通过简化对数据的操作，使得操作的执行效率变得可预估。在很多NoSQL系统里，复杂的操作都是留给应用层来做的，这样，我们对数据层进行的操作得到简化，让操作效率可预知，

> NoSQL systems part with more than just declarative queries over the relational data. Transactional semantics, consistency, and durability are guarantees that organizations such as banks demand of databases. *Transactions* provide an all-or-nothing guarantee when combining several potentially complex operations into one, such as deducting money from one account and adding the money to another. *Consistency* ensures that when a value is updated, subsequent queries will see the updated value. *Durability* guarantees that once a value is updated, it will be written to stable storage (such as a hard drive) and recoverable if the database crashes.

NoSQL系统不仅舍弃了很多关系数据库中的操作。事务性语义，一致性，和持久性是一些组织对数据库的需求，比如银行。事务机制为一个事务内执行多条命令提供了all-or-nothing保证，比如转账时，将金额从一个账户减去，加到另一个账户中。一致性保证了当一个数据更新后，其后的操作都能看到这个更新。持久性保证俩一旦一个数据更新了，它会被写到持久化存储设备上（比如硬盘），并且在数据库崩溃后数据可恢复。

> NoSQL systems relax some of these guarantees, a decision which, for many non-banking applications, can provide acceptable and predictable behavior in exchange for improved performance. These relaxations, combined with data model and query language changes, often make it easier to safely partition a database across multiple machines when the data grows beyond a single machine's capability.

NoSQL通过放宽对上述某些特性的要求，可以为一些非银行类的应用提供更好的性能。对这些要求的放宽，加上数据模型和操作语言的改变，使得数据量超出单个机器的容量时，可以轻松将数据库分片到多台机器上。

> NoSQL systems are still very much in their infancy. The architectural decisions that go into the systems described in this chapter are a testament to the requirements of various users. The biggest challenge in summarizing the architectural features of several open source projects is that each one is a moving target. Keep in mind that the details of individual systems will change. When you pick between NoSQL systems, you can use this chapter to guide your thought process, but not your feature-by-feature product selection.

NoSQL系统还处在发展的初期阶段。本章中提到的很多NoSQL架构都是用于满足各种不同用户的需求的。

对这些架构进行总结不太可能，因为它们总在变化。所以希望你能记住的一点是，这些NoSQL系统不是一成不变的。当你需要在NoSQL系统之间进行选择时，这章内容可以指导你，却不能直接给出答案。

> As you think about NoSQL systems, here is a roadmap of considerations:

  * *Data and query model*: Is your data represented as rows, objects, data structures, or documents? Can you ask the database to calculate aggregates over multiple records?
  * *Durability*: When you change a value, does it immediately go to stable storage? Does it get stored on multiple machines in case one crashes?
  * *Scalability*: Does your data fit on a single server? Do the amount of reads and writes require multiple disks to handle the workload?
  * *Partitioning*: For scalability, availability, or durability reasons, does the data need to live on multiple servers? How do you know which record is on which server?
  * *Consistency*: If you've partitioned and replicated your records across multiple servers, how do the servers coordinate when a record changes?
  * *Transactional semantics*: When you run a series of operations, some databases allow you to wrap them in a transaction, which provides some subset of ACID (Atomicity, Consistency, Isolation, and Durability) guarantees on the transaction and all others currently running. Does your business logic require these guarantees, which often come with performance tradeoffs?
  * *Single-server performance*: If you want to safely store data on disk, what on-disk data structures are best-geared toward read-heavy or write-heavy workloads? Is writing to disk your bottleneck?
  * *Analytical workloads*: We're going to pay a lot of attention to lookup-heavy workloads of the kind you need to run a responsive user-focused web application. In many cases, you will want to build dataset-sized reports, aggregating statistics across multiple users for example. Does your use-case and toolchain require such functionality?

> While we will touch on all of these consideration, the last three, while equally important, see the least attention in this chapter.

当你去考查一个NoSQL系统的时候，下面的的几点是值得注意的：

  * 数据模型及操作模型：你的数据模型是用什么表示的，是元组、对象还是文档？这个系统是否能进行一些统计工作？
  * 持久性：当你更新数据时，新的数据是否立刻写到持久化存储中？新的数据是否同步到多台机器上以防止机器故障？
  * 扩展性：单个服务器是否能容纳你的数据？你的读写量是否需要多个磁盘来均摊处理？
  * 分区策略：考虑到你对扩展性，可用性或者持久性的要求，你是否需要一份数据被存在多台机器上？你如何知道数据在哪台机器上？
  * 一致性：你的数据是否被复制到了多台机器上，这些分布在不同节点的数据如何保证一致性？
  * 事务机制：当你要执行一系列命令时，有些数据库允许你将这些操作放在一个事务里，保证了ACID中的部分性质。你的业务是否需要ACID的事务机制，虽然这么做在性能方面会打折扣，
  * 单机性能：如果你要把数据存在磁盘上，哪种数据结构最符合你的需求（你的需求是读操作多还是写操作多）？写磁盘操作是否会成为瓶颈？
  * 负载可评估：对于一个读多写少的应用，诸如响应用户请求的web应用，我们总会花很多精力来关注负载情况。你可能需要进行数据规模的监控，对多个用户的数据进行汇总统计。你的应用场景是否需要这样的功能呢？

尽管后三点在本章没有独立的小节进行描述，但它们和其它几点一样重要，下面就让我们对以上的几点进行阐述。

## 13.2. NoSQL的数据模型及查询模型 ##

> The data model of a database specifies how data is logically organized. Its query model dictates how the data can be retrieved and updated. Common data models are the relational model, key-oriented storage model, or various graph models. Query languages you might have heard of include SQL, key lookups, and MapReduce. NoSQL systems combine different data and query models, resulting in different architectural considerations.

数据库的数据模型规定了逻辑上数据是如何组织的，操作模型则是如何读取和更新数据。通常的数据模型包括关系模型、面向键模型、以及多种图模型。关于查询语言，你可能听说过的有SQL、键查询、MapReduce。不同的NoSQL选用不同的数据模型和查询模型，会有不同的架构。

### 13.2.1. 面向键的数据模型 ###

> NoSQL systems often part with the relational model and the full expressivity of SQL by restricting lookups on a dataset to a single field. For example, even if an employee has many properties, you might only be able to retrieve an employee by her ID. As a result, most queries in NoSQL systems are key lookup-based. The programmer selects a key to identify each data item, and can, for the most part, only retrieve items by performing a lookup for their key in the database.

NoSQL通常将对数据集的查找限制在单个域内，弃用了关系模型和SQL的表达方式。比如说，尽管职员有很多属性，你可能只能通过她的ID检索到这个特定的职员。因此，NoSQL系统中的大部分操作都是基于键查询的。编程人员选择一个键来区分数据，并且只能通过查询键来检索到想要的数据。

> In key lookup-based systems, complex join operations or multiple-key retrieval of the same data might require creative uses of key names. A programmer wishing to look up an employee by his employee ID and to look up all employees in a department might create two key types. For example, the key ''employee:30'' would point to an employee record for employee ID 30, and ''employee_departments:20'' might contain a list of all employees in department 20. A join operation gets pushed into application logic: to retrieve employees in department 20, an application first retrieves a list of employee IDs from key ''employee_departments:20'', and then loops over key lookups for each ''employee:ID'' in the employee list.

在基于键查询的系统中，复杂的连接操作或多条件的检索操作就不那么容易了，需要我们创造性地使用键名。编程人员希望能通过员工ID查询到员工信息，并且能通过部门号查询一个部门中的所有员工信息，这样就需要建立两种类型的键值对。例如，''emplyee:30''这个键指向员工ID为30的员工信息，''employee_departments:20''这个键可能包含了一个列表，列表存储了20号部门的所有员工。这样数据库中的连接操作就被转换成业务层的逻辑了：要获取部门号为20的所有员工的信息，应用层可以先获取''employee_departments:20''这个列表，然后再循环地拿这个列表中的ID通过获取''employee:ID''得到所有员工的信息，

> The key lookup model is beneficial because it means that the database has a consistent query pattern—the entire workload consists of key lookups whose performance is relatively uniform and predictable. Profiling to find the slow parts of an application is simpler, since all complex operations reside in the application code. On the flip side, the data model logic and business logic are now more closely intertwined, which muddles abstraction.

基于键查询的好处是，对数据库的操作模式是固定的，这些操作所产生的负载也是相对固定且可预知的。这样，分析整个应用中的性能瓶颈这种事，就变得简单了，因为复杂的逻辑操作并不是放在数据库里面黑箱操作了。而另一方面，数据模型逻辑和业务逻辑交叉在一起，使原本的抽象层次变模糊了。

> Let's quickly touch on the data associated with each key. Various NoSQL systems offer different solutions in this space.

让我们快速地看一下各种键值模型，不同的NoSQL系统在这方面提供不同的实现。

#### key-value 存储 ####

> The simplest form of NoSQL store is a *key-value* store. Each key is mapped to a value containing arbitrary data. The NoSQL store has no knowledge of the contents of its payload, and simply delivers the data to the application. In our Employee database example, one might map the key ''employee:30'' to a blob containing JSON or a binary format such as Protocol Buffers((http://code.google.com/p/protobuf/)), Thrift((http://thrift.apache.org/)), or Avro((http://avro.apache.org/)) in order to encapsulate the information about employee 30.

NoSQL中最简单的存储方式就是Key-Value。每个key值对应一个任意的数据值。对NoSQL系统来说，这个任意的数据值是什么，它并不知道，只是简单的将数据传递给应用。比如在员工数据库里，''exployee:30''这个key对应的可能是JSON或一段包含员工所有信息的二进制数据，这个二进制的格式可能是Protocol Buffer、Thrift或者Avro，

> If a developer uses structured formats to store complex data for a key, she must operate against the data in application space: a key-value data store generally offers no mechanisms for querying for keys based on some property of their values. Key-value stores shine in the simplicity of their query model, usually consisting of ''set'', ''get'', and ''delete'' primitives, but discard the ability to add simple in-database filtering capabilities due to the opacity of their values. Voldemort, which is based on Amazon's Dynamo, provides a distributed key-value store. BDB((http://www.oracle.com/technetwork/database/berkeleydb/overview/index.html)) offers a persistence library that has a key-value interface.

如果开发者使用结构化的复杂数据作为value对应一个key，他必须要针对应用空间的数据进行操作：Key-Value存储一般不提供根据某个非键属性的值来查找key的机制。Key-Value存储的优点在于其简单的操作模型，通常只提供像''set''、''get''和''delete''这样的操作，而舍弃了简单的过滤功能。以Dynamo为原型的Voldemort数据库，就只提供了分布式的Key-Value存储功能。BDB是一个提供Key-Value接口的持久化数据存储引擎。

#### key-数据结构 存储 ####

> Key-data structure stores, made popular by Redis((http://redis.io/)), assign each value a type. In Redis, the available types a value can take on are integer, string, list, set, and sorted set. In addition to ''set''/''get''/''delete'', type-specific commands, such as increment/decrement for integers, or push/pop for lists, add functionality to the query model without drastically affecting performance characteristics of requests. By providing simple type-specific functionality while avoiding multi-key operations such as aggregation or joins, Redis balances functionality and performance.

Key-数据结构存储，给Kay-Value中的value赋了类型，由Redis带动发展。在Redis中，Value的类型包括数字、字符串、列表、集合以及有序集合。除了''set''/''get''/''delete''操作以外，Redis还提供了很多针对以上数据类型的特殊操作，比如针对数字可以执行增、减操作，对列表可以执行 push/pop 操作，而这些对特定数据类型的特定操作并没有对性能造成很大的影响。通过提供这种针对特定类型进行操作的功能，同时避免了多个key的操作（如，连接操作），Redis可以说实现了功能与性能的平衡。

#### key-文档 存储 ####

> Key-document stores, such as CouchDB((http://couchdb.apache.org/)), MongoDB((http://www.mongodb.org/)), and Riak((http://www.basho.com/products_riak_overview.php)), map a key to some document that contains structured information. These systems store documents in a JSON or JSON-like format. They store lists and dictionaries, which can be embedded recursively inside one-another.

Key-文档存储的代表有CouchDB、MongoDB和Riak。这种存储方式下Key-Value的Value是包含有结构化信息的文档。通常这些文档是用JSON或者类似于JSON的格式进行存储。文档可以存储列表和字典.

> MongoDB separates the keyspace into collections, so that keys for Employees and Department, for example, do not collide. CouchDB and Riak leave type-tracking to the developer. The freedom and complexity of document stores is a double-edged sword: application developers have a lot of freedom in modeling their documents, but application-based query logic can become exceedingly complex.

MongoDB将Key按业务分到各个容器里，员工信息和部门信息的Key就不会冲突。CouchDB和Riak把类型跟踪这种事留给了开发者去完成。文档型存储的灵活性和复杂性是一把双刃剑：一方面，开发者可以任意组织文档的结构，另一方面，应用层的操作逻辑会变得比较复杂。

#### BigTable的列族式存储 ####

> HBase and Cassandra base their data model on the one used by Google's BigTable. In this model, a key identifies a row, which contains data stored in one or more Column Families (CFs). Within a CF, each row can contain multiple columns. The values within each column are timestamped, so that several versions of a row-column mapping can live within a CF.

HBase和Cassandra的数据模型都借鉴自Google的BigTable。这种数据模型的特点是列式存储，每一行数据的各项被存储在不同的列中（这些列的集合称作列族）。而每一列中每一个数据都包含一个时间戳属性，这样列中的同一个数据项的多个版本都能保存下来，

> Conceptually, one can think of Column Families as storing complex keys of the form (row ID, CF, column, timestamp), mapping to values which are sorted by their keys. This design results in data modeling decisions which push a lot of functionality into the keyspace. It is particularly good at modeling historical data with timestamps. The model naturally supports sparse column placement since row IDs that do not have certain columns do not need an explicit NULL value for those columns. On the flip side, columns which have few or no NULL values must still store the column identifier with each row, which leads to greater space consumption.

列式存储可以理解成这样，将行ID、列族号，列号以及时间戳一起，组成一个Key，然后将Value按Key的顺序进行存储。这样的设计使这种存储能够实现一些特别的功能。最常用的就是将一个数据的多个版本存成时间戳不同的几个值，这样就能很方便的保存历史数据。这种结构当然也能很好地支持稀疏数据，因为对于不存在数据的某行某列，我们并不需要用NULL表示它。而另一方面，对于那些很少有某一行有NULL值的列，由于每一个数据必须包含列标识符，这又会造成空间的浪费，

> Each project data model differs from the original BigTable model in various ways, but Cassandra's changes are most notable. Cassandra introduces the notion of a supercolumn within each CF to allow for another level of mapping, modeling, and indexing. It also does away with a notion of locality groups, which can physically store multiple column families together for performance reasons.

NoSQL系统的数据模型跟原始的BigTable多少有些差别，其中Cassandra的差异最为显著。Cassandra引入了超级列（supercolumn）的概念，在每个列族里，允许更高层次的映射，建模和索引。这一做法也取代了存储位置分组（locality groups）的概念（这一概念的实现可以让多个列族的数据存储在一起，以提高存取性能）。

### 13.2.2. 图结构存储 ###

> One class of NoSQL stores are graph stores. Not all data is created equal, and the relational and key-oriented data models of storing and querying data are not the best for all data. Graphs are a fundamental data structure in computer science, and systems such as HyperGraphDB((http://www.hypergraphdb.org/index)) and Neo4J((http://neo4j.org/)) are two popular NoSQL storage systems for storing graph-structured data. Graph stores differ from the other stores we have discussed thus far in almost every way: data models, data traversal and querying patterns, physical layout of data on disk, distribution to multiple machines, and the transactional semantics of queries. We can not do these stark differences justice given space limitations, but you should be aware that certain classes of data may be better stored and queried as a graph.

图结构存储是NoSQL的另一种存储实现。不是所有的数据都是一样的，关系数据模型或面向键的数据模型并不永远是最好的选择。图是计算机科学中基础的数据结构，HyperGraphDB和Neo4j是现在流行的两个图结构存储的数据库。图结构存储完全不同于我们前面讨论过的几种方式，几乎每个方面都不一样，包括：数据模型，数据遍历，操作模式，物理存储，分布式组织方式，以及事务机制。由于篇幅的限制，对这些方面我们暂时不做深入的讨论，但你需要知道，某些数据使用图结构存储可能会更好。

### 13.2.3. 复杂查询 ###

> There are notable exceptions to key-only lookups in NoSQL systems. MongoDB allows you to index your data based on any number of properties and has a relatively high-level language for specifying which data you want to retrieve. BigTable-based systems support scanners to iterate over a column family and select particular items by a filter on a column. CouchDB allows you to create different views of the data, and to run MapReduce tasks across your table to facilitate more complex lookups and updates. Most of the systems have bindings to Hadoop or another MapReduce framework to perform dataset-scale analytical queries.

在NoSQL存储系统中，有很多比键查找更复杂的操作。比如MongoDB允许用户基于任意数量的属性建立索引，并且可以使用高级一点的语言来查询数据。基于BigTable的系统支持对一个列族进行遍历，并可以从一列中过滤选出特定的数据项。CouchDB允许你创建同一份数据的多个视图，还可以运行MapReduce任务来实现一些更为复杂的查询或者更新操作。大多数NoSQL系统都支持与Hadoop或者其它一些MapReduce框架结合来进行一些大规模数据分析工作。

### 13.2.4. 事务 ###

> NoSQL systems generally prioritize performance over transactional semantics. Other SQL-based systems allow any set of statements—from a simple primary key row retrieval, to a complicated join between several tables which is then subsequently averaged across several fields—to be placed in a transaction.

NoSQL系统一般更看重系统的性能而不是事务机制，而传统的关系型数据库可以将任意语句放到一个事务中去，从简单的键值查询，到复杂的多表联合查询然后求某个域的均值。

> These SQL databases will offer ACID guarantees between transactions. Running multiple operations in a transaction is Atomic (the A in ACID), meaning all or none of the operations happen. Consistency (the C) ensures that the transaction leaves the database in a consistent, uncorrupted state. Isolation (the I) makes sure that if two transactions touch the same record, they will do without stepping on each other's feet. Durability (the D, covered extensively in the next section), ensures that once a transaction is committed, it's stored in a safe place.

传统的SQL数据库的事务通常都是支持ACID的事务机制。A代表原子性，即在事务中执行多个操作是原子性的，事务中的操作要么全部执行，要么一个都不执行。C代表一致性，即保证进行事务的过程中整个数据库的状态是一致的，不会出现数据花掉的情况。I代表隔离性，即两个事务不会相互影响，覆盖彼此数据等。D表示持久性，即事务一旦完成，那么数据应该是被写到安全的，持久化存储的设备上（比如磁盘）。

> ACID-compliant transactions keep developers sane by making it easy to reason about the state of their data. Imagine multiple transactions, each of which has multiple steps (e.g., first check the value of a bank account, then subtract $60, then update the value). ACID-compliant databases often are limited in how they can interleave these steps while still providing a correct result across all transactions. This push for correctness results in often-unexpected performance characteristics, where a slow transaction might cause an otherwise quick one to wait in line.

符合ACID的事务令开发者可以清楚地推断出当前数据的状态。想象一下有多个事务正在同时执行，每个事务都有多个步骤（比如，首先查询一个银行账户的余额，再从中减掉$60，然后更新账户余额）。符合ACID的数据库如果要确保正确的执行结果，就需要限制对事务的交叉执行。这样，为了追求正确性，就可能导致一个很简单的操作需要等待一个复杂操作完成才能进行的情况，

> Most NoSQL systems pick performance over full ACID guarantees, but do provide guarantees at the key level: two operations on the same key will be serialized, avoiding serious corruption to key-value pairs. For many applications, this decision will not pose noticeable correctness issues, and will allow quick operations to execute with more regularity. It does, however, leave more considerations for application design and correctness in the hands of the developer.

对很多NoSQL系统来说，对性能的考虑远在ACID的保证之上，但其实在键级别上还是有提供保证的：为了避免冲突，两个对同一个键的操作会被串行执行。对很多应用来说，这种设计在正确性上不会引起很大的问题，但是却换来了执行效率。但同时，这样增多了开发者在应用设计和正确性方面需要考虑的东西。

> Redis is the notable exception to the no-transaction trend. On a single server, it provides a ''MULTI'' command to combine multiple operations atomically and consistently, and a ''WATCH'' command to allow isolation. Other systems provide lower-level test-and-set functionality which provides some isolation guarantees.

在取消事务机制的浪潮中，Redis翻出了一朵浪花。在单个服务器上，它提供了''MULTI''命令来将多条操作合并起来以提供原子性和一致性，还有''WATCH''命令来保证隔离性。这和其它一些系统提供test-and-set这样的低层级的隔离机制类似。

### 13.2.5. Schema-free存储 ###

> A cross-cutting property of many NoSQL systems is the lack of schema enforcement in the database. Even in document stores and column family-oriented stores, properties across similar entities are not required to be the same. This has the benefit of supporting less structured data requirements and requiring less performance expense when modifying schemas on-the-fly. The decision leaves more responsibility to the application developer, who now has to program more defensively. For example, is the lack of a ''lastname'' property on an employee record an error to be rectified, or a schema update which is currently propagating through the system? Data and schema versioning is common in application-level code after a few iterations of a project which relies on sloppy-schema NoSQL systems.

一个很多NoSQL都有的共同点，就是它通常没有强制的模式约束。即使是在文档型存储或列族式存储上，也不会要求某一个属性在每一行数据上都必须存在。这在非结构化数据的存储上更方便，同时也省去了运行时修改模式的代价。而这一机制对应用层的容错性要求可能会更高。比如说，如果某一个员工的记录里缺少''lastname''这一项，这是否算是需要修正的错误，还是某个模式更新的结果。还有一个问题，就是数据库的结构可能会因为项目的多次迭代而变得混乱不堪。

## 13.3. 数据持久性 ##

> Ideally, all data modifications on a storage system would immediately be safely persisted and replicated to multiple locations to avoid data loss. However, ensuring data safety is in tension with performance, and different NoSQL systems make different *data durability* guarantees in order to improve performance. Failure scenarios are varied and numerous, and not all NoSQL systems protect you against these issues.

理想的情况下，所有数据修改会被立刻写到持久化存储设备上，同时被复制到不同位置的节点上，以防止数据丢失，但是，像这样会影响到数据库的性能。为了改善性能，不同的NoSQL对数据持久性的保证程度是不同的。并不是所有NoSQL都能应对可能发生的故障。

> A simple and common failure scenario is a server restart or power loss. Data durability in this case involves having moved the data from memory to a hard disk, which does not require power to store data. Hard disk failure is handled by copying the data to secondary devices, be they other hard drives in the same machine (RAID mirroring) or other machines on the network. However, a data center might not survive an event which causes correlated failure (a tornado, for example), and some organizations go so far as to copy data to backups in data centers several hurricane widths apart. Writing to hard drives and copying data to multiple servers or data centers is expensive, so different NoSQL systems trade off durability guarantees for performance.

一种典型的出错情形是机器重启或者断电了，这时候需要将数据从内存转存到硬盘才能保证数据持久性，因为硬盘不会因断电而丢失数据。而要避免硬盘损坏这种故障，就需要将数据冗余地存在其它设备上，比如在同一机器上使用RAID来做镜像，或者将数据同步到网络中的不同机器。但是，如果发生像飓风这样的巨大灾害，一整个数据中心可能都不能幸免，所以有些机构会将数据备份到地理距离相对较远的数据中心，而这样一来，写磁盘和拷贝数据到不同服务器或数据中心的代价就变得昂贵，所以，各个NoSQL系统在数据持久性和性能上的权衡策略也不太一样。

### 13.3.1. 单机的持久性 ###

> The simplest form of durability is a *single-server durability*, which ensures that any data modification will survive a server restart or power loss. This usually means writing the changed data to disk, which often bottlenecks your workload. Even if you order your operating system to write data to an on-disk file, the operating system may buffer the write, avoiding an immediate modification on disk so that it can group several writes together into a single operation. Only when the ''fsync'' system call is issued does the operating system make a best-effort attempt to ensure that buffered updates are persisted to disk.

持久性最简单的形式是单个服务器的持久性，保证了数据更新不会因服务器重启或断电而丢失。这通常意味着要将更改的数据写到磁盘中，这一般会称为系统的瓶颈。甚至如果你命令你的操作系统将数据写到存在磁盘上的一个文件中时，操作系统可能会先将写操作缓存起来，而不是直接去写磁盘，这样可以将多个写操作合成一个操作。只有当调用系统调用''fsync''时，系统才会尽可能把已经缓存的数据写到磁盘中。

> Typical hard drives can perform 100-200 random accesses (seeks) per second, and are limited to 30-100 MB/sec of sequential writes. Memory can be orders of magnitudes faster in both scenarios. Ensuring efficient single-server durability means limiting the number of random writes your system incurs, and increasing the number of sequential writes per hard drive. Ideally, you want a system to minimize the number of writes between ''fsync'' calls, maximizing the number of those writes that are sequential, all the while never telling the user their data has been successfully written to disk until that write has been ''fsync''ed. Let's cover a few techniques for improving performance of single-server durability guarantees.

一般的磁盘大概能进行每秒100-200次的随机访问，每秒30-100MB的顺序写入速度。而内存在这两方面的性能都有数量级上的提升。要保证服务器的性能，则意味着尽量减少随机写的次数，取而代之的是对磁盘进行顺序写。也就是说我们需要减少在两次''fsync''调用之间的写操作次数，而增加顺序写操作的次数。下面我们说一下一些在单机持久性的保证下提高性能的方法。

#### Control fsync Frequency ####

> Memcached((http://memcached.org/)) is an example of a system which offers no on-disk durability in exchange for extremely fast in-memory operations. When a server restarts, the data on that server is gone: this makes for a good cache and a poor durable data store.

Memcached是一个非常快的纯内存操作的系统。当然，在我们进行服务器重启或者服务器意外断电后，这些数据就全丢了。因此Memcached是一个非常不错的缓存，但是做不了持久化存储，

> Redis offers developers several options for when to call ''fsync''. Developers can force an ''fsync'' call after every update, which is the slow and safe choice. For better performance, Redis can ''fsync'' its writes every N seconds. In a worst-case scenario, the you will lose last N seconds worth of operations, which may be acceptable for certain uses. Finally, for use cases where durability is not important (maintaining coarse-grained statistics, or using Redis as a cache), the developer can turn off ''fsync'' calls entirely: the operating system will eventually flush the data to disk, but without guarantees of when this will happen.

Redis提供了几种调用''fsync''的时机供选择。开发者可以每次更新后都执行一次''fsync''，这是慢而安全的选择。Redis可以每N秒调用一次''fsync''，这样性能更好一点。在最坏的情况下，你可能会丢失最后N秒的操作，当然这可能对某些应用场景是可接受的。最后，当持久性不重要时（只要求粗粒度的统计结果，或仅把Redis当做缓存时），开发者可以直接关闭''fsync''的调用：由操作系统决定何时将数据更新到磁盘上。

#### Increase Sequential Writes by Logging ####

> Several data structures, such as B+Trees, help NoSQL systems quickly retrieve data from disk. Updates to those structures result in updates in random locations in the data structures' files, resulting in several random writes per update if you ''fsync'' after each update. To reduce random writes, systems such as Cassandra, HBase, Redis, and Riak append update operations to a sequentially-written file called a *log*. While other data structures used by the system are only periodically ''fsync''ed, the log is frequently ''fsync''ed. By treating the log as the ground-truth state of the database after a crash, these storage engines are able to turn random updates into sequential ones.

有几种数据结构，比如说B+树，可以帮助NoSQL系统快速的检索到数据。要对这些数据结构进行更新，就要对存储这些数据结构的文件进行随机写操作。如果你在每次操作后再调用一次''fsync''，那就会造成频繁的磁盘随机访问了。为了减少随机写次数，有些系统像Cassandra，HBase，Redis，和Riak会把更新操作顺序地写到日志文件里。相对于存储系统中的其它数据结构，上面说到的日志文件可以频繁的进行''fsync''操作。这个日志文件记录了操作行为，可以用于在出现故障后恢复丢失那段时间的数据，这样存储引擎就可以把随机写变成顺序写了，

> While NoSQL systems such as MongoDB perform writes in-place in their data structures, others take logging even further. Cassandra and HBase use a technique borrowed from BigTable of combining their logs and lookup data structures into one *log-structured merge tree*. Riak provides similar functionality with a *log-structured hash table*. CouchDB has modified the traditional B+Tree so that all changes to the data structure are appended to the structure on physical storage. These techniques result in improved write throughput, but require a periodic log compaction to keep the log from growing unbounded.

有的NoSQL系统，比如MongoDB，是直接在原数据上进行更新操作的，而其他的一些NoSQL系统采用了更加复杂的日志策略。Cassandra和HBase借鉴了BigTable的做法，即把日志和原有的数据组合起来的日志结构的合并树(log-structured merge tree)。Riak也实现了相似的功能，即一个日志结构的hash表。

CouchDB对传统的B+树结构进行了修改，使得对树的更新可以使用顺序的追加写操作来实现（这种B+树被称作append-only B-Tree）。这些方法的使用，使得存储系统的写操作承载力更高，但同时为了防止数据异构后膨胀得过大，需要定期对日志进行压缩防止日志无限制地增加。

#### Increase Throughput by Grouping Writes ####

> Cassandra groups multiple concurrent updates within a short window into a single ''fsync'' call. This design, called *group commit*, results in higher latency per update, as users have to wait on several concurrent updates to have their own update be acknowledged. The latency bump comes at an increase in throughput, as multiple log appends can happen with a single ''fsync''. As of this writing, every HBase update is persisted to the underlying storage provided by the Hadoop Distributed File System (HDFS)((http://hadoop.apache.org/hdfs/)), which has recently seen patches to allow support of appends that respect ''fsync'' and group commit.

Cassandra有一个机制，它会把一小段时间内的几个并发的写操作放在一起进行一次''fsync''调用。这种做法叫group commit，它导致的一个结果就是更新操作会有些延迟，因为一个更新操作需要等并发的几个更新操作一起进行提交。通过这种方法可以提高写操作承载力，因为一次''fsync''可以提交多个日志的更新。写这篇文章的时候，关于HBase的更新都是关于其底层的Hadoop分布式文件系统HDFS，它最近的一些补丁也在实现一些支持顺序写和group commit的机制。

### 13.3.2. 多服务器的持久性 ###

> Because hard drives and machines often irreparably fail, copying important data across machines is necessary. Many NoSQL systems offer multi-server durability for data.

由于硬件层面有时候会造成无法恢复的故障，所以对于一些重要数据，跨机器做备份保存是必备的安全措施。一些NoSQL系统提供了多机持久性的支持，

> Redis takes a traditional master-slave approach to replicating data. All operations executed against a master are communicated in a log-like fashion to slave machines, which replicate the operations on their own hardware. If a master fails, a slave can step in and serve the data from the state of the operation log that it received from the master. This configuration might result in some data loss, as the master does not confirm that the slave has persisted an operation in its log before acknowledging the operation to the user. CouchDB facilitates a similar form of directional replication, where servers can be configured to replicate changes to documents on other stores.

Redis采用了传统的master-slave节点数据同步的方式。所有在master上执行的操作，都会通过类似于日志的结构传递给slave再执行一遍。如果master发生故障，slave可以继续执行完master传来的操作日志并且成为新的master。可能这中间会导致一些数据丢失，因为master同步操作到slave是非阻塞的，master并不知道操作是否已经同步到slave了。CouchDB实现了一个类似的有向同步功能，可以设置服务器将数据更新复制到其他地方的文档中。

> MongoDB provides the notion of replica sets, where some number of servers are responsible for storing each document. MongoDB gives developers the option of ensuring that all replicas have received updates, or to proceed without ensuring that replicas have the most recent data. Many of the other distributed NoSQL storage systems support multi-server replication of data. HBase, which is built on top of HDFS, receives multi-server durability through HDFS. All writes are replicated to two or more HDFS nodes before returning control to the user, ensuring multi-server durability.

MongoDB提供了一个叫副本集（replica sets）的概念，每一个文档都要被保存在副本集中的机器上。MongoDB给开发者提供了两种选择，一个是确保所有的副本都收到更新，二是继续执行而不需要确保副本都是最新的数据。很多其它的分布式NoSQL存储都提供了类似的多服务器持久性支持。由于HBase的底层存储是HDFS，它也就自然的获得了HDFS提供的多服务器持久性保证。HDFS的多机持久性保证是通过把每个写操作都同步到两个或以上的节点来实现的，

> Riak, Cassandra, and Voldemort support more configurable forms of replication. With subtle differences, all three systems allow the user to specify ''N'', the number of machines which should ultimately have a copy of the data, and ''W`<''N'', the number of machines that should confirm the data has been written before returning control to the user.

Riak、Cassandra和Voldemort提供了更灵活的可配置的方式。虽然也有细微的差别，但基本上都是有一个可以设置的参数''N''，意味着每一个数据最终会被备份的份数，还有一个参数''W''，''W`<''N''，代表每个写操作至少需要同步到多少个机器上才返回成功。

> To handle cases where an entire data center goes out of service, multi-server replication across data centers is required. Cassandra, HBase, and Voldemort have rack-aware configurations, which specify the rack or data center in which various machines are located. In general, blocking the user's request until a remote server has acknowledged an update incurs too much latency. Updates are streamed without confirmation when performed across wide area networks to backup data centers.

为了应对整个数据中心出现故障的情况，需要实现跨数据中心的多机备份功能。Cassandra、HBase和Voldemort都实现了一个机架位置可知的配置，这种配置方式使得整个分布式系统可以了解各个节点的地理位置分布情况。如果一个操作需要等待另外的数据中心的同步操作成功才返回给用户，那时间就太长了。所以通常跨数据中心的同步备份操作都是异步进行的，用户并不需要等待另一个数据中心同步的同步操作执行成功。

## 13.4. 横向扩展带来性能提升 ##

> Having just spoken about handling failure, let's imagine a rosier situation: success! If the system you build reaches success, your data store will be one of the components to feel stress under load. A cheap and dirty solution to such problems is to scale up your existing machinery: invest in more RAM and disks to handle the workload on one machine. With more success, pouring money into more expensive hardware will become infeasible. At this point, you will have to replicate data and spread requests across multiple machines to distribute load. This approach is called scale out, and is measured by the horizontal scalability of your system.

讨论过故障处理，让我们来构想一下理想的情景：成功！如果系统搭建成功，你的数据系统就要开始承担工作负载。一个简单粗暴的解决方法是通过升级你的机器来提升单机性能：通过加大内存和添加硬盘来应对越来越大的工作负载。但是逐渐地，投入在升级机器上的钱将不会产生之前那么大的效果。这时候你就要考虑把数据同步到不同的机器上，并且分摊访问请求，以此来分散负载，这个方法被称为横向扩展，可以用横向扩展能力来衡量。

> The ideal horizontal scalability goal is linear scalability, in which doubling the number of machines in your storage system doubles the query capacity of the system. The key to such scalability is in how the data is spread across machines. Sharding is the act of splitting your read and write workload across multiple machines to scale out your storage system. Sharding is fundamental to the design of many systems, namely Cassandra, HBase, Voldemort, and Riak, and more recently MongoDB and Redis. Some projects such as CouchDB focus on single-server performance and do not provide an in-system solution to sharding, but secondary projects provide coordinators to partition the workload across independent installations on multiple machines.

横向扩展的理想目标是达到线性的效果，即如果你增加一倍的机器，那么负载能力应该也能相应的增加一倍，关键是要考虑如何让数据在多台机器上分布。分片技术是将数据的读和写操作分散到不同机器上的技术，是很多系统设计的基础，比如说，Cassandra，HBase，Voldemort，和Riak，以及最近的MongoDB和Redis。但有的项目，比如说CouchDB，更加专注于单个服务器的性能，而不提供系统内置的分片机制，这时要用到其他工具来实现负载分配。

> Let's cover a few interchangeable terms you might encounter. We will use the terms sharding and partitioning interchangeably. The terms machine, server, or node refer to some physical computer which stores part of the partitioned data. Finally, a cluster or ring refers to the set of machines which participate in your storage system.

先说明一下你可能遇到的一些含义相同的术语。我们可能会交替地使用数据“分片”和”分区“。对于机器，服务器或者节点，可以统一的理解成物理上存储数据的机器。最后，集群或者机器环都是指组成你存储系统的机器的集合，

> Sharding means that no one machine has to handle the write workload on the entire dataset, but no one machine can answer queries about the entire dataset. Most NoSQL systems are key-oriented in both their data and query models, and few queries touch the entire dataset anyway. Because the primary access method for data in these systems is key-based, sharding is typically key-based as well: some function of the key determines the machine on which a key-value pair is stored. We'll cover two methods of defining the key-machine mapping: hash partitioning and range partitioning.

分片的意思是，不存在一台机器需要处理对整个数据集的写请求，也没有一台机器可以处理对整个数据集的读请求。大多数NoSQL系统的数据模型和操作模型都是面向键的，很少有对整个数据集进行查询的时候。在这些系统上主要通过键来对数据进行访问，所以分片也是基于键来做：有一个关于键的函数，确定该键值对存储的机器。我们会对hash分区和范围分区两种分区方式进行讨论。

### 13.4.1. 如果没有必要，请不要分片 ###

> Sharding adds system complexity, and where possible, you should avoid it. Let's cover two ways to scale without sharding: read replicas and caching.

分片会增加系统复杂程序，所以，你应该尽可能避免分片。让我们先讲讲不通过分片如何进行系统扩展：读写分离（read replicas）和使用缓存。

#### Read Replicas ####

> Many storage systems see more read requests than write requests. A simple solution in these cases is to make copies of the data on multiple machines. All write requests still go to a master node. Read requests go to machines which replicate the data, and are often slightly stale with respect to the data on the write master.

在许多存储系统中，读请求的数量要多于写请求的数量。在这种情况下有个简单的办法，就是用多台机器为数据做若干副本。所有的写请求仍旧由主节点来处理。读请求由保存有数据副本的机器来处理，但数据副本跟主节点中的数据相比，往往稍微有点滞后。

> If you are already replicating your data for multi-server durability in a master-slave configuration, as is common in Redis, CouchDB, or MongoDB, the read slaves can shed some load from the write master. Some queries, such as aggregate summaries of your dataset, which might be expensive and often do not require up-to-the-second freshness, can be executed against the slave replicas. Generally, the less stringent your demands for freshness of content, the more you can lean on read slaves to improve read-only query performance.

如果你已经采用了常见于Redis、CouchDB和MongoDB中的方法，将数据的副本们存入主从式（master-slave）配置的多服务器之中了，其中的负责处理读取操作的slave节点就能为负责写操作的master节点分担一部分负载。像对数据集进行统计这样的查询，可能会产生比较大的负载，但往往并不需要非常精确，就完全可以在slave中数据副本之上进行处理。一般来讲，对内容的实时性要求越低，就越能够依赖slave节点来提高只读性查询的性能。

#### Caching ####

> Caching the most popular content in your system often works surprisingly well. Memcached dedicates blocks of memory on multiple servers to cache data from your data store. Memcached clients take advantage of several horizontal scalability tricks to distribute load across Memcached installations on different servers. To add memory to the cache pool, just add another Memcached host.

将系统中最常用的数据放入缓存所带来的效果往往是出奇的好，Memcached就是用来把多台服务器之上的内存块当作数据存储的缓存来使用的。Memcached客户端利用了几个实现横向扩展能力的小技巧，可以将负载分给安装了Memcached的多台不同的服务器进行处理。想要增加缓存池中的内存总量时，只需再增加一台安装了Memcached的主机即可。

> Because Memcached is designed for caching, it does not have as much architectural complexity as the persistent solutions for scaling workloads. Before considering more complicated solutions, think about whether caching can solve your scalability woes. Caching is not solely a temporary band-aid: Facebook has Memcached installations in the range of tens of terabytes of memory!

因为Memcached是专为缓存而设计的，所以从架构上讲，它要比具有通过横向扩展能力来处理所有负载的数据存储方案简单。在考虑使用更加复杂的解决方案之前，请先考虑一下使用缓存能不能解决扩展性方面的难题。缓存可不只是一个临时性的创可贴型解决方案：Facebook部署了总量可达几十T内存的Memcached服务器！

> Read replicas and caching allow you to scale up your read-heavy workloads. When you start to increase the frequency of writes and updates to your data, however, you will also increase the load on the master server that contains all of your up-to-date data. For the rest of this section, we will cover techniques for sharding your write workload across multiple servers.

读写分离并使用缓存可以在面临负载中大量的是进行读取操作的情况下具有很好的扩展性。然而，如果数据写入请求和更新请求的频率开始增大，那么保存了所有最新数据的主服务器上的负载也会相应增大。在本小节中剩下的部分，我们将讨论将写入性的负载分散到多台服务器中进行处理所涉及到的技术。

### 13.4.2. 通过协调器进行分片 ###

> The CouchDB project focuses on the single-server experience. Two projects, Lounge and BigCouch, facilitate sharding CouchDB workloads through an external proxy, which acts as a front end to standalone CouchDB instances. In this design, the standalone installations are not aware of each other. The coordinator distributes requests to individual CouchDB instances based on the key of the document being requested.

CouchDB项目着重于处理单个服务器下的使用情况。另有两个项目，Lounge和BigCouch，使用了一个外部代理帮助CouchDB对负载进行分片，这个代理作为多台独立的CouchDB实例的前端运行。在这样的设计中，分别进行独立安装的多个CouchDB实例并不知晓其它实例的存在。由协调器基于文档请求中的键将请求分发给某个单个的CouchDB进行处理。

> Twitter has built the notions of sharding and replication into a coordinating framework called Gizzard((http://github.com/twitter/gizzard)). Gizzard takes standalone data stores of any type—you can build wrappers for SQL or NoSQL storage systems—and arranges them in trees of any depth to partition keys by key range. For fault tolerance, Gizzard can be configured to replicate data to multiple physical machines for the same key range.

Twitter利用分片和保存副本的概念构建了一个叫做Gizzard的协调性框架。Gizzard可以接受任意类型的数据存储系统 —— 你可以对SQL型或者NoSQL型数据存储系统进行一下封装 —— 然后将它们安排到任意深得的树中，从而可以按照键的范围对键进行分区处理，为了提高容错性，Gizzard可以配置为把同一个键的范围中的数据做多份副本存储多台物理机器之上。

### 13.4.3. 一致Hash环 ###

> Good hash functions distribute a set of keys in a uniform manner. This makes them a powerful tool for distributing key-value pairs among multiple servers. The academic literature on a technique called *consistent hashing* is extensive, and the first applications of the technique to data stores was in systems called *distributed hash tables* (*DHTs*). NoSQL systems built around the principles of Amazon's Dynamo adopted this distribution technique, and it appears in Cassandra, Voldemort, and Riak.

较好的哈希函数可以使一组键以一种均匀的方式进行分布。因此哈希函数成为了一种能够帮助我们把键值对分散保存到多台服务器之上的强大工具。有关一致性哈希技术的学术文献非常之多，第一次将这种技术应用到数据存储系统中的是一个叫做分布式哈希表（distributed hash table，简称DHT） 的系统。基于Amazon的Dynamo原理构建的NoSQL系统都采用了这种分布技术，这在Cassandra、Voldemort和Riak中都有所体现。

#### Hash Rings by Example ####

![](http://www.aosabook.org/images/nosql/hashring.png)

Figure 13.1: A Distributed Hash Table Ring

> Consistent hash rings work as follows. Say we have a hash function ''H'' that maps keys to uniformly distributed large integer values. We can form a ring of numbers in the range [1, L] that wraps around itself with these values by taking H(key) mod L for some relatively large integer L. This will map each key into the range [1,L]. A consistent hash ring of servers is formed by taking each server's unique identifier (say its IP address), and applying H to it. You can get an intuition for how this works by looking at the hash ring formed by five servers (''A''-''E'') in Figure 13.1.

下面讲讲一致性哈希环的原理，假设有哈希函数''H''，它可将键以一种均匀分布的方式影射为一个比较大的整数值。取某个比较大的整数L，通过将H(key)对L进行取余，即H(key)modL，就可以把H(key)所得到的值中超出L的部分调回到[1, L]这个范围，从而形成一个由[1, L]这个范围内的数字组成的环。这样就可以把每个键影射到在范围[1, L]内的一个值。通过取每个服务器的唯一标示（比方说，服务器的IP地址）并将其应用于哈希函数H，便可形成一个由多个服务器组成的一致性哈希环。通过仔细查看图13.1所示的由5台服务器(''A''-''E'')组成的哈希环，就可以对上面所说的计算过程有个直观感受。

> There, we picked ''L = 1000''. Let's say that ''H(A) mod L = 7'', ''H(B) mod L = 234'', ''H(C) mod L = 447'', ''H(D) mod L = 660'', and ''H(E) mod L = 875''. We can now tell which server a key should live on. To do this, we map all keys to a server by seeing if it falls in the range between that server and the next one in the ring. For example, ''A'' is responsible for keys whose hash value falls in the range [7,233], and ''E'' is responsible for keys in the range [875, 6] (this range wraps around on itself at 1000). So if ''H('employee30') mod L = 899'', it will be stored by server ''E'', and if ''H('employee31') mod L = 234'', it will be stored on server ''B''.

其中，令''L = 1000''，假设''H(A) mod L = 7''，''H(B) mod L = 234''，''H(C) mod L = 447''，''H(D) mod L = 660''，''H(E) mod L = 875''。现在就可以判断出一个键到底应该存储到哪个服务器之上了。判断某个键是否影射到了某个服务器之上的方法是，通过查看该键的哈希值是否落到了该服务器同下一个服务器在环中的位置所对应的数值范围之内。例如，''A''负责处理哈希值在[7, 233]区间内的键，''E''负责的区间是[875, 6]（该区间超过了1000并又重新从0开始算起）。因此，如果''H('employee30') mod L = 899'', 那么employee30就应该存储于服务器''E''。再如果''H('employee31') mod L = 234''，那么employee31就应该存储于服务器''B''。

#### Replicating Data ####

> Replication for multi-server durability is achieved by passing the keys and values in one server's assigned range to the servers following it in the ring. For example, with a replication factor of 3, keys mapped to the range [7,233] will be stored on servers ''A'', ''B'', and ''C''. If ''A'' were to fail, its neighbors ''B'' and ''C'' would take over its workload. In some designs, ''E'' would replicate and take over ''A'''s workload temporarily, since its range would expand to include ''A'''s.

通过把落入一个服务器范围内的键值对沿着环上的顺序传递给该服务器后面的若干服务器，就可以将同一份数据保存到多台服务器中了，这样就制作出了数据的多份副本。例如，令副本个数为3，影射到[7, 233]这个范围的键将存储于''A''、''B''和''C''服务器。如果''A''出了故障，与之相邻的''B''和''C''可以为''A''代劳。有些系统设计为将''E''暂时接管''A''的工作，因为''E''负责的区间扩大一下就可以包括了''A''的区间。

#### Achieving Better Distribution ####

> While hashing is statistically effective at uniformly distributing a keyspace, it usually requires many servers before it distributes evenly. Unfortunately, we often start with a small number of servers that are not perfectly spaced apart from one-another by the hash function. In our example, ''A'''s key range is of length 227, whereas ''E'''s range is 132. This leads to uneven load on different servers. It also makes it difficult for servers to take over for one-another when they fail, since a neighbor suddenly has to take control of the entire range of the failed server.

尽管哈希函数在统计学意义上的确可以很好的产生分布均匀的键空间（keyspace），但是真正均匀分布的前提条件往往是需要大量的服务器。很不幸，在系统开始工作时，往往只具有少量的服务器，哈希函数做不到将各个服务器从空间上做个完美的分割。看看上面那个例子中，''A''负责的范围的长度为227，而''E''的才132。这会导致不同服务器需要承担的负载很不均匀，而且也让服务器难以接管别的出故障的服务器的工作，因为与之相邻的服务器突然不得不接管出故障的服务器所负责的整个一个区间。

> To solve the problem of uneven large key ranges, many DHTs including Riak create several 'virtual' nodes per physical machine. For example, with 4 virtual nodes, server ''A'' will act as server ''A_1'', ''A_2'', ''A_3'', and ''A_4''. Each virtual node hashes to a different value, giving it more opportunity to manage keys distributed to different parts of the keyspace. Voldemort takes a similar approach, in which the number of partitions is manually configured and usually larger than the number of servers, resulting in each server receiving a number of smaller partitions.

为了解决这种由于不均匀的比较大的区间导致的问题，包括Riak在内的许多DHT系统都为每个物理机器创建了若干“虚拟”节点。举例来说，创建4个虚拟节点后，服务器''A''就作为服务器''A_1''、''A_2''、''A_3''和''A_4''四个节点运行。每个虚机节点负责的哈希值各不相同，从而就有更多的机会去管理分布在键空间中不同部分的键。Voldemort采用了一种类似的方式，它可以手动地配置分区个数，而这个个数通常要比服务器个数大，这样处理之后，每个服务器接收到的是多个比较小的分区。

> Cassandra does not assign multiple small partitions to each server, resulting in sometimes uneven key range distributions. For load-balancing, Cassandra has an asynchronous process which adjusts the location of servers on the ring depending on their historic load.

Cassandra没有为每个服务器分配多个小分区，结果有时就会造成键范围分布的很不均匀。为了获得负载均衡，Cassandra采用了一个异步过程，用来按照每个服务器的负载的历史信息自动调节服务器的位置。

### 13.4.4. 范围分区 ###

> In the range partitioning approach to sharding, some machines in your system keep metadata about which servers contain which key ranges. This metadata is consulted to route key and range lookups to the appropriate servers. Like the consistent hash ring approach, this range partitioning splits the keyspace into ranges, with each key range being managed by one machine and potentially replicated to others. Unlike the consistent hashing approach, two keys that are next to each other in the key's sort order are likely to appear in the same partition. This reduces the size of the routing metadata, as large ranges are compressed to [start, end] markers.

在分片技术中的范围分区方法里，系统中的有些机器会保存一份元数据，该元数据描述了哪些服务器负责保存哪些范围内的键。每次针对某个键进行查询时都需要先参照元数据才能找到该键所对应的服务器。同一致性哈希环方法类似，范围分区也是将键空间分为多个区间，然后每个区间都由一台机器进行管理，而且还可以让这台机器将其自身中的数据传递给别的服务器制作多份副本。但与一致性哈希方法不同的是，把键排序后互相相邻的两个键很可能是保存于同一个分区的。这可以减小用于查找分区的元数据的数据量，因为一个大大的区间仅仅可以缩小到只保存[开始, 结束]作为分区界标即可.

> In adding active record-keeping of the *range-to-server* mapping, the range partitioning approach allows for more fine-grained control of load-shedding from heavily loaded servers. If a specific key range sees higher traffic than other ranges, a load manager can reduce the size of the range on that server, or reduce the number of shards that this server serves. The added freedom to actively manage load comes at the expense of extra architectural components which monitor and route shards.

通过为范围到服务器（range-to-server）影射关系添加一个活动记录，范围分区方法可以实现对从具有大负载量的服务器中分出一部分负载给别的服务器进行更细的粒度上的控制。如果某特定键范围比别的范围具有更多的流量，负载管理器可以减小该范围对应的服务器所负责的范围，也可以减少该服务器负责的分片总数。由主动管理负载所带来的自由度所需付出的代价是，需要在架构中添加额外的负载监控和查找分区的部件。

#### The BigTable Way ####

> Google's BigTable paper describes a range-partitioning hierarchical technique for sharding data into tablets. A tablet stores a range of row keys and values within a column family. It maintains all of the necessary logs and data structures to answer queries about the keys in its assigned range. Tablet servers serve multiple tablets depending on the load each tablet is experiencing.

Google的BigTable论文描述了一种利用范围分区的层次型技术将数据分片保存为许多tablet的方法。Tablet按照行键的范围存储了一定列族中的值。它还保存了分配给它的范围内的键的所有必需的日志以及数据结构，用以回答针对保存于其中的键的各种查询。Tablet服务器依据每个tablet所经历的负载的不同，服务于多个tablet。

> Each tablet is kept at a size of 100-200 MB. As tablets change in size, two small tablets with adjoining key ranges might be combined, or a large tablet might be split in two. A master server analyzes tablet size, load, and tablet server availability. The master adjusts which tablet server serves which tablets at any time.

每个tablet所保存的数据的大小为100-200MB。随着tablet的大小不断变化，键范围相邻的两个比较小的tablet也可能会合并为一个tablet。主服务器会对各tablet的大小、负载以及tablet 服务器的可用性方面的情况进行分析。主服务器随时会对tablet服务器要服务于哪些tablet做出适当调整。

![](http://www.aosabook.org/images/nosql/bigtable.png)

Figure 13.2: BigTable-based Range Partitioning

> The master server maintains the tablet assignment in a metadata table. Because this metadata can get large, the metadata table is also sharded into tablets that map key ranges to tablets and tablet servers responsible for those ranges. This results in a three-layer hierarchy traversal for clients to find a key on its hosting tablet server, as depicted in Figure 13.2.

主服务器将系统中tablet的分配情况方面的维护性信息保存于一个元数据表中。因为这些元数据也可能会越来越多，所以元数据表也进行了分区，根据键的不同范围保存为多个tablet，同时也由tablet服务器负责维护这些范围值。如此处理之后，就得到一个三级的层次型结构，client在查找一个键到底是保存于哪个tablet服务器时，就需要对该层次结构进行遍历，这个过程可见于图13.2。

> Let's look at an example. A client searching for key ''900'' will query server ''A'', which stores the tablet for metadata level 0. This tablet identifies the metadata level 1 tablet on server 6 containing key ranges 500-1500. The client sends a request to server ''B'' with this key, which responds that the tablet containing keys 850-950 is found on a tablet on server C. Finally, the client sends the key request to server ''C'', and gets the row data back for its query. Metadata tablets at level 0 and 1 may be cached by the client, which avoids putting undue load on their tablet servers from repeat queries. The BigTable paper explains that this 3-level hierarchy can accommodate 2<sup>61</sup> bytes worth of storage using 128MB tablets.

下面来看个例子。一个clent要查找900这个键，那么它就要在服务器''A''上进行查询，''A''保存着第0层的元数据。在该tablet中保存的键的范围为500-1500，通过它找到位于第1级的元数据的服务器B负责的范围包含了900这个键。Client向服务器''B''发送一个请求，''B''做出响应，找到负责范围为850-950的服务器''C''。最后，client向''C''发出请求，并获得其想要查询的键900所对应的那行数据。client可以将第0级和第1级的元数据缓存起来，这样就可以避免重复查询对元数据tablet服务器带来的不必要的负担。BigTable论文解释道，这种使用128MB的tablet的3级层次结构能够保存2<sup>61</sup>个字节的数据。

#### Handling Failures ####

> The master is a single point of failure in the BigTable design, but can go down temporarily without affecting requests to tablet servers. If a tablet server fails while serving tablet requests, it is up to the master to recognize this and re-assign its tablets while requests temporarily fail.

在BigTable的设计中，主服务器是一个单点故障，但暂时宕机也不会影响对tablet服务器的请求。如果tablet服务器在为其所保存的tablet服务中出了故障，此时就由主服务器负责识别出这种情况并对处于故障期间的该tablet服务器负责的tablet进行重新安排。

> In order to recognize and handle machine failures, the BigTable paper describes the use of Chubby, a distributed locking system for managing server membership and liveness. ZooKeeper((http://hadoop.apache.org/zookeeper/)) is the open source implementation of Chubby, and several Hadoop-based projects utilize it to manage secondary master servers and tablet server reassignment.

为了识别出机器故障并做出相应的处理，BigTable论文里使用了一个分布式锁定系统，叫做Chubby，用以管理服务器的加入退出以及运行状况。ZooKeeper是Chubby的一个开源实现，若干基于Hadoop的项目使用ZooKeeper管理二级主服务器以及tablet服务器分配。

#### Range Partitioning-based NoSQL Projects ####

> HBase employs BigTable's hierarchical approach to range-partitioning. Underlying tablet data is stored in Hadoop's distributed filesystem (HDFS). HDFS handles data replication and consistency among replicas, leaving tablet servers to handle requests, update storage structures, and initiate tablet splits and compactions.

HBase利用了BigTable的层次结构的方式进行范围分区。底层的tablet数据保存于Hadoop的分布式文件系统（HDFS）。HDFS处理数据的多份备份并负责在备份间保持一致性，并由tablet服务器处理请求，更新数据存储结构，启动tablet的分裂以及数据压缩。

> MongoDB handles range partitioning in a manner similar to that of BigTable. Several configuration nodes store and manage the routing tables that specify which storage node is responsible for which key ranges. These configuration nodes stay in sync through a protocol called ''two-phase commit'', and serve as a hybrid of BigTable's master for specifying ranges and Chubby for highly available configuration management. Separate routing processes, which are stateless, keep track of the most recent routing configuration and route key requests to the appropriate storage nodes. Storage nodes are arranged in replica sets to handle replication.

MongoDB处理范围分区的方式同BigTable相似。由若干配置节点保存和管理用于指定每个节点负责哪些键范围的路由表。这些配置节点通过一个叫做''两阶段提交法''的协议保持彼此同步，并由这些节点共同担负BigTable中指定范围的主服务器和具有高可用性配置管理的Chubby这二者的角色。路由过程是无状态的，不同的路由过程会记下最近用过的路由配置并将对键的请求发送给恰当的存储节点。存储节点中保存了数据的多份副本，并以副本集的形式安排。

> Cassandra provides an order-preserving partitioner if you wish to allow fast range scans over your data. Cassandra nodes are still arranged in a ring using consistent hashing, but rather than hashing a key-value pair onto the ring to determine the server to which it should be assigned, the key is simply mapped onto the server which controls the range in which the key naturally fits. For example, keys 20 and 21 would both be mapped to server A in our consistent hash ring in Figure 13.1, rather than being hashed and randomly distributed in the ring.

如果你打算要在数据之上进行快速的范围扫描，Cassandra可提供一种可以保留所有的键的顺序的分区方案。Cassandra的节点仍然是通过使用一致性哈希算法安排为一个环，但并不是根据键值对的哈希值决定该它们应该分配给环中的哪个服务器，只是看键自然落在哪个范围就由控制那个范围的服务器负责存取该键。比如，在图13.1中，键20和21将都会保存到服务器A中，而不是要经过哈希处理后被随机地分配到给环中的某个服务器。

> Twitter's Gizzard framework for managing partitioned and replicated data across many back ends uses range partitioning to shard data. Routing servers form hierarchies of any depth, assigning ranges of keys to servers below them in the hierarchy. These servers either store data for keys in their assigned range, or route to yet another layer of routing servers. Replication in this model is achieved by sending updates to multiple machines for a key range. Gizzard routing nodes manage failed writes in different manner than other NoSQL systems. Gizzard requires that system designers make all updates idempotent (they can be run twice). When a storage node fails, routing nodes cache and repeatedly send updates to the node until the update is confirmed.

Twitter的Gizzard框架负责为许多后端系统管理经过分区处理并具有多份备份的数据，它也使用了范围分区对数据进行分片。它的路由服务器可以形成任意深度的层次结构，其中每一层的服务器会为在层次结构中位于其下的服务器分配各自负责的键范围。这些服务器或者存储着键所对应的值，或者根据需要将请求转发到路由服务器中的另外一层中进行处理。在此模型下的多份数据备份的存储是通过向负责同一键范围的多个服务器发出更新请求来实现的。Gizzard的路由节点以不同于其它NoSQL系统的方式来管理失败的写操作。它要求系统的设计者将所有的更新必需是幂等的（idempotent，也即允许同一个更新发出多次请求）。当有某个存储节点出了故障之后，路由节点会对更新进行缓存并不断重复发送该更新请求直到确认该更新完成为止。

### 13.4.5. 使用哪种分区策略 ###

> Given the hash- and range-based approaches to sharding, which is preferable? It depends. Range partitioning is the obvious choice to use when you will frequently be performing range scans over the keys of your data. As you read values in order by key, you will not jump to random nodes in the network, which would incur heavy network overhead. But if you do not require range scans, which sharding scheme should you use?

基于哈希和基于范围的分片策略到底哪个更好？这不能一概而论。在你要经常按照键的范围对数据进行扫描时，显然应该选择范围分区。因为当你按键的顺序对数据进行读取时，无需在网络中的节点间随机乱跳而招致大量的网络开销。但是，如果你也并不需要范围扫描，那么你到底应该采用哪种分片策略呢？

> Hash partitioning gives reasonable distribution of data across nodes, and random skew can be reduced with virtual nodes. Routing is simple in the hash partitioning scheme: for the most part, the hash function can be executed by clients to find the appropriate server. With more complicated rebalancing schemes, finding the right node for a key becomes more difficult.

哈希分区可使数据以合理的方式分布到节点中，并且还可以用虚机节点的办法降低随机性分布不均匀的情况。哈希分区方案中的路由方式大部分情况下也非常简单，client执行一下哈希函数就能够找到所需的服务器。随着采用越来越复杂的二次平衡的方案, 要找找到一个键所对应的节点也就会相应地越来越困难。

> Range partitioning requires the upfront cost of maintaining routing and configuration nodes, which can see heavy load and become central points of failure in the absence of relatively complex fault tolerance schemes. Done well, however, range-partitioned data can be load-balanced in small chunks which can be reassigned in high-load situations. If a server goes down, its assigned ranges can be distributed to many servers, rather than loading the server's immediate neighbors during downtime.

范围分区需要预先花钱来维护多个路由和配置节点，这些节点的网络负载会很重，如果没有相对复杂的容错方案，它们就会成为中心故障点。然而，做得好的话，范围分区数据在小规模时能够实现复负载均衡，而且还可以在负载比较大的情况下进行重新安排。如果其中的某个服务器宕机了，它所负责的范围可以转交多个服务器，而不会象在哈希分区中那样将所有负载转嫁给其环中相邻的服务器。

## 13.5. 一致性 ##

> Having spoken about the virtues of replicating data to multiple machines for durability and spreading load, it's time to let you in on a secret: keeping replicas of your data on multiple machines consistent with one-another is hard. In practice, replicas will crash and get out of sync, replicas will crash and never come back, networks will partition two sets of replicas, and messages between machines will get delayed or lost. There are two major approaches to data consistency in the NoSQL ecosystem. The first is strong consistency, where all replicas remain in sync. The second is eventual consistency, where replicas are allowed to get out of sync, but eventually catch up with one-another. Let's first get into why the second option is an appropriate consideration by understanding a fundamental property of distributed computing. After that, we'll jump into the details of each approach.

上面我们讲到了将数据备份到多个机器上来保证数据持久性和实现负载分摊，接下来我们要来讨论这样做引发的一个问题：保证多个节点上的数据一致性是比较困难的。在实际应用中，同步节点可能会故障，无法保持同步，甚至会无法恢复，网络可能会有延迟或者丢包，网络原因导致集群中的机器被分隔成两个不能互通的子域等等。在NoSQL生态系统中，有两种一致性：第一种是强一致性，既集群中的所有机器状态同步保持一致。第二种是最终一致性，可以允许短暂的数据不一致，但最终会达到一致。我们先看一下分布式计算的基本特性，考虑为什么最终一致性是更合理的选择，然后再来讨论两种一致性的具体细节。

### 13.5.1. 关于CAP ###

> Why are we considering anything short of strong consistency guarantees over our data? It all comes down to a property of distributed systems architected for modern networking equipment. The idea was first proposed by Eric Brewer as the *CAP Theorem*, and later proved by Gilbert and Lynch [  http://www.aosabook.org/en/bib1.html#bib:captheorem |GL02  ]. The theorem first presents three properties of distributed systems which make up the acronym CAP:

为什么我们会考虑使用非强一致性方案来保障我们的数据呢？ 这都是因为考虑现代网络设备的情况所设计出的分布式系统架构所具有的几个基本特性。其基本思想最先是由Eric Brewer提出的CAP定理确定的，随后又通过了Gilbert和Lynch得以证明成立[GL02]. 该定理首先提出了分布式的三个特性，有这三个特性的首字母组合为缩略词CAP：

  * *Consistency*: do all replicas of a piece of data always logically agree on the same version of that data by the time you read it? (This concept of consistency is different than the C in ACID.)
  * *Availability*: Do replicas respond to read and write requests regardless of how many replicas are inaccessible?
  * *Partition tolerance*: Can the system continue to operate even if some replicas temporarily lose the ability to communicate with each other over the network?

  * 一致性（C）: 一个数据的所有副本在你对它们进行读取操作时，是否总是从逻辑上讲保存的都是同一版本的数据？ （此概念之下的一致性同ACID之中的C不同。）
  * 可用性（A）: 是否无论有多少副本节点处于不可访问状态的情况下，其它副本都要对对象请求做出相应？
  * 分区耐受性（P）: 即使有些副本节点暂时失去同其它节点进行网络通信的能力，系统是否还能继续正常运行？

> The theorem then goes on to say that a storage system which operates on multiple computers can only achieve two of these properties at the expense of a third. Also, we are forced to implement partition-tolerant systems. On current networking hardware using current messaging protocols, packets can be lost, switches can fail, and there is no way to know whether the network is down or the server you are trying to send a message to is unavailable. All NoSQL systems should be partition-tolerant. The remaining choice is between consistency and availability. No NoSQL system can provide both at the same time.

该定理接着指出，运行于多台机器之上的存储系统中，这三个特性不可兼得，只能得到其中两个特性而牺牲掉剩下的那个特性。而且，我们不得不实现具有分区耐受性的系统。在当下的网络硬件之上使用当下的消息传递协议，数据包可能会丢失，交换机可能会出故障，节点无法得知到底是网络出了问题还是你想发送给它消息的那个服务器本身出了问题。所有的NoSQL系统应该都是具有分区耐受性的。剩下的就是要在一致性和可用性两者之中做出选择了。没有任何一个NoSQL系统能够同时提供这两种特性的。

> Opting for consistency means that your replicated data will not be out of sync across replicas. An easy way to achieve consistency is to require that all replicas acknowledge updates. If a replica goes down and you can not confirm data updates on it, then you degrade availability on its keys. This means that until all replicas recover and respond, the user can not receive successful acknowledgment of their update operation. Thus, opting for consistency is opting for a lack of round-the-clock availability for each data item.

如果选择一致性的话，就意味着副本间的数据将不会出现不同步的状态。实现一致性的一种简便的方法就是要求所有的副本节点对更新操作都必须进行应答确认。如果其中的一个副本节点出了问题，你就无法确认那个副本中的数据是否真的得到了更新，因而针对正在进行更新的键来说，系统的可用性就降低了。也就是说，只有到了所有副本节点恢复之后并做出响应之后，用户才能等到更新操作成功的确认消息。因此说，选择了一致性就是选择了每个数据项都有可能会缺失全天候的可用性。

> Opting for availability means that when a user issues an operation, replicas should act on the data they have, regardless of the state of other replicas. This may lead to diverging consistency of data across replicas, since they weren't required to acknowledge all updates, and some replicas may have not noted all updates.

如果选择了可用性，就意味着当用户发起一个操作时，副本节点就应该无需考虑其它节点的状态，只需根据它自有的数据完成该操作。这可能会导致副本间数据一致性收到不同程度的损失，因为副本节点无需对所有的更新进行应答确认，所以有些副本节点就可能会没有注意到所有的更新操作。

> The implications of the CAP theorem lead to the strong consistency and eventual consistency approaches to building NoSQL data stores. Other approaches exist, such as the relaxed consistency and relaxed availability approach presented in Yahoo!'s PNUTS [  http://www.aosabook.org/en/bib1.html#bib:pnuts |CRS+08  ] system. None of the open source NoSQL systems we discuss has adopted this technique yet, so we will not discuss it further.

CAP定理所蕴含的理论导致了在构建NoSQL数据存储系统时所采用的两种方法，强一致性方法和最终一致性方法。现在也有一些其它的方法，比如Yahoo!的PNUTS [CRS+08]系统采用的是一种松弛型一致性结合松弛型可用性（relaxed consistency and relaxed availability）的方法。由于我们所要讨论的开源NoSQL系统中还没有采用这种技术，所以我们不再对此技术进行展开讨论了。

### 13.5.2. 强一致性 ###

> Systems which promote strong consistency ensure that the replicas of a data item will always be able to come to consensus on the value of a key. Some replicas may be out of sync with one-another, but when the user asks for the value of ''employee30:salary'', the machines have a way to consistently agree on the value the user sees. How this works is best explained with numbers.

促使达成强一致性的系统要确保，数据项的所要副本节点要能够对键所对应的数值的取值达成一致意见。也许有些副本节点会同别的节点不同步，但是，当用户对''employee30:salary''的值进行查询时，各节点会有一种办法，能够对返回给用户看得的值达成一个一致意见。这个工作过程可以用数字来很好的说明。

> Say we replicate a key on N machines. Some machine, perhaps one of the ''N'', serves as a coordinator for each user request. The coordinator ensures that a certain number of the ''N'' machines has received and acknowledged each request. When a write or update occurs to a key, the coordinator does not confirm with the user that the write occurred until ''W'' replicas confirm that they have received the update. When a user wants to read the value for some key, the coordinator responds when at least R have responded with the same value. We say that the system exemplifies strong consistency if ''R+W>N''.

比方说，我们将某个键分别在N个节点上制作N个副本，将某个节点，可能就是这''N''个节点中的一个，设定为处理用户请求的协调器。该协调器要确保''N''个节点中一定数量的节点能收到或确认每一个请求。当发生对一个键的写入或更新操作时，协调器直到''W''个副本的确认它们的确收到了更新操作的请求，它才会向用户发出更新操作完成确认信息。当用户想读取某键所对应的值时，协调器在至少''R''个节点回应的是相同的值的情况下才会返回该值。我们说，在一个系统中只有当''R+W>N''时，它才是具有强一致性的系统。

> Putting some numbers to this idea, let's say that we're replicating each key across N=3 machines (call them ''A'', ''B'', and ''C''). Say that the key ''employee30:salary'' is initially set to the value $20,000, but we want to give ''employee30'' a raise to $30,000. Let's require that at least ''W=2'' of ''A'', ''B'', or ''C'' acknowledge each write request for a key. When ''A'' and ''B'' confirm the write request for ''(employee30:salary, $30,000)'', the coordinator lets the user know that ''employee30:salary'' is safely updated. Let's assume that machine C never received the write request for ''employee30:salary'', so it still has the value $20,000. When a coordinator gets a read request for key ''employee30:salary'', it will send that request to all 3 machines:

下面用一些具体数字来说明这个概念。假如我们用N=3个节点为每个键制作3个副本（分别把它们称为''A''、''B''和''C''）。''employee30:salary''这个键所对应的值一开始设为$20,000，但现在想把''employee30''的薪水涨到$30,000。我们要求在''A''、''B''和''C''中必须至少有''W=2''个对写请求进行确认。当''A''和''B''确认了''(employee30:salary, $30,000)''所对应的写请求后，协调器就告诉了用户，''employee30:salary''的值已经安全地进行完更新了。我们假设''C''一直都没有接受到对''employee30:salary''进行更新的请求，所以''C''中的值仍然是$20,000。当协调器收到对''employee30:salary''进行读取的请求时，它会把该请求发给所有的3个节点：

  * If we set ''R=1'', and machine ''C'' responds first with $20,000, our employee will not be very happy.
  * However, if we set ''R=2'', the coordinator will see the value from ''C'', wait for a second response from ''A'' or ''B'', which will conflict with ''C'''s outdated value, and finally receive a response from the third machine, which will confirm that $30,000 is the majority opinion.

  * 如果我们设定''R=1''，并且节点''C''首先用$20,000做出响应，我们的雇员就不会很开心。
  * 然而，如果我们设定''R=2''，协调器将看到来自''C''的值并等待来自''A''或''B''的响应，而这个响应必将同''C''中已经过时的值发生冲突，直到最后再从第3个节点接受到响应信息，最终少数服从多数，就可以确认$30,000是那个正确的值。

> So in order to achieve strong consistency in this case, we need to set ''R''=2} so that ''R+W''3}.

因此，在这种情况下，为了获得强一致性，我们需要设定''R''=2，结果就是''R+W''>3。

> What happens when ''W'' replicas do not respond to a write request, or ''R'' replicas do not respond to a read request with a consistent response? The coordinator can timeout eventually and send the user an error, or wait until the situation corrects itself. Either way, the system is considered unavailable for that request for at least some time.

要是不够''W''个副本节点对写请求进行响应，或者不够''R''个副本节点对读请求做出具有一致性的响应怎么办？协调器可以最终以超时结束此次请求的处理并返回给用户一个错误，也可以一直等到这种情况得以纠正为止。无论用上面哪种办法，此时可以认为系统至少会在一段时间内处于可不用状态，无法为该请求做出相应的处理。

> Your choice of ''R'' and ''W'' affect how many machines can act strangely before your system becomes unavailable for different actions on a key. If you force all of your replicas to acknowledge writes, for example, then ''W=N'', and write operations will hang or fail on any replica failure. A common choice is ''R + W = N + 1'', the minimum required for strong consistency while still allowing for temporary disagreement between replicas. Many strong consistency systems opt for ''W=N'' and ''R=1'', since they then do not have to design for nodes going out of sync.

在系统变为对某个键的各种操作处于不可用状态之前，可以处于非正常状态的节点数的最大值取决于''R''和''W''的取值。例如，当你强制所有的副本节点都必须对写操作做出应答时，也就是设定W=N，那么有任何一个副本节点出了问题，写操作都会或者始终处于等待状态或者返回错误信息。比较常见的选择是让''R + W = N + 1''，这是强一致性的最低要求，同时仍能允许某些副本节点临时处于非同步状态。有许多强一致性系统倾向于选择让''W=N''并让''R=1'', 因为这样就无需为可能会出现节点间不同步进行任何多余的设计了。

> HBase bases its replicated storage on HDFS, a distributed storage layer. HDFS provides strong consistency guarantees. In HDFS, a write cannot succeed until it has been replicated to all ''N'' (usually 2 or 3) replicas, so ''W = N''. A read will be satisfied by a single replica, so ''R = 1''. To avoid bogging down write-intensive workloads, data is transferred from the user to the replicas asynchronously in parallel. Once all replicas acknowledge that they have received copies of the data, the final step of swapping the new data in to the system is performed atomically and consistently across all replicas.

HBase是基于HDFS实现其副本存储的。HDFS是一个分布式存储层，保证实现的是强一致性存储。在HDFS中，写操作只有保存到所有的''N''（通常是2或者3）个副本节点中才算成功，所以它的''W=N''。读取操作只需一个副本节点即可完成，因此它的''R=1''。为了避免在频繁写入操作情况下出现系统停顿，其数据从用户到个副本节点的传输是以异步并行的方式进行的。一旦所有副本确认了它们都收到了相应的数据，剩下最后一步就是要用新数据替换掉系统中的旧数据，而这一步将以具有原子性和一致性的操作的形式在所有副本中完成。

### 13.5.3. 最终一致性 ###

> Dynamo-based systems, which include Voldemort, Cassandra, and Riak, allow the user to specify ''N'', ''R'', and ''W'' to their needs, even if ''R + W <= N''. This means that the user can achieve either strong or eventual consistency. When a user picks eventual consistency, and even when the programmer opts for strong consistency but ''W'' is less than ''N'', there are periods in which replicas might not see eye-to-eye. To provide eventual consistency among replicas, these systems employ various tools to catch stale replicas up to speed. Let's first cover how various systems determine that data has gotten out of sync, then discuss how they synchronize replicas, and finally bring in a few dynamo-inspired methods for speeding up the synchronization process.

包括Voldemort、Cassandra和Riak在内的基于Dynamo的系统允许用户根据自己的需要指定''N''、''R''和''W''的值，即使''R+W<=N''也是允许的。这意味着用户可以获得强一致性，也可以仅获得最终一致性。当用户选择了最终一致性，甚至当用户选择了强一致性但''W''小于''N''时，系统中会有在某些时段内，众副本节点间存在着不一致性。为了提供节点间最终的一致性，这些系统会利用各种手段让过时的副本节点赶紧进行更新。首先让我们讲解一下各种不同的系统是如何判定到底是哪些数据处于了不同步的状态，然后再来讨论它们是如何在副本节点间进行更新的，最后提出一些受Dynamo所启发而得的加速该同步过程的方法。

#### Versioning and Conflicts ####

> Because two replicas might see two different versions of a value for some key, data versioning and conflict detection is important. The dynamo-based systems use a type of versioning called *vector clocks*. A vector clock is a vector assigned to each key which contains a counter for each replica. For example, if servers ''A'', ''B'', and ''C'' are the three replicas of some key, the vector clock will have three entries, ''(N_A, N_B, N_C)'', initialized to ''(0,0,0)''.

因为不同的副本对于同一个键值可能会看到两种不同的版本，数据的版本控制和冲突的探查就显得尤为重要。动态的系统使用使用一种向量时钟，一个向量时钟是一个赋予每一个键的向量，这个向量包含一个对于所有副本的计数器，比如，如果服务器''A''，''B''，''C''是3个副本中的某些key，这个向量时钟就会有三个入口''(N_A, N_B, N_C)'', 初始化他们为''(0,0,0)''.

> Each time a replica modifies a key, it increments its counter in the vector. If B modifies a key that previously had version ''(39, 1, 5)'', it will change the vector clock to ''(39, 2, 5)''. When another replica, say ''C'', receives an update from B about the key's data, it will compare the vector clock from ''B'' to its own. As long as its own vector clock counters are all less than the ones delivered from ''B'', then it has a stale version and can overwrite its own copy with ''B'''s. If ''B'' and ''C'' have clocks in which some counters are greater than others in both clocks, say ''(39, 2, 5)'' and ''(39, 1, 6)'', then the servers recognize that they received different, potentially unreconcilable updates over time, and identify a conflict.

每次一个副本改变了一个key，这个向量的计数器就+1。如果''B''修改了一个之前版本为''(39, 1, 5)''的key，它就会把向量时钟改为''(39, 2, 5)''，当另一个副本''C''接收到''B''对于这个数据的更新，它会以''B''和自己的向量时钟做比较。只要它自己的向量时钟计数器比从''B''发送过来的小，那么它就有了一个陈旧的版本，可以使用B的数据来覆盖自己的副本。如果''B''和''C''有时钟的计数器大于其它的副本，如 say''(39, 2, 5)''和''(39, 1, 6)'', 那么服务器就会认出它们就收到的不同的，潜在的不可辨识的超时更新，并确认一个冲突。

#### Conflict Resolution ####

> Conflict resolution varies across the different systems. The Dynamo paper leaves conflict resolution to the application using the storage system. Two versions of a shopping cart can be merged into one without significant loss of data, but two versions of a collaboratively edited document might require human reviewer to resolve conflict. Voldemort follows this model, returning multiple copies of a key to the requesting client application upon conflict.

不同系统有不同的冲突解决办法。Dynamo论文将解决冲突的任务留给了使用存储系统的应用程序解决。同一个购物车所保存的数据有两个版本，可以直接合并起来而不会造成重大的数据损失。但是，协同编辑中的文档内容有两个版本要保存的话，可能就需要人为的干预才能很好的解决冲突。Voldemort遵循了此模型，在遇到冲突时，会将同一个键所对应的多份数据返回给发出请求的客户端应用程序。

> Cassandra, which stores a timestamp on each key, uses the most recently timestamped version of a key when two versions are in conflict. This removes the need for a round-trip to the client and simplifies the API. This design makes it difficult to handle situations where conflicted data can be intelligently merged, as in our shopping cart example, or when implementing distributed counters. Riak allows both of the approaches offered by Voldemort and Cassandra. CouchDB provides a hybrid: it identifies a conflict and allows users to query for conflicted keys for manual repair, but deterministically picks a version to return to users until conflicts are repaired.

Cassandra为每个键都保存有一个时间戳，它在碰到存在冲突的两个版本时，将使用键所对应的最新时间戳中保存的版本。这种方法使得客户端不必来回再传一次数据，从而简化了其API。这种设计让原本可以巧妙的将数据合并的情况下也难以实现，比如，前面讲过的购物车的例子或者分布式计数器就很难实现了，Riak中允许使用Voldemort和Cassandra这两者提高的那两种方法。CouchDB提高了一种二者兼顾的方式：它在识别到冲突后允许为了手动修复冲突而对具有冲突的键进行查询，但会在冲突得以修复后，决定性的选择其中一个版本作为结果返回给用户。

#### Read Repair ####

> If R replicas return non-conflicting data to a coordinator, the coordinator can safely return the non-conflicting data to the application. The coordinator may still notice that some of the replicas are out of sync. The Dynamo paper suggests, and Cassandra, Riak, and Voldemort implement, a technique called *read repair* for handling such situations. When a coordinator identifies a conflict on read, even if a consistent value has been returned to the user, the coordinator starts conflict-resolution protocols between conflicted replicas. This proactively fixes conflicts with little additional work. Replicas have already sent their version of the data to the coordinator, and faster conflict resolution will result in less divergence in the system.

如果协调器从R个副本节点中拿到的数据并不存在不一致的情况，它就可以很安全地将这些具有一致性的数据返回给应用程序。协调器可能仍会发现有些副本出现了不同步的情况。Dynamo论文中建议使用一种叫做读修复的技术来处理情况，这在Cassandra、Riak以及Voldemort中都得以实现。当协调器在读取数据时发现了数据不一致的情况，即使已将一个一致性的数据返回给了用户，协调器会在产生不一致的副本间启动一个冲突解决规程，这样就可以积极主动地以一小部分的额外工作的代价对冲突进行及时修复。副本已经将它们各自所包含的数据发给了协调器，并且冲突解决的越快，系统内的不一致性就会因此而越小。

#### Hinted Handoff ####

> Cassandra, Riak, and Voldemort all employ a technique called *hinted handoff* to improve write performance for situations where a node temporarily becomes unavailable. If one of the replicas for a key does not respond to a write request, another node is selected to temporarily take over its write workload. Writes for the unavailable node are kept separately, and when the backup node notices the previously unavailable node become available, it forwards all of the writes to the newly available replica. The Dynamo paper utilizes a 'sloppy quorum' approach and allows the writes accomplished through hinted handoff to count toward the W required write acknowledgments. Cassandra and Voldemort will not count a hinted handoff against W, and will fail a write which does not have W confirmations from the originally assigned replicas. Hinted handoff is still useful in these systems, as it speeds up recovery when an unavailable node returns.

Cassandra、Riak以及Voldemort这三者都利用了一种称为提示性切换（hinted handoff）的技术，这种技术可以用来改善在某个节点暂时处于不可达状态的情况下写入操作的性能。如果某个键所属的副本无法对写入请求做出回应的话，系统就会暂时选取另外一个节点作为备用节点，并让该备用节点接管该写入操作，这样的写操作都会单独保存起来，一旦备用节点发现原先不可达的那个节点变为可达状态，它会把所有的哪些单独保存起来的写操作转发给刚刚可达的那个副本。Dynamo论文中谈及利用了一种叫做“粗旷仲裁集（sloppy quorum）”的方法，从而允许将通过提示性切换方式完成的写入操作也记入W所需的写入操作应答计数中。Cassandra和Voldemort并不将提示性切换节点记入W，而是将没有从原先所分配的那个副本拿到W确认信息算作一次失败的写入操作。尽管如此，提示性切换技术在这些系统中仍然很有用，因为它加快了不可达节点回到可达状态后的恢复过程。

#### Anti-Entropy ####

> When a replica is down for an extended period of time, or the machine storing hinted handoffs for an unavailable replica goes down as well, replicas must synchronize from one-another. In this case, Cassandra and Riak implement a Dynamo-inspired process called *anti-entropy*. In anti-entropy, replicas exchange *Merkle Trees* to identify parts of their replicated key ranges which are out of sync. A Merkle tree is a hierarchical hash verification: if the hash over the entire keyspace is not the same between two replicas, they will exchange hashes of smaller and smaller portions of the replicated keyspace until the out-of-sync keys are identified. This approach reduces unnecessary data transfer between replicas which contain mostly similar data.

如果某个副本节点在相当长一段时间内都是处于宕机状态，或者为不可达副本节点保存提示性切换（hinted handoff）信息的机器本身也宕机了，那么副本节点间就必须两两进行同步处理。在这种情况下，Cassandra和Riak实现的都是受Dynamo所启发而得的一个处理过程：反熵法(Anti-Entropy)。在反熵法中，副本节点间通过互相交换默克尔树（Merkle Trees）信息来找出它们所保存的副本键值中存在不同步情况的范围。默克尔树是一种层次型的hash验证校验值：如果在两个副本间，它们的整个键值空间（keyspace）之上的hash值不同的话，它们两个就要进一步对越来越小范围内的副本键值空间之上的hash值进行比对，直到找出所有的处于不同步状态的键。在包含的数据中绝大部分都是相似的副本间，这种方法可以减少没必要的数据传输。

#### Gossip ####

> Finally, as distributed systems grow, it is hard to keep track of how each node in the system is doing. The three Dynamo-based systems employ an age-old high school technique known as *gossip* to keep track of other nodes. Periodically (every second or so), a node will pick a random node it once communicated with to exchange knowledge of the health of the other nodes in the system. In providing this exchange, nodes learn which other nodes are down, and know where to route clients in search of a key.

最后，随着分布式系统规模的增大，想搞清楚系统中每个节点的工作状态就会变得非常困难。前文提及的那三个基于Dynamo的系统利用了一个在中学里流传已久的方法来完成这些工作：gossip。每个节点将周期性的（大约每秒钟左右）随机选择一个曾经同它进行过通信的节点并与之交换系统中其它节点工作状态方面的信息。通过这种信息交换，所有的节点都能够了解到系统中有哪些其它的节点宕机了，从而可以知晓在客户端对某个键值进行搜索时应该去到哪个节点哪里去找到它。

## 13.6. 写在最后的话 ##

> The NoSQL ecosystem is still in its infancy, and many of the systems we've discussed will change architectures, designs, and interfaces. The important takeaways in this chapter are not what each NoSQL system currently does, but rather the design decisions that led to a combination of features that make up these systems. NoSQL leaves a lot of design work in the hands of the application designer. Understanding the architectural components of these systems will not only help you build the next great NoSQL amalgamation, but also allow you to use current versions responsibly.

NoSQL生态系统还处在发展的初期阶段，我们上面讨论过的很多系统可能还会更改它们的架构，设计，以及接口。这章节的重要点不在于现在的NoSQL系统是如何实现的，而在于其中的设计决策，这些设计使得NoSQL具有了这样或那样的特性。NoSQL将很多工作留给了应用的开发者。理解了上面讨论的系统组件不仅可以帮助你实现下一个NoSQL系统，也可以让你对现有的系统运用得更好。

## 13.7. 致谢 ##

> I am grateful to Jackie Carter, Mihir Kedia, and the anonymous reviewers for their comments and suggestions to improve the chapter. This chapter would also not be possible without the years of dedicated work of the NoSQL community. Keep building!

感谢Jackie Carter，Mihir Kedia，以及对本章节提出宝贵意见的人们，当然，如果没有NoSQL社区这些年的专注工作，就不会有本章节的诞生。干巴爹。

## 译者注 ##

原文作者：Adam Marcus

## 脚注 ##







