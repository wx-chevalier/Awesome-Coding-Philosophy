# Chap30. THE DATABASE IS A DETAIL 数据库只是实现细节

![](https://ngte-superbed.oss-cn-beijing.aliyuncs.com/book/mono/un/CH-UN30.jpg)

From an architectural point of view, the database is a non-entity—it is a detail that does not rise to the level of an architectural element. Its relationship to the architecture of a software system is rather like the relationship of a doorknob to the architecture of your home.

> 从系统架构的角度来看，数据库并不重要——它只是一个实现细节，在系统架构中并不占据重要角色。如果就数据库与整个系统架构的关系打个比方，它们之间就好比是门把手和整个房屋架构的关系。

I realize that these are fighting words. Believe me, I’ve had the fight. So let me be clear: I am not talking about the data model. The structure you give to the data within your application is highly significant to the architecture of your system. But the database is not the data model. The database is piece of software. The database is a utility that provides access to the data. From the architecture’s point of view, that utility is irrelevant because it’s a low-level detail—a mechanism. And a good architect does not allow low-level mechanisms to pollute the system architecture.

> 这个比喻肯定会招来非议。相信我，这种架我吵过很多次了。所以我在这里要把话说得清楚一点：这里讨论的不是数据模型。为应用程序中的数据设计结构，对于系统架构来说当然是很重要的，但是数据库并不是数据模型。数据库只是一款软件，是用来存取数据的工具。从系统架构的角度来看，工具通常是无关紧要的——因为这只是一个底层的实现细节，一种达成目标的手段。一个优秀的架构师是不会让实现细节污染整个系统架构的。

## RELATIONAL DATABASES 关系型数据库

Edgar Codd defined the principles of relational databases in 1970. By the mid-1980s, the relational model had grown to become the dominant form of data storage. There was a good reason for this popularity: The relational model is elegant, disciplined, and robust. It is an excellent data storage and access technology.

> 关系型数据库的基本原理是 Edgar Codd 在 1970 年定义的。到了 20 世纪 80 年代中期，这种关系模型已经成为数据存储设计的主流。由于关系模型优雅、自律、非常稳健，因此得到了非常广泛的应用。总之，关系型数据库是一种非常优秀的数据存储与访问技术。

But no matter how brilliant, useful, and mathematically sound a technology it is, it is still just a technology. And that means it’s a detail.

> 但不管关系型数据库的设计有多么有智慧，多么精巧，多么符合数学原理，它仍然也只是一种技术。换句话说，它终究只是一种实现细节。

While relational tables may be convenient for certain forms of data access, there is nothing architecturally significant about arranging data into rows within tables. The use cases of your application should neither know nor care about such matters. Indeed, knowledge of the tabular structure of the data should be restricted to the lowest-level utility functions in the outer circles of the architecture.

> 虽然关系型数据的表模型设计对某一类数据访问需要来说可能很方便，但是把数据按行组成表结构本身并没有什么系统架构意义上的重要性。应用程序的用例不应该知道，也不应该关心这么低层次的实现细节，需要了解数据表结构的代码应该被局限在系统架构的最外圈、最低层的工具函数中。

Many data access frameworks allow database rows and tables to be passed around the system as objects. Allowing this is an architectural error. It couples the use cases, business rules, and in some cases even the UI to the relational structure of the data.

> 很多数据访问框架允许将数据行和数据表以对象的形式在系统内部传递。这么做在系统架构上来说是完全错误的，这会导致程序的用例、业务逻辑、甚至 UI 与数据的关系模型相互绑定在一起。

## WHY ARE DATABASE SYSTEMS SO PREVALENT? 为什么数据库系统如此流行

Why are software systems and software enterprises dominated by database systems? What accounts for the preeminence of Oracle, MySQL, and SQL Server? In a word: disks.

> 为什么数据库系统在软件系统和企业软件领域如此流行？ Oracle、MySQL 和 SQL Server 这些产品广泛流行的原因是什么？答案是硬盘。

The rotating magnetic disk was the mainstay of data storage for five decades. Several generations of programmers have known no other form of data storage. Disk technology has grown from huge stacks of massive platters 48 inches in diameter that weighed thousands of pounds and held 20 megabytes, to single thin circles, 3 inches in diameter, that weigh just a few grams and hold a terabyte or more. It’s been a wild ride. And throughout that ride programmers have been plagued by one fatal trait of disk technology: Disks are slow.

> 带有高速旋转的盘片，以磁感应方式读取数据的硬盘在过去五十年成为数据存储的主流手段，以至于最近几代软件工程师对其他类型的数据存储几乎一无所知。而且硬盘技术一直在发展，早先一大摞重达数吨的直径 48 英寸的盘片只能存储 20 兆字节，现在单个直径 3 英寸、重量仅仅几克的薄薄的一张硬盘就能存储上 TB 的数据。这发展得实在是太快了！但是在硬盘的整个发展过程中，程序员们始终被一个限制困扰着：磁盘的访问速度太慢了！

On a disk, data is stored within circular tracks. Those tracks are divided into sectors that hold a convenient number of bytes, often 4K. Each platter may have hundreds of tracks, and there can be a dozen or so platters. If you want to read a particular byte off the disk, you have to move the head to the proper track, wait for the disk to rotate to the proper sector, read all 4K of that sector into RAM, and then index into that RAM buffer to get the byte you want. And all that takes time—milliseconds of times.

> 在磁盘上，数据是按照环形轨道存储的。这些轨道又会进一步被划分成一系列扇区，这些扇区的大小通常是 4 KB。而每个盘片上都有几百条轨道，整个硬盘可能由十几个盘片组成。如果要从硬盘上读取某一个特定字节，需要将磁头挪到正确的轨道上，等待盘片旋转到正确的位置上，再将整个扇区读入内存中，从内存中查询对应的字节。这些过程当然需要时间，所以硬盘的访问速度一般在毫秒级。

Milliseconds might not seem like a lot, but a millisecond is a million times longer than the cycle time of most processors. If that data was not on a disk, it could be accessed in nanoseconds, instead of milliseconds.

> 毫秒级的速度看起来好像并不是很慢，但这已经比大多数处理器的速度慢一百万倍了。如果数据不在硬盘上，访问速度通常就通常是纳秒级，而不是毫秒级了。

To mitigate the time delay imposed by disks, you need indexes, caches, and optimized query schemes; and you need some kind of regular means of representing the data so that these indexes, caches, and query schemes know what they are working with. In short, you need a data access and management system. Over the years these systems have split into two distinct kinds: file systems and relational database management systems (RDBMS).

> 为了应对硬盘访问速度带来的限制，必须使用索引、缓存以及查询优化器等技术。同时，我们还需要一种数据的标准展现格式，以便让索引、缓存及查询优化器来使用。概括来说，我们需要的就是某种数据访问与管理系统。过去几十年内，业界逐渐发展出了两种截然不同的系统：文件系统与关系型数据库系统（RDBMS）。

File systems are document based. They provide a natural and convenient way to store whole documents. They work well when you need to save and retrieve a set of documents by name, but they don’t offer a lot of help when you’re searching the content of those documents. It’s easy to find a file named login.c, but it’s hard, and slow, to find every .c file that has a variable named x in it.

> 文件系统是基于文档格式的，它提供的是一种便于存储整个文档的方式。当需要按照名字存储数据和查找一系列文档时，文件系统很有用，但当我们需要检索文档内容时，它就没那么有用了。也就是说，我们在文件系统中查找一个名字为`login.c` 的文件很容易，但要检索出所有包括变量 x 的 `.c` 文件就很困难，速度也很慢。

Database systems are content based. They provide a natural and convenient way to find records based on their content. They are very good at associating multiple records based on some bit of content that they all share. Unfortunately, they are rather poor at storing and retrieving opaque documents.

> 而数据库系统则主要关注的是内容，它提供的是一种便于进行内容检索的存储方式。其最擅长的是根据某些共同属性而检索一系列记录。然而它对存储和访问内容不透明的文档的支持就没那么强了。

Both of these systems organize the data on disk so that it can be stored and retrieved in as efficient a way as possible, given their particular access needs. Each has their own scheme for indexing and arranging the data. In addition, each eventually brings the relevant data into RAM, where it can be quickly manipulated.

> 这两种系统都是为了优化磁盘存储而设计的，人们需要根据它们的特点来将数据组织成最便于访问的模式。每个系统都有一套索引和安排数据的方式。同时，每种系统最终都会将数据缓存在内存中，方便快速操作。

## WHAT IF THERE WERE NO DISK? 假设磁盘不存在会怎样

As prevalent as disks once were, they are now a dying breed. Soon they will have gone the way of tape drives, floppy drives, and CDs. They are being replaced by RAM.

> 虽然硬盘现在还是很常见，但其实已经在走下坡路了。很快它们就会和磁带、软盘、CD 一样成为历史，RAM 正在替代一切。

Ask yourself this question: When all the disks are gone, and all your data is stored in RAM, how will you organize that data? Will you organize it into tables and access it with SQL? Will you organize it into files and access it through a directory?

> 现在，我们要来考虑一下：如果所有的数据都存在内存中，应该如何组织它们呢？需要按表格存储并且用 SQL 查询吗？需要用文件形式存储，然后按目录查找吗？

Of course not. You’ll organize it into linked lists, trees, hash tables, stacks, queues, or any of the other myriad data structures, and you’ll access it using pointers or references—because that’s what programmers do.

> 当然不，我们会将数据存储为链表、树、哈希表、堆栈、队列等各种各样的数据结构，然后用指针或者引用来访问这些数据——因为这对程序员来说是最自然的方式。

In fact, if you think carefully about this issue, you’ll realize that this is what you already do. Even though the data is kept in a database or a file system, you read it into RAM and then you reorganize it, for your own convenience, into lists, sets, stacks, queues, trees, or whatever data structure meets your fancy. It is very unlikely that you leave the data in the form of files or tables.

> 事实上，如果你再仔细想想，就会发现我们已经在这样做了。即使数据保存在数据库或者文件系统中，我们最终也会将其读取到内存中，并按照最方便的形式将其组织成列表、集合、堆栈、队列、树等各种数据结构，继续按文件和表格的形式来操作数据是非常少见的。

## DETAILS 实现细节

This reality is why I say that the database is a detail. It’s just a mechanism we use to move the data back and forth between the surface of the disk and RAM. The database is really nothing more than a big bucket of bits where we store our data on a long-term basis. But we seldom use the data in that form.

> 上面所说的，就是为什么我们认为数据库只是一种实现细节的原因。数据库终究只是在硬盘与内存之间相互传输数据的一种手段而已，它真的可以被认为只是一个长期存储数据的、装满字节的大桶。我们通常并不会真的以这种形式来使用数据。

Thus, from an architectural viewpoint, we should not care about the form that the data takes while it is on the surface of a rotating magnetic disk. Indeed, we should not acknowledge that the disk exists at all.

> 因此，从系统架构的视角来看，真的不应该关系数据在旋转的磁盘表面上以什么样的格式存在。实际上，系统架构应该对磁盘本身的存在完全不关心。

## BUT WHAT ABOUT PERFORMANCE? 但性能怎么办呢

Isn’t performance an architectural concern? Of course it is—but when it comes to data storage, it’s a concern that can be entirely encapsulated and separated from the business rules. Yes, we need to get the data in and out of the data store quickly, but that’s a low-level concern. We can address that concern with low-level data access mechanisms. It has nothing whatsoever to do with the overall architecture of our systems.

> 性能难道不是系统架构的一个考量标准吗？当然是——但当问题涉及数据存储时，这方面的操作通常是被封装起来，隔离在业务逻辑之外的。也就是说，我们确实需要从数据存储中快速地存取数据，但这终究只是一个底层实现问题。我们完全可以在数据访问这一较低的层面上解决这个问题，而不需要让它与系统架构相关联。

## ANECDOTE 一段轶事

In the late 1980s, I led a team of software engineers at a startup company that was trying to build and market a network management system that measured the communications integrity of T1 telecommunication lines. The system retrieved data from the devices at the endpoints of those lines, and then ran a series of predictive algorithms to detect and report problems.

> 在 20 世纪 80 年代末，我曾在一家创业公司中带领一组软件工程师开发和推广一个用于监控 T1 线路通信质量的网络管理系统。该系统从 T1 线路两端的设备抓取数据，然后利用预测算法来检测和汇报问题。

We were using UNIX platforms, and we stored our data in simple random access files. We had no need of a relational database because our data had few content-based relationships. It was better kept in trees and linked lists in those random access files. In short, we kept the data in a form that was most convenient to load into RAM where it could be manipulated.

> 我们当时采用的是 UNIX 平台，并将数据存储成简单的可随机访问的格式。该项目当时也不需要用到关系型数据库，因为数据之间几乎没有内容之间的关系，用树以及链表的形式来存储数据就够了。简单来说，我们的数据存储格式是为了便于加载到内存中处理而设计的。

We hired a marketing manager for this startup—a nice and knowledgeable guy. But he immediately told me that we had to have a relational database in the system. It wasn’t an option and it wasn’t an engineering issue—it was a marketing issue.

> 创业公司后来招聘了一个市场推广经理 他人很好，知识也很全面。然而他告诉我的第一件事就是我们系统中必须有一个关系型数据库。这容不得商量，也不是一个工程问题——而是一个市场问题。

This made no sense to me. Why in the world would I want to rearrange my linked lists and trees into a bunch of rows and tables accessed through SQL? Why would I introduce all the overhead and expense of a massive RDBMS when a simple random access file system was more than sufficient? So I fought him, tooth and nail.

> 这对我来说很难接受，为什么我要将链表和树重新按照表格与行模式重组，并且用 SQL 方式存储呢？为什么我们要在随机访问文件系统已经足够用的情况下引入大型关系型数据库系统？所以我一直和他针锋相对，互不相让。

We had a hardware engineer at this company who took up the RDBMS chant. He became convinced that our software system needed an RDBMS for technical reasons. He held meetings behind my back with the executives of the company, drawing stick figures on the whiteboard of a house balancing on a pole, and he would ask the executives, “Would you build a house on a pole?” His implied message was that an RDBMS that keeps its tables in random access files was somehow more reliable than the random access files that we were using.

> 后来公司内有一位硬件工程师被关系型数据库大潮所感染：它坚信我们的软件系统在技术上有必要采用关系型数据库，他背着我召集了公司的管理层开会，在白板上画了一间用几根杆子支撑的房子，问道：“谁会会把房子建在几根杆子搭起来的地基上？”这背后的逻辑是：通过关系型数据库将数据存储于文件系统中，在某种程度上要比我们自己存储这些文件更可靠。

I fought him. I fought the marketing guy. I stuck to my engineering principles in the face of incredible ignorance. I fought, and fought, and fought.

> 我当然没有放弃，一直不停地和他还有市场部斗争到底。我誓死捍卫了自己的工程原则，不停地开会、斗争。

In the end, the hardware developer was promoted over my head to become the software manager. In the end, they put a RDBMS into that poor system. And, in the end, they were absolutely right and I was wrong.

> 最终，这位硬件工程师被提拔为软件开发经理，最终，系统中也加入了一个关系型数据库。最终，我不得不承认，他们是对的，而我是错的。

Not for engineering reasons, mind you: I was right about that. I was right to fight against putting an RDBMS into the architectural core of the system. The reason I was wrong was because our customers expected us to have a relational database. They didn’t know what they would do with it. They didn’t have any realistic way of using the relational data in our system. But it didn’t matter: Our customers fully expected an RDBMS. It had become a check box item that all the software purchasers had on their list. There was no engineering rationale—rationality had nothing to do with it. It was an irrational, external, and entirely baseless need, but it was no less real.

> 但这里说的不是软件工程问题：在这个问题上我仍然坚持自己没有错，在系统的核心架构中的确不应该引入关系型数据库。这里说我错了的原因，是因为我们的客户希望该系统中能有一个关系型数据库。他们其实也不知道为什么需要，因为他们自己是没有任何机会使用这个关系型数据库的。但这不是重点，问题的重点是我们的客户需要一个关系型数据库。它已经成为当时所有软件购买合同中的一个必选项。这背后毫无工程逻辑——是不理智的。但尽管它是不理智的、外行的、毫无根基的需求，但却是真实存在的。

Where did that need come from? It originated from the highly effective marketing campaigns employed by the database vendors at the time. They had managed to convince high-level executives that their corporate “data assets” needed protection, and that the database systems they offered were the ideal means of providing that protection.

> 这种需求是从哪里来的？其实是来自于当时数据库厂商非常有效的市场推广。他们说服了企业高管，他们的“数据资产”需要某种保护，数据库则提供了非常便捷的保护能力。

We see the same kind of marketing campaigns today. The word “enterprise” and the notion of “Service-Oriented Architecture” have much more to do with marketing than with reality.

> 直到今天我们也能看到这种市场宣传，譬如“企业级”“面向服务的架构”这样的措辞大部分都是市场宣传噱头，而跟实际的工程质量无关。

What should I have done in that long-ago scenario? I should have bolted an RDBMS on the side of the system and provided some narrow and safe data access channel to it, while maintaining the random access files in the core of the system. What did I do? I quit and became a consultant.

> 回头想想，我在这个场景中应该怎么做呢？事实上，我当时应该在系统的某个角落接上一个关系型数据库，在维持系统核心数据结构的同时给关系型数据库提供一些安全的、受限的数据访问方式。但我没这么做，我辞职了，干起了咨询这一行。

## CONCLUSION 本章小结

The organizational structure of data, the data model, is architecturally significant. The technologies and systems that move data on and off a rotating magnetic surface are not. Relational database systems that force the data to be organized into tables and accessed with SQL have much more to do with the latter than with the former. The data is significant. The database is a detail.

> 数据的组织结构，数据的模型，都是系统架构中的重要部分，但是从磁盘上存储、读取数据的机制和手段却没那么重要。关系型数据库强制我们将数据存储成表格并且以 SQL 访问，主要是为了后者。总而言之，数据本身很重要，但数据库系统仅仅是一个实现细节。
