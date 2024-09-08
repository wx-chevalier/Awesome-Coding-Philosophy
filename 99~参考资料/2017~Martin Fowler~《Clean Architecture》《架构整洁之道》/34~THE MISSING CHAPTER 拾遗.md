# Chap34. THE MISSING CHAPTER 拾遗

![](https://ngte-superbed.oss-cn-beijing.aliyuncs.com/book/mono/un/CH-UN34.jpg)

By Simon Brown

All of the advice you’ve read so far will certainly help you design better software, composed of classes and components with well-defined boundaries, clear responsibilities, and controlled dependencies. But it turns out that the devil is in the implementation details, and it’s really easy to fall at the last hurdle if you don’t give that some thought, too.

> 根据本书之前给出的所有建议，相信读者一定能够建构出具有良好边界设计的类和组件，以形成清晰的责任划分以及可控的依赖关系，设计出更好的软件了。但是困难之处往往在于细节之中，一旦疏忽，也有可能会对软件质量造成不良影响。

Let’s imagine that we’re building an online book store, and one of the use cases we’ve been asked to implement is about customers being able to view the status of their orders. Although this is a Java example, the principles apply equally to other programming languages. Let’s put the Clean Architecture to one side for a moment and look at a number of approaches to design and code organization.

> 下面我们再来看一个例子，假设正在构建一个在线书店，这个例子的任务是实现一个客户査看订单状态的用例，虽然这是一个 Java 程序的示例，但其所示范的原理适用于任何语言。现在，让我们暂时将整洁架构的概念放在一边，先来看一下如何具体安排代码设计和代码结构。

## PACKAGE BY LAYER 按层封装

The first, and perhaps simplest, design approach is the traditional horizontal layered architecture, where we separate our code based on what it does from a technical perspective. This is often called “package by layer.” Figure 34.1 shows what this might look like as a UML class diagram.

> 我们首先想到的，也可能是最简单的设计方式，就是传统的水平分层架构。在这个架构里，我们将代码从技术角度进行分类。这通常被称为“按层封装”。图 34.1 用 UML 类图展示了这种设计。

In this typical layered architecture, we have one layer for the web code, one layer for our “business logic,” and one layer for persistence. In other words, code is sliced horizontally into layers, which are used as a way to group similar types of things. In a “strict layered architecture,” layers should depend only on the next adjacent lower layer. In Java, layers are typically implemented as packages. As you can see in Figure 34.1, all of the dependencies between layers (packages) point downward. In this example, we have the following Java types:

> 在这种常见的分层架构中，Web 代码分为一层，业务逻辑分为一层，持久化是另外一层。换句话说，我们对代码进行了水平分层，相同类型的代码在一层。在“严格的分层架构”中，每一层只能对相邻的下层有依赖关系。在 Java 中，分层的概念通常是用包来表示的。如图 34.1 所示，所有的分层（包）之间的依赖关系都是指向下的。这里包括了以下 Java 类。

- OrdersController: A web controller, something like a Spring MVC controller, that handles requests from the web.
- OrdersService: An interface that defines the “business logic” related to orders.
- OrdersServiceImpl: The implementation of the orders service.1
- OrdersRepository: An interface that defines how we get access to persistent order information.
- JdbcOrdersRepository: An implementation of the repository interface.

---

> - OrdersController：Web 控制器，类似 Spring MVC 控制器，负责处理 Web 请求。
> - OrderService：定义订单相关业务逻辑的接口。
> - OrdersServiceImpl：Order 服务的具体实现
> - OrdersRepository：定义如何访问订单持久信息的接口。
> - JdbcOrderRepository：持久信息访问接口的实现。

<Figures figure="34-1">Package by layer</Figures>

In “Presentation Domain Data Layering,”2 Martin Fowler says that adopting such a layered architecture is a good way to get started. He’s not alone. Many of the books, tutorials, training courses, and sample code you’ll find will also point you down the path of creating a layered architecture. It’s a very quick way to get something up and running without a huge amount of complexity. The problem, as Martin points out, is that once your software grows in scale and complexity, you will quickly find that having three large buckets of code isn’t sufficient, and you will need to think about modularizing further.

> 在 presentation Domain Data Layering 这篇文章中，Martin Fowler 声称釆用这种分层架构是初期一个不错选择。他的观点并不缺乏拥戴者。很多书籍、教程和代码示范都在教育你采用分层架构。这种方式在在项目初期之所以会很合适，是因为它不会过于复杂。但就像 Martin 指出的那样，一旦软件规模扩展了，我们很快就会发现将代码分为三大块并不够，需要进一步进行模块化。

Another problem is that, as Uncle Bob has already said, a layered architecture doesn’t scream anything about the business domain. Put the code for two layered architectures, from two very different business domains, side by side and they will likely look eerily similar: web, services, and repositories. There’s also another huge problem with layered architectures, but we’ll get to that later.

> 如 Bob 所说，这里还存在另外一个问题是，分层架构无法展现具体的业务领域信息。把两个不同业务领域的、但是都采用了分层架构的代码进行对比，你会发现它们的相似程度极高：都有 Web 层、服务层和数据仓库层。这是分层架构的另外一个问题，后文会具体讲述。

## PACKAGE BY FEATURE 按功能封装

Another option for organizing your code is to adopt a “package by feature” style. This is a vertical slicing, based on related features, domain concepts, or aggregate roots (to use domain-driven design terminology). In the typical implementations that I’ve seen, all of the types are placed into a single Java package, which is named to reflect the concept that is being grouped.

> 另外一种组织代码的形式是“按功能封装”，即垂直切分，根据相关的功能、业务概念或者聚合根（领域驱动设计原则中的术语）来切分。在常见的实现中，所有的类型都会放在一个相同的包中，以业务概念来命名。

With this approach, as shown in Figure 34.2, we have the same interfaces and classes as before, but they are all placed into a single Java package rather than being split among three packages. This is a very simple refactoring from the “package by layer” style, but the top-level organization of the code now screams something about the business domain. We can now see that this code base has something to do with orders rather than the web, services, and repositories. Another benefit is that it’s potentially easier to find all of the code that you need to modify in the event that the “view orders” use case changes. It’s all sitting in a single Java package rather than being spread out.3

> 图 34.2 展示了这种方式，类和接口与之前类似，但是相比之前，这次它们都被放到了同一个 Java 包中。相 比 “按层封装”，这只是一个小变化，但是现在顶层代码结构至少与业务领域有点相关了。我们可以看到这段代码是与订单有关的，而不是只能看到 Web、服务及数据访问。另外一个好处是，如果需要修改 “查看订单”这个业务用例，比较容易找到相关代码，毕竟它们都在一个包中，而不是分散在各处。

I often see software development teams realize that they have problems with horizontal layering (“package by layer”) and switch to vertical layering (“package by feature”) instead. In my opinion, both are suboptimal. If you’ve read this book so far, you might be thinking that we can do much better—and you’re right.

> 软件研发团队常常一开始采用水平分层方式（即 “按层封装”），遇到困难后再切换到垂直分层方式（即 “按功能封装”）。我认为，两种方式都很不好。看完本书，你应该意识到还有更好的分类方式——没错。

<Figures figure="34-2">Package by feature</Figures>

## PORTS AND ADAPTERS 端口和适配器

As Uncle Bob has said, approaches such as “ports and adapters,” the “hexagonal architecture,” “boundaries, controllers, entities,” and so on aim to create architectures where business/domain-focused code is independent and separate from the technical implementation details such as frameworks and databases. To summarize, you often see such code bases being composed of an “inside” (domain) and an “outside” (infrastructure), as suggested in Figure 34.3.

> 如 Bob 大叔所说，通过采用“端口和适配器”“六边形架构”“边界、控制器、实体”等，我们可以创造出一个业务领域代码与具体实现细节（数据库、框架等）隔离的架构。总结下来，如图 34.3 所示，我们可以区分出代码中的内部代码（领域，Domain）与外部代码（基础设施，Infrastructure）。

<Figures figure="34-3">A code base with an inside and an outside</Figures>

The “inside” region contains all of the domain concepts, whereas the “outside” region contains the interactions with the outside world (e.g., UIs, databases, third-party integrations). The major rule here is that the “outside” depends on the “inside”—never the other way around. Figure 34.4 shows a version of how the “view orders” use case might be implemented.

> 内部区域包含了所有的领域概念，而外部区域则包含了与外界交互的部分（例如 UI、数据库、第三方集成等）。这里主要的规则是，只有外部代码能依赖内部代码，反之则不能。图 34.4 展示了 “查看订单”这个业务用例是如何用这种方式实现的。

The `com.mycompany.myapp.domain` package here is the “inside,” and the other packages are the “outside.” Notice how the dependencies flow toward the “inside.” The keen-eyed reader will notice that the OrdersRepository from previous diagrams has been renamed to simply be Orders. This comes from the world of domain-driven design, where the advice is that the naming of everything on the “inside” should be stated in terms of the “ubiquitous domain language.” To put that another way, we talk about “orders” when we’re having a discussion about the domain, not the “orders repository.”

> 这里 `com.mycompnay.myapp.domain` 包是内部代码，另外一个包是外部代码。注意这里的依赖关系是由外向内的。眼尖的读者可以注意到之前的 OrderRepository 类现在被改名为 Orders。这个概念基于领域驱动设计理念，其中要求内部代码都应该用独特的领域语言来描述。换句话说，我们在业务领域里面讨论的应该是 Orders，而不是“OrdersRepository”。

<Figures figure="34-4">View orders use case</Figures>

It’s also worth pointing out that this is a simplified version of what the UML class diagram might look like, because it’s missing things like interactors and objects to marshal the data across the dependency boundaries.

> 值得注意的是，这里是 UML 类图的一个简化版，这里缺少了交互器，以及跨边界调用时对应的数据编码解码对象。

## PACKAGE BY COMPONENT 按组件封装

Although I agree wholeheartedly with the discussions about SOLID, REP, CCP, and CRP and most of the advice in this book, I come to a slightly different conclusion about how to organize code. So I’m going to present another option here, which I call “package by component.” To give you some background, I’ve spent most of my career building enterprise software, primarily in Java, across a number of different business domains. Those software systems have varied immensely, too. A large number have been web-based, but others have been client–server4, distributed, message-based, or something else. Although the technologies differed, the common theme was that the architecture for most of these software systems was based on a traditional layered architecture.

> 虽然我对本书中的 SOLID、REP、CCP、CRP 以及其他大部分建议完全认同，我想提出对代码组织方式的一个不同看法——“按组件封装”。一些背景信息：在我的职业生涯中，我基于 Java 构建了大量不同领域的企业软件，这些软件系统要求各异。大部分系统都是基于 Web 的，也有一些是 CS 架构，或者是分布式架构的、基于消息的，或者其他的。虽然具体采用的技术不同，但大部分系统都是基于传统的分层架构的。

I’ve already mentioned a couple of reasons why layered architectures should be considered bad, but that’s not the whole story. The purpose of a layered architecture is to separate code that has the same sort of function. Web stuff is separated from business logic, which is in turn separated from data access. As we saw from the UML class diagram, from an implementation perspective, a layer typically equates to a Java package. From a code accessibility perspective, for the OrdersController to be able to have a dependency on the OrdersService interface, the OrdersService interface needs to be marked as public, because they are in different packages. Likewise, the OrdersRepository interface needs to be marked as public so that it can be seen outside of the repository package, by the OrdersServiceImpl class.

> 我已经给出一些分层架构不好的理由，但这还不是全部。分层架构设计的目的是将功能相似的代码进行分组。处理 Web 的代码应该与处理业务逻辑的代码分开，同时也与处理数据访问的代码分开。正如我们在 UML 类图中所见，从实现角度讲，层就是代表了 Java 包。从代码可访问性角度来讲，如果需要 OrdersController 依赖 OrderService 接口，那么这个接口必须设置为 public，因为它们在不同的包中。同样的，OrdersRepository 接口也需要设置为 public，这样才能被包外的类 OrdersServicelmple 使用。

In a strict layered architecture, the dependency arrows should always point downward, with layers depending only on the next adjacent lower layer. This comes back to creating a nice, clean, acyclic dependency graph, which is achieved by introducing some rules about how elements in a code base should depend on each other. The big problem here is that we can cheat by introducing some undesirable dependencies, yet still create a nice, acyclic dependency graph.

> 在严格分层的架构中，依赖指向的箭头应该永远向下，每一层只能依赖相邻的下一层。通过引入一些代码互相依赖的规则，我们就形成了一个干净、漂亮的单向依赖图。这里有一个大问题——只要通过引入一些不应该有的依赖来作弊，依然可以形成漂亮的单向依赖图。

Suppose that you hire someone new who joins your team, and you give the newcomer another orders-related use case to implement. Since the person is new, he wants to make a big impression and get this use case implemented as quickly as possible. After sitting down with a cup of coffee for a few minutes, the newcomer discovers an existing OrdersController class, so he decides that’s where the code for the new orders-related web page should go. But it needs some orders data from the database. The newcomer has an epiphany: “Oh, there’s an OrdersRepository interface already built, too. I can simply dependency-inject the implementation into my controller. Perfect!” After a few more minutes of hacking, the web page is working. But the resulting UML diagram looks like Figure 34.5.

> 假设新员工加入了团队，你给新人安排了一个订单相关的业务用例的实现任务。由于这个人刚刚入职，他想好好表现，尽快完成这项功能。粗略看过代码之后，新人发现了 OrdersController 这个类，于是他将新的订单相关的 Web 代码都塞了进去。但是这段代码需更从数据库查找一些订单数据。这时候这个新人灵机一动：“代码己经有了一个 OrdersRepository 接口，只需要将它用依赖注入框架引入控制器就行，我真机智！”几分钟之后，功能已经正常了，但是 UML 结构图变成了图 34.5 这样。

The dependency arrows still point downward, but the OrdersController is now additionally bypassing the OrdersService for some use cases. This organization is often called a relaxed layered architecture, as layers are allowed to skip around their adjacent neighbor(s). In some situations, this is the intended outcome—if you’re trying to follow the CQRS pattern, for example. In many other cases, bypassing the business logic layer is undesirable, especially if that business logic is responsible for ensuring authorized access to individual records, for example.

> 依赖关系箭头依然向下，但是现在 OrdersController 在某些情况下绕过了 OrderService 类。这种组织形式被称为宽松的分层采构，允许某些层跳过直接相邻的邻居。在有些情况下，这是意料之中的——例如，如果我们在遵循 CQRS 设计模式，这是合理的。但是更多的情况下，绕过业务逻辑层是不合理的，尤其是在业务逻辑层要控制权限的情况下。

While the new use case works, it’s perhaps not implemented in the way that we were expecting. I see this happen a lot with teams that I visit as a consultant, and it’s usually revealed when teams start to visualize what their code base really looks like, often for the first time.

> 虽然新的业务用例可以正常工作，但是它可能不是按照合理方式实现的。作为咨询师，我曾经见过很多团队出现这种情况，只有他们开始仔细观察自己的代码结构图时才会发现。

<Figures figure="34-5">Relaxed layered architecture</Figures>

What we need here is a guideline—an architectural principle—that says something like, “Web controllers should never access repositories directly.” The question, of course, is enforcement. Many teams I’ve met simply say, “We enforce this principle through good discipline and code reviews, because we trust our developers.” This confidence is great to hear, but we all know what happens when budgets and deadlines start looming ever closer.

> 这里我们有的其实只是一个规范——一个架构设计原则内容是“Web 控制器永远不应该直接访问数据层”。这里的核心问题当然是如何强制执行。我遇见的很多团队仅仅通过采用“自律”或者“代码评审”方式来执行，“我相信我的程序员”。有这种自信当然很好，但是我们都知道当预算缩减、工期临近的时候会发生什么事情。

A far smaller number of teams tell me that they use static analysis tools (e.g., NDepend, Structure101, Checkstyle) to check and automatically enforce architecture violations at build time. You may have seen such rules yourself; they usually manifest themselves as regular expressions or wildcard strings that state “types in package `**/web` should not access types in `**/data`”; and they are executed after the compilation step.

> 有一小部分团队告诉我，他们会采用静态分析工具（例如 Ndepend、Structure101、Checkstyle）来在构建阶段自动检查违反架构设计规则的代码。估计你见过这种代码，一般来说就是一段正则表达式，例如“包 `**/web` 下面的类型不允许访问 `**/data` 下面的类型”，这些检查在编译步骤之后执行。

This approach is a little crude, but it can do the trick, reporting violations of the architecture principles that you’ve defined as a team and (you hope) failing the build. The problem with both approaches is that they are fallible, and the feedback loop is longer than it should be. If left unchecked, this practice can turn a code base into a “big ball of mud.”6 I’d personally like to use the compiler to enforce my architecture if at all possible.

> 这种方式虽然简单粗暴，但是确实能起效果，可以锁定违反了团队定义的系统架构设计原则的情况，并且（理想情况下）导致构建失败。这两种方法的共同问题是容易出错，同时反馈循环时间太长了。如果不精心维护，整个代码库可能很快就变成“一团泥巴”。我个人更倾向选择能够让编译器执法的做法。

This brings us to the “package by component” option. It’s a hybrid approach to everything we’ve seen so far, with the goal of bundling all of the responsibilities related to a single coarse-grained component into a single Java package. It’s about taking a service-centric view of a software system, which is something we’re seeing with micro-service architectures as well. In the same way that ports and adapters treat the web as just another delivery mechanism, “package by component” keeps the user interface separate from these coarse-grained components. Figure 34.6 shows what the “view orders” use case might look like.

> 那么，看一下“按组件封装”的做法。这种做法混合了我们之前讲的所有的方法，目标是将一个粗粒度组件相关的所有类放入一个 Java 包中。这就像是以一种面向服务的视角来构建软件系统，与微服务架构类似。这里，就像端口和适配器模式将 Web 视为一种交付手段一样，“按组件封装”将 U1 与粗粒度组件分离。图 34.6 展示了“查看订单”这个用例的设计图。

In essence, this approach bundles up the “business logic” and persistence code into a single thing, which I’m calling a “component.” Uncle Bob presented his definition of “component” earlier in the book, saying:

> 总的来说，这种方式将“业务逻辑”与“持久化代码”合并在一起，称为“组件”，Bob 大叔在本书中对“组件”的定义如下：

Components are the units of deployment. They are the smallest entities that can be deployed as part of a system. In Java, they are jar files.

> 组件是部署单元。组件是系统中能够部署的最小单位，对应在 Java 里就是 jar 文件。

<Figures figure="34-6">View orders use case</Figures>

My definition of a component is slightly different: “A grouping of related functionality behind a nice clean interface, which resides inside an execution environment like an application.” This definition comes from my “C4 software architecture model,”7 which is a simple hierarchical way to think about the static structures of a software system in terms of containers, components, and classes (or code). It says that a software system is made up of one or more containers (e.g., web applications, mobile apps, stand-alone applications, databases, file systems), each of which contains one or more components, which in turn are implemented by one or more classes (or code). Whether each component resides in a separate jar file is an orthogonal concern.

> 我对组件的定义稍有不同：“在一个执行环境（应川程序）中的、一个干净、良好的接口背后的一系列相关功能的集合”。这个定义来自我的“C4 软件架构模型" 这个模里以一种层级模型讨论软件系统的静态结构，其中的概念包括容器、细件、类。这个模型认为，系统由一个或者多个容器组成（例 如 Web 应用、移动 App、独立应用、数据库、文件系统），每个容器包含一个或多个组件，每个组件由一个或多个类组成。每个组件具体存在于哪个 jar 文件中则是另外一个维度的事情。

A key benefit of the “package by component” approach is that if you’re writing code that needs to do something with orders, there’s just one place to go—the OrdersComponent. Inside the component, the separation of concerns is still maintained, so the business logic is separate from data persistence, but that’s a component implementation detail that consumers don’t need to know about. This is akin to what you might end up with if you adopted a micro-services or Service-Oriented Architecture—a separate OrdersService that encapsulates everything related to handling orders. The key difference is the decoupling mode. You can think of well-defined components in a monolithic application as being a stepping stone to a micro-services architecture.

> 这种“按组件封装”的方式的一个好处是，如果我们需要编写和订单有关的代码，只有一个位置需要修改 OrdersComponent。在这个组件中，仍然应该关注重点隔离原则，但这是组件内部|可题，使用者不需要关心。这就有点像采用微服务架构，或者是面向服务架构的结果——独立的 OrderService 会将所有订单相关的东西封装起来。这里关键的区别是解耦的方式。我们可以认为，单体程序中的一个良好定义的组件，是微服务化架构的一个前提条件。

## THE DEVIL IS IN THE IMPLEMENTATION DETAILS 具体实现细节中的陷阱

On the face of it, the four approaches do all look like different ways to organize code and, therefore, could be considered different architectural styles. This perception starts to unravel very quickly if you get the implementation details wrong, though.

> 表面上看，四种代码组织方式各不相同，可以认为是不同的架构设计风格。可是，如果具体实现中不严加注意，很快就会出现偏差。

Something I see on a regular basis is an overly liberal use of the public access modifier in languages such as Java. It’s almost as if we, as developers, instinctively use the public keyword without thinking. It’s in our muscle memory. If you don’t believe me, take a look at the code samples for books, tutorials, and open source frameworks on GitHub. This tendency is apparent, regardless of which architectural style a code base is aiming to adopt—horizontal layers, vertical layers, ports and adapters, or something else. Marking all of your types as public means you’re not taking advantage of the facilities that your programming language provides with regard to encapsulation. In some cases, there’s literally nothing preventing somebody from writing some code to instantiate a concrete implementation class directly, violating the intended architecture style.

> 我经常遇到的一个问题是，Java 中 public 访问控制修饰符的滥用。我们作为程序员，好像天生就喜欢使用严 public 关键词。这就好像是肌肉记忆一样。如果不信，请看一下各种书籍的代码示范、各种入门教程，以及 GitHub 上的开源框架。这个趋势是显而易见的，不管采用了哪种系统架构风格。将所有的类都设置为 public 意味着就无法利用编程语言提供的封装手段。这样一来，没有任何东西可以阻碍某人写一段直接初始化具体实现类的代码，哪怕它违反了架构设计的要求。

## ORGANIZATION VERSUS ENCAPSULATION 组织形式与封装的区别

Looking at this issue another way, if you make all types in your Java application public, the packages are simply an organization mechanism (a grouping, like folders), rather than being used for encapsulation. Since public types can be used from anywhere in a code base, you can effectively ignore the packages because they provide very little real value. The net result is that if you ignore the packages (because they don’t provide any means of encapsulation and hiding), it doesn’t really matter which architectural style you’re aspiring to create. If we look back at the example UML diagrams, the Java packages become an irrelevant detail if all of the types are marked as public. In essence, all four architectural approaches presented earlier in this chapter are exactly the same when we overuse this designation (Figure 34.7).

> 从另外一个角度来看，如果我们将 Java 程序中的所有类型都设置为 public，那么包就仅仅是一种组织形式了（类似文件夹一样的分组方式），而不是一种封装方式，由于 public 类型可以在代码库的任何位置调用，我们事实上就可以忽略包的概念，因为它并不提供什么价值。最终，如果忽视包的概念（因为并不起到任何封装和隐藏的功能），那么想要采用的任何架构风格就都不重要了。我们回过头来看一下例子中的 UML 图，如果所有的类型都是 public，那么 Java 包就成了一个无关紧要的细节信息。于是，所有四种架构方式事实上并没有任何区别（参见图 34.7）。

Take a close look at the arrows between each of the types in Figure 34.7: They’re all identical regardless of which architectural approach you’re trying to adopt. Conceptually the approaches are very different, but syntactically they are identical. Furthermore, you could argue that when you make all of the types public, what you really have are just four ways to describe a traditional horizontally layered architecture. This is a neat trick, and of course nobody would ever make all of their Java types public. Except when they do. And I’ve seen it.

> 我们再详细看一下图 34.7 中各个类之间的箭头：不论采用哪种架构设计风格，它们的指向都是一致的。虽然概念不同，但是语法上都是一致的。更进一步说，如果所有的类都是 public 的，那么其实我们就是在用四种不同的方式描述一个传统的分层架构设计方式。你会说当然没有人会将所有的 Java 类都设置为 public，但是相信我，我见过。

The access modifiers in Java are not perfect,8 but ignoring them is just asking for trouble. The way Java types are placed into packages can actually make a huge difference to how accessible (or inaccessible) those types can be when Java’s access modifiers are applied appropriately. If I bring the packages back and mark (by graphically fading) those types where the access modifier can be made more restrictive, the picture becomes pretty interesting (Figure 34.8).

> 虽然 Java 中的访问修饰符并不完美，但是忽略它们的存存就是在自找麻烦。Java 类与包的组织形式其实可以很大程度决定这个类的可访问性（或者不可访问性）。如果我们将包的概念引入这幅图，同时标记（虚化的形式展示）应用到访问控制符的地方，这个图就很有意思了（参见图 34.8）。

<Figures figure="34-7">All four architectural approaches are the same</Figures>

Moving from left to right, in the “package by layer” approach, the OrdersService and OrdersRepository interfaces need to be public, because they have inbound dependencies from classes outside of their defining package. In contrast, the implementation classes (OrdersServiceImpl and JdbcOrdersRepository) can be made more restrictive (package protected). Nobody needs to know about them; they are an implementation detail.

> 从左向右，在 “按层封装”方式中，OrderService 与 OrderRepository 需要 public 修饰符，因为包外的类需要依赖它们。然而，具体实现类（OrdersServiceImpl 和 JdbcOrdersRepository）则可以设置更细致的访问权限（包范围内的 protected）。不需要有人依赖它们，它们是具体的实现细节。

In the “package by feature” approach, the OrdersController provides the sole entry point into the package, so everything else can be made package protected. The big caveat here is that nothing else in the code base, outside of this package, can access information related to orders unless they go through the controller. This may or may not be desirable.

> 在“按功能封装”模式中，OrdersController 是整个包的入口，所以其他的类都可以设置为包范围内的 protected。这里的一个问题是，代码库中的其他代码都必须通过控制器才能访问订单信息——这可能是好处，也可能是坏处，视实际情况而定。

In the ports and adapters approach, the OrdersService and Orders interfaces have inbound dependencies from other packages, so they need to be made public. Again, the implementation classes can be made package protected and dependency injected at runtime.

> 在端口与适配器模式中，OrderService 与 Orders 接口都有来自包外的依赖关系，所以需要 public 修饰符。同样，实现类可以设置为包范围内 protected，依赖在运行时注入。

<Figures figure="34-8">Grayed-out types are where the access modifier can be made more restrictive</Figures>

Finally, in the “package by component” approach, the OrdersComponent interface has an inbound dependency from the controller, but everything else can be made package protected. The fewer public types you have, the smaller the number of potential dependencies. There’s now no way9 that code outside this package can use the OrdersRepository interface or implementation directly, so we can rely on the compiler to enforce this architectural principle. You can do the same thing in .NET with the internal keyword, although you would need to create a separate assembly for every component.

> 最后、在“组件”封装模式中，OrdersComponent 接口有来自 Controller 的依赖关系，但是其他类都可以设置为包 protected。Public 类型越少，潜在的依赖关系就越少。现在包外代码就不能再直接使用 OrdersRepository 接口或者具对应的实现，我们就可以利用编译器来维护架构设计原则了。在 .Net 语言中，我们可以用 internal 关键词达到一样的目的，然而我们需要给每个组件创建一个单独的 assembly。

Just to be absolutely clear, what I’ve described here relates to a monolithic application, where all of the code resides in a single source code tree. If you are building such an application (and many people are), I would certainly encourage you to lean on the compiler to enforce your architectural principles, rather than relying on self-discipline and post-compilation tooling.

> 再澄清一点，这里描述的全都和单体程序有关，所有代码都存放在同一个代码树下。如果你在构建这种程序（大部分程序都是如此），那么我强烈建议利用编译器来维护架构设计原理，而不要依赖个人自律和编译过程之后的工具。

## OTHER DECOUPLING MODES 其他的解耦合模式

In addition to the programming language you’re using, there are often other ways that you can decouple your source code dependencies. With Java, you have module frameworks like OSGi and the new Java 9 module system. With module systems, when used properly, you can make a distinction between types that are public and types that are published. For example, you could create an Orders module where all of the types are marked as public, but publish only a small subset of those types for external consumption. It’s been a long time coming, but I’m enthusiastic that the Java 9 module system will give us another tool to build better software, and spark people’s interest in design thinking once again.

> 除编程语言自带的工具之外，通常还有其他方式可以进一步解耦源代码级别的依赖关系。在 Java 语言中，有模块化框架 OSGi，以及最新的 Java 9 模块系统。正确利用模块系统，我们可以进一步区分 public 类型和对外发布的类型。例如，我们可以创建一个 Orders 模块。将所有的类型标记为 public，但仅仅公布一小部分类供外部调用。虽然耗时很久，但是我十分期待 Java 9 的模块系统，它能提供构建更好软件的另一套工具，希望能够再次点燃人们思考设计的热情。

Another option is to decouple your dependencies at the source code level, by splitting code across different source code trees. If we take the ports and adapters example, we could have three source code trees:

> 另外一个选择是将代码分散到不同的代码树中，以从源代码级别解耦依赖关系。以端口和适配器方式为例，我们会有三个代码树：

- The source code for the business and domain (i.e., everything that is independent of technology and framework choices): OrdersService, OrdersServiceImpl, and Orders
- The source code for the web: OrdersController
- The source code for the data persistence: JdbcOrdersRepository

---

> - 业务代码（所有技术和框架无关的代码）：OrdersService、OrdersServiceImpl 以及 Orders。
> - Web 源代码：OrdersController。
> - 持久化源代码：JdbcOrdersRepository。

The latter two source code trees have a compile-time dependency on the business and domain code, which itself doesn’t know anything about the web or the data persistence code. From an implementation perspective, you can do this by configuring separate modules or projects in your build tool (e.g., Maven, Gradle, MSBuild). Ideally you would repeat this pattern, having a separate source code tree for each and every component in your application. This is very much an idealistic solution, though, because there are real-world performance, complexity, and maintenance issues associated with breaking up your source code in this way.

> 后面两个源代码树对业务代码有编译期依赖关系，而业务代码则对 Web 和数据持久毫无所知。从实现角度来看，我们可以通过将这些代码在构建工具中组织成不同的模块或者项目（例如 Maven> Gradle> MSBUILD 等）来达到目的。理想情况下，我们可以用这种模式将所有组件都划分成不同的项目。然而，这有点太理想化了，因为拆分代码库经常会带来性能、复杂度和维护性方面的问题。

A simpler approach that some people follow for their ports and adapters code is to have just two source code trees:

> 有些人采用一个稍微简单的组织方式，仅使用两个代码树：

- Domain code (the “inside”)
- Infrastructure code (the “outside”)

---

> - 业务（Domain）代码（内部）
> - 基础设施（Infrastructure）代码（外部）

This maps on nicely to the diagram (Figure 34.9) that many people use to summarize the ports and adapters architecture, and there is a compile-time dependency from the infrastructure to the domain.

> 这与图 34.9 完美对应，很多人都用这个方式来简化对端口和适配器架构的描述。基础设施部分对业务代码有一个编译期的依赖关系。

<Figures figure="34-9">Domain and infrastructure code</Figures>

This approach to organizing source code will also work, but be aware of the potential trade-off. It’s what I call the “Périphérique anti-pattern of ports and adapters.” The city of Paris, France, has a ring road called the Boulevard Périphérique, which allows you to circumnavigate Paris without entering the complexities of the city. Having all of your infrastructure code in a single source code tree means that it’s potentially possible for infrastructure code in one area of your application (e.g., a web controller) to directly call code in another area of your application (e.g., a database repository), without navigating through the domain. This is especially true if you’ve forgotten to apply appropriate access modifiers to that code.

> 这种代码组织方式是可行的，但是需要额外注意随之而来的问题。我称这个问题为“端口与适配器模式中的 Peripherique 反模式”。法国巴黎有一条环形公路，名字是 Peripherique 大道。这条大道允许车辆环绕巴黎而不需要进入社区。同样的，将所有的基础设施代码放在同一个源代码树中，就有可能使得应用中的一个区域的基础设施代码（Web 控制器）直接调用另外一个区域的代码（数据库访问），而不经过领域代码。如果没有设置正确的访问修饰符，就更是如此了。

## CONCLUSION: THE MISSING ADVICE 本章小结：本书拾遗

The whole point of this chapter is to highlight that your best design intentions can be destroyed in a flash if you don’t consider the intricacies of the implementation strategy. Think about how to map your desired design on to code structures, how to organize that code, and which decoupling modes to apply during runtime and compile-time. Leave options open where applicable, but be pragmatic, and take into consideration the size of your team, their skill level, and the complexity of the solution in conjunction with your time and budgetary constraints. Also think about using your compiler to help you enforce your chosen architectural style, and watch out for coupling in other areas, such as data models. The devil is in the implementation details.

> 这一章的中心思想就是，如果不考虑具体实现细节，再好的设计也无法长久。必须要将设计映射到对应的代码结构上，考虑如何组织代码树，以及在编译期和运行期采用哪种解耦合的模式。保持开放，但是一定要务实，同时要考虑到团队的大小、技术水平，以及对应的时间和预算限制。最好能利用编译器来维护所选的系统架构设计风格，小心防范来自其他地方的耦合模式，例如数据结构。所有的实现细节都是关键的！
