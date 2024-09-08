# Chap24. PARTIAL BOUNDARIES 不完全边界

![](https://ngte-superbed.oss-cn-beijing.aliyuncs.com/book/mono/un/CH-UN24.jpg)

Full-fledged architectural boundaries are expensive. They require reciprocal polymorphic Boundary interfaces, Input and Output data structures, and all of the dependency management necessary to isolate the two sides into independently compilable and deployable components. That takes a lot of work. It’s also a lot of work to maintain.

> 构建完整的架构边界是一件很耗费成本的事。在这个过程中，需要为系统设计双向的多态边界接口，用于输入和输出的数据结构，以及所有相关的依赖关系管理，以便将系统分割成可独立编译与部署的组件。这里会涉及大量的前期工作以及大量的后期维护工作。

In many situations, a good architect might judge that the expense of such a boundary is too high—but might still want to hold a place for such a boundary in case it is needed later.

> 在很多情况下，一位优秀的架构师都会认为设计架构边界的成本太高了——但为了应对将来可能的需要，通常还是希望预留一个边界。

This kind of anticipatory design is often frowned upon by many in the Agile community as a violation of YAGNI: “You Aren’t Going to Need It.” Architects, however, sometimes look at the problem and think, “Yeah, but I might.” In that case, they may implement a partial boundary.

> 但这种预防性设计在敏捷社区里是饱受诟病的，因为它显然违背了 YAGNI 原则（"You Are’t Going to Need It”，意即“不要预测未来的需要”）。然而，架构师的工作本身就是要做这样的预见性设计，这时候，我们就需要引入不完全边界（partial boundary）的概念了。

## SKIP THE LAST STEP 省掉最后一步

One way to construct a partial boundary is to do all the work necessary to create independently compilable and deployable components, and then simply keep them together in the same component. The reciprocal interfaces are there, the input/output data structures are there, and everything is all set up—but we compile and deploy all of them as a single component.

> 构建不完全边界的一种方式就是在将系统分割成一系列可以独立编译、独立部署的组件之后，再把它们构建成一个组件。换句话说，在将系统中所有的接口、用于输入/输出的数据格式等每一件事都设置好之后，仍选择将它们统一编译和部署为一个组件。

Obviously, this kind of partial boundary requires the same amount of code and preparatory design work as a full boundary. However, it does not require the administration of multiple components. There’s no version number tracking or release management burden. That difference should not be taken lightly.

> 显然，这种不完全边界所需要的代码量以及设计的工作量，和设计完整边界时是完全一样的。但它省去了多组件管理这部分的工作，这就等于省去了版本号管理和发布管理方面的工作——这其中的工作量其实可不小。

This was the early strategy behind FitNesse. The web server component of FitNesse was designed to be separable from the wiki and testing part of FitNesse. The idea was that we might want to create other web-based applications by using that web component. At the same, we did not want users to have to download two components. Recall that one of our design goals was “download and go.” It was our intent that users would download one jar file and execute it without having to hunt for other jar files, work out version compatibilities, and so on.

> 这也是 FitNesse 项目早期所采取的策略。我们在设计 Web 服务器之初就将它设计为一个可以独立于 wiki 和测试部分的组件。该设计背后的想法是我们未来可能需要使用该 Web 组件来构建其他应用程序。但是同时，我们又不想让用户下载两个组件。正如我们之前所说，该项目其中的一个设计目标是实现让用户下载即可运行。我们希望用户只需下载一个 jar 文件就立即可以执行，不需要再去寻找其他的 jar 文件，更不需要操心版本兼容性等问题。

The story of FitNesse also points out one of the dangers of this approach. Over time, as it became clear that there would never be a need for a separate web component, the separation between the web component and the wiki component began to weaken. Dependencies started to cross the line in the wrong direction. Nowadays, it would be something of a chore to re-separate them.

> 在这里，FitNcssc 项目的故事也可以作为一个反例来说明这种设计的危害性。随着时间的推移，我们慢慢发现，将 Web 组件独立的需求越来越少，Wiki 组件与 Web 组件的隔离也弱化了。到如今，如果真想要再分离 Web 组件的话，会需要不少工作量。

## ONE-DIMENSIONAL BOUNDARIES 单向边界

The full-fledged architectural boundary uses reciprocal boundary interfaces to maintain isolation in both directions. Maintaining separation in both directions is expensive both in initial setup and in ongoing maintenance.

> 在设计一套完整的系统架构边界时，往往需要用反向接口来维护边界两侧组件的隔离性。而且，维护这种双向的隔离性，通常不会是一次性的工作，它需要我们持续地长期投入资源维护下去。

A simpler structure that serves to hold the place for later extension to a full-fledged boundary is shown in Figure 24.1. It exemplifies the traditional Strategy pattern. A ServiceBoundary interface is used by clients and implemented by ServiceImpl classes.

> 在图 24.1 中，你会看到一个临时占位的，将来可被替换成完整架构边界的更简单的结构。这个结构采用了传统的策略模式（strategy pattern）。如你所见，其 Client 使用的是一个由 ServiceImpl 类实现的 ServiceBoundary 接口。

<Figures figure="24-1">The Strategy pattern</Figures>

It should be clear that this sets the stage for a future architectural boundary. The necessary dependency inversion is in place in an attempt to isolate the Client from the ServiceImpl. It should also be clear that the separation can degrade pretty rapidly, as shown by the nasty dotted arrow in the diagram. Without reciprocal interfaces, nothing prevents this kind of backchannel other than the diligence and discipline of the developers and architects.

> 很明显，上述设计为未来构建完整的系统架构边界打下了坚实基础。为了未来将 Client 与 ServiceImpl 隔离，必要的依赖反转已经做完了。同时，我们也能清楚地看到，图中的虚线箭头代表了未来有可能很快就会出问题的隔离问题。由于没有采用双向反向接口，这部分就只能依赖开发者和架构师的自律性来保证组件持久隔离了。

## FACADES 门户模式

An even simpler boundary is the Facade pattern, illustrated in Figure 24.2. In this case, even the dependency inversion is sacrificed. The boundary is simply defined by the Facade class, which lists all the services as methods, and deploys the service calls to classes that the client is not supposed to access.

> 下面，我们再来看一个更简单的架构边界设计：采用门户模式（facade pattern），其架构如图 24.2 所示。在这种模式下，我们连依赖反转的工作都可以省了。这里的边界将只能由 Facade 类来定义，这个类的背后是一份包含了所有服务函数的列表，它会负责将 Client 的调用传递给对 client 不可见的服务函数。

<Figures figure="24-2">The Facade pattern</Figures>

Note, however, that the Client has a transitive dependency on all those service classes. In static languages, a change to the source code in one of the Service classes will force the Client to recompile. Also, you can imagine how easy backchannels are to create with this structure.

> 但需要注意的是，在该设计中 Client 会传递性地依赖于所有的 Service 类。在静态类型语言中，这就意味着对 Service 类的源码所做的任何修改都会导致 Client 的重新编译。另外，我们应该也能想象得到为这种结构建立反向通道是多容易的事。

## CONCLUSION 本章小结

We’ve seen three simple ways to partially implement an architectural boundary. There are, of course, many others. These three strategies are simply offered as examples.

> 在本章，我们介绍了三种不完全地实现架构边界的简单方法。当然，这类边界还有许多种其他实现方式，本章所介绍的这三种策略只为示范之用。

Each of these approaches has its own set of costs and benefits. Each is appropriate, in certain contexts, as a placeholder for an eventual full-fledged boundary. Each can also be degraded if that boundary never materializes.

> 每种实现方式都有相应的成本和收益。每种方式都有自己所适用的场景，它们可以被用来充当最终完整架构边界的临时替代品。同时，瞅这些边界最终被证明是没有必要存在的，那么也可以被自然降解。

It is one of the functions of an architect to decide where an architectural boundary might one day exist, and whether to fully or partially implement that boundary.

> 架构师的职责之一就是预判未来哪里有可能会需要设置架构边界，并决定应该以完全形式还是不完全形式来实现它们。
