# Chap27. SERVICES: GREAT AND SMALL 服务：宏观与微观

![](https://ngte-superbed.oss-cn-beijing.aliyuncs.com/book/mono/un/CH-UN27.jpg)

Service-oriented “architectures” and micro-service “architectures” have become very popular of late. The reasons for their current popularity include the following:

> 面向服务的“架构”以及微服务“架构”近年来非常流行，其中的原因如下：

- Services seem to be strongly decoupled from each other. As we shall see, this is only partially true.
- Services appear to support independence of development and deployment. Again, as we shall see, this is only partially true.

---

> - 服务之间似乎是强隔离的，但是下文我们会讲到，并不完全是这样。
> - 服务被认为是支持独立开发和部署的，同样，下文我们也会讲到，并不完全是这样。

## SERVICE ARCHITECTURE? 面向服务的架构

First, let’s consider the notion that using services, by their nature, is an architecture. This is patently untrue. The architecture of a system is defined by boundaries that separate high-level policy from low-level detail and follow the Dependency Rule. Services that simply separate application behaviors are little more than expensive function calls, and are not necessarily architecturally significant.

> 首先，我们来批判“只要使用了服务，就等于有了一套架构”这种思想。这显然是完全错误的。如前文所述，架构设计的任务就是找到高层策略与低层细节之间的架构边界，同时保证这些边界遵守依赖关系规则。所谓的服务本身只是一种比函数调用方式成本稍高的，分割应用程序行为的一种形式，与系统架构无关。

This is not to say that all services should be architecturally significant. There are often substantial benefits to creating services that separate functionality across processes and platforms—whether they obey the Dependency Rule or not. It’s just that services, in and of themselves, do not define an architecture.

> 当然，这里并不是说所有的服务都应该具有系统架构上的意义。有时候，用服务这种形式来隔离不同平台或进程中的程序行为这件事本身就很重要——不管它们是否遵守依赖关系规则。我们只是认为，服务本身并不能完全代表系统架构。

A helpful analogy is the organization of functions. The architecture of a monolithic or component-based system is defined by certain function calls that cross architectural boundaries and follow the Dependency Rule. Many other functions in those systems, however, simply separate one behavior from another and are not architecturally significant.

> 为了帮助读者理解上面所说的区别，我们用函数的组织形式来做个类比。不管是单体程序，还是多组件程序，系统架构都是由那些跨越架构边界的关键函数调用来定义的，并且整个架构必须遵守依赖关系规则。系统中许多其他的函数虽然也起到了隔离行为的效果，但它们显然并不具有架构意义。

So it is with services. Services are, after all, just function calls across process and/or platform boundaries. Some of those services are architecturally significant, and some aren’t. Our interest, in this chapter, is with the former.

> 服务的情况也一样，服务这种形式说到底不过是一种跨进程/平台边界的函数调用而己。有些服务会具有架构上的意义，有些则没有。我们这里重点要讨论的，当然是前者。

## SERVICE BENEFITS? 服务所带来的好处

The question mark in the preceding heading indicates that this section is going to challenge the current popular orthodoxy of service architecture. Let’s tackle the benefits one at a time.

> 我在本节的标题后面打了个问号，意味着我打算在这一节好好挑战一下目前流行的针对服务架构的崇拜情节。下面就让我们针对那些所谓的好处，一个一个地来批驳。

### THE DECOUPLING FALLACY 解耦合的谬论

One of the big supposed benefits of breaking a system up into services is that services are strongly decoupled from each other. After all, each service runs in a different process, or even a different processor; therefore those services do not have access to each other’s variables. What’s more, the interface of each service must be well defined.

> 很多人认为将系统拆分成服务的一个最忙要的好处就是让每个服务之间实现强解耦。毕竟，每个服务都是以一个不同的进程来运行的，甚至运行在不同处理器上。因此，服务之间通常不能访问彼此的变量。其外，服务之间的接口一定是充分定义的。

There is certainly some truth to this—but not very much truth. Yes, services are decoupled at the level of individual variables. However, they can still be coupled by shared resources within a processor, or on the network. What’s more, they are strongly coupled by the data they share.

> 从一定程度上来说，这是对的。确实，服务之间的确在变量层面做到了彼此隔离。然而，它们之间还是可能会因为处理器内的共享资源，或者通过网络共享资源而彼此耦合的。另外，任何形式的共享数据行为都会导致强耦合。

For example, if a new field is added to a data record that is passed between services, then every service that operates on the new field must be changed. The services must also strongly agree about the interpretation of the data in that field. Thus those services are strongly coupled to the data record and, therefore, indirectly coupled to each other.

> 例如，如果给服务之间传递的数据记录中增加了一个新字段，那么每个需要操作这个字段的服务都必须要做出相应的变更，服务之间必须对这条数据的解读达成一致。因此其实这些服务全部是强耦合于这条数据结构的，因此它们是间接彼此耦合的。

As for interfaces being well defined, that’s certainly true—but it is no less true for functions. Service interfaces are no more formal, no more rigorous, and no better defined than function interfaces. Clearly, then, this benefit is something of an illusion.

> 再来说说服务能很好地定义接口——它确实能很好地定义接口——但函数也能做到这一点。事实上，服务的接口与普通的函数接口相比，并没有比后者更正式、更严谨，也没有更好，这一点根本算不上什么好处。

### THE FALLACY OF INDEPENDENT DEVELOPMENT AND DEPLOYMENT 独立开发部署的谬论

Another of the supposed benefits of services is that they can be owned and operated by a dedicated team. That team can be responsible for writing, maintaining, and operating the service as part of a dev-ops strategy. This independence of development and deployment is presumed to be scalable. It is believed that large enterprise systems can be created from dozens, hundreds, or even thousands of independently developable and deployable services. Development, maintenance, and operation of the system can be partitioned between a similar number of independent teams.

> 人们认为的另一个使用服务的好处就是，不同的服务可以由不同的专门团队负责和运维。这让开发团队可以釆用 dev-ops 混合的形式来编写、维护以及运维各自的服务，这种开发和部署上的独立性被认为是可扩展的。这种观点认为大型系统可以由几十个、几百个、甚至几千个独立开发部署的服务组成。整个系统的研发、维护以及运维工作就可以由同等量级的团队来共同完成。

There is some truth to this belief—but only some. First, history has shown that large enterprise systems can be built from monoliths and component-based systems as well as service-based systems. Thus services are not the only option for building scalable systems.

> 这种理念有一些道理——但也仅仅是一些而已。首先，无数历史事实证明，大型系统一样可以釆用单体模式，或打组件模式来构建，不一定非得服务化。因此服务化并不是构建大型系统的唯一选择。

Second, the decoupling fallacy means that services cannot always be independently developed, deployed, and operated. To the extent that they are coupled by data or behavior, the development, deployment, and operation must be coordinated.

> 其次，上文说到的解耦合谬论已经说明拆分服务并不意味着这些服务可以彼此独立开发、部署和运维。如果这些服务之间以数据形式或者行为形式相耦合，那么它们的开发、部署和运维也必须彼此协调来进行。

## THE KITTY PROBLEM 运送猫咪的难题

As an example of these two fallacies, let’s look at our taxi aggregator system again. Remember, this system knows about many taxi providers in a given city, and allows customers to order rides. Let’s assume that the customers select taxis based on a number of criteria, such as pickup time, cost, luxury, and driver experience.

> 下面，我们再以之前那个出租车调度系统为例来说明上面那两个谬论。各位还记得吗？该系统会负责统一调度给定城市中的多个出租车提供商，而用户可以集中在它那里下订单。在这里，我们假设用户在租车时往往会附带一组参考条件，例如接送时间、价格、豪华程度、司机的经验，等等。

We wanted our system to be scalable, so we chose to build it out of lots of little micro-services. We subdivided our development staff into many small teams, each of which is responsible for developing, maintaining, and operating a correspondingly1 small number of services.

> 我们希望整个系统是可扩展的，于是该系统大量采用了微服务架构。然后，我们进一步将整个研发团队划分为许多个小团队，每个团队都负责开发、维护和运维相应的小数量的微服务。

The diagram in Figure 27.1 shows how our fictitious architects arranged services to implement this application. The TaxiUI service deals with the customers, who use mobile devices to order taxis. The TaxiFinder service examines the inventories of the various TaxiSuppliers and determines which taxies are possible candidates for the user. It deposits these into a short-term data record attached to that user. The TaxiSelector service takes the user’s criteria of cost, time, luxury, and so forth, and chooses an appropriate taxi from among the candidates. It hands that taxi off to the TaxiDispatcher service, which orders the appropriate taxi.

> 这个虚构系统的架构如图 27.1 所示，整个系统都是依靠服务来构建的。TaxiUI 服务负责与用户打交道，用户会通过移动设备向它下订单。TaxiFinder 服务负责调用不同的 TaxiSupplier 服务来获取可用车辆的信息，并且找出可用的出租车以作为可推荐项。这些可推荐项会短期地被固化成一条数据记录，与用户信息挂钩。TaxiSelector 服务则负责根据用户所选择的价格、时间、豪华程度等条件从可选项中筛选结果，最后这些结果会被传递给 TaxiDispatcher 服务，由它负责分派订单。

<Figures figure="27-1">Services arranged to implement the taxi aggregator system</Figures>

Now let us suppose that this system has been in operation for more than a year. Our staff of developers have been happily developing new features while maintaining and operating all these services.

> 现在，假设我们的系统已经运行了一年有余，其研发团队在持续开发新功能的同时，维护着所有的服务。

One bright and cheerful day, the marketing department holds a meeting with the development team. In this meeting, they announce their plans to offer a kitten delivery service to the city. Users can order kittens to be delivered to their homes or to their places of business.

> 在一个阳光明媚的早上，市场部召集整个研发部开会。会议上，市场部宣布了他们在该城市升展猫咪送达服务的计划。该计划将允许用户向系统下订单，要求将他们的猫咪送到自己家里或者办公室。

The company will set up several kitten collection points across the city. When a kitten order is placed, a nearby taxi will be selected to collect a kitten from one of those collection points, and then deliver it to the appropriate address.

> 公司的计划是在城市中建立几个猫咪集散点。当用户下订单时，附近的一辆出租车将被选中去集散点取猫，并将猫送到指定地点。

One of the taxi suppliers has agreed to participate in this program. Others are likely to follow. Still others may decline.

> 现在已经有一家出租车公司参加了这项活动，未来可能还会有其他公司参与进来，但肯定也会有不参与的公司。

Of course, some drivers may be allergic to cats, so those drivers should never be selected for this service. Also, some customers will undoubtedly have similar allergies, so a vehicle that has been used to deliver kittens within the last 3 days should not be selected for customers who declare such allergies.

> 当然，由于有些司机会对猫过敏，所以系统还必须要避免选中这些人去运送猫咪。同样的，由于出租车的乘客中也会有对猫过敏的人，所以当他们叫车时，系统也必须避免指派过去三天内运送过猫咪的车。

Look at that diagram of services. How many of those services will have to change to implement this feature? All of them. Clearly, the development and deployment of the kitty feature will have to be very carefully coordinated.

> 现在根据上述需求再来看我们的系统架构图，数一数有多少个服务需要变更？答案是全部！显然，为了增加这个运送猫咪的功能，该系统所有的服务都需要做变更，而且这些服务之间还要彼此做好协调。

In other words, the services are all coupled, and cannot be independently developed, deployed, and maintained.

> 换句话说，这些服务事实上全都是强耦合的，并不能真正做到独立开发、部署和维护。

This is the problem with cross-cutting concerns. Every software system must face this problem, whether service oriented or not. Functional decompositions, of the kind depicted in the service diagram in Figure 27.1, are very vulnerable to new features that cut across all those functional behaviors.

> 这就是所谓的横跨型变更（cross-cutting concern）问题，它是所有软件系统都要面对的问题，无论服务化还是非服务化的。其中，图 27.1 所示的这种按功能划分服务的架构方式，在跨系统的功能变更时是最脆弱的。

## OBJECTS TO THE RESCUE 对象化是救星

How would we have solved this problem in a component-based architecture? Careful consideration of the SOLID design principles would have prompted us to create a set of classes that could be polymorphically extended to handle new features.

> 如果采用组件化的系统架构，如何解决这个难题呢？通过对 SOLID 设计原则的仔细考虑，我们应该一开始就设计一系列多态化的类，以应对将来新功能的扩展需要。

The diagram in Figure 27.2 shows the strategy. The classes in this diagram roughly correspond to the services shown in Figure 27.1. However, note the boundaries. Note also that the dependencies follow the Dependency Rule.

> 这种策略下的系统架构如图 27.2 所示，我们可以看到该图中的类与图 27.1 中的服务大致是相互对应的。然而，请读者注意这里设置了架构边界，并且遵守了依赖关系原则。

Much of the logic of the original services is preserved within the base classes of the object model. However, that portion of the logic that was specific to rides has been extracted into a Rides component. The new feature for kittens has been placed into a Kittens component. These two components override the abstract base classes in the original components using a pattern such as Template Method or Strategy.

> 现在，原先服务化设计中的大部分逻辑都被包含在对象模型的基类中。然而，针对每次特定行程的逻辑被抽离到一个单独的 Rides 组件中。运送猫咪的新功能被放入到 Kittens 组件中。这两个组件覆盖了原始组件中的抽象基类，这种设计模式被称作模板方法模式或策略模式。

Note again that the two new components, Rides and Kittens, follow the Dependency Rule. Note also that the classes that implement those features are created by factories under the control of the UI.

> 同时，我们也会注意到 Rides 和 Kittens 这两个新组件都遵守了依赖关系原则。另外，实现功能的类也都是由 UI 控制下的工厂类创建出来的。

Clearly, in this scheme, when the Kitty feature is implemented, the TaxiUI must change. But nothing else needs to be changed. Rather, a new jar file, or Gem, or DLL is added to the system and dynamically loaded at runtime.

> 显然，如果我们在这种架构下引入运送猫咪的功能，TaxiUI 组件就必须随之变更，但其他的组件就无须变更了。这里只需要引入一个新的 jar 文件或者 Gem、DLL。系统在运行时就会自动动态地加载它们。

Thus the Kitty feature is decoupled, and independently developable and deployable.

> 这样一来，运送猫咪的功能就与系统的其他部分实现了解耦，可以实现独立开发和部署了。

<Figures figure="27-2">Using an object-oriented approach to deal with cross-cutting concerns</Figures>

## COMPONENT-BASED SERVICES 基于组件的服务

The obvious question is: Can we do that for services? And the answer is, of course: Yes! Services do not need to be little monoliths. Services can, instead, be designed using the SOLID principles, and given a component structure so that new components can be added to them without changing the existing components within the service.

> 那么，问题来了：服务化也可以做到这一点吗？答案是肯定的。服务并不一定必须是小型的单体程序。服务也可以按照 SOLID 原则来设计，按照组件结构来部署，这样就可以做到在添加/删除组件时不影响服务中的其他组件。

Think of a service in Java as a set of abstract classes in one or more jar files. Think of each new feature or feature extension as another jar file that contains classes that extend the abstract classes in the first jar files. Deploying a new feature then becomes not a matter of redeploying the services, but rather a matter of simply adding the new jar files to the load paths of those services. In other words, adding new features conforms to the Open-Closed Principle.

> 我们可以将 Java 中的服务看作是一个或多个 jar 文件中的一组抽象类，而每个新功能或功能扩展都是另一个 jar 文件中的类，它们都扩展了之前 jar 文件中的抽象类。这样一来，部署新功能就不再是部署服务了，而只是简单地在服务的加载路径下增加一个 jar 文件。换句话说，这种增加新功能的过程符合开闭原则（OCP）。

The service diagram in Figure 27.3 shows the structure. The services still exist as before, but each has its own internal component design, allowing new features to be added as new derivative classes. Those derivative classes live within their own components.

> 这种服务的架构如图 27.3 所示。我们可以看到，在该架构中服务仍然和之前一样，但是每个服务中都增加了内部组件结构，以便使用衍生类来添加新功能，而这些衍生类都有各自所生存的组件。

<Figures figure="27-3">Each service has its own internal component design, enabling new features to be added as new derivative classes</Figures>

## CROSS-CUTTING CONCERNS 横跨型变更

What we have learned is that architectural boundaries do not fall between services. Rather, those boundaries run through the services, dividing them into components.

> 现在我们应该已经明白了，系统的架构边界事实上并不落在服务之间，而是穿透所有服务，在服务内部以组件的形式存在。

To deal with the cross-cutting concerns that all significant systems face, services must be designed with internal component architectures that follow the Dependency Rule, as shown in the diagram in Figure 27.4. Those services do not define the architectural boundaries of the system; instead, the components within the services do.

> 为了处理这个所有大型系统都会遇到的横跨型变更问题，我们必须在服务内部采用遵守依赖关系原则的组件设计方式，如图 27.4 所示。总而言之，服务边界并不能代表系统的架构边界，服务内部的组件边界才是。

<Figures figure="27-4">Services must be designed with internal component architectures that follow the Dependency Rule</Figures>

## CONCLUSION 本章小结

As useful as services are to the scalability and develop-ability of a system, they are not, in and of themselves, architecturally significant elements. The architecture of a system is defined by the boundaries drawn within that system, and by the dependencies that cross those boundaries. That architecture is not defined by the physical mechanisms by which elements communicate and execute.

> 虽然服务化可能有助于提升系统的可扩展性和可研发性，但服务本身却并不能代表整个系统的架构设计。系统的架构是由系统内部的架构边界，以及边界之间的依赖关系所定义的，与系统中各组件之间的调用和通信方式无关。

A service might be a single component, completely surrounded by an architectural boundary. Alternatively, a service might be composed of several components separated by architectural boundaries. In rare2 cases, clients and services may be so coupled as to have no architectural significance whatever.

> 一个服务可能是一个独立组件，以系统架构边界的形式隔开。一个服务也可能由几个组件组成，其中的组件以架构边界的形式互相隔离。在极端情况下，客户端和服务端甚至可能会由于耦合得过于紧密而不具备系统架构意义上的隔离性。
