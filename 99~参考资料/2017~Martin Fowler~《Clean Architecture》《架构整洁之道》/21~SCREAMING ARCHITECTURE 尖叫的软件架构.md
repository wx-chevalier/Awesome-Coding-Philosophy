# Chap21. SCREAMING ARCHITECTURE 尖叫的软件架构

![](https://ngte-superbed.oss-cn-beijing.aliyuncs.com/book/mono/un/CH-UN21.jpg)

Imagine that you are looking at the blueprints of a building. This document, prepared by an architect, provides the plans for the building. What do these plans tell you?

> 假设我们现在正在查看某个建筑的设计架构图，那么在这个反映建筑设计师精心设计成果的文件中，究竟应该是怎样的架构图呢？

If the plans you are viewing are for a single-family residence, then you’ll likely see a front entrance, a foyer leading to a living room, and perhaps a dining room. There will likely be a kitchen a short distance away, close to the dining room. Perhaps there is a dinette area next to the kitchen, and probably a family room close to that. When you looked at those plans, there would be no question that you were looking at a single family home. The architecture would scream: “HOME.”

> 如果这是一幅单户住宅的建筑架构图，那么我们很可能会先看到一个大门，然后是一条连接到起居室的通道，同时可能还会看到一个餐厅。接着，距离餐厅不远处应该会有一个厨房，可能厨房附件还会有一个非正式用餐区，或一个亲子房。当我们阅读这个架构图时，应该不会怀疑这是一个单户住宅。几乎整个建筑设计都在尖叫着告诉你：这是一个“家”。

Now suppose you were looking at the architecture of a library. You would likely see a grand entrance, an area for check-in/out clerks, reading areas, small conference rooms, and gallery after gallery capable of holding bookshelves for all the books in the library. That architecture would scream: “LIBRARY.”

> 假设我们阅读的是一幅图书馆的建筑设计图，情况也差不多。我们应该会先看到一个超大入口，然后是一个用于签到/签出的办公区，接下来是阅读区、小型会议室，以及一排排的书架区。同样，几乎整个建筑设计都在尖叫着跟你说：这是一个“图书馆”。

So what does the architecture of your application scream? When you look at the top-level directory structure, and the source files in the highest-level package, do they scream “Health Care System,” or “Accounting System,” or “Inventory Management System”? Or do they scream “Rails,” or “Spring/Hibernate,” or “ASP”?

> 那么，我们的应用程序的架构设计又会“喊”些什么呢？当我们查看它的顶层结构目录，以及顶层软件包中的源代码时，它们究竟是在喊“健康管理系统”“账务系统” “库存管理系统”，还是在喊：“Rails” “Spring/Hibernate” “ASP” 这样的技术名词呢？

## THE THEME OF AN ARCHITECTURE 架构设计的主题

Go back and read Ivar Jacobson’s seminal work on software architecture: Object Oriented Software Engineering. Notice the subtitle of the book: A Use Case Driven Approach. In this book Jacobson makes the point that software architectures are structures that support the use cases of the system. Just as the plans for a house or a library scream about the use cases of those buildings, so should the architecture of a software application scream about the use cases of the application.

> 在这里，再次推荐读者仔细阅读 Ivar Jacobson 关于软件架构设计的那本书：Object Oriented Software Engineering，请读者注意这本书的副标题 A Use Case Driven Approach（业务用例驱动的设计方式）。在这本书中，Jacobson 提出了一个观点：软件的系统架构应该为该系统的用例提供支持。这就像住宅和图书馆的建筑计划满篇都在非常明显地凸显这些建筑的用例一样，软件系统的架构设计图也应该非常明确地凸显该应用程序会有哪些用例。

Architectures are not (or should not be) about frameworks. Architectures should not be supplied by frameworks. Frameworks are tools to be used, not architectures to be conformed to. If your architecture is based on frameworks, then it cannot be based on your use cases.

> 架构设计不是（或者说不应该是）与框架相关的，这件事不应该是基于框架来完成的。对于我们来说，框架只是一个可用的工具和手段，而不是一个架构所规范的内容。如果我们的架构是基于框架来设计的，它就不能基于我们的用例来设计了。

## THE PURPOSE OF AN ARCHITECTURE 架构设计的核心目标

Good architectures are centered on use cases so that architects can safely describe the structures that support those use cases without committing to frameworks, tools, and environments. Again, consider the plans for a house. The first concern of the architect is to make sure that the house is usable—not to ensure that the house is made of bricks. Indeed, the architect takes pains to ensure that the homeowner can make decisions about the exterior material (bricks, stone, or cedar) later, after the plans ensure that the use cases are met.

> 一个良好的架构设计应该围绕着用例来展开，这样的架构设计可以在脱离框架、工具以及使用环境的情况下完整地描述用例。这就好像一个住宅建筑设计的首要目标应该是满足住宅的使用需求，而不是确保一定要用砖来构建这个房子。架构师应该花费更多的精力来确保该架构的设计在满足用例需求的情况下，尽可能地允许用户能自由地选择建筑材料（砖头、石料或者木材）。

A good software architecture allows decisions about frameworks, databases, web servers, and other environmental issues and tools to be deferred and delayed. Frameworks are options to be left open. A good architecture makes it unnecessary to decide on Rails, or Spring, or Hibernate, or Tomcat, or MySQL, until much later in the project. A good architecture makes it easy to change your mind about those decisions, too. A good architecture emphasizes the use cases and decouples them from peripheral concerns.

> 而且，良好的架构设计应该尽可能地允许用户推迟和延后决定釆用什么框架、数据库、Web 服务以及其他与环境相关的工具。框架应该是一个可选项，良好的架构设计应该允许用户在项目后期再决定是否采用 Rails、Spring、Hibernate、Tomcat、MySQL 这些工具。同时，良好的架构设计还应该让我们很容易改变这些决定。总之，良好的架构设计应该只关注用例，并能将它们与其他的周边因素隔离。

## BUT WHAT ABOUT THE WEB? 那 Web 呢

Is the web an architecture? Does the fact that your system is delivered on the web dictate the architecture of your system? Of course not! The web is a delivery mechanism—an IO device—and your application architecture should treat it as such. The fact that your application is delivered over the web is a detail and should not dominate your system structure. Indeed, the decision that your application will be delivered over the web is one that you should defer. Your system architecture should be as ignorant as possible about how it will be delivered. You should be able to deliver it as a console app, or a web app, or a thick client app, or even a web service app, without undue complication or change to the fundamental architecture.

> Web 究竟是不是一种架构？如果我们的系统需要以 Web 形式来交付，这是否意味着我们只能采用某种系统架构？当然不是！Web 只是一种交付手段——一种 IO 设备——这就是它在应用程序的架构设计中的角色。换句话说，应用程序采用 Web 方 式来交付只是一个实现细节，这不应该主导整个项目的结构设计。事实上，关于一个应用程序是否应该以 Web 形式来交付这件事，它本身就应该是一个被推迟和延后的决策。一个系统应该尽量保持它与交付方式之间的无关性。在不更改基础架构设计的情况下，我们应该可以将一个应用程序交付成命令行程序、Web 程序、富客户端程序、Web 服务程序等任何一种形式的程序。

## FRAMEWORKS ARE TOOLS, NOT WAYS OF LIFE 框架是工具而不是生活信条

Frameworks can be very powerful and very useful. Framework authors often believe very deeply in their frameworks. The examples they write for how to use their frameworks are told from the point of view of a true believer. Other authors who write about the framework also tend to be disciples of the true belief. They show you the way to use the framework. Often they assume an all-encompassing, all-pervading, let-the-framework-do-everything position.

> 当然，框架通常可以是非常强大、非常有用的。但框架作者往往对自己写出的框架有着极深的信念，他们所写出来的使用手册一般都是从如何成为该框架的虔诚信徒的角度来描绘如何使用这个框架的。甚至这些框架的使用者所写的教程也会出现这种传教士模式。他们会告诉你某个框架是能包揽一切、超越一切、解决一切问题的存在。

This is not the position you want to take.

> 这不应该成为你的观点。

Look at each framework with a jaded eye. View it skeptically. Yes, it might help, but at what cost? Ask yourself how you should use it, and how you should protect yourself from it. Think about how you can preserve the use-case emphasis of your architecture. Develop a strategy that prevents the framework from taking over that architecture.

> 我们一定要带着怀疑的态度审视每一个框架。是的，采用框架可能会很有帮助，但采用它们的成本呢？我们一定要懂得权衡如何使用一个框架，如何保护自己。无论如何，我们需要仔细考虑如何能保持对系统用例的关注，避免让框架主导我们的架构设计。

## TESTABLE ARCHITECTURES 可测试的架构设计

If your system architecture is all about the use cases, and if you have kept your frameworks at arm’s length, then you should be able to unit-test all those use cases without any of the frameworks in place. You shouldn’t need the web server running to run your tests. You shouldn’t need the database connected to run your tests. Your Entity objects should be plain old objects that have no dependencies on frameworks or databases or other complications. Your use case objects should coordinate your Entity objects. Finally, all of them together should be testable in situ, without any of the complications of frameworks.

> 如果系统架构的所有设计都是围绕着用例来展开的，并且在使用框架的问题上保持谨慎的态度，那么我们就应该可以在不依赖任何框架的情况下针对这些用例进行单元测试。另外，我们在运行测试的时候不应该运行 Web 服务，也不应该需要连接数据库。我们测试的应该只是一个简单的业务实体对象，没有任何与框架、数据库相关的依赖关系。总而言之，我们应该通过用例对象来调度业务实体对象，确保所有的测试都不需要依赖框架。

## CONCLUSION 本章小结

Your architecture should tell readers about the system, not about the frameworks you used in your system. If you are building a health care system, then when new programmers look at the source repository, their first impression should be, “Oh, this is a heath care system.” Those new programmers should be able to learn all the use cases of the system, yet still not know how the system is delivered. They may come to you and say:

> 一个系统的架构应该着重于展示系统本身的设计，而并非该系统所使用的框架。如果我们要构建的是一个医疗系统，新来的程序员第一次看到其源码时就应该知道这是一个医疗系统。新来的程序员应该先了解该系统的用例，而非系统的交付方式。他们可能会走过来问你：

“We see some things that look like models—but where are the views and controllers?”

> "我看到了一些看起来像是模型的代码——但它们的视图和控制器在哪里？”

And you should respond:

> 这时你的回答应该是：

“Oh, those are details that needn’t concern us at the moment. We’ll decide about them later.”

> “哦，我们现在先不考虑这些细节问题，回头再来决定应该怎么做。”
