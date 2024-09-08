# Chap19. POLICY AND LEVEL 策略与层次

![](https://ngte-superbed.oss-cn-beijing.aliyuncs.com/book/mono/un/CH-UN19.jpg)

Software systems are statements of policy. Indeed, at its core, that’s all a computer program actually is. A computer program is a detailed description of the policy by which inputs are transformed into outputs.

> 本质上，所有的软件都是一组策略语句的集合。是的，可以说计算机程序不过就是一组仔细描述如何将输入转化为输出的策略语句的集合。

In most nontrivial systems, that policy can be broken down into many different smaller statements of policy. Some of those statements will describe how particular business rules are to be calculated. Others will describe how certain reports are to be formatted. Still others will describe how input data are to be validated.

> 在大多数非小型系统（nontrivial system）中，整体业务策略通常都可以被拆解为多组更小的策略语句。一部分策略语句专门用于描述计算部分的业务逻辑，另一部分策略语句则负责描述计算报告的格式。除此之外，可能还会有一些用于描述如何校验输入数据的策略。

Part of the art of developing a software architecture is carefully separating those policies from one another, and regrouping them based on the ways that they change. Policies that change for the same reasons, and at the same times, are at the same level and belong together in the same component. Policies that change for different reasons, or at different times, are at different levels and should be separated into different components.

> 软件架构设计的工作重点之一就是，将这些策略彼此分离，然后将它们按照变更的方式进行重新分组。其中变更原因、时间和层次相同的策略应该被分到同一个组件中。反之，变更原因、时间和层次不同的策略则应该分属于不同的组件。

The art of architecture often involves forming the regrouped components into a directed acyclic graph. The nodes of the graph are the components that contain policies at the same level. The directed edges are the dependencies between those components. They connect components that are at different levels.

> 架构设计的工作常常需要将组件重排组合成为一个有向无环图。图中的每一个节点代表的是一个拥有相同层次策略的组件，每一条单向链接都代表了一种组件之间的依赖关系，它们将不同级别的组件链接起来。

Those dependencies are source code, compile-time dependencies. In Java, they are import statements. In C#, they are using statements. In Ruby, they are require statements. They are the dependencies that are necessary for the compiler to function.

> 这里提到的依赖关系是源码层次上的、编译期的依赖关系。这在 Java 语言中就是指 import 语句，在 C# 语言中就是指 using 语句，在 Ruby 语言中就是指 require 语句。这里的依赖关系都是在编译过程中所必需的。

In a good architecture, the direction of those dependencies is based on the level of the components that they connect. In every case, low-level components are designed so that they depend on high-level components.

> 在一个设计良好的架构中，依赖关系的方向通常取决于它们所关联的组件层次。一般来说，低层组件被设计为依赖于高层组件。

## LEVEL 层次（Level）

A strict definition of “level” is “the distance from the inputs and outputs.” The farther a policy is from both the inputs and the outputs of the system, the higher its level. The policies that manage input and output are the lowest-level policies in the system.

> 我们对“层次”是严格按照“输入与输出之间的距离”来定义的。也就是说，一条策略距离系统的输入/输出越远，它所属的层次就越高。而直接管理输入/输出的策略在系统中的层次是最低的。

The data flow diagram in Figure 19.1 depicts a simple encryption program that reads characters from an input device, translates the characters using a table, and then writes the translated characters to an output device. The data flows are shown as curved solid arrows. The properly designed source code dependencies are shown as straight dashed lines.

> 在图 19.1 中，我们看到的是一个简单加密程序的数据流向图，该程序从输入设备读取字符，然后用查表法转换这些字符'并将转换后的字符输出到输出设备。我们将图中数据的流向用弯曲实心箭头标识了出来，而对于经精妙设计过的源码中的依赖关系则使用直虚线来标识。

<Figures figure="19-1">A simple encryption program</Figures>

The Translate component is the highest-level component in this system because it is the component that is farthest from the inputs and outputs.1

> 在图 19.1 中，Translate 组件是这个系统中层次最高的组件，因为该组件距离系统输入/输出距离最远。

Note that the data flows and the source code dependencies do not always point in the same direction. This, again, is part of the art of software architecture. We want source code dependencies to be decoupled from data flow and coupled to level.

> 另外需要注意的是，图 19.1 中的数据流向和源码中的依赖关系并不总处于同一方向上。这也是软件架构设计工作的一部分。我们希望源码中的依赖关系与其数据流向脫钩，而与组件所在的层次挂钩。

It would be easy to create an incorrect architecture by writing the encryption program like this:

> 但我们很容易将这个加密程序写成下面这样，这就构成了一个不正确的架构：

```js
function encrypt() {
  while (true) writeChar(translate(readChar()));
}
```

This is incorrect architecture because the high-level encrypt function depends on the lower-level readChar and writeChar functions.

> 上面这个程序架构设计的错误在于，它让高层组件中的函数 encrypt() 依赖于低层组件中的函数 readChar() 与 writeChar()。

A better architecture for this system is shown in the class diagram in Figure 19.2. Note the dashed border surrounding the Encrypt class, and the CharWriter and CharReader interfaces. All dependencies crossing that border point inward. This unit is the highest-level element in the system.

> 更好的系统架构设计应如图 19.2 所示。请注意图 19.2 中被虚线框起来的 Encrypt 类及其两个接口 CharReader 和 CharWriter。所有的依赖关系都指向了边界内部。这一切都说明它是系统中最高层次的组件。

<Figures figure="19-2">Class diagram showing a better architecture for the system</Figures>

ConsoleReader and ConsoleWriter are shown here as classes. They are low level because they are close to the inputs and outputs.

> 在图 19.2 中，ConsoleReader 和 ConsoleWriter 都属于具体类。由于它们与输入/输出最近，因此属于低层组件。

Note how this structure decouples the high-level encryption policy from the lower-level input/output policies. This makes the encryption policy usable in a wide range of contexts. When changes are made to the input and output policies, they are not likely to affect the encryption policy.

> 另外应该注意的是，这个架构将高层的加密策略与低层的输入/输出策略解耦了。也就是说，当输入/输出部分的策略发生变更时，它们不太可能会影响加密部分的策略。

Recall that policies are grouped into components based on the way that they change. Policies that change for the same reasons and at the same times are grouped together by the SRP and CCP. Higher-level policies—those that are farthest from the inputs and outputs—tend to change less frequently, and for more important reasons, than lower-level policies. Lower-level policies—those that are closest to the inputs and outputs—tend to change frequently, and with more urgency, but for less important reasons.

> 正如之前提到的，我们应该根据策略发生变更的方式来将它们分成不同的组件。变更原因和变更时间相同的策略应在 SRP 和 CCP 这两个原则的指导下合并为同一组件。离输入/输出最远的策略——高层策略——一般变更没有那么频繁。即使发生变更，其原因也比低层策略所在的组件更重大。反之，低层策略则很有可能会频繁地进行一些小变更。

For example, even in the trivial example of the encryption program, it is far more likely that the IO devices will change than that the encryption algorithm will change. If the encryption algorithm does change, it will likely be for a more substantive reason than a change to one of the IO devices.

> 例如，即使在这个简单的加密程序中，加密算法发生变更的可能性也要远小于 IO 设备发生变更的可能性。如果加密算法真的要变更，也很有可能比 I/O 设备的变更更重大。

Keeping these policies separate, with all source code dependencies pointing in the direction of the higher-level policies, reduces the impact of change. Trivial but urgent changes at the lowest levels of the system have little or no impact on the higher, more important, levels.

> 通过将策略隔离，并让源码中的依赖方向都统一调整为指向高层策略，我们可以大幅度降低系统变更所带来的影响。因为一些针对系统低层组件的紧急小修改几乎不会影响系统中更高级、更重要的组件。

Another way to look at this issue is to note that lower-level components should be plugins to the higher-level components. The component diagram in Figure 19.3 shows this arrangement. The Encryption component knows nothing of the IODevices component; the IODevices component depends on the Encryption component.

> 从另一个角度来说，低层组件应该成为高层组件的插件。图 19.3 中的组件图展示了这种关系，我们可以看到 Encryption 组件对 IODevices 组件的情况一无所知，而 IODevices 组件则依赖于 Encryption 组件。

<Figures figure="19-3">Lower-level components should plug in to higher-level components</Figures>

## CONCLUSION 本章小结

At this point, this discussion of policies has involved a mixture of the Single Responsibility Principle, the Open-Closed Principle, the Common Closure Principle, the Dependency Inversion Principle, the Stable Dependencies Principle, and the Stable Abstractions Principle. Look back and see if you can identify where each principle was used, and why.

> 综上所述，本章针对策略的讨论涉及单一职责原则（SRP）、开闭原则（OCP）、共同闭包原则（CCP）、依赖反转原则（DIP）、稳定依赖原则（SDP）以及稳定抽象原则（SAP）。读者可以自行结合之前的内容来匹配每个原则所适用的场景以及背后的原因。
