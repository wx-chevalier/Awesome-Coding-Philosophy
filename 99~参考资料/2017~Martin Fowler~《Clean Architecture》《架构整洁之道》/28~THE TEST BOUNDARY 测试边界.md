# Chap28. THE TEST BOUNDARY 测试边界

![](https://ngte-superbed.oss-cn-beijing.aliyuncs.com/book/mono/un/CH-UN28.jpg)

Yes, that’s right: The tests are part of the system, and they participate in the architecture just like every other part of the system does. In some ways, that participation is pretty normal. In other ways, it can be pretty unique.

> 对，你没看错：和程序代码一样，测试代码也是系统的一部分。甚至，测试代码有时在系统架构中的地位还要比其他部分更独特一些。

## TESTS AS SYSTEM COMPONENTS 测试也是一种系统组件

There is a great deal of confusion about tests. Are they part of the system? Are they separate from the system? Which kinds of tests are there? Are unit tests and integration tests different things? What about acceptance tests, functional tests, Cucumber tests, TDD tests, BDD tests, component tests, and so on?

> 讨论测试的时候，业界总会有许多自相矛盾的声音。测试应该是系统的一部分吗？还是应该独立于系统之外存在呢？测试分为哪几种？单元测试与集成测试是不同的东西吗？质量检查测试、功能性测试、Cucumber 测试、TDD 测试、BDD 测试、组件测试分别又都是什么？

It is not the role of this book to get embroiled in that particular debate, and fortunately it isn’t necessary. From an architectural point of view, all tests are the same. Whether they are the tiny little tests created by TDD, or large FitNesse, Cucumber, SpecFlow, or JBehave tests, they are architecturally equivalent.

> 在本书中我们并不想卷入对这些问题的辩论中，而且也很庆幸没有卷入的必要。因为从架构的角度来讲，所有的测试都是一样的。不论它们是小型的 TDD 测试，还是大型的 FitNesse、Cucumber、SpecFlow 或 JBehave 测试，对架构来说都是一样的。

Tests, by their very nature, follow the Dependency Rule; they are very detailed and concrete; and they always depend inward toward the code being tested. In fact, you can think of the tests as the outermost circle in the architecture. Nothing within the system depends on the tests, and the tests always depend inward on the components of the system.

> 究其本质而言，测试组件也是要遵守依赖关系原则的。因为其中总是充满了各种细节信息，非常具体，所以它始终都是向内依赖于被测试部分的代码的。事实上，我们可以将测试组件视为系统架构中最外圈的程序。它们始终是向内依赖的，而且系统中没有其他组件依赖于它们。

Tests are also independently deployable. In fact, most of the time they are deployed in test systems, rather than in production systems. So, even in systems where independent deployment is not otherwise necessary, the tests will still be independently deployed.

> 另外，测试组件是可以独立部署的。事实上，大部分测试组件都是被部署在测试环境中，而不是生产环境中。所以，即使是那些本身不需要独立部署的系统中，其测试代码也总是独立部署的。

Tests are the most isolated system component. They are not necessary for system operation. No user depends on them. Their role is to support development, not operation. And yet, they are no less a system component than any other. In fact, in many ways they represent the model that all other system components should follow.

> 测试组件通常是一个系统中最独立的组件。系统的正常运行并不需要用到测试组件，用户也不依赖于测试组件。测试组件的存在是为了支持开发过程，而不是运行过程。然而，测试组件仍然是系统中不可或缺的一个组件。事实上，测试组件在许多方面都反映了系统中其他组件所应遵循的设计模型。

## DESIGN FOR TESTABILITY 可测试性设计

The extreme isolation of the tests, combined with the fact that they are not usually deployed, often causes developers to think that tests fall outside of the design of the system. This is a catastrophic point of view. Tests that are not well integrated into the design of the system tend to be fragile, and they make the system rigid and difficult to change.

> 由于测试代码的独立性，以及往往不会被部署到生产坏境的特点，开发者常常会在系统设计中忽视测试的重要性，这种做法是极为错误的。测试如果没有被集成到系统设计中，往往是非常脆弱的，这种脆弱性会使得系统变得死板，非常难以更改。

The issue, of course, is coupling. Tests that are strongly coupled to the system must change along with the system. Even the most trivial change to a system component can cause many coupled tests to break or require changes.

> 当然，这里的关键之处就是耦合。如果测试代码与系统是强耦合的，它就得随着系统变更而变更。哪怕只是系统中组件的一点小变化，都可能会导致许多与之相耦合的测试出现问题，需要做出相应的变更。

This situation can become acute. Changes to common system components can cause hundreds, or even thousands, of tests to break. This is known as the Fragile Tests Problem.

> 这个问题可能会很严重。修改一个通用的系统组件可能会导致成百上千个测试出现问题，我们通常将这类问题称为脆弱的测试问题（fragile tests problem）。

It is not hard to see how this can happen. Imagine, for example, a suite of tests that use the GUI to verify business rules. Such tests may start on the login screen and then navigate through the page structure until they can check particular business rules. Any change to the login page, or the navigation structure, can cause an enormous number of tests to break.

> 这类问题的发生过程并不难想象。例如，假设我们有一套利用 GUI 来校验系统业务逻辑的测试。这些测试可能从登录页面开始，按照导航顺序遍历整个页面结构，直到完成某个特定的业务逻辑为止。这时候，任何针对登录页面或导航顺序的变更，都可能导致大量的测试出错。

Fragile tests often have the perverse effect of making the system rigid. When developers realize that simple changes to the system can cause massive test failures, they may resist making those changes. For example, imagine the conversation between the development team and a marketing team that requests a simple change to the page navigation structure that will cause 1000 tests to break.

> 另外，脆弱的测试还往往会让系统变得非常死板。当开发者意识到一些简单的修改就会导致大量的测试出错时，他们自然就会抵制修改。请想象一下，如果市场部门所要求的一个针对页面导航结构的简单变更会导致一千个测试出错时，开发部门会怎么说吧。

The solution is to design for testability. The first rule of software design—whether for testability or for any other reason—is always the same: Don’t depend on volatile things. GUIs are volatile. Test suites that operate the system through the GUI must be fragile. Therefore design the system, and the tests, so that business rules can be tested without using the GUI.

> 要想解决这个问题，就必须在设计中考虑到系统的可测试性。软件设计的第一条原则——不管是为了可测试性还是其他什么东西——是不变的，就是不要依赖于多变的东西。譬如，GUI 往往是多变的，因此通过 GUI 来验证系统的测试一定是脆弱的。因此，我们在系统设汁与测试设计时，应该让业务逻辑不通过 GUI 也可以被测试。

## THE TESTING API 测试专用 API

The way to accomplish this goal is to create a specific API that the tests can use to verify all the business rules. This API should have superpowers that allow the tests to avoid security constraints, bypass expensive resources (such as databases), and force the system into particular testable states. This API will be a superset of the suite of interactors and interface adapters that are used by the user interface.

> 设计这样一个系统的方法之一就是专门为验证业务逻辑的测试创建一个 API。这个 API 应该被授予超级用户权限，允许测试代码可以忽视安全限制，绕过那些成本高昂的资源（例如数据库），强制将系统设置到某种可测试的状态中。总而言之，该 API 应该成为用户界面所用到的交互器与接口适配器的一个超集。

The purpose of the testing API is to decouple the tests from the application. This decoupling encompasses more than just detaching the tests from the UI: The goal is to decouple the structure of the tests from the structure of the application.

> 设置测试 API 是为了将测试部分从应用程序中分离出来。换句话说，这种解耦动作不只是为了分隔测试部分与 UI 部分，而是要将测试代码的结构与应用程序其他部分的代码结构分开。

### STRUCTURAL COUPLING 结构性耦合

Structural coupling is one of the strongest, and most insidious, forms of test coupling. Imagine a test suite that has a test class for every production class, and a set of test methods for every production method. Such a test suite is deeply coupled to the structure of the application.

> 结构性耦合是测试代码所具有的耦合关系中最强大、最阴险的一种形式。假设我们现在有一组测试套件，它针对每个产品类都有一个对应的测试类，每个产品函数都有一个对应的测试函数。显然，该测试套件与应用程序在结构上是紧耦合的。

When one of those production methods or classes changes, a large number of tests must change as well. Consequently, the tests are fragile, and they make the production code rigid.

> 每当应用程序中的一个函数或类发生变更时，该测试套件就必须进行大量相应的修改。因此，这些测试是非常脆弱的，它们也会让产品代码变得非常死板。

The role of the testing API is to hide the structure of the application from the tests. This allows the production code to be refactored and evolved in ways that don’t affect the tests. It also allows the tests to be refactored and evolved in ways that don’t affect the production code.

> 测试专用 API 的作用就是将应用程序与测试代码解耦。这样，我们的产品代码就可以在不影响测试的情况下进行重构和演进。同样的，这种设计也允许测试代码在不影响生产代码的情况下进行重构和演进。

This separation of evolution is necessary because as time passes, the tests tend to become increasingly more concrete and specific. In contrast, the production code tends to become increasingly more abstract and general. Strong structural coupling prevents—or at least impedes—this necessary evolution, and prevents the production code from being as general, and flexible, as it could be.

> 这种对演进过程的隔离是很重要的，因为随着时间的推移，测试代码趋向于越来越具体和详细。相比之下，我们的产品代码则会趋向于越来越抽象和通用。结构性的强耦合可能会让这种必需的演进无法进行——至少会形成强烈的干扰。

### SECURITY 安全性

The superpowers of the testing API could be dangerous if they were deployed in production systems. If this is a concern, then the testing API, and the dangerous parts of its implementation, should be kept in a separate, independently deployable component.

> 当然，这种具有超级权限的测试专用 API 如果被部署到我们的产品系统中，可能会是非常危险的。如果要避免这种情况发生，应该将测试专用 API 及其对应的具体实现放置在一个单独的、可独立部署的组件中。

## CONCLUSION 本章小结

Tests are not outside the system; rather, they are parts of the system that must be well designed if they are to provide the desired benefits of stability and regression. Tests that are not designed as part of the system tend to be fragile and difficult to maintain. Such tests often wind up on the maintenance room floor—discarded because they are too difficult to maintain.

> 测试并不是独立于整个系统之外的，恰恰相反，它们是系统的一个重要组成部分。我们需要精心设计这些测试，才能让它们发挥验证系统稳定性和预防问题复发的作用。没有按系统组成部分来设计的测试代码，往往是非常脆弱且难以维护的。这种测试最后常常会被抛弃，因为它们终究会出问题。
