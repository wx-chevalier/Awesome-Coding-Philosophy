# Chap16. INDEPENDENCE 独立性

![](https://ngte-superbed.oss-cn-beijing.aliyuncs.com/book/mono/un/CH-UN16.jpg)

As we previously stated, a good architecture must support:

> 正如我们之前所述，

- The use cases and operation of the system.
- The maintenance of the system.
- The development of the system.
- The deployment of the system.

---

> - 系统的用例与正常运行。
> - 系统的维护。
> - 系统的开发。
> - 系统的部署。

## USE CASES 用例

The first bullet—use cases—means that the architecture of the system must support the intent of the system. If the system is a shopping cart application, then the architecture must support shopping cart use cases. Indeed, this is the first concern of the architect, and the first priority of the architecture. The architecture must support the use cases.

> 我们先来看第一个支持目标：用例。我们认为一个系统的架构必须能够支持其自身的设计意图。也就是说，如果某系统是一个购物车应用，那么该系统的架构就必须非常直观地支持这类应用可能会涉及的所有用例。事实上，这本来就是架构师们首先要关注的问题，也是架构设计过程中的首要工作。软件的架构必须为其用例提供支持。

However, as we discussed previously, architecture does not wield much influence over the behavior of the system. There are very few behavioral options that the architecture can leave open. But influence isn’t everything. The most important thing a good architecture can do to support behavior is to clarify and expose that behavior so that the intent of the system is visible at the architectural level.

> 然而，正如我们前面所讨论的，一个系统的架构对其行为并没有太大的影响。虽然架构也可以限制一些行为选项，但这种影响所涉及的范围并不大。一个设计良好的架构在行为上对系统最重要的作用就是明确和显式地反映系统设计意图的行为，使其在架构层面上可见。

A shopping cart application with a good architecture will look like a shopping cart application. The use cases of that system will be plainly visible within the structure of that system. Developers will not have to hunt for behaviors, because those behaviors will be first-class elements visible at the top level of the system. Those elements will be classes or functions or modules that have prominent positions within the architecture, and they will have names that clearly describe their function.

> 譬如说，一个架构优良的购物车应用看起来就该像是一个购物车应用。该系统的主要用例会在其系统结构上明确可见。开发人员将不需要在系统中查找系统所应有的行为，因为这些行为在系统顶层作为主要元素已经是明确可见的了，这些元素会以类、函数或模块的形式在架构中占据明显位置，它们的名字也能够清晰地描述对应的功能。

Chapter 21, “Screaming Architecture,” will make this point much clearer.

> 在第 21 章“尖叫的软件架构”中，我们还会更详细地解释这部分内容。

## OPERATION 运行

Architecture plays a more substantial, and less cosmetic, role in supporting the operation of the system. If the system must handle 100,000 customers per second, the architecture must support that kind of throughput and response time for each use case that demands it. If the system must query big data cubes in milliseconds, then the architecture must be structured to allow this kind of operation.

> 架构在支持系统运行方面扮演着更实际的角色。如果某个系统每秒要处理 100 000 个用户，该系统的架构就必须能支持这种级别的吞吐量和响应时间。同样的，如果某个系统要在毫秒级的时间内完成对大数据仓库的查询，那么该系统的架构也必须能支持这类操作。

For some systems, this will mean arranging the processing elements of the system into an array of little services can be run in parallel on many different servers. For other systems, it will mean a plethora of little lightweight threads sharing the address space of a single process within a single processor. Still other systems will need just a few processes running in isolated address spaces. And some systems can even survive as simple monolithic programs running in a single process.

> 对一些系统来说，这意味着它的架构应该支持将其计算部分拆分成一系列小型服务，然后让它们并行运行在不同的服务器上。而在另一些系统中，采用一堆轻量级线程，然后让这些线程共享一个运行在单处理器上的进程的地址空间。还有一些系统，它们可能只是一组运行在独立地址空间内的进程。甚至有些系统设计为一个单进程的单体程序就够了。

As strange as it may seem, this decision is one of the options that a good architect leaves open. A system that is written as a monolith, and that depends on that monolithic structure, cannot easily be upgraded to multiple processes, multiple threads, or micro-services should the need arise. By comparison, an architecture that maintains the proper isolation of its components, and does not assume the means of communication between those components, will be much easier to transition through the spectrum of threads, processes, and services as the operational needs of the system change over time.

> 虽然看起来有点奇怪，但上述问题的决策的确也应该属于一个优秀的架构师为我们保留的可选项之一。毕竟一个按照单体模式编写的系统，它依赖的必然是单体结构，之后再想把它改造成多进程、多线程或微服务模式可就没有那么容易了。相比之下，如果该系统的架构能够在其组件之间做一些适当的隔离，同时不强制规定组件之间的交互方式，该系统就可以随时根据不断变化的运行需求来转换成各种运行时的线程、进程或服务模型。

## DEVELOPMENT 开发

Architecture plays a significant role in supporting the development environment. This is where Conway’s law comes into play. Conway’s law says:

> 系统的架构在支持开发环境方面当然扮演着重要的角色，我们在这里可以引述一下康威定律：

Any organization that designs a system will produce a design whose structure is a copy of the organization’s communication structure.

> 任何一个组织在设计系统时，往往都会复制出一个与该组织内沟通结构相同的系统。

A system that must be developed by an organization with many teams and many concerns must have an architecture that facilitates independent actions by those teams, so that the teams do not interfere with each other during development. This is accomplished by properly partitioning the system into well-isolated, independently developable components. Those components can then be allocated to teams that can work independently of each other.

> 一个由多个不同目标的团队协作开发的系统必须具有相应的软件架构。这样，这些团队才可以各自独立地完成工作，不会彼此干扰。这就需要恰当地将系统切分为一系列隔离良好、可独立开发的组件。然后才能将这些组件分配给不同的团队各自独立开发。

## DEPLOYMENT 部署

The architecture also plays a huge role in determining the ease with which the system is deployed. The goal is “immediate deployment.” A good architecture does not rely on dozens of little configuration scripts and property file tweaks. It does not require manual creation of directories or files that must be arranged just so. A good architecture helps the system to be immediately deployable after build.

> 一个系统的架构在其部署的便捷性方面起到的作用也是非常大的。设计目标一定是实现“立刻部署”。一个设计良好的架构通常不会依赖于成堆的脚本与配置文件，也不需要用户手动创建一堆“有严格要求”的目录与文件。总而言之，一个设计良好的软件架构可以让系统在构建完成之后立刻就能部署。

Again, this is achieved through the proper partitioning and isolation of the components of the system, including those master components that tie the whole system together and ensure that each component is properly started, integrated, and supervised.

> 同样的，这些也需要通过正确地划分、隔离系统组件来实现，这其中包括开发一些主组件，让它们将整个系统黏合在一起，正确地启动、连接并监控每个组件。

## LEAVING OPTIONS OPEN 保留可选项

A good architecture balances all of these concerns with a component structure that mutually satisfies them all. Sounds easy, right? Well, it’s easy for me to write that.

> 一个设计良好的架构应该充分地权衡以上所述的所有关注点，然后尽可能塔成一个可以同时满足所有需求的组件结构。这说起来还挺容易的，不是吗？

The reality is that achieving this balance is pretty hard. The problem is that most of the time we don’t know what all the use cases are, nor do we know the operational constraints, the team structure, or the deployment requirements. Worse, even if we did know them, they will inevitably change as the system moves through its life cycle. In short, the goals we must meet are indistinct and inconstant. Welcome to the real world.

> 事实上，要实现这种平衡是很困难的。主要问题是，我们在大部分时间里无法预知系统的所有用例的，而且我们也无法提前预知系统的运行条件、开发团队的结构，或者系统的部署需求。更糟糕的是，就算我们能提前了解这些需求，随着系统生命周期的演进，这些需求也会不可避免地发生变化。总而言之，事实上我们想要达到的目标本身就是模糊多变的。真实的世界就这样。

But all is not lost: Some principles of architecture are relatively inexpensive to implement and can help balance those concerns, even when you don’t have a clear picture of the targets you have to hit. Those principles help us partition our systems into well-isolated components that allow us to leave as many options open as possible, for as long as possible.

> 然而，我们还是可以通过采用一些实现成本较低的架构原则来做一些事情的。虽然我们没有清晰的目标，但采用一些原则总是有助于提前解决一些平衡问题。通过遵守这些原则可以帮助我们正确地划分为一些隔离良好的组件，以便尽可能长时间地为我们的未来保留尽可能多的可选项。

A good architecture makes the system easy to change, in all the ways that it must change, by leaving options open.

> 一个设计良好的架构应该通过保留可选项的方式，让系统在任何情况下都能方便地做出必要的变更。

## DECOUPLING LAYERS 按层解耦

Consider the use cases. The architect wants the structure of the system to support all the necessary use cases, but does not know what all those use cases are. However, the architect does know the basic intent of the system. It’s a shopping cart system, or it’s a bill of materials system, or it’s an order processing system. So the architect can employ the Single Responsibility Principle and the Common Closure Principle to separate those things that change for different reasons, and to collect those things that change for the same reasons—given the context of the intent of the system.

> 从用例的角度来看，架构师的目标是让系统结构支持其所需要的所有用例。但是问题恰恰是我们无法预知全部的用例。好在架构师应该还是知道整个系统的基本设计意图的。也就是说，架构师应该知道自己要设计的是一个购物车系统，或是运输清单系统，还是订单处理系统。所以架构师可以通过采用单一职责原则（SRP）和共同闭包原则（CCP），以及既定的系统设计意图来隔离那些变更原因不同的部分，集成变更原因相同的部分。

What changes for different reasons? There are some obvious things. User interfaces change for reasons that have nothing to do with business rules. Use cases have elements of both. Clearly, then, a good architect will want to separate the UI portions of a use case from the business rule portions in such a way that they can be changed independently of each other, while keeping those use cases visible and clear.

> 哪些部分的变更原因是不同的呢？这在有些情况下是很显而易见的。譬如，用户界面的变更原因肯定和业务逻辑是不相关的，而业务用例则通常在两边都存在着相关的元素。所以很显然，优秀的架构师应该会将用例的 UI 部分与其业务逻辑部分隔离，这样这两部分就既可以各自进行变更，也能保证用例的完整清晰。

Business rules themselves may be closely tied to the application, or they may be more general. For example, the validation of input fields is a business rule that is closely tied to the application itself. In contrast, the calculation of interest on an account and the counting of inventory are business rules that are more closely associated with the domain. These two different kinds of rules will change at different rates, and for different reasons—so they should be separated so that they can be independently changed.

> 而业务逻辑则既可以是与应用程序紧密相关的，也可以是更具有普适性的。例如，对输入字段的校验是一个与应用程序本身紧密相关的业务逻辑。相反，计算账户利息以及清点库存则是一个与具体领域更为相关的业务逻辑。这两种不同的业务逻辑通常有着不同的变更速率和变更原因——它们应该被相互隔离，以方便各自的变更。

The database, the query language, and even the schema are technical details that have nothing to do with the business rules or the UI. They will change at rates, and for reasons, that are independent of other aspects of the system. Consequently, the architecture should separate them from the rest of the system so that they can be independently changed.

> 至于数据库，以及其所采用的查询语言，甚至表结构，这些都是系统的技术细节信息，它们与业务规则或 UI 毫无关系。这就意味着它们的变更原因、变更速率必然与系统的其他方面各不相同。因此，架构师也应该将它们与系统其他部分隔离，以方便各自的变更。

Thus we find the system divided into decoupled horizontal layers—the UI, application-specific business rules, application-independent business rules, and the database, just to mention a few.

> 这样一来，我们就发现了一个系统可以被解耦成若干个水平分层应用独有的业务逻辑、领域普适的业务逻辑、数据库等。

## DECOUPLING USE CASES 用例的解耦

What else changes for different reasons? The use cases themselves! The use case for adding an order to an order entry system almost certainly will change at a different rate, and for different reasons, than the use case that deletes an order from the system. Use cases are a very natural way to divide the system.

> 接下来，还有什么不同的原因的变更呢？答案正是这些用例本身！譬如说，添加新订单的用例与删除订单的用例在发生变更的原因上几乎肯定是不同的，而且发生变更的速率也不同。因此，我们按照用例来切分系统是非常自然的选择。

At the same time, use cases are narrow vertical slices that cut through the horizontal layers of the system. Each use case uses some UI, some application-specific business rules, some application-independent business rules, and some database functionality. Thus, as we are dividing the system in to horizontal layers, we are also dividing the system into thin vertical use cases that cut through those layers.

> 与此同时，这些用例也是上述系统水平分层的一个个垂直切面。每个用例都会用到一些 UI、特定应用的业务逻辑、应用无关的业务逻辑以及数据库功能。因此，我们时系统水平切分成多个分层的同时，也在按用例将其切分成多个垂直切分。

To achieve this decoupling, we separate the UI of the add-order use case from the UI of the delete-order use case. We do the same with the business rules, and with the database. We keep the use cases separate down the vertical height of the system.

> 为了实现这样的解耦，我们应该将增加订单这个用例的 UI 与删除订单用例的 UI 分开。而且，对业务逻辑的部分、数据库的部分，也要做同样的事情，将其按照用例进行垂直切分。

You can see the pattern here. If you decouple the elements of the system that change for different reasons, then you can continue to add new use cases without interfering with old ones. If you also group the UI and database in support of those use cases, so that each use case uses a different aspect of the UI and database, then adding new use cases will be unlikely to affect older ones.

> 由此，我们可以总结出一个模式：如果我们按照变更原因的不同对系统进行解耦，就可以持续地向系统内添加新的用例，而不会影响旧有的用例。如果我们同时对支持这些用例的 UI 和数据库也进行了分组，那么每个用例使用的就是不同面向的 UI 与数据库，因此增加新用例就更不太可能会影响旧有的用例了。

## DECOUPLING MODE 解耦的模式

Now think of what all that decoupling means for the second bullet: operations. If the different aspects of the use cases are separated, then those that must run at a high throughput are likely already separated from those that must run at a low throughput. If the UI and the database have been separated from the business rules, then they can run in different servers. Those that require higher bandwidth can be replicated in many servers.

> 现在我们来想想所有的这些解耦动作对架构设计的第二个目标——系统运行——究竟有什么意义。如果不同面向之间的用例得到了良好的隔离，那么需要高吞吐量的用例就和需要低吞吐量的用例互相自然分开了。如果 UI 和数据库的部分能从业务逻辑分离出来，那么它们就可以运行在不同的服务器上。而且需要较大带宽的应用也可以在多个服务器上运行多个实例。

In short, the decoupling that we did for the sake of the use cases also helps with operations. However, to take advantage of the operational benefit, the decoupling must have the appropriate mode. To run in separate servers, the separated components cannot depend on being together in the same address space of a processor. They must be independent services, which communicate over a network of some kind.

> 总而言之，这种按用例解耦的动作是有利于系统运行的。然而出于系统运行效率的考虑，我们旳解耦动作还应该注意选择恰当的模式。譬如，为了在不同的服务器上运行，被隔离的组件不能依赖于某个处理器上的同一个地址空间，它们必须是独立的服务，然后通过某种网络来进行通信。

Many architects call such components “services” or “micro-services,” depending upon some vague notion of line count. Indeed, an architecture based on services is often called a service-oriented architecture.

> 许多架构帅将上面这种组件称为“服务”，或“微服务”，至于是前者还是后者，往往取决于某些非常模糊的代码行数阈值。对于这种基于服务来构建的架构，架构师们通常称之为面向服务的架构（service-oriented architecture）。

If that nomenclature set off some alarm bells in your mind, don’t worry. I’m not going to tell you that SoA is the best possible architecture, or that micro-services are the wave of the future. The point being made here is that sometimes we have to separate our components all the way to the service level.

> 如果因为这里提到了 SOA 这个概念而引起了某些读者的警觉，请不用担心，在这里并没有鼓吹 SOA 是一种最佳的软件架构，或者微服务就是未来的潮流。我只是认为有时候我们必须把组件切割到服务这个应用层次。

Remember, a good architecture leaves options open. The decoupling mode is one of those options.

> 请记住，一个设计良好的架构总是要为将来多留一些可选项，这里所讨论的解耦模式也是这样的可选项之一。

Before we explore that topic further, let’s look to the other two bullets.

> 接下来，在我们继续深入探讨这个话题之前，先回过头来看看其他两个设计目标。

## INDEPENDENT DEVELOP-ABILITY 开发的独立性

The third bullet was development. Clearly when components are strongly decoupled, the interference between teams is mitigated. If the business rules don’t know about the UI, then a team that focuses on the UI cannot much affect a team that focuses on the business rules. If the use cases themselves are decoupled from one another, then a team that focuses on the addOrder use case is not likely to interfere with a team that focuses on the deleteOrder use case.

> 我们进行架构设计的第三个目标是支持系统的开发。很显然，当系统组件之间被高度解耦之后，开发团队之间的干扰就大大减少了。譬如说，如果系统的业务逻辑与其 UI 无关，那么专注于 UI 开发的团队就不会对专注于业务逻辑开发的团队造成多大的影响。同样的，如果系统的各个用例之间相互隔离，那么专注于 addOrder 用例的团队就不太可能干扰到负责 deleteOrder 用例的团队。

So long as the layers and use cases are decoupled, the architecture of the system will support the organization of the teams, irrespective of whether they are organized as feature teams, component teams, layer teams, or some other variation.

> 只要系统按照其水平分层和用例进行了恰当的解耦，整个系统的架构就可以支持多团队开发，不管团队组织形式是分功能开发、分组件开发、分层开发，还是按照别的什么变量分工都可以。

## INDEPENDENT DEPLOYABILITY 部署的独立性

The decoupling of the use cases and layers also affords a high degree of flexibility in deployment. Indeed, if the decoupling is done well, then it should be possible to hot-swap layers and use cases in running systems. Adding a new use case could be as simple as adding a few new jar files or services to the system while leaving the rest alone.

> 这种按用例和水平分层的解耦也会给系统的部署带来极大的灵活性。实际上，如果解耦工作做得好，我们甚至可以在系统运行过程中热切换（hot-swap）其各个分层实现和具体的用例。在这种情况下，我们增加新测试用例就只需要在系统中添加一些新的 jar 文件，或启动一些服务即可，其他部分将完全不受影响。

## DUPLICATION 重复

Architects often fall into a trap—a trap that hinges on their fear of duplication.

> 架构师们经常会钻进一个牛角尖——害怕重复。

Duplication is generally a bad thing in software. We don’t like duplicated code. When code is truly duplicated, we are honor-bound as professionals to reduce and eliminate it.

> 当然，重复在软件行业里一般来说都是坏事。我们不喜欢重复的代码，当代码真的出现重复时，我们经常会感到作为一个专业人士’自己是有责任减少或消除这种重复的。

But there are different kinds of duplication. There is true duplication, in which every change to one instance necessitates the same change to every duplicate of that instance. Then there is false or accidental duplication. If two apparently duplicated sections of code evolve along different paths—if they change at different rates, and for different reasons—then they are not true duplicates. Return to them in a few years, and you’ll find that they are very different from each other.

> 但是重复也存在着很多种情况。其中有些是真正的重复，在这种情况下，每个实例上发生的每项变更都必须同时应用到其所有的副本上。重复的情况中也有一些是假的，或者说这种重复只是表面性的。如果有两段看起来重复的代码，它们走的是不同的演进路径，也就是说它们有着不同的变更速率和变更缘由，那么这两段代码就不是真正的重复。等我们几年后再回过头来看，可能就会发现这两段代码是非常不一样的了。

Now imagine two use cases that have very similar screen structures. The architects will likely be strongly tempted to share the code for that structure. But should they? Is that true duplication? Or it is accidental?

> 现在，我们假设某系统中有两个用例在屏幕展现形式上非常类似。每当这种时候，架构师们就很可能非常想复用同一段代码来处理它们的屏幕展示。那么，我们到底是否应该这样做呢？这里是真正的重复，还只是一种表面性的重复？

Most likely it is accidental. As time goes by, the odds are that those two screens will diverge and eventually look very different. For this reason, care must be taken to avoid unifying them. Otherwise, separating them later will be a challenge.

> 恐怕这里很可能只是表面性的重复。随着时间推移，这两个用例的屏幕展示功能可能会各自演变，最终很可能完全不同。正是由于这样的原因，我们必须加倍小心地避免让这两个用例复用同一段代码，否则，未来再想将它们分开会面临很大的挑战。

When you are vertically separating use cases from one another, you will run into this issue, and your temptation will be to couple the use cases because they have similar screen structures, or similar algorithms, or similar database queries and/or schemas. Be careful. Resist the temptation to commit the sin of knee-jerk elimination of duplication. Make sure the duplication is real.

> 当我们按用例垂直切分系统时，这样的问题会经常出现。我们经常遇到一些不同的用例为了上述原因被耦合在了一起。不管是因为它们展现形式类似，还是使用了相似的语法、相似的数据库查询/表结构等，总之，我们一定要小心避免对任何重复都要立即消除的应激反应模式中。一定要确保这些消除动作只针对那些真正意义上的重复。

By the same token, when you are separating layers horizontally, you might notice that the data structure of a particular database record is very similar to the data structure of a particular screen view. You may be tempted to simply pass the database record up to the UI, rather than to create a view model that looks the same and copy the elements across. Be careful: This duplication is almost certainly accidental. Creating the separate view model is not a lot of effort, and it will help you keep the layers properly decoupled.

> 同样的道理，当我们对系统进行水平分层时，也可能会发现某个数据库记录的结构和某个屏幕展示的数据接口非常相似。我们可能也会为了避免再创建一个看起来相同的视图模型并在两者之间复制元素，而选择直接将数据库记录传递给 UI 层。我们也一定要小心，这里几乎肯定只是一种表面性的重复。而且，另外创建一个视图模型并不会花费太多力气，这可以帮助我们保持系统水平分层之间的隔离。

## DECOUPLING MODES (AGAIN) 再谈解耦模式

Back to modes. There are many ways to decouple layers and use cases. They can be decoupled at the source code level, at the binary code (deployment) level, and at the execution unit (service) level.

> 让我们再回到解耦模式的问题上来。按水平分层和用例解耦一个系统有很多种方式。例如，我们可以在源码层次上解耦、二进制层次上解耦（部署），也可以在执行单元层次上解耦（服务）。

- Source level. We can control the dependencies between source code modules so that changes to one module do not force changes or recompilation of others (e.g., Ruby Gems).

> - 源码层次：我们可以控制源代码模块之间的依赖关系，以此来实现一个模块的变更不会导致其他模块也需要变更或重新编译（例如 Ruby Gem）。

In this decoupling mode the components all execute in the same address space, and communicate with each other using simple function calls. There is a single executable loaded into computer memory. People often call this a monolithic structure.

> 在这种解耦模式下，系统所有的组件都会在同一个地址空间内执行，它们会通过简单的函数调用来进行彼此的交互。这类系统在运行时是作为一个执行文件被统一加载到计算机内存中的。人们经常把这种模式叫作单体结构。

- Deployment level. We can control the dependencies between deployable units such as jar files, DLLs, or shared libraries, so that changes to the source code in one module do not force others to be rebuilt and redeployed.

> - 部署层次：我们可以控制部署单元（譬如 jar 文件、DLL、共享库等）之间 的依赖关系，以此来实现一个模块的变更不会导致其他模块的重新构建和部署。

Many of the components may still live in the same address space, and communicate through function calls. Other components may live in other processes in the same processor, and communicate through interprocess communications, sockets, or shared memory. The important thing here is that the decoupled components are partitioned into independently deployable units such as jar files, Gem files, or DLLs.

> 在这种模式下，大部分组件可能还是依然运行在同一个地址空间内，通过彼此的函数调用通信。但有一些别的组件可能会运行在同一个处理器下的其他进程内，使用跨进程通信，或者通过 socket 或共享内存进行通信。这里最重要的是，这些组件的解耦产生出许多可独立部署的单元，例如 jar 文件、Gem 文件和 DLL 等。

- Service level. We can reduce the dependencies down to the level of data structures, and communicate solely through network packets such that every execution unit is entirely independent of source and binary changes to others (e.g., services or micro-services).

> - 服务层次：我们可以将组件间的依赖关系降低到数据结构级别’然后仅通过网络数据包来进行通信。这样系统的每个执行单元在源码层和二进制层都会是一个独立的个体，它们的变更不会影响其他地方（例如常见的服务或微服务就都是如此的）。

What is the best mode to use?

> 现在，我们要问的是究竟哪个模式是最好的呢？

The answer is that it’s hard to know which mode is best during the early phases of a project. Indeed, as the project matures, the optimal mode may change.

> 答案是，在项目早期很难知道哪种模式是最好的。事实上，随着项目的逐渐成熟，最好的模式可能会发生变化。

For example, it’s not difficult to imagine that a system that runs comfortably on one server right now might grow to the point where some of its components ought to run on separate servers. While the system runs on a single server, the source-level decoupling might be sufficient. Later, however, it might require decoupling into deployable units, or even services.

> 例如，我们不难想象，一个在某台服务器上运行良好的程序发展到一定程度，可能就会需要将其某些组件迁移到其他服务器上才能满足运行要求。当该系统只运行在一台服务器上时，我们进行源码层次的解耦就已经足够了。但在这之后，我们可能需要进行部署单元层次的解耦，甚至服务层次的解耦。

One solution (which seems to be popular at the moment) is to simply decouple at the service level by default. A problem with this approach is that it is expensive and encourages coarse-grained decoupling. No matter how “micro” the micro-services get, the decoupling is not likely to be fine-grained enough.

> 另一个解决方案（似乎也是目前最流行的方案）是，默认就采用服务层次的解耦。这种做法的问题主要在于它的成本很高，并且是在鼓励粗粒度的解耦。毕竟，无论微服务有多么“微”，其解耦的精细度都可能是不够的。

Another problem with service-level decoupling is that it is expensive, both in development time and in system resources. Dealing with service boundaries where none are needed is a waste of effort, memory, and cycles. And, yes, I know that the last two are cheap—but the first is not.

> 服务层次解耦的另一个问题是不仅系统资源成本高昂，而且研发成本更高。处理服务边界不仅非常耗费内存、处理器资源，而且更耗费人力。虽然内存和处理器越来越便宜，但是人力成本可一直都很高。

My preference is to push the decoupling to the point where a service could be formed. should it become necessary; but then to leave the components in the same address space as long as possible. This leaves the option for a service open.

> 通常，我会倾向于将系统的解耦推行到某种一旦有需要就可以随时转变为服务的程度即可，让整个程序尽量长时间地保持单体结构，以便给未来留下可选项。

With this approach, initially the components are separated at the source code level. That may be good enough for the duration of the project’s lifetime. If, however, deployment or development issues arise, driving some of the decoupling to a deployment level may be sufficient—at least for a while.

> 在这种方式下，系统最初的组件隔离措施都是做在源码层次上的，这样的解耦可能在整个项目的生命周期里已经足够了。然而，如果部署和开发方面有更高的需求出现，那么将某些组件解耦到部署单元层次就可能够了，起码能撑上一阵。

As the development, deployment, and operational issues increase, I carefully choose which deployable units to turn into services, and gradually shift the system in that direction.

> 当然，随着系统在开发、部署、运行各方面所面临问题持续增加，我们应该挑选一下可以将哪些可部署单元转化为服务，并且逐渐将系统向这个方向转变。

Over time, the operational needs of the system may decline. What once required decoupling at the service level may now require only deployment-level or even source-level decoupling.

> 而随着时间的流逝，系统的运维需求可能又会降低。之前需要进行服务层次解耦的系统可能现在只需要进行部署层次或源码层次的解耦就够了。

A good architecture will allow a system to be born as a monolith, deployed in a single file, but then to grow into a set of independently deployable units, and then all the way to independent services and/or micro-services. Later, as things change, it should allow for reversing that progression and sliding all the way back down into a monolith.

> 一个设计良好的架构应该允许一个系统从单体结构开始，以单一文件的形式部署，然后逐渐成长为一组相互独立的可部署单元，甚至是独立的服务或者微服务。最后还能随着情况的变化，允许系统逐渐回退到单体结构。

A good architecture protects the majority of the source code from those changes. It leaves the decoupling mode open as an option so that large deployments can use one mode, whereas small deployments can use another.

> 并且，一个设计良好的架构在上述过程中还应该能保护系统的大部分源码不受变更影响。对整个系统来说，解耦模式也应该是一个可选项。我们在进行大型部署时可以采用一种模式，而在进行小型部署时则可以釆用另一种模式。

## CONCLUSION 本章小结

Yes, this is tricky. And I’m not saying that the change of decoupling modes should be a trivial configuration option (though sometimes that is appropriate). What I’m saying is that the decoupling mode of a system is one of those things that is likely to change with time, and a good architect foresees and appropriately facilitates those changes.

> 是的，要达到上述要求难度不小。我并没有说系统的部署模式就一定要是某种简单的配置项（虽然在某些情况下的确应该这样做）。这里的主要观点认为，一个系统所适用的解耦模式可能会随着时间而变化，优秀的架构师应该能预见这一点，并且做岀相应的对策。
