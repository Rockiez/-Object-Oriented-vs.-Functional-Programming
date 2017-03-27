
# Object-Oriented vs. Functional Programming
>  Bridging the Divide between Opposing Paradigms  
- - - -

## 序言
我最喜欢的行业活动就是在软件研讨会上演讲。遇到一些为自己的技术充满激情的开发者以及给予你成为一个演讲者的机会去与他们分享你的知识。 最近有一个我喜欢的演讲叫“双胞胎：FP和OOP“我在大量的研讨会和用户组集会上分享它，我甚至想在O’Reilly山直播这次分享。开发者们喜欢这个演讲因为它大量引用的电影“Twins”并讨论了一个悠久以来函数式和面向对象编程之间的关系。
这就是你所能在研讨会上讲述的内容，所以当O’Reilly联系询问我是否喜欢愿意在一个报告上扩大这个主题时，我非常兴奋。你也可以认为这是我早期在O'Reilly出版的书《Java 8 Lambdas》的简短后续。 
你可以观看发布在研讨会在线或者O'Reilly网站的演讲。 

### Object-Oriented和Functional 程序员可以相互学习什么？ 
在我们深入Lambdas和设计模式的技术本质之前，让我们先看一看技术社区。这将解释为什么比较functional和object-oriented之间的关系如此重要并富有意义。 如果你订阅过任何Hacker News，一种关于编程的论坛主题订阅，或者其他一些在线论坛，你也许会注意到在函数式程序员和使用面向对象风格的开发者之间的摩擦。他们经常听起来在互相讨论两种不同的语言，sometimes even going so far as to  throw the odd snarky insult around.有时甚至到了恶声恶气地辱骂对方的程度。
一方面，函数式程序员会经常瞧不起他们的OO同行。函数式程序可以变的非常简短和优雅，将大量的操作浓缩在非常少量的代码之中。函数式程序员会将任务置入多核场景中，你需要避开可变状态以便扩展。这种编程本质上来说是其实就是数学，而且这时每个人都可以去仅需要考量实际功能。
面向对象程序员会反驳在实际生产环境中，很少有程序员会使用函数式语言。面向对象编程对开发者而言刻意很好地向外扩展，而且作为一个企业用户，我们也知道应当如何去完成这一过程。虽然编程可以被视为应用数学的一门学科，但软件工程要求我们将技术解决方案和业务问题相匹配。OOP鼓励开发者领域建模并专注于描绘真实世界对象，这有助于减少这个差距。
当然，这些刻板的观念夸大了它们的差别。这两种程序员群体都被雇佣去解决相似的企业问题。他们都工作在相同的企业。他们真的天差地别么？
我不这么认为，我觉得我们可以从他们之间学习到很多。

### 这份报告有什么内容？
这份报道指出，有很多良好的面向对象设计的机构同样存在于函数式编程中。为了确保你能听懂我们说什么。第一章节将解释一点点关于函数式编程和Java 8 种的lambda表达式的基础。
在第二章节中，我们会看到由Robert Martin定义的SOLID理论，以及他们是如何映射到函数式语言和范例的。这表明在更高层次概念方面的相似性。
在第三章节中，我们将会看到一些行为设计模式。设计模式通常被用做一种在面向对象程序员中分享知识的单词。他们也经常遭受来自函数式程序员的批评。这里我们将看到一些最常见的面向对象设计模式是如何存在于函数式世界中。
这份指南中的大部分实例都使用Java语言写的。这并不是说Java是唯一可用的语言，实际上它甚至不是一个可以将其实践良好的语言！然而，这已经完全足够完成这个任务并使得人们能够理解。这份报道也受到Java 8 发布和语言中的lambda表达式的加入的影响。说了这么多，很多原理和概念应用到很多其他程序语言同样可行，我希望无论你所使用的程序语言是什么，都可以从中受益。
## chapter 1 lambdas: parameterizing Code by Behavior

### 为什么我们需要学习lambda表达式？
接下来两个章节，我们将深入讨论关于函数式和面向对象编程原则之间的关系，但是首先让我们先浏览一遍一些基础。我们将讨论关于一对涉及到函数式编程的语言功能关键词：lambda表达式和方法引用。

> 如果你已经拥有函数式编程的使用经历，那么你会想要跳过这个章节直到下个章节。  

我们还将谈论他们所能实现的思维的变化，这是函数式思维的关键：通过行为参数化代码。这种在函数和行为参数化方面的思考，而不是状态，才是区分函数式编程和面向对象编程的关键。理论上，这些我们在以往版本的Java中使用匿名类实现，但是很少这么做，因为这样做非常笨重且冗长。
我们也会看到Java语言中lambda表达式的语法。如我在前言中提及的那样，很多这样的理念超越了Java语言本身；我们只是将Java作为一个交流方式：一个很多开发者都熟知的常用语言。

### lambda表达式的基础
我们将会定义一个lambda表达式，可以看作是描述一个匿名函数的简单方式。我知道有很多东西需要一次解决，所以，我们将通过一些现有的Java代码示例来解释lambda表达式。Swing是一个用来写图形用户界面（GUI）的平台无关Java库。它采用了一个相当普遍的风格：为了发现你的用户做了什么，你需要注册一个事件监听。这个事件监听可以在运行一些动作来对用户的输入作出反应。（看代码1-1）

_Example 1-1. 使用一个匿名内部类来将行为和一个点击按钮联系起来。_
```java
button.addActionListener(new ActionListener() {
    public void actionPerformed(ActionEvent event) {
        System.out.println("button clicked");
    }
}); 
```

在这个例子中，我们创建一个实现了**ActionListener**类的实例对象。这个接口有唯一一个方法，**actionPerformed**，当用户真实点击屏幕上的按钮时这个方法会被**按钮**实例执行。这个匿名内部类提供了方法的实现。在Example1-1中，它所做的就是打印一条信息以示按钮被点击了。

> 这是一个_行为参数化_的实际例子——我们提供给按钮一个封装了行为的对象。  

匿名内部类被设计用来为Java开发者方便地提供或者忽略行为。不幸的是，它们被设计的还不够简单。只是一行主要的逻辑代码，却需要提供四行无关的模板代码。
冗余的模板代码不是唯一的问题，而且这些代码非常难于阅读，因为它复杂化了开发者的意图。我们不想在对象上花心思，行为才是我们真正需要去注意的。在Java 8 中，我们使用一个lambda表达式来写这个例子，如Example 1-2所示。
_Example 1-2. 使用一个lambda表达式_```java
button.addActionListener(event -> System.out.println("button clicked"));
```

不再使用一个实现了接口的对象，我们使用了一个代码块——一个没有名字的函数。**事件**是一个参数的名字，和在匿名内部类例子中的参数一样，-> 将lambda表达式的参数和函数体分开，函数体就是一些当用户点击我们的按钮时会运行的代码。
该例和匿名内部类另一个不同的地方是我们怎么声明这个**事件**变量？之前，我们需要明确提供它的类型——**ActionEvent** **事件**。在这个例子中，我们根本没有提供这个类型，这个例子却仍然编译成功了。小树林里到底藏了什么小秘密？**javac**通过上下文推断出**事件**变量的类型——从**addActionListener**的函数签名上。这意味着当类型显而易见时，你不需要明确地写出类型。我们过会儿再具体讨论着种推断行为，现在先让我们看看编写lambda表达式的不同方式。

> 相比较之前，虽然lambda方法参数需要更少的模板代码，但它们仍然是静态类型。为了可读性和友好型，你还可以包含类型声明，有的时候编译器也会无法推断类型。  

### 方法引用

一个需要注意的常用方式是创建一个lambda表达式并在它的参数上调用一个方法。如果我们想要一个lambda表达式用于获取艺术家的名字，我们会这么写：

```java
artist -> artist.getName()
```

这种惯用法十分常见，以至于有了专属的缩略语法，让你重复使用一个已经存在的方法，称为**方法引用**。如果我们使用方法引用去写上一个lambda表达式，它看起来会是这个样子的。

```java
Artist::getName
```

标准格式是 `Clasname::methodName` 。要记住虽然它是个方法，但你不需要使用圆括号，因为你没有真正意义上的调用这个方法。你相当于提供了一个用来调用方法的lambda表达式。你同样可以在使用lambda表达式的地方使用方法引用。
你也可以使用同样的缩略语法来调用构造函数。如果你要使用一个lambda表达式来创建一个Artist对象，你会这么写：

```java
(name, nationality) -> new Artist(name, nationality)
```

我们也可以使用方法引用：

```java
Artist::new
```

这段代码不仅更短，而且非常易于阅读。 **Artist::new** 立刻就能让你知道你在创建一个新的Artist对象而不需要去阅读一整行代码。这里另一个需要注意的是方法引用自动地支持多个参数，只要你相匹配的函数接口。
也可以使用这个方法创建一个数组。创建一个字符串数组可以这样：

```java
String[]::new
```

当我们第一次浏览Java8的更新时，我的一个朋友说方法引用“像开了挂一样”。他的意思是，看到我们可以像传递数据一样传递代码，能直接引用方法感觉像在作弊。
事实上，方法引用的确使函数成为一级公民。这意思是我们可以像对待数据一样传输行为并执行它。比如，我们可以将函数组合在一起。

### 总结
在这里，我们学习了一点点Java8中介绍的新语法，它减少了回调和事件处理的冗余代码。但是事实上这些改变有着更加深远的意义。我们现在可以减少传输行为代码块的冗余代码：我们将函数作为一级公民。这使得通过行为参数化代码更具吸引力。这是函数式编程的关键，事实上，它有个非正式的名字：高阶函数。
高阶函数是返回其他函数或者将函数作为参数的函数或方法。在下一个章节中，我们将看到，通过采用诸如高阶函数的函数式概念，可以简化很多面向对象编程的设计准则。然后我们将看看有多少行为设计模式做了与高阶函数相同的工作。

## CHAPTER 2 SOLID 原则
### Lambda 中的 SOLID原则
SOLID原则是设计面向对象程序的基础原则的集合。该名是一个首字母缩写词，其中每一个原则以一个字母命名：单一功能原则，开闭原则，里氏替换原则，接口隔离原则，依赖反转原则。这些原则作为指导方针帮助你构建将来易于维护和扩展的代码。
其中每种原则都对应可能存于你代码中的代码异味（code smell），它们提供一个远离问题成因的方式。很多书都已经聊过这个话题，我不会再去全面详尽地覆盖这些原则。
我曾经尝试在函数式编程领域中寻找概念上与这些所有面向对象原则相关的表示。这个目标展示了函数式和面向对象编程之间的关联，并解释面向对象程序员可以从函数式风格中学到什么。

### 单一功能原则
_程序中的每个类或者方法都应该只有单一的功能。_
改需求是软件开发中一个难以避免且常常会遇到的情况。无论是需要添加一个新功能，你对你问题领域的理解或者是客户改变主意了，或者你需要你的应用能够运行的更快，软件时刻需要改进。
当你的软件需求改变时，实现需求的类或者方法的职责也需要改变。如果你有一个拥有多个职责的类，当一个职责改变时，代码的修改会影响到同一类中的其他职责。这可能引起bug并阻碍代码的可扩展性。
让我们思考一个简单的示例代码，生成一个**资产负债表**。该程序需要从资产列表中列出资产负债表，并把它转换为一个PDF文档。如果代码实现选择将筛选列出和转换的职责放进同一个类中，那么这个类就有两个功能了。你也许会为了生成其他的输出，比如HTML，而修改转换器。你也可能会想改变资产负债表本身的详细程度。这是一个十足的理由在高层次中将问题分离到两个类中：一个筛选列出一个用来转换。
然而，单一功能原则比这个更加严格。一个类不应该只有一个职责：它应该囊括该功能。换句话说，如果我想去该表输出格式，我应该只去关注转换器类，而不是筛选列出类。
这是一部分设计思想所展示的高_内聚性_。一个类，如果它的方法和字段因关系密切而应该被一同对待，那么这个类就是内聚的。如果你尝试去分离一个内聚的类，不出意外，你只会创建出数个相互耦合的类。
现在你熟悉了单一功能原则，那么问题来了，这和lambda表达式有什么关系呢？在方法级别，lambda表达式可以将单一功能原则更简单地实现。让我们看看一些关于统计某个数之内素数数量的代码。

_Example 2-1. 统计素数——将多个职责放入一个方法中。_
```java
public long countPrimes(int upTo) { 
	long tally = 0;	for (int i = 1; i < upTo; i++) { 
		boolean isPrime = true;		for (int j = 2; j < i; j++) {			if (i % j == 0) { 
				isPrime = false;			} 
		}		if (isPrime) { 
			tally++;		} 
	}	return tally; 
}
```

很明显，我们真的把两个不同的职责放进的Example 2-1中：我们使用某个属性统计数字，并检查一个数字是否是素数。就像 Example 2-2中所示，我们可以轻易地重构去分离这两个职责。
Example 2-2 统计素数——重构出isPrime方法。
```java
public long countPrimes(int upTo) { 
	long tally = 0;	for (int i = 1; i < upTo; i++) { 
		if (isPrime(i)) {			tally++; 
		}	}	return tally; 
}private boolean isPrime(int number) {    for (int i = 2; i < number; i++) {		if (number % i == 0) { 
			return false;		} 
	}	return true; 
}
```

不幸的是，我们仍然处于一种情况，我们的代码有两个职责。大多数情况下，我们的代码仍在处理重复（looping over）的数字。如果我们遵循单一职责原则，那么迭代应该被囊括到其他地方。这也是一个很好的改进代码的时机原因。如果我们想去统计一个非常巨大的**upTo**值的素数的数量，然后我们想使这个操作并行运行。是的，线程模型是代码的一个职责。
我们使用Java 8的streams类库来重构我们的代码（看 Exmaple 2-3），它将控制循环的责任委托给库本身。这里我们使用**Range**方法来统计从0到**upTo**之间的数字，将它们筛选出检查为素数的数字。然后**count**结果。

_Example 2-3. 统计素数——使用Java 8 streams API_
```java
public long countPrimes(int upTo) {    return IntStream.range(1, upTo)                    .filter(this::isPrime)                    .count();}private boolean isPrime(int number) {    return IntStream.range(2, number)                    .allMatch(x -> (number % x) != 0);}
```

所以，我们可以使用高阶函数来帮助我们轻易地实现单一功能原则。

### 开闭原则

> 软件中的对象（类，模块，函数等等）应该对于扩展是开放的，但是对于修改是封闭的。  
> ——Bertrand Meyer  

开闭原则的总体目标和单一功能原则相似：使你的软件易于扩展。问题是对软件的单一功能请求或者修改会影响整个代码基础，比如引入了一些新的bug。通过保证已存在类可以被扩展，而不是在类内部进行修改，开闭原则可以有效地避免问题。
当你第一次听说开闭原则是，它听起来有点白日做梦。你该怎么扩展一个类的功能而不修改它的内部实现？实际的答案是，你设置一个抽象概念，并能插入实现该抽象概念的新功能。

#### 抽象
Robert Martin对开闭原则的解释是利用多态性从而轻松地依赖于一个抽象概念。让我们思考一个真实的例子。我们在编写一个软件能够计量系统性能信息并图形化计量结果。举个例子，我们有张图表描绘出计算机花了多久在用户空间，内核空间，和I/O执行上。我将会调用有具有显示指标功能的**MetericDataGraph**类。
一种设计**MetricDataGraph**类的方法是将每一种新指标从收集数据的代理中推入。所以，它的公共API看起来会像Example2-4。

_Example 2-4. MetricDataGraph 的公共API_
```javaclass MetricDataGraph {	public void updateUserTime(int value);
	public void updateSystemTime(int value); 
	public void updateIoTime(int value);}
```

但是这将意味着每次我们想要在图中添加一组新的时间点，都需要去修改**MetricDataGraph**类。我们可以通过引出一个抽象概念来解决这个问题，我将引入一个**TimeSeries**对象，它表示一系列的时间点。现在我们的**MetricDataGraph**的API变的精简，不依赖于需要显示的不同类型的指标，如Example 2-5所示。

_Example 2-5. 精简化的MetricDataGraph API_```java
    class MetricDataGraph {        public void addTimeSeries(TimeSeries values);}
```

每组指标数据可以实现**TimeSeries**接口并导入。举个例子，我们有实现类叫**UserTimeSeries**，**SystemTimeSeries**，和**IoTimesSeries**。如果我们想要添加表示机器虚拟化所损耗的CPU时间量，就需要添加一个新的**TimeSeries**实现，叫做**StealTimeSeries**。这样，**MetricDataGraph**在没有被修改的情况下被修改了。

#### 高阶函数
高阶函数也表现出与扩展开放相同的性质，尽管对于修改是封闭的。**TreadLocal**类中有个好例子。**ThreadLocal**类提供了一个特殊的变量，每个线程都有一个副本供它交互。它的静态**withInitial**方法是一个高阶函数，它接受一个的lambda表达式，该表达式表示一个用于生产初始值的工厂。
开闭原则的实现，是因为我们从**ThreadLOcal**获取了新的行为，而没有修改它。我们将不同的工厂方法输入到**winthInitial**并根据不同的行为获得一个**ThreadLocal**示例。举个例子，我们使用ThreadLocal来生成一个线程安全的**DataFormatter**，如Example 2-6所示。

_Example 2-6 一个ThreadLocal化的日期格式化工具_
```java
/* One implementation*/ThreadLocal<DateFormat> localFormatter    = ThreadLocal.withInitial(SimpleDateFormat::new);
/* Usage */DateFormat formatter = localFormatter.get();
```

我们也可以通过输入不同的lambda表达式来生成完全不同的行为。比如在Example 2-7中，我们为每个Java线程按顺序创建一个独一无二的身份标识。

```java/* Or... */AtomicInteger threadId = new AtomicInteger();ThreadLocal<Integer> localId    = ThreadLocal.withInitial(
	 	() -> threadId.getAndIncrement());
/* Usage */int idForThisThread = localId.get();

```

#### 不可变性
另一种开闭原则的理解是不遵循面向对象原则，而使用不可变对象来实现开闭原则。不可变对象是一个被创建后不能被修改的对象。
“不可变”这个术语可以有两种潜在解释：_可观测的不可变_或者_实现不可变_。可观察的不变性意味着从任何其他对象的角度来看，类是不可变的；实现不变性意味着对象永远不会改变。 实现不变性表示可观察的不变性，但是逆向不一定是正确的。
举个例子，**java.lang.String**是一个宣称自己是不可变性但实际上只是可观测的不可变，因为它缓存了它第一次调用其 hashCode 方法时计算的哈希码。这在其他类看来这完全是安全的，因为没有办法让它们观察它在构造函数中每次被计算或被缓存之间的差异。
我在这份报告中提及不可变对象的原因是他们在函数式编程中是非常常见的概念。他们可以融洽地存在于我所说的编程风格中。
不可变对象通过内部状态不可被修改来实现了开闭原则，这对它们来说，添加新的方法非常安全。新的方法不能改变对象的内部状态，所以他们对修改是封闭的，但是他们可以添加行为，所以他们对扩展是开放的。当然，你仍然需要注意在程序中的其他地方避免修改状态。
此外，不可变对象比较有趣的地方是它们本质上是线程安全的。它们没有内在状态可以被修改，所以它们可以在不同的线程之间被共享。
如果我们想想这些方法，很明显，我们已经偏离了传统的开闭原则。事实上，当Bertrand Meyer第一次介绍该原则，他是这么定义的，如果一个类已经完成，那么就不能再去修改它。在现代敏捷开发环境中，很明显，完成一个类的想法是非常过时的。业务要求和应用的使用可能要求一个类被用于一些意料之外的地方。但是这并不是无视开闭原则的理由，这些原则应该被作为启发式的指导方针，而不是虔诚地遵循它，走向极端。我们不应该苛刻地评价原定义，不管如何，他被用在一个对软件有明确清晰需求的不同的年代。
最后一个我觉得值得思考的点是 Java 8 中的内容，将开闭原则解释为提倡一种我们可以插入多个类的抽象，或者以相同的做法提倡高阶函数。因为我们的抽象需要通过调用方法的接口来表示，这种开闭原则的做法实际上只是一种多态性的使用。
在 Java 8 中，一些lambda表达式能作为一个函数结构传入一个高阶函数。这个高阶函数调用它的单一方法，它能根据传入的lambda表达式而拥有不同的行为。再说一遍，在底层，我们使用多态去实现开闭原则。

### 里氏替换原则
_令q(x)证明x对象为T类型。那么当类型S为类型T的子类时，对象y的类型为S，q(y)为真。_
里氏替换原则经常出现在非常正式的术语表达中，但事实上是一个非常普通的概念。我们可以通俗的理解为子类应该维护它们从父类继承的行为。我们可以将这个特性分为四个不同的方面：
* 子类中的前置条件不能变严格。子类应在父类运行的地方运行。
* 子类中的后置条件不能变宽松。子类应在父类生效的地方生效。
* 超类中的不变式必须保留在子类中。父类常常留下或保留某事的地方，那么子类也应该这样做。
* 历史规则：父类中不被修改的状态，子类也不允许修改。举个例子，一个可变指针不能以不可变指针为基类。
函数式编程倾向于用不同的视角看待LSP。在函数式编程中继承行为不是关键特性。如果你避免了继承层次，那么那就避免了与它们相关的问题，比如里氏替代原则旨在解决的反模式。在面向对象的社区中，实际上复合复用原则也越来越多地被接受：组合，而不是继承。

### 接口隔离原则
_一个类对另外一个类的依赖是建立在最小的接口上。_
为了正确的理解接口隔离原则，让我们考虑一个工作的例子，我们有一个白天在工厂工作，晚上回家的人。我们可以定义我们的工人接口如下：

_Example 2-8. 解析文件中的标题_
```java
interface Worker { 
	public void goHome(); 
	public void work();}
```

起初我们的**AssemblyLine**需要两种类型的**Worker**：一个**AssemblyWorker**和一个**Manager**。它们都在晚上回家，但根据它们所做的内容，工作方法有不同的实现。
随着时间流逝，无论以何种方式，工厂开始现代化，它们开始引入机器人。我们的机器人也需要在工厂中工作，但它们在白天结束时不回家。我们可以看到我们的worker接口不适用ISP，除非**goHome()**方法不是最小接口的一部分。
现在关于这个例子的有趣的一点是这一切都涉及到子类型。大多数静态类型的面向对象语言，比如Java和C++，有所谓的名义子类型。这意味着**Foo**类扩展自**Bar**类时，你需要在你的代码中看到**Foo extends Bar**。这个关系时明确的，并建立于类的名称。这同样适用接口和类。在我们的工作例子中， 我们有类似 Example 2-9 的代码去让我们的编译器了解类之间的关系。

_Example 2-9. 解析文件中的标题_
```java
    class AssemblyWorker implements Worker    class Manager implements Worker    class Robot implements Worker
```

当编译器编译器检查一个已经完成类型检查的参数变量时，它可以识别父类型为Worker，并根据这些明确的命名关系进行检查。如Example 2-10所示。

_Example 2-10.  解析文件中的标题_
```java
public void addWorker(Worker worker) {        workers.add(worker);}
    public static AssemblyLine newLine() {        AssemblyLine line = new AssemblyLine();        line.addWorker(new Manager());        line.addWorker(new AssemblyWorker());        line.addWorker(new Robot());        return line;
}
```

替代方法称为结构子类型，这里的隐含关系是基于shape/structure类型的之间。如果你在变量上调用一个名为**getFoo()** 的方法，那么那个变量只需要一个**getFoo**方法；它不需要实现实现一个接口或者扩展自其他的类。你在函数式编程或者在类似C++模版框架中系统中看到很多。像Ruby和Python语言中的鸭子类型就是结构子类的动态类型变体。
如果我们考虑假设语言中有一个使用结构子类型的例子，我们的例子会被重写为Example 2-11这样。

_Example 2-11.   解析文件中的标题_
```ruby
class StructuralWorker {	def work(step:ProductionStep) {  	  println("I'm working on: " + step.getName)  }}def addWorker(worker) { 
	workers += worker}def newLine() = {	val line = new AssemblyLine 
	line.addWorker(new Manager())
 	line.addWorker(new StructuralWorker()) 
	line.addWorker(new Robot())	line}
```

由于它去除了这些接口的显性性质，结构子类型不再需要接口隔离原则。使用这些参数，编译器就可以自动推断出最小化的接口。

### 依赖反转原则
_抽象接口不应该依赖于具体实现。而具体实现则应该依赖于抽象接口。_
一种会使得程序僵化脆弱难以修改的做法是将高级的业务逻辑和低级的代码设计粘合为一个模块。因为这两个是会随着时间而改变的不同的隐患。
依赖反转原则的目标是允许程序员编写高级业务逻辑且独立于底层的胶水代码。这允许我们使用它所依赖实现的抽象去重用高层代码。模块化和重用是双向的：我们可以替换不同的具体实现来重用高层代码，并且我们可以通过将顶层的业务逻辑分层替换来重用具体实现。
我们来看一下具体的例子，通过考虑实现构建地址簿应用所涉及到的高级分解来说明如何传统地使用依赖关系反转原理。我们的应用获取一组电子名片作为输入并将地址簿聚集存储到一些硬盘装置。
显而易见，我们可以将代码分离到单个基础模块中：
* 解析电子名片格式的名片读取器。
* 存储数据到一个文本文件的地址簿存储器。
* 从名片中拾取有用信息并将其置入地址簿的聚集模块。
我们可以图形化模块之间的关系，如下Figure 2-1所示。

![](2017-03-27/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202017-03-24%20%E4%B8%8B%E5%8D%884.24.42.png)
_Figure 2-1. 依赖关系图_

在这个系统中，聚集模型的重用更加复杂，名片读取器和地址簿存储器不依赖其他的组件。所以我们可以轻松地在其他系统中重用它们。我们也可以修改它们；举个例子，我们会想要试用一个不同的读取器，比如读取人们的Twitter个人信息；或者我们会想存储我们的地址簿到其他非文本文件，比如数据库。
为了在修改系统中的组件时的给予我们更多的灵活性，我们需要确保聚集模块的实现不依赖任何名片阅读器和地址簿存储器的具体细节。所以我们引入了名片阅读器和写入信息的抽象。聚集模块的实现依赖这些抽象。我们可以在运行时注入这些实现的具体细节。这就是依赖反转原则的工作。
在lambda表达式中，我们有遇到很多高阶函数可以应用依赖反转。比如**map**方法允许我们进行一般意义上的代码重用，根据不同的特定转化器转换数值流。**map**方法不依赖任何具体转化器的细节，而是依赖于一个抽象。在这种情况下，这个抽象就是函数接口**Function**。
一个关于依赖反转的更复杂例子是资源管理。很明显，有很多需要被管理的资源，比如数据库连接，线程循环，文件和网络连接。我将使用文件作为例子，因为它们是一种相对简单的资源，但是原理可以简单被应用到你应用中更复杂的资源。
来看一些代码，从假设的标记语言中提取标题，每个标题的位置被设计为以冒号为后缀。我们的方法是通过解析文件，从文件中提取标题，一行接着一行筛选出头部，然后关闭文件。我们将一些I/O相关的异常封装进一个友好的领域异常**HeadingLookupException**。代码看起来像Example 2-12。

_Example 2-12 解析文件中的标题_
```java
public List<String> findHeadings(Reader input) {    try (BufferedReader reader = new BufferedReader(input)) {        return reader.lines()                     .filter(line -> line.endsWith(":"))                     .map(line -> line.substring(0, line.length() - 1))                     .collect(toList());    } catch (IOException e) {        throw new HeadingLookupException(e);    }}
```

不幸的是，我们的标题查找代码把资源管理和标题处理代码组合在了一起。我们真正想写的代码是查找标题，并把文件的实现委托给另一个方法。我们可以使用**Stream<String>**作为抽象进行依赖，而不是一个特定文件。**Stream**更加安全且不会被滥用。针对文件问题，我们也想能够注入一个方法来创建我们领域异常。解决方法如Example 2-13，允许我们将领域级别的错误处理从资源管理级别的错误处理分离。

_Example 2-13 将领域逻辑和文件处理分离_
```java
public List<String> findHeadings(Reader input) {  return withLinesOf(    input,    lines -> lines.filter(line -> line.endsWith(":"))                  .map(line -> line.substring(0, line.length()-1))                  .collect(toList()),    HeadingLookupException::new);}
```

我猜你一定对**withLinesOf**一定很疑惑，请看Example 2-14.

Example 2-14 withLinesOf的定义
```java
private <T> T withLinesOf(   Reader input,   Function<Stream<String>, T> handler,   Function<IOException, RuntimeException> error) {   try (BufferedReader reader = new BufferedReader(input)) {
       return handler.apply(reader.lines());   } catch (IOException e) {       throw error.apply(e);    }}
```

**withLinesOf**以一个reader作为参数来处理底层的文件I/O。再将其传入**BufferedReader**，以让我们能够一行接着一行地读取文件。**handler**函数是任何想要使用的代码的主体。它需要一行行文本为元素的Stream作为参数。我们还需要另一个名为**error**的处理函数，它会在当I /O代码发生异常时被调用。这是构建任何领域异常都需要的。这个异常会在发生问题事件后被抛出。
总而言之，高阶函数提供了一种控制反转，这是依赖反转的一种形式。我们通过lambda表达式轻松的使用它们。其他关于依赖反转原则需要注意的地方是我们所依赖的对象不一定是一个**借口**。上面，我们通过已有的**Stream**作为原reader和文件处理的抽象。这种方法也适用于以函数式语言执行资源管理的方式——通常一个管理资源的高阶函数，传入一个应用一个开放资源的回调函数，然后关闭。实际上，如果 lambda 表达式当时可用，那么可以说 Java 7 的 try-with-resources 特性可以用一个库函数来实现的。

### 总结
我们现在已经到了 SOLID 这个部分的末尾，我想这值得去对我们所浏览过的内容作一个简短的总结。我们曾经说过单一功能原则是要求一个类应该只能被单一动机去被改变。在那最后我们还说过我们该如何使用函数式编程思维去实现它，举个例子，从应用逻辑中分离线程模型。开闭原则通常理解为利用多态来让类被写入得更加灵活。我们还讨论了函数式编程的两种技术不变性和高阶函数如何展示出同样的动态开闭。
里氏替换原则利用一组围绕子类化的限制来定义如何实现一个正确的子类。在函数式编程中，我们不强调继承。没有继承，就没有错误！接口隔离原则鼓励我们减少对多重职责的接口的依赖。而在函数式语言中，会鼓励使用结构子类型，我们免去了声明接口的必要。
最后我们讲了高阶函数也是依赖反转的一种形式。在所有情况下，SOLID原则提供了一种编写有效面向对象代码的方式，但是我们也可以考虑在函数式风格中，有哪些对等的方法。

## CHAPTER 3 设计模式
### 函数式设计模式
其他的设计堡垒之一是我们都很熟悉的设计模式思想。模式文档的可重用模版可以解决软件架构中的常见的问题。假如你发现问题有一个适用的模式，那么你可以将这个模式应用到你的环境中。在某种意义上，模式是人们认为给定问题的解决方案的最佳实践的整理。
在本章节， _我们 -不- 去讨论已有的设计模式如何变的更好，更简约，或者在某些情况下，用不同方法来实现_ 。 _在所有情况中，_ lambda表达式的应用和更多的函数式方法是模式变换的潜在因素。
### 命令模式
_命令对象_是一个对象，该对象封装了以后以后调用另一个方法所需要所有的信息。命令模式利用该对象， _旨在编写基于运行时决策的序列与执行方法的通用代码_ 。命令模式由四个类型组成，如Figure 3-1所示：
* _Receiver_
执行实际工作。
* _Command_
封装所有请求调用Receiver的信息。
* _Invoker_
控制一个或多个指令的顺序和执行。
* _Client_
创建具体的指令实例。

![](2017-03-27/0EB300F3-D910-46A1-AE93-96CCB6972FAC.png)
_Figure 3-1. 命令模式_

观察命令模式的具体例子并注意它是如何利用lambda表达式进行改进的。假设我们有一个图形界面的编辑器组件，当对其进行任何操作时会通知我们，比如**open**或者**save**，如Example 3-1所示。我们想要实现宏功能——它可以记录一组操作封装为一个单独的操作供以后运行。这就是receiver。
_Example 3-1. 一个文字编辑器会有的通用函数_
```java
interface Editor { 
	void save(); 
	void open();	void close();
}
```

在这个例子中，每一个操作，比如**open**或者**save**都是指令。 我们需要一个通用的命令接口去接受不同操作。我把这个接口称为**Action**，它描述了在区域内执行单一操作。所有命令对象需要实现的接口如下（Example 3-2）。

_Example 3-2.  所有actions都需要实现的Action接口_
```java
interface Action {    void perform();}
```

我们现在可以为每一个操作实现**Action**接口了。这些类都需要在我们的Editor上调用单一方法并将该方法的调用封装**Action**接口中。在封装操作之后，用合适的类型命名惯例来命名这些类——所以，与save方法相对应的类是Save。Example 3-3 和 Example 3-4 是我们的命令对象。

_Example 3-3 将save操作委托给编辑器底层的方法调用_
```java
class Save implements Action {    private final Editor editor;    public Save(Editor editor) {        this.editor = editor;    }    @Override    public void perform() {        editor.save();    }}
```

_Example 3-4 将open操作委托给编辑器底层的方法调用。_
```java
class Open implements Action {    private final Editor editor;    public Open(Editor editor) {        this.editor = editor;    }    @Override    public void perform() {        editor.open();    }}
```

现在我们可以实现Macro类了。该类可以record操作，并作为group运行。我们使用List来存储一组操作，然后调用ForEach方法依次执行每一个方法。Example 3-5 实现了invoker。

_Example 3-5 一个由一组可以依次调用的操作组成的宏_
```java
class Macro {    private final List<Action> actions;    public Macro() {        actions = new ArrayList<>();	  }    public void record(Action action) {        actions.add(action);	  }    public void run() {        actions.forEach(Action::perform);	  } 
}
```

当我们用代码构建一个宏时，我们添加了每个命令的实例，它们都被记录到 Macro 对象中。我们立即运行该宏，它会依次调用每一个命令。作为一个懒癌程序员，我喜欢使用宏定义命令工作流。我说了“懒？”我的意思是专注于提高生产力。**Macro**对象是我们的client的代码，如Example 3-6 所示。

_Example 3-6 使用命令模式构建宏_
```java
Macro macro = new Macro();macro.record(new Open(editor));macro.record(new Save(editor));macro.record(new Close(editor));macro.run();
```

lambda表达式有什么帮助？事实上，所有的命令类，比如Save和Open，都可以利用lambda表达式剥离它们的外壳。它们是为了传递而创建的类的行为块。整个模式会因为使用lambda表达式可以完全抛开这些类而变得很简单。Example 3-7 展示了Macro类如何使用lambda表达式来代替这些命令类。

_Example 3-7 使用lambda表达式来构建宏_
```java
Macro macro = new Macro();macro.record(() -> editor.open());macro.record(() -> editor.save());macro.record(() -> editor.close());macro.run();
```

事实上，我们甚至可以做的更好，  因为我们发现每个lambda表达式都执行单一方法调用。所以，我们可以使用方法引用，将编辑器指令发送到宏对象。（见Example 3-8）1

_Example 3-8 使用方法引用来构建宏_
```java
Macro macro = new Macro(); 
macro.record(editor::open); 
macro.record(editor::save); 
macro.record(editor::close); 
macro.run();
```

_命令模式只是缩水版的lambda表达式的开始_ 。通过使用实际的lambda表达式或者方法引用，我们可以清理代码，减少大量需要的模版并使得代码的目的更加明确。
宏只是一个如何使用命令模式的例子。它经常被用在基于组件实现的GUI系统，从而避免了函数，线程池，数据处理和向导。
> core Java中已经有一个与Action结构相同的函数接口——Runnable。我们可以在宏类中选择使用，但是在该例中，考虑使用Action作为我们域中的一部分并创造我们自己的接口会更加合适。  

### 策略模式
策略模式是基于运行时决策的软件改变算法行为的方式。根据你的实际情况决定如何实现策略模式，但百变不离其宗，其主要理念是定义一个能通用主题，它能被使用不同的算法解决，并将所有的算法抽象为同一个程序接口。
举一个压缩文件算法的例子。我们会让用户选择使用zip还是gzip算法来压缩文件，并实现通用的Compressor类来使用相应的算法。
首先，我们需要定义策略的API（见 Figure 3-2），命名为CompressionStrategy。每个压缩算法都会实现这个接口。它们有一个参数和返回值都为OutputStream实例的compress方法。其中返回的OutputStream对象是输入的压缩后版本。（见Example 3-9）

![](2017-03-27/3AC1D462-0723-4581-ACDF-7922AD491226.png)
_Figure 3-2 策略模式_

_Example 3-9 定义压缩数据用的策略接口_
```java
interface CompressionStrategy {	OutputStream compress(OutputStream data) throws IOException;}
```

我们有该接口的两种具体实现，一种是gzip，一种是zip，使用内建的Java类来编写gzip（Example 3-10）和zip（Example 3-11）文件。

_Example 3-10. 使用gzip算法来压缩数据_```java
class GzipCompressionStrategy implements CompressionStrategy {    @Override    public OutputStream compress(OutputStream data) throws IOException {        return new GZIPOutputStream(data);    }}
```

_Example 3-11. 使用zip算法来压缩数据_```java
class ZipCompressionStrategy implements CompressionStrategy {
	@Override	public OutputStream compress(OutputStream data) throws IOException {      return new ZipOutputStream(data);
	}
}
```

现在我们可以实现作为策略模式中_context_部分的**Compressor**类了。它有一个compress方法，能接收输入和输出文件，并将输入文件的压缩版本写入到输出文件中。它接收**CompressionStrategy**类型的实例作为构造函数的参数，可以在运行时选择使用哪种压缩策略并调用该参数——举个例子，获得将出决定的用户输入（见 Example 3-12）。

Example 3-12. 在构造压缩器时，需要提供压缩策略```java
class Compressor {	private final CompressionStrategy strategy;	public Compressor(CompressionStrategy strategy) { 
		this.strategy = 	strategy;	}	public void compress(Path inFile, File outFile) throws IOException {
		try (OutputStream outStream = new FileOutputStream(outFile)) {			Files.copy(inFile, strategy.compress(outStream)); 
			}		} 
}
```

我们有策略模式的传统实现，然后就可以编写客户端代码，它能根据任何一个策略创建新Compressor。（见Example 3-13）。

Example 3-13. Instantiating the Compressor using concrete strategy classes```java
Compressor gzipCompressor =	new Compressor(new GzipCompressionStrategy());gzipCompressor.compress(inFile, outFile);
Compressor zipCompressor = new Compressor(new ZipCompressionStrategy());zipCompressor.compress(inFile, outFile);
```

与先前讨论的命令模式相同，使用lambda或者方法引用可以省却该模式中的整个模版代码层。这里，我们可以删去每个策略的具体实现并指向显示该算法的方法。这里的算法由相关的 OutputStream 实现的构造函数表示。当采取这种方法，我们可以摒弃掉GzipCompressionStrategy和ZipCompressionStrategy类。Example 3-14 就是使用方法引用后代码的样子。

Example 3-14.使用方法引用代替Compressor```java
Compressor gzipCompressor = new Compressor(GZIPOutputStream::new);gzipCompressor.compress(inFile, outFile);Compressor zipCompressor = new Compressor(ZipOutputStream::new);zipCompressor.compress(inFile, outFile);
```

再用更加函数的方式思考——根据函数而不是类和对象建模——能帮助我们减少模版并简化已有的设计模式。能够组合函数式和面向对象世界观是一次伟大的胜利：你可以针对不同的情况选择适当的解决方法。

### 总结
在本节中，我们已经评估了一系列设计模式，并讨论了如何将它们全部用于 lambda 表达式。在某些方面，很多面向对象的设计模式时函数式思维的化身。拿命令模式来说。它是一个有一些不同交互组件的模式，但是该模式的本质时传递并调用行为。命令模式就是一等函数。
策略模式也有相同的地方。它将一些行为封装并传递；这又是一个模仿一等函数的设计模式。有一等函数特性的编程语言通常不需要讨论策略模式或者指令模式，但是这正是开发者所在做的。这是本书的一个重要主题。很多时候，函数式和面向对象编程语言都最终得到类似的代码模式，但却有不同的名字。

## CHAPTER 4 结尾
### 面向对象 vs. 函数式语言
在本书中，我们涵盖了很多已有面向对象设计模式相关的函数式编程思想。这些称呼名副其实。函数式编程更强调代码复用能力并通过高阶函数构成行为。毫无疑问不可变性数据可以提高代码的安全性。而且这些功能也能在面向对象环境中得到很好的支持， _共通主题得到的好处是被两种方法都可以实现。_ 我们经常寻找一种编写安全的代码并提供更多的以灵活的方式组合行为的机会。
函数式编程是思考的过程。你不必为了实用函数式风格编程而学习一门新的语言。一些语言功能经常有帮助。Java 8 引入了lambda表达式使得语言更加适应函数式编程。传统意义上的面向对象编程会将数据和行为封装在一起，现在针对行为提供了很多的支持，感谢函数式编程的思维。
具有更先进函数式思想的语言，比如Scala或者Haskell。Scala具有函数式和面向对象范式的混合能力，而Haskell专注于函数式范式的纯粹性。浏览这些语言并查看在你的问题中有哪些语言功能有用。无论如何都没有必要转移到Scala或者Haskell，它们不是使用函数式编程风格的唯一途径。当然，它们可以提供一些Java缺乏的功能，有时候值得使用不同的编程语言。
我明白这对那些用生命拥护函数式编程的人来说可能是一个有争议的意见，但软件开发不是唯心主义或宗教传播：需要为我们的客户和业务生产可靠的软件。函数式编程风格可以帮助我们实现这一目标，但它本身还没有不够完善。

### 编程语言的演进
编程语言中有一个非常有趣的趋势，语言相互之间逐渐变的更加面向对象或者更加函数式。如果我们跳进Delorean回到1980年代，会发生很多有意思的改变。老一代编程语言被淘汰，面向对象和函数式编程语言逐渐流行起来。
有趣的是，很多早期的函数式和面向对象语言的拥护者们之间相互借鉴彼此的功能。如果你问那些面向对象的纯粹主义者，最理想的编程语言是什么，她会告诉你是Smalltalk。Smalltalk 80具有lambda表达式，而且Smalltalk的集合库是原生的内建函数，其中有功能类似于map，reduce，和 filter的函数存在（尽管具有不同的名字）。
在一段时间内很多纯粹的函数式程序员会告诉你Common LISP是最理想的函数式语言。有趣的是，它有一个面向对象系统，名为CLOS（Common LISP Object System）。所以回到1980年代，有理由认为，无论何种范式都不是启蒙的唯一真正途径。
1990年代期间，编程发生了改变。面向对象编程确认成为企业用户的首要编程方式。Java和C++之类的语言逐渐受到欢迎。在1990年代末到2000年代初，Java成为非常流行的语言。在2001年，JavaOne会议有28000位出席者，那可是摇滚音乐会的大小！
在本书编写时，这种趋势将再次发生改变。流行的编程语言将不再极端偏向面向对象或者函数式。你将不用担心诸如Haskell或者Clojure之类的老顽固，总的来说，语言逐渐变的混杂。C++和 Java 8 都有添加lambda表达式，并从函数式编程中选择有用的元素加装到它们已有的面向对象能力中。不仅如此， Java 中的泛型和 C ++ 模板中采用的基本思想起源于功能编程语言。
较新的语言则从一开始就被设计为多重范式。F# 是一个很好的例子，它作为函数式风格的语言却能就面向对象方面与C# 保持一致。比如Ruby、Python和Groovy之类的语言都可以同时使用函数式和面向对象的方式编写，它们的集合API中都具有函数式功能。近十年，有大量基于JVM的新语言被开发，它们大多是函数式和面向对象的混合。Scala，Ceylon，和Kotlin在这方面都考虑到这一点。
混合体的未来：挑选函数式和面向对象中最好的功能和想法来解决手头的问题。













