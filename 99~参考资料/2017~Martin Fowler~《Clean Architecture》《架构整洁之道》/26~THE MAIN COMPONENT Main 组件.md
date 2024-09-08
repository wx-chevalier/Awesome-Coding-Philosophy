# Chap26. THE MAIN COMPONENT Main 组件

![](https://ngte-superbed.oss-cn-beijing.aliyuncs.com/book/mono/un/CH-UN26.jpg)

In every system, there is at least one component that creates, coordinates, and oversees the others. I call this component Main.

> 在所有的系统中，都至少要有一个组件来负责创建、协调、监督其他组件的运转。我们将其称为 Main 组件。

## THE ULTIMATE DETAIL 最细节化的部分

The Main component is the ultimate detail—the lowest-level policy. It is the initial entry point of the system. Nothing, other than the operating system, depends on it. Its job is to create all the Factories, Strategies, and other global facilities, and then hand control over to the high-level abstract portions of the system.

> Main 组件是系统中最细节化的部分——也就是底层的策略，它是整个系统的初始点。在整个系统中，除了操作系统不会再有其他组件依赖于它了。Main 组件的任务是创建所有的工厂类、策略类以及其他的全局设施，并最终将系统的控制权转交给最高抽象层的代码来处理。

It is in this Main component that dependencies should be injected by a Dependency Injection framework. Once they are injected into Main, Main should distribute those dependencies normally, without using the framework.

> Main 组件中的依赖关系通常应该由依赖注入框架来注入。在该框架将依赖关系注入到 Main 组件之后，Main 组件就应该可以在不依赖于该框架的情况下自行分配这些依赖关系了。

Think of Main as the dirtiest of all the dirty components.

> 请记住，Main 组件是整个系统中细节信息最多的组件。

Consider the following Main component from a recent version of Hunt the Wumpus. Notice how it loads up all the strings that we don’t want the main body of the code to know about.

> 下面，我们来看一下最新版 Hunt the Wumpus 游戏的 Main 组件。请注意这里加载字符串的方法，这些字符串全都是我们不想让游戏主体代码了解的内容。

```java
public class Main implements HtwMessageReceiver {
  private static HuntTheWumpus game;
  private static int hitPoints = 10;
  private static final List<String> caverns = new   ArrayList<>();
  private static final String[] environments = new String[]{
    "bright",
    "humid",
    "dry",
    "creepy",
    "ugly",
    "foggy",
    "hot",
    "cold",
    "drafty",
    "dreadful"
  };

  private static final String[] shapes = new String[] {
    "round",
    "square",
    "oval",
    "irregular",
    "long",
    "craggy",
    "rough",
    "tall",
    "narrow"
  };

  private static final String[] cavernTypes = new String[] {
    "cavern",
    "room",
    "chamber",
    "catacomb",
    "crevasse",
    "cell",
    "tunnel",
    "passageway",
    "hall",
    "expanse"
  };

  private static final String[] adornments = new String[] {
   "smelling of sulfur",
    "with engravings on the walls",
    "with a bumpy floor",
    "",
    "littered with garbage",
    "spattered with guano",
    "with piles of Wumpus droppings",
    "with bones scattered around",
    "with a corpse on the floor",
    "that seems to vibrate",
    "that feels stuffy",
    "that fills you with dread"
  };
```

Now here’s the main function. Notice how it uses the HtwFactory to create the game. It passes in the name of the class, htw.game.HuntTheWumpusFacade, because that class is even dirtier than Main. This prevents changes in that class from causing Main to recompile/redeploy.

> 接下来是 main 函数。请注意这里是如何使用 HtwFactory 来构建这个游戏的。我们可以看到这里传入了 一个名为 `htw.game.HuntTheWumpusFacade` 的类。由于这个类中的细节信息比 Main 组件还多，变更也更频繁，因此这样做可以避免这个类的变更导致 Main 组件的重新编译和重新部署。

```java
public static void main(String[] args) throws IOException {
   game = HtwFactory.makeGame("htw.game.HuntTheWumpusFacade",
                                 new Main());
   createMap();
   BufferedReader br =
     new BufferedReader(new InputStreamReader(System.in));
   game.makeRestCommand().execute();
   while (true) {
     System.out.println(game.getPlayerCavern());
     System.out.println("Health: " + hitPoints + " arrows: " +
                           game.getQuiver());
     HuntTheWumpus.Command c = game.makeRestCommand();
      System.out.println(">");
      String command = br.readLine();
      if (command.equalsIgnoreCase("e"))
        c = game.makeMoveCommand(EAST);
      else if (command.equalsIgnoreCase("w"))
        c = game.makeMoveCommand(WEST);
      else if (command.equalsIgnoreCase("n"))
        c = game.makeMoveCommand(NORTH);
      else if (command.equalsIgnoreCase("s"))
        c = game.makeMoveCommand(SOUTH);
      else if (command.equalsIgnoreCase("r"))
        c = game.makeRestCommand();
      else if (command.equalsIgnoreCase("sw"))
        c = game.makeShootCommand(WEST);
      else if (command.equalsIgnoreCase("se"))
        c = game.makeShootCommand(EAST);
      else if (command.equalsIgnoreCase("sn"))
        c = game.makeShootCommand(NORTH);
      else if (command.equalsIgnoreCase("ss"))
        c = game.makeShootCommand(SOUTH);
      else if (command.equalsIgnoreCase("q"))
        return;

      c.execute();
    }
  }
```

Notice also that main creates the input stream and contains the main loop of the game, interpreting the simple input commands, but then defers all processing to other, higher-level components.

> 你还应该注意到 main 函数中创建了输入数据流，并纳入了游戏的主循环。主循环将负责处理简单的输入指令，但它会将具体的处理过程交给其他更高层次的组件来处理。

Finally, notice that main creates the map.

> 最后，Main 组件还要负责生成整个游戏的地图。

```java
private static void createMap() {
   int nCaverns = (int) (Math.random() * 30.0 + 10.0);
   while (nCaverns-- > 0)
     caverns.add(makeName());

    for (String cavern : caverns) {
      maybeConnectCavern(cavern, NORTH);
      maybeConnectCavern(cavern, SOUTH);
      maybeConnectCavern(cavern, EAST);
      maybeConnectCavern(cavern, WEST);
    }

    String playerCavern = anyCavern();
    game.setPlayerCavern(playerCavern);
    game.setWumpusCavern(anyOther(playerCavern));
    game.addBatCavern(anyOther(playerCavern));
    game.addBatCavern(anyOther(playerCavern));
    game.addBatCavern(anyOther(playerCavern));

    game.addPitCavern(anyOther(playerCavern));
    game.addPitCavern(anyOther(playerCavern));
    game.addPitCavern(anyOther(playerCavern));

    game.setQuiver(5);
  }

  // much code removed…
}
```

The point is that Main is a dirty low-level module in the outermost circle of the clean architecture. It loads everything up for the high level system, and then hands control over to it.

> 我们在这里的重点是要说明 Main 组件是整个系统中的一个底层模块，它处于整洁架构的最外圈，主要负责为系统加载所有必要的信息，然后再将控制权转交回系统的高层组件。

## CONCLUSION 本章小结

Think of Main as a plugin to the application—a plugin that sets up the initial conditions and configurations, gathers all the outside resources, and then hands control over to the high-level policy of the application. Since it is a plugin, it is possible to have many Main components, one for each configuration of your application.

> Main 组件也可以被视为应用程序的一个插件——这个插件负责设置起始状态、配置信息、加载外部资源，最后将控制权转交给应用程序的其他高层组件。另外，由于 Main 组件能以插件形式存在于系统中，因此我们可以为一个系统设计多个 Main 组件，让它们各自对应于不同的配置。

For example, you could have a Main plugin for Dev, another for Test, and yet another for Production. You could also have a Main plugin for each country you deploy to, or each jurisdiction, or each customer.

> 例如，我们既可以设计专门针对开发环境的 Main 组件，也可以设计专门针对测试的或者生产环境的 Main 组件。除此之外，我们还可以针对要部署的国家、地区甚至客户设计不同的 Main 组件。

When you think about Main as a plugin component, sitting behind an architectural boundary, the problem of configuration becomes a lot easier to solve.

> 当我们将 Main 组件视为一种插件时，用架构边界将它与系统其他部分隔离开这件事，在系统的配置上是不是就变得更容易了呢？
