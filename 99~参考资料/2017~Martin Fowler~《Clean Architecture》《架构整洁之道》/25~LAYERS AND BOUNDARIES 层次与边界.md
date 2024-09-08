# Chap25. LAYERS AND BOUNDARIES 层次与边界

![](https://ngte-superbed.oss-cn-beijing.aliyuncs.com/book/mono/un/CH-UN25.jpg)

It is easy to think of systems as being composed of three components: UI, business rules, and database. For some simple systems, this is sufficient. For most systems, though, the number of components is larger than that.

> 人们通常习惯于将系统分成三个组件：UI、业务逻辑和数据库。对于一些简单系统来说，的确可以这样，但稍复杂一些系统的组件就远不止三个了。

Consider, for example, a simple computer game. It is easy to imagine the three components. The UI handles all messages from the player to the game rules. The game rules store the state of the game in some kind of persistent data structure. But is that all there is?

> 以一个简单的计算机游戏为例。粗略看来，它好像也很符合三个组件的架构设定。首先，让 UI 负责接收用户输入的数据，并将数据传递给游戏的业务逻辑。然后，游戏的业务逻辑会将游戏状态保存在某种持久化数据结构中。但是，仅仅是这样而已吗？

## HUNT THE WUMPUS 基于文本的冒险游戏：Hunt The Wumpus

Let’s put some flesh on these bones. Let’s assume that the game is the venerable Hunt the Wumpus adventure game from 1972. This text-based game uses very simple commands like GO EAST and SHOOT WEST. The player enters a command, and the computer responds with what the player sees, smells, hears, and experiences. The player is hunting for a Wumpus in a system of caverns, and must avoid traps, pits, and other dangers lying in wait. If you are interested, the rules of the game are easy to find on the web.

> 现在让我们往上面的设想中加入一些细节。假设这个游戏是 1972 年风靡一时的基于文本的冒险游戏：Hunt the Wumpus 这个游戏的操作是通过一些像 GO EAST 和 SHOOT WEST 这样的简单文字命令来完成的。玩家在输入命令之后，计算机就会返回玩家角色所看到的、闻到的、听到的或体会到的事情。在这个游戏中，玩家会在一系列洞穴中追捕 Wumpus。玩家必须避开陷阱、陷坑以及其他一系列危险。如果有兴趣，在网上很容易找到该游戏的规则说明。

Let’s assume that we’ll keep the text-based UI, but decouple it from the game rules so that our version can use different languages in different markets. The game rules will communicate with the UI component using a language-independent API, and the UI will translate the API into the appropriate human language.

> 现在，假设我们决定保留这种基于文本的 UI，但是需要将 UI 与游戏业务逻辑之间的耦合解开，以便我们的游戏版本可以在不同地区使用不同的语言。也就是说，游戏的业务逻辑与 UI 之间应该用一种与自然语言无关的 API 来进行通信，而由 UI 负责将 API 传递进来的信息转换成合适的自然语言。

If the source code dependencies are properly managed, as shown in Figure 25.1, then any number of UI components can reuse the same game rules. The game rules do not know, nor do they care, which human language is being used.

> 如果我们能管理好源码中的依赖关系，就应该像图 25.1 所展示的那样，多个 UI 组件复用同一套游戏业务逻辑。而游戏的业务逻辑组件不知道，也不必知道 UI 正在使用哪一种自然语言。

<Figures figure="25-1">Any number of UI components can reuse the game rules</Figures>

Let’s also assume that the state of the game is maintained on some persistent store—perhaps in flash, or perhaps in the cloud, or maybe just in RAM. In any of those cases, we don’t want the game rules to know the details. So, again, we’ll create an API that the game rules can use to communicate with the data storage component.

> 同时，假设玩家在游戏中的状态会被保存在某种持久化存储介质中——有可能闪存，也有可能是某种云端存储，或只是本机内存。无论怎样，我们都并不希望游戏引擎了解这些细节。所以，我们仍然需要创建一个 API 来负责游戏的业务逻辑组件与数据存储组件之间的通信。

We don’t want the game rules to know anything about the different kinds of data storage, so the dependencies have to be properly directed following the Dependency Rule, as shown in Figure 25.2.

> 由于我们不会希望让游戏的业务逻辑依赖于不同种类的数据存储，所以这里的设计也要合理地遵守依赖关系原则，这样的话，该游戏的结构应如图 25.2 所示。

<Figures figure="25-2">Following the Dependency Rule</Figures>

## CLEAN ARCHITECTURE? 可否采用整洁架构

It should be clear that we could easily apply the clean architecture approach in this context,1 with all the use cases, boundaries, entities, and corresponding data structures. But have we really found all the significant architectural boundaries?

> 很显然，这里具备了采用整洁架构方法所需要的—切，包括用例、业务实体以及对应的数据结构都有了，但我们是否已经找到了所有相应的架构边界呢？

For example, language is not the only axis of change for the UI. We also might want to vary the mechanism by which we communicate the text. For example, we might want to use a normal shell window, or text messages, or a chat application. There are many different possibilities.

> 例如，语言并不是 UI 变更的唯一方向。我们可能还会需要变更文字输入/输出的方式。例如我们的输入/输出可以采用命令行窗口，或者用短信息，或者采用某种聊天程序。这里的可能性有很多。

That means that there is a potential architectural boundary defined by this axis of change. Perhaps we should construct an API that crosses that boundary and isolates the language from the communications mechanism; that idea is illustrated in Figure 25.3.

> 这就意味着这类变更应该有一个对应的架构边界。也许我们需要构造一个 API，以便将语言部分与通信部分隔开，这样一来，该设计的结构应如图 25.3 所示。

<Figures figure="25-3">The revised diagram</Figures>

The diagram in Figure 25.3 has gotten a little complicated, but should contain no surprises. The dashed outlines indicate abstract components that define an API that is implemented by the components above or below them. For example, the Language API is implemented by English and Spanish.

> 在图 25.3 中可以看到，现在系统的结构已经变得有点复杂了。在该图中，虚线框代表的是抽象组件，它们所定义的 API 通常要交由其上下层的组件来实现。例如 Language 部分的 API 是由 English 和 Spanish 这两个组件来实现的。

GameRules communicates with Language through an API that GameRules defines and Language implements. Language communicates with TextDelivery using an API that Language defines but TextDelivery implements. The API is defined and owned by the user, rather than by the implementer.

> 我们也可以看到 GameRules 与 Language 这两个组件之间的交互是通过一个由 GameRules 定义，并由 Language 实现的 API 来完成的。同样的，Language 与 TextDelivery 之间的交互也是通过由 Language 定义，并由 TextDelivery 实现的 API 来完成。这些 API 的定义和维护都是由使用方来负责的，而非实现方。

If we were to look inside GameRules, we would find polymorphic Boundary interfaces used by the code inside GameRules and implemented by the code inside the Language component. We would also find polymorphic Boundary interfaces used by Language and implemented by code inside GameRules.

> 如果我们进一步查看 GameRules 内部，就会发现 GameRules 组件的代码中使用的 Boundary 多态接口是由 Language 组件来实现的；同时还会发现 Language 组件使用的 Boundary 多态接口由 GameRules 代码实现。

If we were to look inside of Language, we would find the same thing: Polymorphic Boundary interfaces implemented by the code inside TextDelivery, and polymorphic Boundary interfaces used by TextDelivery and implemented by Language.

> 如果再探究一下 Language 组件，我们也会看到类似的情况：它的 Boundary 多态接口是在 TextDelivery 组件的代码中实现的，而 TextDelivery 使用的 Boundary 多态接口则由 Language 来实现。

In each case, the API defined by those Boundary interfaces is owned by the upstream component.

> 在所有这些场景中，由 Boundary 接口所定义的 API 都是由其使用者的上一层组件负责维护的。

The variations, such as English, SMS, and CloudData, are provided by polymorphic interfaces defined in the abstract API component, and implemented by the concrete components that serve them. For example, we would expect polymorphic interfaces defined in Language to be implemented by English and Spanish.

> 不同的具体实现类，例如 English、SMS、CloudData 都实现了由抽象的 API 组件所定义的多态接口。例如，Language 组件中定义的多态接口是由 English 和 Spanish 这两个组件来定义的。

We can simplify this diagram by eliminating all the variations and focusing on just the API components. Figure 25.4 shows this diagram.

> 我们可以去掉所有的具体实现类，只保留 API 组件来进一步简化上面这张设计图，其简化的结果如图 25.4 所示。

<Figures figure="25-4">Simplified diagram</Figures>

Notice that the diagram is oriented in Figure 25.4 so that all the arrows point up. This puts GameRules at the top. This orientation makes sense because GameRules is the component that contains the highest-level policies.

> 请注意图 25.4 中的朝向设计，所有的箭头都是朝上的。这样 GameRules 组件就被放在顶层的位置。这种朝向设计很好地反映了 GameRules 作为最高层策略组件的事买。

Consider the direction of information flow. All input comes from the user through the TextDelivery component at the bottom left. That information rises through the Language component, getting translated into commands to GameRules. GameRules processes the user input and sends appropriate data down to DataStorage at the lower right.

> 下面，我们来考虑一些信息流的方向。首先，所有来自用户的信息都会通过左下角的 TextDelivery 组件传入。当这些信息被上传到 Language 组件时，就会转换为具体的命令输入给 GameRules 组件。然后，GameRules 组件会负责处理用户的输入，并将数据发送给右下角的 DataStorage 组件。

GameRules then sends output back down to Language, which translates the API back to the appropriate language and then delivers that language to the user through TextDelivery.

> 接下来，GameRules 会将输出向下传递到 Language 组件，将其转成合适的语言并通过 TextDelivery 将该语言传递给用户。

This organization effectively divides the flow of data into two streams.2 The stream on the left is concerned with communicating with the user, and the stream on the right is concerned with data persistence. Both streams meet at the top3 at GameRules, which is the ultimate processor of the data that goes through both streams.

> 这种设计方式将数据流分成两路。左侧的数据流关注如何与用户通信，而右侧的数据流关注的是数据持久化。两条数据流在顶部的 GameRules 汇聚。GameRules 组件是所有数据的最终处理者。

## CROSSING THE STREAMS 交汇数据流

Are there always two data streams as in this example? No, not at all. Imagine that we would like to play Hunt the Wumpus on the net with multiple players. In this case, we would need a network component, like that shown in Figure 25.5. This organization divides the data flow into three streams, all controlled by the GameRules.

> 那么，这个例子中是否永远只有这两条数据流呢？当然不是。假设我们现在要在网络上与多个其他玩家一起玩这个游戏，就会需要一个网络组件，如图 25.5 所示。这样一来，我们有了三条数据流，它们都由 GameRules 组件所控制。

<Figures figure="25-5">Adding a network component</Figures>

So, as systems become more complex, the component structure may split into many such streams.

> 由此可见，随着系统的复杂化，组件在架构中自然会分裂出多条数据流来。

## SPLITTING THE STREAMS 数据流的分割

At this point you may be thinking that all the streams eventually meet at the top in a single component. If only life were so simple! The reality, of course, is much more complex.

> 此时你可能会认为所有的数据流最终都会汇聚到一个组件上。生活要是果真如此简单，那就真是太好了！现实情况往往不如人所愿啊。

Consider the GameRules component for Hunt the Wumpus. Part of the game rules deal with the mechanics of the map. They know how the caverns are connected, and which objects are located in each cavern. They know how to move the player from cavern to cavern, and how to determine the events that the player must deal with.

> 我们可以再来看一下 Hunt The Wumpu 这个游戏的 GameRules 组件。游戏的部分业务逻辑处理的是玩家在地图中的行走。这一部分需要知道游戏中的洞穴如何相连，每个洞穴中有什么物体存在，还要知道如何将玩家从一个洞穴移到另一个洞穴，以及如何触发各种需要玩家处理的事件。

But there is another set of policies at an even higher level—policies that know the health of the player, and the cost or benefit of a particular event. These policies could cause the player to gradually lose health, or to gain health by discovering food. The lower-level mechanics policy would declare events to this higher-level policy, such as FoundFood or FellInPit. The higher-level policy would then manage the state of the player (as shown in Figure 25.6). Eventually that policy would decide whether the player wins or loses.

> 但是，游戏中还有一组更高层次的策略——这些策略负责了解玩家的血量，以及每个事件的后果和影响。这些策略既可以让玩家逐渐损失血量，也可能由于发现事物而增加血量。总而言之，游戏的低层策略会负责向高层策略传递事件，例如 FoundFood 和 FellInPit 而高层组件则要管理玩家状态（如图 25.6 所示），最终该策略将会决定玩家在游戏中的输赢。

<Figures figure="25-6">The higher-level policy manages the player</Figures>

Is this an architectural boundary? Do we need an API that separates MoveManagement from PlayerManagement? Well, let’s make this a bit more interesting and add micro-services.

> 这些究竟是否属于架构边界呢？是否需要设计一个 API 来分割 MoveManagement 和 PlayerManagement 呢？在回答这些问题之前，让我们把问题弄得更有意思一点，再往里面加上微服务吧！

Let’s assume that we’ve got a massive multiplayer version of Hunt the Wumpus. MoveManagement is handled locally within the player’s computer, but PlayerManagement is handled by a server. PlayerManagement offers a micro-service API to all the connected MoveManagement components.

> 假设我们现在面对的是一个可以面向海量玩家的新版 Hurit The Wumpus 游戏。它的 MoveManagement 组合是由玩家的本地计算机来处理的 。而 PlayerManagement 组件则由服务端来处理。但 PlayerManagement 组件会为所有连接上它的 MoveManagement 组件提供一个微服务的 API。

The diagram in Figure 25.7 depicts this scenario in a somewhat abbreviated fashion. The Network elements are a bit more complex than depicted—but you can probably still get the idea. A full-fledged architectural boundary exists between MoveManagement and PlayerManagement in this case.

> 在图 25.7 中，我们为该游戏绘制了一个简化版的设计图。现实中的 Network 组件通常会比图中的更复杂一些 但这里的已经足够说明情况了。在图中，可以看到 MoveManagement PlayerManagement 之间存在一个完整的系统架构边界。

<Figures figure="25-7">Adding a micro-service API</Figures>

## CONCLUSION 本章小结

What does all this mean? Why have I taken this absurdly simply program, which could be implemented in 200 lines of Kornshell, and extrapolated it out with all these crazy architectural boundaries?

> 本章究竟想讨论什么呢？为什么要将一个极为简单的、在 Kornshell 中只需 200 行代码就能写完的小程序扩展成具有这些系统架构边界的复杂程序？

This example is intended to show that architectural boundaries exist everywhere. We, as architects, must be careful to recognize when they are needed. We also have to be aware that such boundaries, when fully implemented, are expensive. At the same time, we have to recognize that when such boundaries are ignored, they are very expensive to add in later—even in the presence of comprehensive test-suites and refactoring discipline.

> 我们设计这个例子的目的就是为了证明架构边界可以存在于任何地方。作为架构师，我们必须要小心审视究竟在什么地方才需要设计架构边界。另外，我们还必须弄清楚完全实现这些边界将会带来多大的成本。同时，我们也必须要了解如果事先忽略了这些边界，后续再添加会有多么困难——哪怕有覆盖广泛的测试，严加小心的重构也于事无补。

So what do we do, we architects? The answer is dissatisfying. On the one hand, some very smart people have told us, over the years, that we should not anticipate the need for abstraction. This is the philosophy of YAGNI: “You aren’t going to need it.” There is wisdom in this message, since over-engineering is often much worse than under-engineering. On the other hand, when you discover that you truly do need an architectural boundary where none exists, the costs and risks can be very high to add such a boundary.

> 所以作为架构师，我们应该怎么办？这个问题恐怕没有答案。一方面，就像一些很聪明的人多年来一直告诉我们的那样，不应该将未来的需求抽象化。这就是 YAGNI 原则：“You aren't going to need it”，臆想中的需求事实上住往是不存在的。这是一句饱含智慧的建议，因为过度的工程设计往往比工程设计不足还要糟糕。但另一方面，如果我们发现自己在某个位置确实需要设置架构边界，却又没有事先准备的时候，再添加边界所需要的成本和风险往往是很高的。

So there you have it. O Software Architect, you must see the future. You must guess—intelligently. You must weigh the costs and determine where the architectural boundaries lie, and which should be fully implemented, and which should be partially implemented, and which should be ignored.

> 现实就是这样。作为软件架构师，我们必须有一点未卜先知的能力。有时候要依靠猜测——当然还要用点脑子。软件架构师必须仔细权衡成本，决定哪里需要设计架构边界，以及这些地方需要的是完整的边界，还是不完全的边界，还是可以忽略的边界。

But this is not a one-time decision. You don’t simply decide at the start of a project which boundaries to implement and which to ignore. Rather, you watch. You pay attention as the system evolves. You note where boundaries may be required, and then carefully watch for the first inkling of friction because those boundaries don’t exist.

> 而且，这不是一次性的决定。我们不能在项目开始时就决定好哪里需要边界，哪里不需要。相反，架构师必须持续观察系统的演进，时刻注意哪里可能需要设计边界，然后仔细观察这些地方会由于不存在边界而出现哪些问题。

At that point, you weigh the costs of implementing those boundaries versus the cost of ignoring them—and you review that decision frequently. Your goal is to implement the boundaries right at the inflection point where the cost of implementing becomes less than the cost of ignoring.

> 当出现问题时，我们还需要权衡一下实现这个边界的成本，并拿它与不实现这个边界的成本对比——这种对比经常需要反复地进行。我们的目标是找到设置边界的优势超过其成本的拐点，那就是实现该边界的最佳时机。

It takes a watchful eye.

> 持之以恒，一刻也不能放松。
