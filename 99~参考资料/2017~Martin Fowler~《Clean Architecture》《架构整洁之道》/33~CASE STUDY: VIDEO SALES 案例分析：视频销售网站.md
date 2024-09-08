# Chap33. CASE STUDY: VIDEO SALES 案例分析：视频销售网站

![](https://ngte-superbed.oss-cn-beijing.aliyuncs.com/book/mono/un/CH-UN33.jpg)

Now it’s time to put these rules and thoughts about architecture together into a case study. This case study will be short and simple, yet will depict both the process a good architect uses and the decisions that such an architect makes.

> 现在是时候将所有的这些设计规则和架构理念整合起来了。下面，我们来做一次案财析，这个案例分析虽然很简短，但可以描述清楚一个优秀的系统架构师在设计过程和设计决策中应该如何行事。

## THE PRODUCT 产品

For this case study, I’ve chosen a product with which I am rather intimately familiar: the software for a website that sells videos. Of course, it is reminiscent of cleancoders.com, the site where I sell my software tutorial videos.

> 在这个案例分析中，我要讲的是一个我自己很熟悉的产品：线上收费视频网站。当然，这个有点像 `cleancoders.com`，我在这个网站上出售我的软件开发教程视频。

The basic idea is trivial. We have a batch of videos we want to sell. We sell them, on the web, to both individuals and businesses. Individuals can pay one price to stream the videos, and another, higher price to download those videos and own them permanently. Business licenses are streaming only, and are purchased in batches that allow quantity discounts.

> 这个案例的设计很简单，就是我们打算向一些个人或者企业提供一批收费的线上教学视频。个人用户既可以选择在线支付之后直接在线观看视频，也可以选择付一笔更高的费用将视频下载到本地，永久地拥有它们。而企业用户就只能在线播放，但他们可以选择批量购买，以此来获得一定折扣。

Individuals typically act as both the viewers and the purchasers. Businesses, in contrast, often have people who buy the videos that other people will watch.

> 个人用户通常既是购买者又是观看者。而企业用户则不同，他们购买视频通常是用来给其他人观看的。

Video authors need to supply their video files, written descriptions, and ancillary files with exams, problems, solutions, source code, and other materials.

> 视频作者需要负责上传视频文件、写简介，并且提供视频附带的一系列习题、课后作业、答案、源代码以及其他各类资料。

Administrators need to add new video series, add and delete videos to and from the series, and establish prices for various licenses.

> 管理员需要负责增加新的视频播放列表，往视频播放列表里添加和删除视频，并且为各种许可类型设置价格。

Our first step in determining the initial architecture of the system is to identify the actors and use cases.

> 系统架构设计中的第一步，是识别系统中的各种角色和用例。

## USE CASE ANALYSIS 用例分析

Figure 33.1 shows a typical use-case analysis.

> 下面，我们通过图 33.1 来示范一次典型的用例分析。

<Figures figure="33-1">A typical use-case analysis</Figures>

The four main actors are evident. According to the Single Responsibility Principle, these four actors will be the four primary sources of change for the system. Every time some new feature is added, or some existing feature is changed, that step will be taken to serve one of these actors. Therefore we want to partition the system such that a change to one actor does not affect any of the other actors.

> 如你所见，图中显然存在着四个角色。根据单一职责原则（SRP），这四个角色将成为系统变更的主要驱动力。每当添加新功能，或者修改现有功能时，我们所做的一切都是在为这些角色服务。所以我们希望能够对系统进行分区处理，避免其中一个角色的变更需求影响其他角色。

The use cases shown in Figure 33.1 are not a complete list. For example, you won’t find log-in or log-out use cases. The reason for this omission is simply to manage the size of the problem in this book. If I were to include all the different use cases, then this chapter would have to turn into a book in its own right.

> 另外，图 33.1 中的用例并不是一个完整的列表。例如，这里没有分析用于执行登录、注销的用例。省略它们的原因很简单，为了控制本书篇幅。如果列出所有的用例，这一章就会变成一本单独的书了。

Note the dashed use cases in the center of Figure 33.1. They are abstract1 use cases. An abstract use case is one that sets a general policy that another use case will flesh out. As you can see, the View Catalog as Viewer and View Catalog as Purchaser use cases both inherit from the View Catalog abstract use case.

> 读者应该注意到图 33.1 中还有一些用虚线框起来的用例。我们称之为抽象用例，它们通常用来负责设置通用策略，然后交由其他具体用例来使用。譬如在该图“查看目录”这个用例同时被“购买者查看目录”和“观看者查看目录”这两个用例所继承并实现。

On the one hand, it was not strictly necessary for me to create that abstraction. I could have left the abstract use case out of the diagram without compromising any of the features of the overall product. On the other hand, these two use cases are so similar that I thought it wise to recognize the similarity and find a way to unify it early in the analysis.

> 一方面来说，其实这种抽象并不是必需的。如果没有这一层拥象：整个产品并不会受到影响；但是另一方面来说，由于这两个用例十分相近，我认为以某种方式来将它们合并起来分析是很合理的。

## COMPONENT ARCHITECTURE 组件架构

Now that we know the actors and use cases, we can create a preliminary component architecture (Figure 33.2).

> 既然我们弄清楚了系统中的各种角色和用例，接下来就可以构造一个初步的组件架构图了（如图 33.2 所示）。

The double lines in the drawing represent architectural boundaries as usual. You can see the typical partitioning of views, presenters, interactors, and controllers. You can also see that I’ve broken each of those categories up by their corresponding actors.

> 在该图中，双实线代表了系统架构边界。可以看到这里将系统划分成视图、展示器、交互器以及控制器这几个组件，同时也按照对应的系统角色进行了分组。

Each of the components in Figure 33.2 represents a potential .jar file or .dll file. Each of those components will contain the views, presenters, interactors, and controllers that have been allocated to it.

> 图 33.2 中的每一个组件都对应着一个潜在的 .jar 文件或 .dll 文件。每一个组件都会包含归属于它的视图、展示器、交互器、控制器文件。

Note the special components for the Catalog View and the Catalog Presenter. This is how I dealt with the abstract View Catalog use case. I assume that those views and presenters will be coded into abstract classes within those components, and that the inheriting components will contain view and presenter classes that will inherit from those abstract classes.

> 值得注意的是，这里有两个特殊的组件：目录视图（Catalog View）和目录展示器（Catalog Presenter）。这就是我应对查看目录列表这个抽象用例的方法。我假设这些视图和展示器将会被编写为抽象类，而继承它们的组件将会包括它们的派生类。

<Figures figure="33-2">A preliminary component architecture</Figures>

Would I really break the system up into all these components, and deliver them as .jar or .dll files? Yes and no. I would certainly break the compile and build environment up this way, so that I could build independent deliverables like that. I would also reserve the right to combine all those deliverables into a smaller number of deliverables if necessary. For example, given the partitioning in Figure 33.2, it would be easy to combine them into five .jar files—one for views, presenters, interactors, controllers, and utilities, respectively. I could then independently deploy the components that are most likely to change independently of each other.

> 但问题是，我们真的需要将系统拆分成这么多组件，然后以 .jar 或 .dll 文件的形式一个个交付吗？是，又不全是。我们确实要按照组件将编译和构建环境分开，以便单独构建对应的组件。但我们仍然可以考虑将所有的交付单元组合起来交付，例如，根据图 33.2 中的分组，我们可以很简单地将它们交付为 5 个 .jar 文件——视图、展示器、交互器、控制器和工具类，这样就可以分别单独部署这些被修改的组件了。

Another possible grouping would be to put the views and presenters together into the same .jar file, and put the interactors, controllers, and utilities in their own .jar file. Still another, even more primitive, grouping would be to create two .jar files, with views and presenters in one file, and everything else in the other.

> 除此之外，还有另一种分组方式，就是将视图和展示器放在同一个 .jar 文件中，而将交互器、控制器以及工具类各自放在独立的 .jar 文件中。还有一种更简单的方式，就是将视图和展示器放在一个 .jar 文件中，而将其他所有的组件合并为另一个 .jar 文件。

Keeping these options open will allow us to adapt the way we deploy the system based on how the system changes over time.

> 随着系统的演进，我们可以根据系统变更来调整部署方式。

## DEPENDENCY MANAGEMENT 依赖关系管理

The flow of control in Figure 33.2 proceeds from right to left. Input occurs at the controllers, and that input is processed into a result by the interactors. The presenters then format the results, and the views display those presentations.

> 如你所见，图 33.2 中的控制流是从右向左的。输入发生在控制器端，然后输入的数据经交互器处理后交由展示器格式化出结果，最后由视图来展示这个结果。

Notice that the arrows do not all flow from the right to the left. In fact, most of them point from left to right. This is because the architecture is following the Dependency Rule. All dependencies cross the boundary lines in one direction, and they always point toward the components containing the higher-level policy.

> 请注意，图中的箭头并不是一直从右向左的。事实上大部分的箭头都是从左向右的。这是因为该架构设计要遵守依赖关系原则。所有跨域边界的依赖关系都应该是同一个方向，而且都指向包含更高级策略的组件。

Also notice that the using relationships (open arrows) point with the flow of control, and that the inheritance relationships (closed arrows) point against the flow of control. This depicts our use of the Open–Closed Principle to make sure that the dependencies flow in the right direction, and that changes to low-level details do not ripple upward to affect high-level policies.

> 另外，还注意一下图中的“使用”关系（开放箭头），它和控制流方向是一致的：而"继承"关系（闭合箭头）则与之相反，它反映的是我们对开闭原则的应用，通过调整依赖关系，可以保证底层细节的变更不会影响到高层策略组件。

## CONCLUSION 本章小结

The architecture diagram in Figure 33.2 includes two dimensions of separation. The first is the separation of actors based on the Single Responsibility Principle; the second is the Dependency Rule. The goal of both is to separate components that change for different reasons, and at different rates. The different reasons correspond to the actors; the different rates correspond to the different levels of policy.

> 图 33.2 中的架构实现的是两个维度上的隔离。第一个是根据单一职责原则对所使用的系统的各个角色进行了隔离，第二个则是对依赖关系原则的应用。这两个维度的隔离都是为了将不同变更原因和不同变更速率的组件分隔开来。譬如变更的原因不同是因为组件使用的角色不同，而变更速率则取决于组件所在的层级。

Once you have structured the code this way, you can mix and match how you want to actually deploy the system. You can group the components into deployable deliverables in any way that makes sense, and easily change that grouping when conditions change.

> 按照这样的方式组织代码的结构，我们就可以在部署时做灵活的选择。可以随时将组件整合部署，也可以在要求变化的时候灵活地调整。
