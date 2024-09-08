# Chap13. COMPONENT COHESION 组件聚合

![](https://ngte-superbed.oss-cn-beijing.aliyuncs.com/book/mono/un/CH-UN13.jpg)

Which classes belong in which components? This is an important decision, and requires guidance from good software engineering principles. Unfortunately, over the years, this decision has been made in an ad hoc manner based almost entirely on context.

> 那么，究竟是哪些类应该被组合成一个组件呢？这是一个非常重要的设计决策，应该遵循优秀的软件工程经验来行事。但不幸的是，很多年以来，我们对于这么重要的决策经常是根据当下面临的实际情况临时拍脑门决定的。

In this chapter we will discuss the three principles of component cohesion:

> 在本章中，我们会具体讨论以下三个与构建组件相关的基本原则：

- REP: The Reuse/Release Equivalence Principle
- CCP: The Common Closure Principle
- CRP: The Common Reuse Principle

---

> - REP：复用/发布等同原则。
> - CCP：共同闭包原则。
> - CRP：共同复用原则。

## THE REUSE/RELEASE EQUIVALENCE PRINCIPLE 复用/发布等同原则

The granule of reuse is the granule of release.

> 软件复用的最小粒度应等同于其发布的最小粒度。

The last decade has seen the rise of a menagerie of module management tools, such as Maven, Leiningen, and RVM. These tools have grown in importance because, during that time, a vast number of reusable components and component libraries have been created. We are now living in the age of software reuse—a fulfillment of one of the oldest promises of the object-oriented model.

> 过去十年间，模块管理工具得到了长足的发展，例如 Maven、Leiningen、RVM 等。这些工具日益重要的原因是正好在这十年间出现了大量可复用的组件和组件库应该说，我们现在至少已经实现了面向对象编程的一个原始初衷——软件复用。

The Reuse/Release Equivalence Principle (REP) is a principle that seems obvious, at least in hindsight. People who want to reuse software components cannot, and will not, do so unless those components are tracked through a release process and are given release numbers.

> REP 原则初看起来好像是不言自明的。毕竟如果想要复用某个软件组件的话，一般就必须要求该组件的开发由某种发布流程来驱动，并且有明确的发布版本号。

This is not simply because, without release numbers, there would be no way to ensure that all the reused components are compatible with each other. Rather, it also reflects the fact that software developers need to know when new releases are coming, and which changes those new releases will bring.

> 这其中的一个原因是，如果没有设定版本号，我们就没有办法保证所有被复用的组件之间能够彼此兼容。另外更重要的一点是，软件开发者必须要能够知道这些组件的发布时间，以及每次发布带来了哪些变更。

It is not uncommon for developers to be alerted about a new release and decide, based on the changes made in that release, to continue to use the old release instead. Therefore the release process must produce the appropriate notifications and release documentation so that users can make informed decisions about when and whether to integrate the new release.

> 只有这样，软件工程师才能在收到相关组件新版本发布的通知之后，依据该发布所变更的内容来决定是继续使用旧版本还是做些相应的升级，这是很基本的要求。因此，组件的发布过程还必须要能够产生适当的通知和发布文档，以便让它的用户根据这些信息做出有效的升级决策。

From a software design and architecture point of view, this principle means that the classes and modules that are formed into a component must belong to a cohesive group. The component cannot simply consist of a random hodgepodge of classes and modules; instead, there must be some overarching theme or purpose that those modules all share.

> 从软件设计和架构设汁的角度来看，REP 原则就是指组件中的类与模块必须是彼此紧密相关的。也就是说，一个组件不能由一组毫无关联的类和模块组成，它们之间应该有一个共同的主题或者大方向。

Of course, this should be obvious. However, there is another way to look at this issue that is perhaps not quite so obvious. Classes and modules that are grouped together into a component should be releasable together. The fact that they share the same version number and the same release tracking, and are included under the same release documentation, should make sense both to the author and to the users.

> 但从另外一个视角来看，这个原则就没那么简单了。因为根据该原则，一个组件中包含的类与模块还应该是可以同时发布的。这意味着它们共享相同的版本号与版本跟踪，并且包含在相同的发行文档中，这些都应该同时对该组件的作者和用户有意义。

This is weak advice: Saying that something should “make sense” is just a way of waving your hands in the air and trying to sound authoritative. The advice is weak because it is hard to precisely explain the glue that holds the classes and modules together into a single component. Weak though the advice may be, the principle itself is important, because violations are easy to detect—they don’t “make sense.” If you violate the REP, your users will know, and they won’t be impressed with your architectural skills.

> 这层建议听起来就比较薄弱了，毕竟说某项事情的安排应该“合理”的确有点假大空，不着实际。该建议薄弱的原因是它没有清晰地定义出到底应该如何将类与模块组合成组件。但即使这样，REP 原则的重要性也是毋庸置疑的，因为违反这个原则的后果事实上很明显——一定会有人抱怨你的安排“不合理”，并进而对你的软件架构能力产生怀疑。

The weakness of this principle is more than compensated for by the strength of the next two principles. Indeed, the CCP and the CRP strongly define this principle, but in a negative sense.

> 而且，REP 原则的上述薄弱性也会由以下两个原则所补充。CCP 和 CRP 会从相反的角度对这个原则进行有力的补偿。

## THE COMMON CLOSURE PRINCIPLE 共同闭包原则

Gather into components those classes that change for the same reasons and at the same times. Separate into different components those classes that change at different times and for different reasons.

> 我们应该将那些会同时修改，并且为相同目的而修改的类放到同一个组件中，而将不会同时修改，并且不会为了相同目的而修改的那些类放到不同的组件中。

This is the Single Responsibility Principle restated for components. Just as the SRP says that a class should not contain multiples reasons to change, so the Common Closure Principle (CCP) says that a component should not have multiple reasons to change.

> 这其实是 SRP 原则在组件层面上的再度阐述。正如 SRP 原则中提到的“一个类不应该同时存在着多个变更原因”一样，CCP 原则也认为一个组件不应该同时存在着多个变更原因。

For most applications, maintainability is more important than reusability. If the code in an application must change, you would rather that all of the changes occur in one component, rather than being distributed across many components.1 If changes are confined to a single component, then we need to redeploy only the one changed component. Other components that don’t depend on the changed component do not need to be revalidated or redeployed.

> 对大部分应用程序来说，可维护性的重要性要远远高于可复用性。如果某程序中的代码必须要进行某些变更，那么这些变更最好都体现在同一个组件中，而不是分布于很多个组件中打因为如果这些变更都集中在同一个组件中，我们就只需要重新部署该组件，其他组件则不需要被重新验证、重新部署了。

The CCP prompts us to gather together in one place all the classes that are likely to change for the same reasons. If two classes are so tightly bound, either physically or conceptually, that they always change together, then they belong in the same component. This minimizes the workload related to releasing, revalidating, and redeploying the software.

> 总而言之，CCP 的主要作用就是提示我们要将所有可能会被一起修改的类集中在一处。也就是说，如果两个类紧密相关，不管是源代码层面还是抽象理念层面，永远都会被一起修改，那么它们就应该被归属为同一个组件。通过遵守这个原则，我们就可以有效地降低因软件发布、验证及部署所带来的工作压力。

This principle is closely associated with the Open Closed Principle (OCP). Indeed, it is “closure” in the OCP sense of the word that the CCP addresses. The OCP states that classes should be closed for modification but open for extension. Because 100% closure is not attainable, closure must be strategic. We design our classes such that they are closed to the most common kinds of changes that we expect or have experienced.

> 另外 CCP 原则和开闭原则（OCP）也是紧密相关的。CCP 讨论的就是 OCP 中所指的“闭包”。OCP 原则认为一个类应该便于扩展，而抗拒修改。由于 100% 的闭包是不可能的，所以我们只能战略性地选择闭包范围。在设计类的时候，我们需要根据历史经验和预测能力，尽可能地将需要被一同变更的那些点聚合在一起。

The CCP amplifies this lesson by gathering together into the same component those classes that are closed to the same types of changes. Thus, when a change in requirements comes along, that change has a good chance of being restricted to a minimal number of components.

> 对于 CCP，我们还可以在此基础上做进一步的延伸，即可以将某一类变更所涉及的所有类尽量聚合在一处。这样当此类变更出现时，我们就可以最大限度地做到使该类变更只影响到有限的相关组件。

### SIMILARITY WITH SRP 与 SRP 原则的相似点

As stated earlier, the CCP is the component form of the SRP. The SRP tells us to separate methods into different classes, if they change for different reasons. The CCP tells us to separate classes into different components, if they change for different reasons. Both principles can be summarized by the following sound bite:

> 如前所述，CCP 原则实际上就是 SRP 原则的组件版。在 SRP 原则的指导下，我们将会把变更原因不同的函数放入不同的类中。而 CCP 原则指导我们应该将变更原因不同的类放入不同的组件中。简而言之，这两个原则都可以用以下一句简短的话来概括：

Gather together those things that change at the same times and for the same reasons. Separate those things that change at different times or for different reasons.

> 将由于相同原因而修改，并且需要同时修改的东西放在一起，将由于不同原因而修改，并且不同时修改的东西分开。

## THE COMMON REUSE PRINCIPLE 共同复用原则

Don’t force users of a component to depend on things they don’t need.

> 不要强迫一个组件的用户依赖他们不需要的东西。

The Common Reuse Principle (CRP) is yet another principle that helps us to decide which classes and modules should be placed into a component. It states that classes and modules that tend to be reused together belong in the same component.

> 共同复用原则（CRP）是另外一个帮助我们决策类和模块归属于哪一个组件的原则。该原则建议我们将经常共同复用的类和模块放在同一个组件中。

Classes are seldom reused in isolation. More typically, reusable classes collaborate with other classes that are part of the reusable abstraction. The CRP states that these classes belong together in the same component. In such a component we would expect to see classes that have lots of dependencies on each other.

> 通常情况下，类很少会被单独复用。更常见的情况是多个类同时作为某个可复用的抽象定义被共同复用。CRP 原则指导我们将这些类放在同一个组件中，而在这样的组件中，我们应该预见到会存在着许多互相依赖的类。

A simple example might be a container class and its associated iterators. These classes are reused together because they are tightly coupled to each other. Thus they ought to be in the same component.

> 一个简单的例子就是容器类与其相关的遍历器类，这些类之间通常是紧密相关的，一般会被共同复用，因此应该被放置在同一个组件中。

But the CRP tells us more than just which classes to put together into a component: It also tells us which classes not to keep together in a component. When one component uses another, a dependency is created between the components. Perhaps the using component uses only one class within the used component—but that still doesn’t weaken the dependency. The using component still depends on the used component.

> 但是 CRP 的作用不仅是告诉我们应该将哪些类放在一起，更重要的是要告诉我们应该将哪些类分开。因为每当一个组件引用了另一个组件时，就等于增加了一条依赖关系。虽然这个引用关系仅涉及被引用组件中的一个类，但它所带来的依赖关系丝毫没有减弱。也就是说，引用组件已然依赖于被引用组件了。

Because of that dependency, every time the used component is changed, the using component will likely need corresponding changes. Even if no changes are necessary to the using component, it will likely still need to be recompiled, revalidated, and redeployed. This is true even if the using component doesn’t care about the change made in the used component.

> 由于这种依赖关系的存在，每当被引用组件发生变更时，引用它的组件一般也需要做出相应的变更。即使它们不需要进行代码级的变更，一般也免不了需要被重新编译、验证和部署。哪怕引用组件根本不关心被引用组件中的变更，也要如此。

Thus when we depend on a component, we want to make sure we depend on every class in that component. Put another way, we want to make sure that the classes that we put into a component are inseparable—that it is impossible to depend on some and not on the others. Otherwise, we will be redeploying more components than is necessary, and wasting significant effort.

> 因此，当我们决定要依赖某个组件时，最好是实际需要依赖该组件中的每个类。换句话说，我们希望组件中的所有类是不能拆分的，即不应该出现别人只需要依赖它的某几个类而不需要其他类的情况。否则，我们后续就会浪费不少时间与精力来做不必要的组件部署。

Therefore the CRP tells us more about which classes shouldn’t be together than about which classes should be together. The CRP says that classes that are not tightly bound to each other should not be in the same component.

> 因此在 CRP 原则中，关于哪些类不应该被放在一起的建议是其更为重要的内容。简而言之，CRP 原则实际上是在指导我们：不是紧密相连的类不应该被放在同一个组件里。

### RELATION TO ISP 与 ISP 原则的关系

The CRP is the generic version of the ISP. The ISP advises us not to depend on classes that have methods we don’t use. The CRP advises us not to depend on components that have classes we don’t use.

> CRP 原则实际上是 LSP 原则的一个普适版。ISP 原则是建议我们不要依赖带有不需要的函数的类，而 CRP 原则则是建议我们不要依赖带有不需要的类的组件。不要依赖不需要用到的东西。

All of this advice can be reduced to a single sound bite:

Don’t depend on things you don’t need.

## THE TENSION DIAGRAM FOR COMPONENT COHESION 组件聚合张力图

You may have already realized that the three cohesion principles tend to fight each other. The REP and CCP are inclusive principles: Both tend to make components larger. The CRP is an exclusive principle, driving components to be smaller. It is the tension between these principles that good architects seek to resolve.

> 读到这里，读者可能已经意识到上述三个原则之间彼此存在着竞争关系。REP 和 CCP 原则是黏合性原则，它们会让组件变得更大，而 CRP 原则是排除性原则，它会尽量让组件变小。软件架构师的任务就是要在这三个原则中间进行取舍。

Figure 13.1 is a tension diagram2 that shows how the three principles of cohesion interact with each other. The edges of the diagram describe the cost of abandoning the principle on the opposite vertex.

> 下面我们来看一下图 13.1。这是一张组件聚合三大原则的张力图，图的边线所描述的是忽视对应原则的后果。

<Figures figure="13-1">Cohesion principles tension diagram</Figures>

An architect who focuses on just the REP and CRP will find that too many components are impacted when simple changes are made. In contrast, an architect who focuses too strongly on the CCP and REP will cause too many unneeded releases to be generated.

> 简而言之，只关注 REP 和 CRP 的软件架构师会发现，即使是简单的变更也会同时影响到许多组件。相反，如果软件架构师过于关注 CCP 和 REP，则会导致很多不必要的发布。

A good architect finds a position in that tension triangle that meets the current concerns of the development team, but is also aware that those concerns will change over time. For example, early in the development of a project, the CCP is much more important than the REP, because develop-ability is more important than reuse.

> 优秀的软件架构师应该能在上述三角张力区域中定位一个最适合目前研发团队状态的位置，同时也会根据时间不停调整。例如在项目早期，CCP 原则会比 REP 原则更重要，因为在这一阶段研发速度比复用性更重要。

Generally, projects tend to start on the right hand side of the triangle, where the only sacrifice is reuse. As the project matures, and other projects begin to draw from it, the project will slide over to the left. This means that the component structure of a project can vary with time and maturity. It has more to do with the way that project is developed and used, than with what the project actually does.

> 一般来说，一个软件项目的重心会从该三角区域的右侧开始，先期主要牺牲的是复用性。然后，随着项目逐渐成熟，其他项目会逐渐开始对其产生依赖，项目重心就会逐渐向该二角区域的左侧滑动。换句话说，一个项目在组件结构设计上的重心是根据该项目的开发时间和成熟度不断变动的，我们对组件结构的安排主要与项目开发的进度和它被使用的方式有关，与项目本身功能的关系其实很小。

## CONCLUSION 本章小结

In the past, our view of cohesion was much simpler than the REP, CCP, and CRP implied. We once thought that cohesion was simply the attribute that a module performs one, and only one, function. However, the three principles of component cohesion describe a much more complex variety of cohesion. In choosing the classes to group together into components, we must consider the opposing forces involved in reusability and develop-ability. Balancing these forces with the needs of the application is nontrivial. Moreover, the balance is almost always dynamic. That is, the partitioning that is appropriate today might not be appropriate next year. As a consequence, the composition of the components will likely jitter and evolve with time as the focus of the project changes from develop-ability to reusability.

> 过去，我们对组件在构建过程中要遵循的组合原则的理解要比 REP、CCP、CRP 这三个原则更有限。我们最初所理解的组合原则可能完全基于单一职责原则。然而，本章介绍的这三个原则为我们描述了一个更为复杂的决策过程。在决定将哪些类归为同一个组件时，必须要考虑到研发性与复用性之间的矛盾，并根据应用程序的需要来平衡这两个矛盾，这是一件很不容易的事。而且，这种平衡本身也在不断变化。也就是说，当下适用的分割方式可能明年就不再适用了。所以，组件的构成安排应随着项目重心的不同，以及研发性与复用性的不同而不断演化。
