# Chap20. BUSINESS RULES 业务逻辑

![](https://ngte-superbed.oss-cn-beijing.aliyuncs.com/book/mono/un/CH-UN20.jpg)

If we are going to divide our application into business rules and plugins, we’d better get a good grasp on just what business rules actually are. It turns out there are several different kinds.

> 如果我们要将自己的应用程序划分为业务逻辑和插件两部分，就必须更仔细地了解业务逻辑究竟是什么它到底有几种类型。

Strictly speaking, business rules are rules or procedures that make or save the business money. Very strictly speaking, these rules would make or save the business money, irrespective of whether they were implemented on a computer. They would make or save money even if they were executed manually.

> 严格地讲，业务逻辑就是程序中那些真正用于赚钱或省钱的业务逻辑与国产。更严格地讲，无论这些业务逻辑是在计算机上实现的，还是人工执行的，它们在省钱/赚钱上的作用都是一样的。

The fact that a bank charges N% interest for a loan is a business rule that makes the bank money. It doesn’t matter if a computer program calculates the interest, or if a clerk with an abacus calculates the interest.

> 例如，银张行要对借贷收取 N% 利息这个逻辑就是银行获取收入方面的一条业务逻辑，对它来说，我们通过计算机来计算利息，还是让一个银行职员用计算器来计算利息并不重要。

We shall call these rules Critical Business Rules, because they are critical to the business itself, and would exist even if there were no system to automate them.

> 我们通常称这些逻辑为“关键业务逻辑”，因为它们是一项业务的关键部分，不管有没有自动化系统来执行这项业务，这—点是不会改变的。

Critical Business Rules usually require some data to work with. For example, our loan requires a loan balance, an interest rate, and a payment schedule.

> “关键业务逻辑”通常会需要处理一些数据，例如，在借贷的业务逻辑中，我们需要知道借贷的数量、利率以及还款日程。

We shall call this data Critical Business Data. This is the data that would exist even if the system were not automated.

> 我们将这些数据称为“关键业务数据”，这是因为这些数据无论自动化程序存在与否，都必须要存在。

The critical rules and critical data are inextricably bound, so they are a good candidate for an object. We’ll call this kind of object an Entity.1

> 关键业务逻辑和关键业务数据是紧密相关的，所以它们很适合被放在同一个对象中处理。我们将这种对象称为“业务实体（Entity）”。

## ENTITIES 业务实体

An Entity is an object within our computer system that embodies a small set of critical business rules operating on Critical Business Data. The Entity object either contains the Critical Business Data or has very easy access to that data. The interface of the Entity consists of the functions that implement the Critical Business Rules that operate on that data.

> 业务实体实际上就是计算机系统中的一种对象，这种对象中包含了一系列用于操作关键数据的业务逻辑。这些实体对象要么直接包含关键业务数据，要么可以很容易地访问这些数据。业务实体的接口层则是由那些实现关键业务逻辑、操作关键业务数据的函数组成的。

For example, Figure 20.1 shows what our Loan entity might look like as a class in UML. It has three pieces of Critical Business Data, and presents three related Critical Business Rules at its interface.

> 例如，在图 20.1 中，我们看到的是一个对应于借贷业务的实体类 Loan 的 UML 图。如你所见，该类中包含了三个关键业务数据，以及三个代表了其关键业务逻辑的接口。

<Figures figure="20-1">Loan entity as a class in UML</Figures>

When we create this kind of class, we are gathering together the software that implements a concept that is critical to the business, and separating it from every other concern in the automated system we are building. This class stands alone as a representative of the business. It is unsullied with concerns about databases, user interfaces, or third-party frameworks. It could serve the business in any system, irrespective of how that system was presented, or how the data was stored, or how the computers in that system were arranged. The Entity is pure business and nothing else.

> 当我们创建这样一个类时，其实就是在将软件中具体实现了该关键业务的部分聚合在一起，将其与自动化系统中我们所构建的其他部分隔离区分。这个类独自代表了整个业务逻辑，它与数据库、用户界面、第三方框架等内容无关。该类可以在任何一个系统中提供与其业务逻辑相关的服务，它不会去管这个系统是如何呈现给用户的，数据是如何存储的，或者是以何种方式运行的。总而言之，业务实体这个概念中应该只有业务逻辑，没有别的。

Some of you may be concerned that I called it a class. Don’t be. You don’t need to use an object-oriented language to create an Entity. All that is required is that you bind the Critical Business Data and the Critical Business Rules together in a single and separate software module.

> 有些读者可能会担心我在这里把业务实体解释成一个类。不是这样的，业务实体不一定非要用面向对象编程语言的类来实现。业务实体这个概念只要求我们将关键业务数据和关键业务逻辑绑定在一个独立的软件模块内。

## USE CASES 用例

Not all business rules are as pure as Entities. Some business rules make or save money for the business by defining and constraining the way that an automated system operates. These rules would not be used in a manual environment, because they make sense only as part of an automated system.

> 并不是所有的业务逻辑都是一个纯粹的业务实体。例如，有些业务逻辑是通过定义或限制自动化系统的运行方式来实现赚钱或省钱的业务的。这些业务逻辑就不能靠人工来执行，它们只有作为自动化系统的一部分时才有意义。

For example, imagine an application that is used by bank officers to create a new loan. The bank may decide that it does not want the loan officers to offer loan payment estimates until they have first gathered, and validated, contact information and ensured that the candidate’s credit score is 500 or higher. For this reason, the bank may specify that the system will not proceed to the payment estimation screen until the contact information screen has been filled out and verified, and the credit score has been confirmed to be greater than the cutoff.

> 例如，假设我们现在有一个银行职员们用来新建借贷的应用程序，银行可能设计的业务逻辑是，银行职员必须首先收集、验证客户的联系信息，确保客户的信用值在 500 以上，然后才允许向用户提供借贷还款的预估值。因此，银行就必须要求在设计其计算机系统时确保两件事：首先，客户必须能通过屏幕填写所有的联系信息并且让其通过相关验证；其次，客户只有在其信用值大于既定阈值时才能进入还款预估页。

This is a use case.2 A use case is a description of the way that an automated system is used. It specifies the input to be provided by the user, the output to be returned to the user, and the processing steps involved in producing that output. A use case describes application-specific business rules as opposed to the Critical Business Rules within the Entities.

> 我们在上面所描述的就是一个用例（user case）。用例本质上就是关于如何操作一个自动化系统的描述，它定义了用户需要提供的输入数据、用户应该得到的输出信息以及产生输出所应该采取的处理步骤。当然，用例所描述的是某种特定应用情景下的业务逻辑，它并非业务实体中所包含的关键业务逻辑。

Figure 20.2 shows an example of a use case. Notice that in the last line it mentions the Customer. This is a reference to the Customer entity, which contains the Critical Business Rules that govern the relationship between the bank and its customers.

> 下面，让我们来看看图 20.2 中的这个用例。请注意，图 20.2 的最后一行中提到的“客户”，这里的客户是指代表客户的业务实体，其中包含了处理银行与客户之间关系的关键业务逻辑。

<Figures figure="20-2">Example use case</Figures>

Use cases contain the rules that specify how and when the Critical Business Rules within the Entities are invoked. Use cases control the dance of the Entities.

> 如上所示，用例中包含了对如何调用业务实体中的关键业务逻辑的定义。简而言之，用例控制着业务实体之间的交互方式。

Notice also that the use case does not describe the user interface other than to informally specify the data coming in from that interface, and the data going back out through that interface. From the use case, it is impossible to tell whether the application is delivered on the web, or on a thick client, or on a console, or is a pure service.

> 除此之外，这里还应该注意，用例除非正式地描述了数据流入/流出接口以外，并不详细描述用户界面。也就是说，如果我们只看用例，是没有办法分辨出系统是在 Web 平台上交付的，还是交付了某种常客门端；或后是以命令行模式交付的，还是以一个内部服务模式交付的。

This is very important. Use cases do not describe how the system appears to the user. Instead, they describe the application-specific rules that govern the interaction between the users and the Entities. How the data gets in and out of the system is irrelevant to the use cases.

> 这是非常重要的。用例并不描述系统与用户之间的接口，它只描述该应用在某些特定情境下的业务逻辑，这些业务逻辑所规范的是用户与业务实体之间的交互方式，它与数据流入/流出系统的方式无关。

A use case is an object. It has one or more functions that implement the application-specific business rules. It also has data elements that include the input data, the output data, and the references to the appropriate Entities with which it interacts.

> 在我们的系统中，用例本身也是一个对象，该对象中包含了一个或多个实现了特定应用情景的业务逻辑函数。当然除此之外，用例对象中也包含了输入数据、输出数据以及相关业务实体的引用，以方便调用。

Entities have no knowledge of the use cases that control them. This is another example of the direction of the dependencies following the Dependency Inversion Principle. High-level concepts, such as Entities, know nothing of lower-level concepts, such as use cases. Instead, the lower-level use cases know about the higher-level Entities.

> 当然，业务实体并不会知道是哪个业务用例在控制它们，这也是依赖反转原则（DIP）的另一个应用情景。也就是像业务实体这样的高层概念是无须了解像用例这样的低层概念的。反之，低层的业务用例却需要了解高层的业务实体。

Why are Entities high level and use cases lower level? Because use cases are specific to a single application and, therefore, are closer to the inputs and outputs of that system. Entities are generalizations that can be used in many different applications, so they are farther from the inputs and outputs of the system. Use cases depend on Entities; Entities do not depend on use cases.

> 那么，为什么业务实体属于高层概念，而用例属于低层概念呢？因为用例描述的是一个特定的应用情景，这样一来，用例必然会更靠近系统的输入和输出。而业务实体是一个可以适用于多个应用情景的一般化概念，相对地离系统的输入和输出更远。所以，用例依赖于业务实体，而业务实体并不依赖于用例。

## REQUEST AND RESPONSE MODELS 请求和响应模型

Use cases expect input data, and they produce output data. However, a well-formed use case object should have no inkling about the way that data is communicated to the user, or to any other component. We certainly don’t want the code within the use case class to know about HTML or SQL!

> 在通常情况下，用例会接收输入数据，并产生输出数据。但在一个设计良好的架构中，用例对象通常不应该知道数据展现给用户或者其他组件的方式。很显然我们当然不会希望这些用例类中的代码出现 HTML 和 SQL。

The use case class accepts simple request data structures for its input, and returns simple response data structures as its output. These data structures are not dependent on anything. They do not derive from standard framework interfaces such as HttpRequest and HttpResponse. They know nothing of the web, nor do they share any of the trappings of whatever user interface might be in place.

> 因此，用例类所接收的输入应该是一个简单的请求性数据结构，而返回输出的应该是一个简单的响应性数据结构。这些数据结构中不应该存在任何依赖关系，它们并不派生自 HttpRequest 和 HttpResponse 这样的标准框架接口。这些数据接口应该与 Web 无关，也不应该了解任何有关用户界面的细节。

This lack of dependencies is critical. If the request and response models are not independent, then the use cases that depend on them will be indirectly bound to whatever dependencies the models carry with them.

> 这种独立性非常关键，如果这里的请求和响应模型不是完全独立的，那么用到这些模型的用例就会依赖于这些模型所带来的各种依赖关系。

You might be tempted to have these data structures contain references to Entity objects. You might think this makes sense because the Entities and the request/response models share so much data. Avoid this temptation! The purpose of these two objects is very different. Over time they will change for very different reasons, so tying them together in any way violates the Common Closure and Single Responsibility Principles. The result would be lots of tramp data, and lots of conditionals in your code.

> 可能有些读者会选择直接在数据结构中使用对业务实体对象的引用。毕竟，业务请求响应模型之间有很多相同的数据。但请一定不要这样做！这两个对象存在的意义是非常、非常不一样的。随着时间的推移，这两个对象会以不同的原因、不同的速率发生变更。所以将它们以任何方式整合在一起都是对共同闭包原则（CCP）和单一职责原则（SRP）的违反。这样做的后果，往往会导致代码中出现很多分支判断语句和中间数据。

## CONCLUSION 本章小结

Business rules are the reason a software system exists. They are the core functionality. They carry the code that makes, or saves, money. They are the family jewels.

> 业务逻辑是一个软件系统存在的意义，它们属于核心功能，是系统用来赚钱或省钱的那部分代码，是整个系统中的皇冠明珠。

The business rules should remain pristine, unsullied by baser concerns such as the user interface or database used. Ideally, the code that represents the business rules should be the heart of the system, with lesser concerns being plugged in to them. The business rules should be the most independent and reusable code in the system.

> 这些业务逻辑应该保持纯净，不要掺杂用户界面或者所使用的数据库相关的东西。在理想情况下，这部分代表业务逻辑的代码应该是整个系统的核心，其他低层概念的实现应该以插件形式接入系统中。业务逻辑应该是系统中最独立、复用性最高的代码。
