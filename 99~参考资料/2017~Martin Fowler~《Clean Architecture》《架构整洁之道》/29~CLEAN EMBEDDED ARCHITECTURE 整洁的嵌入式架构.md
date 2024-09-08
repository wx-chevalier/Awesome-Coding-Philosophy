# Chap29. CLEAN EMBEDDED ARCHITECTURE 整洁的嵌入式架构

![](https://ngte-superbed.oss-cn-beijing.aliyuncs.com/book/mono/un/CH-UN29.jpg)

By James Grenning

A while ago I read an article entitled “The Growing Importance of Sustaining Software for the DoD”1 on Doug Schmidt’s blog. Doug made the following claim:

> 前一段时间，我在 Doug Schmidt 的个人博客上看到了一篇文章，标题是“The Growing Importance of Sustaining Software for the DoD”，Doug 在这篇文章中提出了以下观点：

“Although software does not wear out, firmware and hardware become obsolete, thereby requiring software modifications.”

> “虽然软件本身并不会随时间推移而磨损，但硬件及其固件却会随时间推移而过时，随即也需要对软件做相应改动。”

It was a clarifying moment for me. Doug mentioned two terms that I would have thought to be obvious—but maybe not. Software is this thing that can have a long useful life, but firmware will become obsolete as hardware evolves. If you have spent any time in embedded systems development, you know the hardware is continually evolving and being improved. At the same time, features are added to the new “software,” and it continually grows in complexity.

> 这句话对我有如醍醐灌顶。Doug 在这里用到了两个专业名词，我一直认为是显而易见的，但是其他人可能并没有这么觉得。其中，软件（software）应该是一种使用周期很长的东西，而固件（firmware）则会随着硬件演进而淘汰过时。曾经开发过嵌入式系统的人一定都知道，硬件系统是在持续不断地演进的。与此同时，随着新功能不断地增加，软件复杂度也在不断上升。

I’d like to add to Doug’s statement:

> 在这里，我想对 Dough 上面的那个观点做一点补充：

Although software does not wear out, it can be destroyed from within by unmanaged dependencies on firmware and hardware.

> “虽然软件质量本身并不会随时间推移而损耗，但是未妥善管理的硬件依赖和固件依赖却是软件的头号杀手。”

It is not uncommon for embedded software to be denied a potentially long life due to being infected with dependencies on hardware.

> 也就是说，本可以长期使用的嵌入式软件可能会由于其中隐含的硬件依赖关系而无法继续使用，这种情况是很常见的。

I like Doug’s definition of firmware, but let’s see which other definitions are out there. I found these alternatives:

> 我个人很喜欢 Doug 对固件所做的定义，但我们也可以来看一下其他人对固件的定义，以下是我目前所找到的一些说法：

- “Firmware is held in non-volatile memory devices such as ROM, EPROM, or flash memory.” (https://en.wikipedia.org/wiki/Firmware)
- “Firmware is a software program or set of instructions programmed on a hardware device.” (https://techterms.com/definition/firmware)
- “Firmware is software that is embedded in a piece of hardware.” (https://www.lifewire.com/what-is-firmware-2625881)
- Firmware is “Software (programs or data) that has been written onto read-only memory (ROM).” (http://www.webopedia.com/TERM/F/firmware.html)

---

> - “固件通常被存储在非可变内存设备，例如 ROM、EPROM 或者闪存中。”（https://en.wikipedia.org/wiki/Firmware）
> - “固件是直接编程在一个硬件设备上的一组指令或者一段程序。”（https://techterms.com/definition/firmware）
> - “固件是嵌入在一个硬件中的软件程序。”（https://www.lifewire.com/what-is-firmware-2625881）
> - “固件是被写入到只读内存设备中的（ROM）程序或数据。”（http://www.webopedia.com/TERM/F/firmware.html）

Doug’s statement makes me realize that these accepted definitions of firmware are wrong, or at least obsolete. Firmware does not mean code lives in ROM. It’s not firmware because of where it is stored; rather, it is firmware because of what it depends on and how hard it is to change as hardware evolves. Hardware does evolve (pause and look at your for phone for evidence), so we should structure our embedded code with that reality in mind.

> Doug 的这段观点表述让我意识到，大家普遍所认知的固件定义是错误的，或者至少是过时的。固件并不一定是存储在 ROM 中的代码。固件也不是依据其存储的位置来定义的，而是由其代码的依赖关系，及其随着硬件的演进在变更难度上的变化来定义的。硬件的演进是显而易见的（如果对此有任何疑问，请想一想你手中的手机），我们在架构嵌入式代码时要时刻记住这一点。

I have nothing against firmware, or firmware engineers (I’ve been known to write some firmware myself). But what we really need is less firmware and more software. Actually, I am disappointed that firmware engineers write so much firmware!

> 我并不反对固件，也不反对固件工程师（我自己也曾经写过固件）。但是我们真的应该少写点固件，而多写点软件。事实上，我最失望的是固件工程师竟然要写那么多固件程序！

Non-embedded engineers also write firmware! You non-embedded developers essentially write firmware whenever you bury SQL in your code or when you spread platform dependencies throughout your code. Android app developers write firmware when they don’t separate their business logic from the Android API.

> 还有，非嵌入式工程师竟然也要写固件程序！虽然你可能并不是嵌入式系统的开发者，但如果你在代码中嵌入了 SQL 或者是代码中引入了对某个平台的依赖的话，其实就是在写固件代码。譬如，Android 工程师在没有将业务逻辑与 Android API 分离之前，实际上也是在写固件代码。

I’ve been involved in a lot of efforts where the line between the product code (the software) and the code that interacts with the product’s hardware (the firmware) is fuzzy to the point of nonexistence. For example, in the late 1990s I had the fun of helping redesign a communications subsystem that was transitioning from time-division multiplexing (TDM) to voice over IP (VOIP). VOIP is how things are done now, but TDM was considered the state of the art from the 1950s and 1960s, and was widely deployed in the 1980s and 1990s.

> 我参与过很多软件项目，其中一些产品的功能代码（软件）与硬件支持代码（固件）的边界模糊得几乎不存在。例如，在 20 世纪 90 年代末，我有幸参与了一套通信系统的重新设计，将其从时分复用（TDM）模式迁移到 VOIP 模式。虽然 VOIP 如今已经是行业标准，但是从 20 世纪 50 年代到 60 年代，TDM 一直都是非常先进的技术，直到 20 世纪 80 年代和 90 年代它也被广泛部署在各种系统中。

Whenever we had a question for the systems engineer about how a call should react to a given situation, he would disappear and a little later emerge with a very detailed answer. “Where did he get that answer?” we asked. “From the current product’s code,” he’d answer. The tangled legacy code was the spec for the new product! The existing implementation had no separation between TDM and the business logic of making calls. The whole product was hardware/technology dependent from top to bottom and could not be untangled. The whole product had essentially become firmware.

> 每当我们向系统工程师提出一个产品问题——系统在某个情况下应该如何处理某通电话，这位系统工程师就会消失一段时间，然后给出一个非常具体的答案。我们问他是从哪里查到这个结果的？答案是“从当前的产品代码里！”这些复杂交错的老代码己经成为系统定义的一部分。该系统在实现过程中并没有区分 TDM 技术代码和拨打电话这样的业务逻辑代码。整个产品从头到尾都与具体技术、具体硬件息息相关，无法分割。可以说整个产品已经成为事实上的固件。

Consider another example: Command messages arrive to this system via serial port. Unsurprisingly, there is a message processor/dispatcher. The message processor knows the format of messages, is able to parse them, and can then dispatch the message to the code that can handle the request. None of this is surprising, except that the message processor/dispatcher resides in the same file as code that interacts with a UART hardware. The message processor is polluted with UART details. The message processor could have been software with a potentially long useful life, but instead it is firmware. The message processor is denied the opportunity to become software—and that is just not right!

> 再来看另外一个例子：我们都知道命令消息是通过串行端口传递给系统的。这自然就要有一个消息的处理器/分发器系统。其中，消息处理器得了解消息格式，可以解析消息，然后将消息分发给具体的处理代码。这些都很正常，但消息处理器分发器的代码和操作 UART 硬件的代码往往会被放存同一个文件中消息处理器的代码中常常充斥着与 UART 相关的实现细节。这样一来，本可以长时间使用的消息处理器代码变成了一段固件代码，这太不应该了！

I’ve known and understood the need for separating software from hardware for a long time, but Doug’s words clarified how to use the terms software and firmware in relationship to each other.

> 虽然我意识到要从使用意义上将软件与硬件、固件区分开来也已经有一段时间了，但是借助 Doug 对软件和固件的定义，我现在可以吧这件事情说得更明白一些。

For engineers and programmers, the message is clear: Stop writing so much firmware and give your code a chance at a long useful life. Of course, demanding it won’t make it so. Let’s look at how we can keep embedded software architecture clean to give the software a fighting chance of having a long and useful life.

> 对于程序员和工程师，我的意思很明确：不要再写固件代码了，让我们的代码活得更久点！当然，我们也不能总是空谈理念，下面就来看一下应该如何通过好的架构设计让嵌入式代码拥有更长的有效生命周期。

## APP-TITUDE TEST “程序适用测试”测试

Why does so much potential embedded software become firmware? It seems that most of the emphasis is on getting the embedded code to work, and not so much emphasis is placed on structuring it for a long useful life. Kent Beck describes three activities in building software (the quoted text is Kent’s words and the italics are my commentary):

> 为什么这么多嵌入式软件最后都成了固件？看起来，很可能是因为我们在做嵌入式设计时只关注代码能否顺利运行，并不太关心其结构能否撑起一个较长的有效生命周期。Kent Beck 描述了软件构建过程中的三个阶段（引号部分是他的原话，楷体部分是我的注解）：

1. “First make it work.” You are out of business if it doesn’t work.
2. “Then make it right.” Refactor the code so that you and others can understand it and evolve it as needs change or are better understood.
3. “Then make it fast.” Refactor the code for “needed” performance.

---

> 1. “先让代码工作起来”——如果代码不能工作，就不能产生价值。
> 2. “然后再试图将它变好——通过对代码进行重构，让我们自己和其他人更好地理解代码，并能按照需求不断地修改代码。
> 3. “最后再试着让它运行得更快”——按照性能提升的“需求”来重构代码。

Much of the embedded systems software that I see in the wild seems to have been written with “Make it work” in mind—and perhaps also with an obsession for the “Make it fast” goal, achieved by adding micro-optimizations at every opportunity. In The Mythical Man-Month, Fred Brooks suggests we “plan to throw one away.” Kent and Fred are giving virtually the same advice: Learn what works, then make a better solution.

> 我所见过的大部分“野生”的嵌入式代码，都只关注“先让它工作起来”这个目标——也许还有些团队会同时痴迷于“让它更快”这个目标，不放过任何一个机会加入各种微优化。在《人月神话》这本书中，Fred Brooks 建议我们应该随时准备“抛弃一个设计”。Kent 和 Fred 说的其实是同一件事：“在实践中学习正确的工作方法，然后再重写一个更好的版本”。

Embedded software is not special when it comes to these problems. Most non-embedded apps are built just to work, with little regard to making the code right for a long useful life.

> 这个建议对非嵌入式软件系统开发同样有用。毕竟目前大部分非嵌入式应用也仅仅停留在“可用”这个目标上，很少考虑为了长久使用而进行正确的设计。

Getting an app to work is what I call the App-titude test for a programmer. Programmers, embedded or not, who just concern themselves with getting their app to work are doing their products and employers a disservice. There is much more to programming than just getting an app to work.

> 对于程序员来说，让他的程序工作这件事只能被称为“程序适用测试（app-titude test）”。一个程序员，不论他写的是否是嵌入式程序，如果目标仅仅是让程序可以工作，恐怕对他的老板和这个程序本身而言都是一件坏事。毕竟，编程这件事可远不止是让程序可以工作这么简单。

As an example of code produced while passing the App-titude test, check out these functions located in one file of a small embedded system:

> 下面我们来示范一下可以通过“程序适用测试”的代码是什么样子的。先来看一个小型嵌入式系统中某个源文件中的一段函数声明：

```c
ISR(TIMER1_vect) { ... }
ISR(INT2_vect) { ... }
void btn_Handler(void) { ... }
float calc_RPM(void) { ... }
static char Read_RawData(void) { ... }
void Do_Average(void) { ... }
void Get_Next_Measurement(void) { ... }
void Zero_Sensor_1(void) { ... }
void Zero_Sensor_2(void) { ... }
void Dev_Control(char Activation) { ... }
char Load_FLASH_Setup(void) { ... }
void Save_FLASH_Setup(void) { ... }
void Store_DataSet(void) { ... }
float bytes2float(char bytes[4]) { ... }
void Recall_DataSet(void) { ... }
void Sensor_init(void) { ... }
void uC_Sleep(void) { ... }
```

That list of functions is in the order I found them in the source file. Now I’ll separate them and group them by concern:

> 可以看到该源文件中的函数是按一定顺序列出来的。现在我们要按照功能进行分组：

- Functions that have domain logic

> - 用于定义域逻辑（domain logic）的函数

```c
float calc_RPM(void) { ... }

void Do_Average(void) { ... }

void Get_Next_Measurement(void) { ... }

void Zero_Sensor_1(void) { ... }

void Zero_Sensor_2(void) { ... }
```

- Functions that set up the hardware platform

> - 用于设置硬件平台的函数

```c
ISR(TIMER1_vect) { ... }*

ISR(INT2_vect) { ... }

void uC_Sleep(void) { ... }

Functions that react to the on off button press

void btn_Handler(void) { ... }

void Dev_Control(char Activation) { ... }
```

A Function that can get A/D input readings from the hardware

```java
static char Read_RawData(void) { ... }
```

- Functions that store values to the persistent storage

> - 用于执行持久化存储的函数

```java
char Load_FLASH_Setup(void) { ... }

void Save_FLASH_Setup(void) { ... }

void Store_DataSet(void) { ... }

float bytes2float(char bytes[4]) { ... }

void Recall_DataSet(void) { ... }
```

- Function that does not do what its name implies

> - 功能与其名字不符的函数

```java
void Sensor_init(void) { ... }
```

Looking at some of the other files in this application, I found many impediments to understanding the code. I also found a file structure that implied that the only way to test any of this code is in the embedded target. Virtually every bit of this code knows it is in a special microprocessor architecture, using “extended” C constructs3 that tie the code to a particular tool chain and microprocessor. There is no way for this code to have a long useful life unless the product never needs to be moved to a different hardware environment.

> 在查看该应用程序其他源代码文件的过程中，我同样发现了许多理解上的障碍点。同时，我还发现这个项目的结构决定了该应用程序的所有代码只有在指定硬件平台上才能被测试。几乎代码的所有部分都知道它要运行在一个特殊的微处理器平台上，因为它们用的是“被扩展了的” C 结构，需要特殊的工具链和微处理器才能执行。除非这个产品永远不需要迁移到另一个硬件平台上，否则这段代码几乎不可能有长久的使用价值。

This application works: The engineer passed the App-titude test. But the application can’t be said to have a clean embedded architecture.

> 所以你看，这段代码的确能够正常工作：它的工程师也通过了“程序适用性测试”，但我们不能说该应用程序有一套整洁的嵌入式架构。

## THE TARGET-HARDWARE BOTTLENECK 目标硬件瓶颈

There are many special concerns that embedded developers have to deal with that non-embedded developers do not—for example, limited memory space, real-time constraints and deadlines, limited IO, unconventional user interfaces, and sensors and connections to the real world. Most of the time the hardware is concurrently developed with the software and firmware. As an engineer developing code for this kind of system, you may have no place to run the code. If that’s not bad enough, once you get the hardware, it is likely that the hardware will have its own defects, making software development progress even slower than usual.

> 嵌入式系统的程序员通常需要处理很多在写非嵌入式系统时不需要关心的事情——例如，有限的地址空间、实时性限制、运行截止时间、有限的 I/O 能力、非常规的用户接口、感应器，以及其他与物理世界的实际链接。大部分时候，这些系统的硬件是和它的软件、固件并行开发的。工程师在为这种系统编写代码的时候，往往没有任何地方可以运行。如果你认为这还不算糟糕的话，请想象一下，如果我们要等到真正拿到硬件时，才能了解代码在该硬件上存在着哪些意料之外的缺陷，这会在多大程度上拖慢我们的开发进度?

Yes, embedded is special. Embedded engineers are special. But embedded development is not so special that the principles in this book are not applicable to embedded systems.

> 是的，我们承认嵌入式系统的开发有其特殊性，嵌入式工程师的工作有其特殊性，但我们并不认为嵌入式开发特殊到本书所讲的原则都不适用的程度。

One of the special embedded problems is the target-hardware bottleneck. When embedded code is structured without applying clean architecture principles and practices, you will often face the scenario in which you can test your code only on the target. If the target is the only place where testing is possible, the target-hardware bottleneck will slow you down.

> 标硬件瓶颈（target-hardware bottleneck）是嵌入式开发所特有的一个问题，如果我们没有采用某种清晰的架构来设计嵌入式系统的代码结构，就经常会面临只能在目标系统平台上测试代码的难题。如果只能在特定的平台上测试代码，那么这一定会拖慢项目的开发进度。

### A CLEAN EMBEDDED ARCHITECTURE IS A TESTABLE EMBEDDED ARCHITECTURE 整洁的嵌入式架构就是可测试的嵌入式架构

Let’s see how to apply some of the architectural principles to embedded software and firmware to help you eliminate the target-hardware bottleneck.

> 下面，我们来看一下具体应如何将架构设计的原则应用在嵌入式软件和固件上，以避免陷入目标硬件瓶颈。

### Layers 分层

Layering comes in many flavors. Let’s start with three layers, as shown in Figure 29.1. At the bottom, there is the hardware. As Doug warns us, due to technology advances and Moore’s law, the hardware will change. Parts become obsolete, and new parts use less power or provide better performance or are cheaper. Whatever the reason, as an embedded engineer, I don’t want to have a bigger job than is necessary when the inevitable hardware change finally happens.

> 分层可以有很多种方式，这里先按图 29.1 所示的设计将系统分成三层。首先，底层是硬件层。正如 Doug 警告我们的那样，由于科技的进步与摩尔定律，硬件是一定会改变的。旧的硬件不见将会被淘汰，新的硬件部件可能耗电量更少，或者性能更好，或者价格更便宜。不管硬件更新的原因是什么，作为嵌入式工程师，我们都不会希望这些不可避免的硬件变动带来更多的工作量。

<Figures figure="29-1">Three layers</Figures>

The separation between hardware and the rest of the system is a given—at least once the hardware is defined (Figure 29.2). Here is where the problems often begin when you are trying to pass the App-titude test. There is nothing that keeps hardware knowledge from polluting all the code. If you are not careful about where you put things and what one module is allowed to know about another module, the code will be very hard to change. I’m not just talking about when the hardware changes, but when the user asks for a change, or when a bug needs to be fixed.

> 硬件与系统其他部分的分隔是既定的——至少在硬件设计完成之后如此（如图 29.2 所示）。这也是在我们试图通过程序适用测试之时往往会发生问题的地方。因为没有什么东西可以真正阻碍硬件实现细节污染到应用代码。如果我们在构建代码的时候不够小心，没有小心安排哪些模块之间可以互相依赖，代码很快就非常难以更改了。请注意，这里所说的变更不仅仅是指来自硬件的变更，还包括用户的功能性变更、修复代码中的 Bug。

<Figures figure="29-2">Hardware must be separated from the rest of the system</Figures>

Software and firmware intermingling is an anti-pattern. Code exhibiting this anti-pattern will resist changes. In addition, changes will be dangerous, often leading to unintended consequences. Full regression tests of the whole system will be needed for minor changes. If you have not created externally instrumented tests, expect to get bored with manual tests—and then you can expect new bug reports.

> 另外，软件与固件集成在一起也属于设计上的反模式（anti-pattern）。符合这种反模式的代码修改起来都会很困难。同时，这种代码也很危险，容易造成意外事故，这导致它经历任何微少的改动都需要进行完整的回归测试。如果没有完善的测试流程，那么你就等着无穷无尽的手工测试吧——同时还有纷沓而来的 Bug 报告。

### The Hardware Is a Detail 硬件是实现细节

The line between software and firmware is typically not so well defined as the line between code and hardware, as shown in Figure 29.3.

> 软件与固件之间的边界往往没有代码与硬件之间的边界那么清晰，如图 29.3 所示。

<Figures figure="29-3">The line between software and firmware is a bit fuzzier than the line between code and hardware</Figures>

One of your jobs as an embedded software developer is to firm up that line. The name of the boundary between the software and the firmware is the hardware abstraction layer (HAL) (Figure 29.4). This is not a new idea: It has been in PCs since the days before Windows.

> 所以，我们的工作之一就是将这个边界定义得更清晰一些。软件与固件之间的边界被称为硬件抽象层（HAL），如图 29.4 所示。这不是一个新概念，它在 PC 上的存在甚至可以追溯到 Windows 诞生之前。

<Figures figure="29-4">The hardware abstraction layer</Figures>

The HAL exists for the software that sits on top of it, and its API should be tailored to that software’s needs. As an example, the firmware can store bytes and arrays of bytes into flash memory. In contrast, the application needs to store and read name/value pairs to some persistence mechanism. The software should not be concerned that the name/value pairs are stored in flash memory, a spinning disk, the cloud, or core memory. The HAL provides a service, and it does not reveal to the software how it does it. The flash implementation is a detail that should be hidden from software.

> HAL 的存在是为了给它上层的软件提供服务，HAL 的 API 应该按照这些软件的需要来量身定做。例如，固件可以直接将字节和字节组存入闪存中。相比之下，软件需要的是从某种持久化平台保存和读取 name/value 对信息，它不应该关心自己信息到底是被存储到闪存中、磁盘中、云端存储中，还是在内存中读取/存储这些信息。总之，HAL 的作用是为软件部分提供一种服务，以便隐藏具体的实现细节。毕竟是专门针对闪存的实现代码是一种细节信息，它应该与软件部分隔离。

As another example, an LED is tied to a GPIO bit. The firmware could provide access to the GPIO bits, where a HAL might provide Led_TurnOn(5). That is a pretty low-level hardware abstraction layer. Let’s consider raising the level of abstraction from a hardware perspective to the software/product perspective. What is the LED indicating? Suppose that it indicated low battery power. At some level, the firmware (or a board support package) could provide Led_TurnOn(5), while the HAL provides Indicate_LowBattery(). You can see the HAL expressing services needed by the application. You can also see that layers may contain layers. It is more of a repeating fractal pattern than a limited set of predefined layers. The GPIO assignments are details that should be hidden from the software.

> 我们再来看另一个例子：有一个 LED 被连接到一个 GPIO 比特位上。固件可以直接操作 GPIO 比特位，而 HAL 则会提供一个 Led_TurnOn(5) 数。这种硬件抽象层的层次是相当低的。现在，假设我们想将抽象层次从硬件层次提升到软件/产品的层次。这时候就要弄清楚这个 LED 到底代表的是什么。假设它代表了电池电量不足，那么其固码（或该电路板的支持包）可能就会负责提供 Led_TurnOn(5) 函数，而 HAL 则负责提供 Indicate_LowBattery() 函数。由此可见，HAL 层是按照应用程序的需要来提供服务的。同时，我们也能看出来系统的每一个分层中都可以包含许多分层。相对于之前的固定分层法，这里更像是一种无限分层模式。总之，GPIO 位的对应关系应该是一个具体的实现细节，它应该与软件部分隔离。

### DON’T REVEAL HARDWARE DETAILS TO THE USER OF THE HAL 不要向 HAL 的用户暴露硬件细节

A clean embedded architecture’s software is testable off the target hardware. A successful HAL provides that seam or set of substitution points that facilitate off-target testing.

> 依照整洁的嵌入式架构所建构的软件应该是可以脱离目标硬件平台来进行测试的。因为设计合理的 HAL 可以为我们脱离硬件平台的测试提供相应的支撑。

### The Processor Is a Detail

When your embedded application uses a specialized tool chain, it will often provide header files to `<i>`help you`</i>`.4 These compilers often take liberties with the C language, adding new keywords to access their processor features. The code will look like C, but it is no longer C.

> 当我们的嵌入式应用依赖于某种特殊的工具链时，该工具链通常会为我们提供一些“`<i>帮助</i>`"性质的头文件。这些编译器往往会自带一些基于 C 语言的扩展库，并添加一些用于访问特殊功能的关键词。这会导致这些程序的代码看起来仍然用的是 C 语言，但实际上它们已经不是 C 语言了。

Sometimes vendor-supplied C compilers provide what look like global variables to give access directly to processor registers, IO ports, clock timers, IO bits, interrupt controllers, and other processor functions. It is helpful to get access to these things easily, but realize that any of your code that uses these helpful facilities is no longer C. It won’t compile for another processor, or maybe even with a different compiler for the same processor.

> 有时候，这些嵌入式应用的提供商所指定的 C 编译器还会提供类似于全局变量的功能，以便我们直接访问寄存器、I/O 端口、时钟信息、I/O 位、中断控制器以及其他处理器函数，这些函数会极大地方便我们对相关硬件的访问。但请注意，一旦你在代码中使用了这些函数，你写的就不再是 C 语言程序，它就不能用其他编译器来编译了，甚至可能连同一个处理器的不同编译器也不行。

I would hate to think that the silicon and tool provider is being cynical, tying your product to the compiler. Let’s give the provider the benefit of a doubt by assuming that it is truly trying to help. But now it’s up to you to use that help in a way that does not hurt in the future. You will have to limit which files are allowed to know about the C extensions.

> 我不想说这是提供商故意给我们设置的陷阱，即便我们假设这些硬件提供商这样做真的是为了“帮助”我们，我们自己也要知道如何来利用这些“帮助”，这才是问题的关键。为避免自己的代码在未来出现问题，我们就必须限制这些 C 扩展的使用范围。

Let’s look at this header file designed for the ACME family of DSPs—you know, the ones used by Wile E. Coyote:

> 下面来看一下针对 ACME DSP（数字信号处理器）系统设计的头文件——While E Coyote 采用的就是这个系统：

```c
#ifndef _ACME_STD_TYPES
#define _ACME_STD_TYPES
```

```c
#if defined(_ACME_X42)
    typedef unsigned int        Uint_32;
    typedef unsigned short      Uint_16;
    typedef unsigned char       Uint_8;

    typedef int                 Int_32;
    typedef short               Int_16;
    typedef char                Int_8;

#elif defined(_ACME_A42)
    typedef unsigned long       Uint_32;
    typedef unsigned int        Uint_16;
    typedef unsigned char       Uint_8;

    typedef long                Int_32;
    typedef int                 Int_16;
    typedef char                Int_8;
#else
    #error <acmetypes.h> is not supported for this environment
#endif

#endif
```

The acmetypes.h header file should not be used directly. If you do, your code gets tied to one of the ACME DSPs. You are using an ACME DSP, you say, so what is the harm? You can’t compile your code unless you include this header. If you use the header and define `__ACME_X42` or `__ACME_A42`, your integers will be the wrong size if you try to test your code off-target. If that is not bad enough, one day you’ll want to port your application to another processor, and you will have made that task much more difficult by not choosing portability and by not limiting what files know about ACME.

> 该 `acmetypes.h` 头文件通常不应该直接使用。因为如果这样做的话，代码就和某个 ACME DSP 绑定在一起了。这时候你可能会问，我们在这里写代码不就是为了使用 ACME DSP 吗？不引用这个头文件如何编译代码呢？但如果引用了这个头文件，就等于同时定义了 `__ACME_X42` 和 `__ACME_A42`，那么我们的代码在平台之外进行测试的时候整数类型的大小就会是错误的。更糟糕的是，有一天当我们想将代码迁移到另外一个处理器上的时候，如果没有在这里限制 ACME 头文件被引用的范围，就会大大增加这项迁移工作的难度。

Instead of using acmetypes.h, you should try to follow a more standardized path and use stdint.h. But what if the target compiler does not provide stdint.h? You can write this header file. The stdint.h you write for target builds uses the acmetypes.h for target compiles like this:

> 因此在这里，我们应该用标准的 `stdint.h` 来替代 `acmetypes.h`。如果目标编译器没有提供 `stdint.h` 的话，我们可以自己写一个。例如，下面就是一个针对目标编译器的，可以用 `acmetypes.h` 来构建目标的自定义 `stdint.h`：

```c
#ifndef _STDINT_H_
#define _STDINT_H_

#include <acmetypes.h>

typedef Uint_32 uint32_t;
typedef Uint_16 uint16_t;
typedef Uint_8  uint8_t;

typedef Int_32  int32_t;
typedef Int_16  int16_t;
typedef Int_8   int8_t;

#endif
```

Having your embedded software and firmware use stdint.h helps keep your code clean and portable. Certainly, all of the software should be processor independent, but not all of the firmware can be. This next code snippet takes advantage of special extensions to C that gives your code access to the peripherals in the micro-controller. It’s likely your product uses this micro-controller so that you can use its integrated peripherals. This function outputs a line that says "hi" to the serial output port. (This example is based on real code from the wild.)

> 使用 `stdint.h` 来编写嵌入式的软件和固件，你的代码会是整洁且可移植的。当然，我们应该让所有的软件都独立于处理器，但这并不是所有固件都可以做到的。下面这段代码使用了特殊的 C 扩展来访问微处理器的配件，这样做的目的很可能就是为了使用这个配件。这个函数的作用就是输出一行"hi"到串口。（该例子来自于一个真实项目。）

```c
void say_hi()
{
  IE = 0b11000000;
  SBUF0 = (0x68);
  while(TI_0 == 0);
  TI_0 = 0;
  SBUF0 = (0x69);
  while(TI_0 == 0);
  TI_0 = 0;
  SBUF0 = (0x0a);
  while(TI_0 == 0);
  TI_0 = 0;
  SBUF0 = (0x0d);
  while(TI_0 == 0);
  TI_0 = 0;
  IE = 0b11010000;
}
```

There are lots of problems with this small function. One thing that might jump out at you is the presence of 0b11000000. This binary notation is cool; can C do that? Unfortunately, no. A few other problems relate to this code directly using the custom C extensions:

> 这个小函数中存在大量的问题。首先，你注意到的可能就是 `0b11000000`。二进制表示法的确很酷，但 C 语言支持它吗？并不支持。另外还有一些问题也与 C 语言的扩展有关。

IE: Interrupt enable bits.

SBUF0: Serial output buffer.

TI_0: Serial transmit buffer empty interrupt. Reading a 1 indicates the buffer is empty.

> IE：设置中断比特位。
>
> SBUF0：串口输出缓冲区。
>
> TI_0：串口传输区空中断。读取到 1 表明缓冲区为空。

The uppercase variables actually access micro-controller built-in peripherals. If you want to control interrupts and output characters, you must use these peripherals. Yes, this is convenient—but it’s not C.

> 这些名字大写的变量实际上都是用来访问微处理器的内置部件的。如果我们需要控制中断并且输出字符，就必须使用这些部件，它们也确实很方便——但这不是 C 代码。

A clean embedded architecture would use these device access registers directly in very few places and confine them totally to the firmware. Anything that knows about these registers becomes firmware and is consequently bound to the silicon. Tying code to the processor will hurt you when you want to get code working before you have stable hardware. It will also hurt you when you move your embedded application to a new processor.

> 在整洁的嵌入式架构中，我们会将这些用于设备访问的寄存器访问集中在一起，并将其限制在固件层中。这样一来，任何需要知道这些寄存器值的代码都必须成为固件代码，与硬件实现绑定。一旦这些代码与处理器实现强绑定，那么在处理器稳定工作之前它们是无法工作的，并且在需要将其迁移到一个新处理器上时也会遇到麻烦。

If you use a micro-controller like this, your firmware could isolate these low-level functions with some form of a processor abstraction layer (PAL). Firmware above the PAL could be tested off-target, making it a little less firm.

> 如果我们真的需要使用这种微处理器，固件就必须将这类底层函数隔离成处理器抽象层（PAL），这样一来，使用 PAL 的固件代码就可以在目标平台之外被测试了。

### The Operating System Is a Detail 操作系统是实现细节

A HAL is necessary, but is it sufficient? In bare-metal embedded systems, a HAL may be all you need to keep your code from getting too addicted to the operating environment. But what about embedded systems that use a real-time operating system (RTOS) or some embedded version of Linux or Windows?

> HAL 的必要性是不言而喻的，但光有 HAL 就够了吗？在裸机的嵌入式系统中，HAL 的确可能可以保证我们的代码不会和运行环境绑定得太紧密。但如果嵌入式系统使用了某种实时操作系统（RTOS），或者某种嵌入式的 Linux 或 Windows 呢？

To give your embedded code a good chance at a long life, you have to treat the operating system as a detail and protect against OS dependencies.

> 为了延长代码的生命周期，我们必须将操作系统也定义为实现细节，让代码避免与操作系统层产生依赖。

The software accesses the services of the operating environment through the OS. The OS is a layer separating the software from firmware (Figure 29.5). Using an OS directly can cause problems. For example, what if your RTOS supplier is bought by another company and the royalties go up, or the quality goes down? What if your needs change and your RTOS does not have the capabilities that you now require? You’ll have to change lots of code. These won’t just be simple syntactical changes due to the new OS’s API, but will likely have to adapt semantically to the new OS’s different capabilities and primitives.

> 软件通过操作系统来访问运行环境服务。操作系统是将软件与固件隔离的那一层（见图 29.5），直接使用操作系统服务可能会带来问题。例如，如果更换了 RTOS 操作系统厂商，授权费用提高，或者质量下降怎么办？如果需求发生变化，RTOS 无法满足怎么办？很多代码都需要变动，不仅要更改语法适应新操作系统 API，很有可能需要重新适应新操作系统的语义与原语。

<Figures figure="29-5">Adding in an operating system</Figures>

A clean embedded architecture isolates software from the operating system, through an operating system abstraction layer (OSAL) (Figure 29.6). In some cases, implementing this layer might be as simple as changing the name of a function. In other cases, it might involve wrapping several functions together.

> 整洁的嵌入式架构会引入操作系统抽象层（OSAL，如图 29.6 所示），将软件与操作系统分割开。在某些情况下，实现这个抽象层就像给函数改个名字那么简单。而在另一些情况下，则需要将几个函数封装在一起。

<Figures figure="29-6">The operating system abstraction layer</Figures>

If you have ever moved your software from one RTOS to another, you know it is painful. If your software depended on an OSAL instead of the OS directly, you would largely be writing a new OSAL that is compatible with the old OSAL. Which would you rather do: modify a bunch of complex existing code, or write new code to a defined interface and behavior? This is not a trick question. I choose the latter.

> 如果你有迁移过 RTOS 系统的经历，就一定知道那有多痛苦。如果我们能让自己的软件依赖于 OSAL，而不是直接依赖于操作系统，我们就只需要写一个兼容以前的 OSAL 实现的新版本即可。你觉得哪一种方式更好？是修改一堆复杂的现有代码，还是按照接口和行为定义来写一套新代码？这里显而易见，后者更好。

You might start worrying about code bloat about now. Really, though, the layer becomes the place where much of the duplication around using an OS is isolated. This duplication does not have to impose a big overhead. If you define an OSAL, you can also encourage your applications to have a common structure. You might provide message passing mechanisms, rather than having every thread handcraft its concurrency model.

> 当然，我们可能会担心代码膨胀的问题。但是，其实上面这种分层已经将因为使用操作系统所带来的重复性代码隔离开了，因此这种重复不一定会带来很大的额外负担。而且，如果我们定义了 OSAL，还可以让自己的应用共享一种公用结构。比如采用一套标准的消息传递机制，这样每个线程就不用自己定义一个并行模型了。

The OSAL can help provide test points so that the valuable application code in the software layer can be tested off-target and off-OS. A clean embedded architecture’s software is testable off the target operating system. A successful OSAL provides that seam or set of substitution points that facilitate off-target testing.

> 另外，OSAL 还可以帮助高价值的应用程序实现在目标平台、目标操作系统之外进行测试。一个由整洁的嵌入式架构所构建出来的软件是可以在目标操作系统之外被测试的。设计良好的 OSAL 会为这种目标环境外的测试提供支撑点。

### PROGRAMMING TO INTERFACES AND SUBSTITUTABILITY 面向接口编程与可替代性

In addition to adding a HAL and potentially an OSAL inside each of the major layers (software, OS, firmware, and hardware), you can—and should—apply the principles described throughout this book. These principles encourage separation of concerns, programming to interfaces, and substitutability.

> 除了在嵌入式系统的主要分层（指软件、操作系统、固件、硬件这四层）之中增加 HAL 和 OSAL 之外，我们还可以——也应该——应用本书中提到的其他设计原则。这些设计原则可以帮助我们按功能模块、接口编程以及可替代性来划分系统。

The idea of a layered architecture is built on the idea of programming to interfaces. When one module interacts with another though an interface, you can substitute one service provider for another. Many readers will have written their own small version of printf for deployment in the target. As long as the interface to your printf is the same as the standard version of printf, you can override the service one for the other.

> 分层架构的理念是基于接口编程的理念来设计的。当模块之间能以接口形式交互时，我们就可以将一个服务替换成另外一个服务。例如，很多读者应该都写过能在某个目标机器上运行的、小型的自定义的 printf 函数。只要我们的 printf 与标准的 printf 函数接口一致，它们就可以互相替换。

One basic rule of thumb is to use header files as interface definitions. When you do so, however, you have to be careful about what goes in the header file. Limit header file contents to function declarations as well as the constants and struct names that are needed by the function.

> 目前的普适规则之一就是用头丈件来充当接口的定义。然而，如果真的要这样做的话，就需要小心控制头文件中的内容，尽量确保头文件中只包括函数声明，以及函数所需要的结构体名字和常量。

Don’t clutter the interface header files with data structures, constants, and typedefs that are needed by only the implementation. It’s not just a matter of clutter: That clutter will lead to unwanted dependencies. Limit the visibility of the implementation details. Expect the implementation details to change. The fewer places where code knows the details, the fewer places where code will have to be tracked down and modified.

> 另外，不要在定义接口的头文件中包含只有具体实现代码才需要的数据结构、常量以及类型定义（typedef）。这不仅仅是架构是否整洁的问题，而是这样做可能会导致意外的依赖关系。总之，我们必须控制好实现细节的可见性，因为这些实现细节是肯定会变化的。关注实现细节的代码越少，它们所需的变更就越少。

A clean embedded architecture is testable within the layers because modules interact through interfaces. Each interface provides that seam or substitution point that facilitates off-target testing.

> 由整洁的嵌入式架构所构建的系统应该在每一个分层中都是可测试的，因为它的模块之间采用接口通信，每一个接口都为平台之外的测试提供了替换点。

### DRY CONDITIONAL COMPILATION DIRECTIVES DRY 条件性编译命令

One use of substitutability that is often overlooked relates to how embedded C and C++ programs handle different targets or operating systems. There is a tendency to use conditional compilation to turn on and off segments of code. I recall one especially problematic case where the statement #ifdef BOARD_V2 was mentioned several thousand times in a telecom application.

> 另一个经常被忽视的可替代换性规则的实际案例是嵌入式 C/C++ 程序对不同平台和操作系统的处理方式。这些程序经常会用条件性编译命令来根据不同的平台启用和禁用某一段代码。例如，我曾经遇到过 `#ifdef BOARD_V2` 这条语句在一个电信应用程序中出现了几千次的情况。

This repetition of code violates the Don’t Repeat Yourself (DRY) principle.5 If I see #ifdef BOARD_V2 once, it’s not really a problem. Six thousand times is an extreme problem. Conditional compilation identifying the target-hardware’s type is often repeated in embedded systems. But what else can we do?

> 很显然，这种代码的重复违背了“不要重复自己（DRY）”原则。如果 `#ifdef BOARD_V2` 只出现一次，这当然不是什么问题，而如果出现了 6000 次，那就非常严重了。但这类条件性编译语句在嵌入式编程中非常常见，有什么好的解决方案吗？

What if there is a hardware abstraction layer? The hardware type would become a detail hidden under the HAL. If the HAL provides a set of interfaces, instead of using conditional compilation, we could use the linker or some form of runtime binding to connect the software to the hardware.

> 使用硬件抽象层如何？这样的话，硬件类型就只是 HAL 中的一个实现细节了。而且，如果系统中使用的是 HAL 所提供的一系列接口，而不是条件性编译语句，那么我们就可以用链接器，或者某种运行时加载器来将软件与硬件相结合了。

## CONCLUSION 本章小结

People who are developing embedded software have a lot to learn from experiences outside of embedded software. If you are an embedded developer who has picked up this book, you will find a wealth of software development wisdom in the words and ideas.

> 嵌入式编程人员应该多学习一些非嵌入式系统的编程经验。如果你从事的是嵌入式编程工作，相信你一定会从本章的建议中得到很多启发。

Letting all code become firmware is not good for your product’s long-term health. Being able to test only in the target hardware is not good for your product’s long-term health. A clean embedded architecture is good for your product’s long-term health.

> 为了让我们的产品能长期地保持健康，请别让你的代码都变成固件。如果一个系统的代码只能在目标硬件上测试，那么它的开发过程会变得非常艰难。总之，为产品的长期健康着想而采用一套整洁的嵌入式架构是很有必要的。
