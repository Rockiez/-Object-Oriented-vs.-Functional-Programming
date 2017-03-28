# 开闭原则

> 软件中的对象（类，模块，函数等等）应该对于扩展是开放的，但是对于修改是封闭的。  
> ——Bertrand Meyer  

开闭原则的总体目标和单一功能原则相似：使你的软件易于扩展。问题是对软件的单一功能请求或者修改会影响整个代码基础，比如引入了一些新的bug。通过保证已存在类可以被扩展，而不是在类内部进行修改，开闭原则可以有效地避免问题。
当你第一次听说开闭原则是，它听起来有点白日做梦。你该怎么扩展一个类的功能而不修改它的内部实现？实际的答案是，你设置一个抽象概念，并能插入实现该抽象概念的新功能。

### 抽象
Robert Martin对开闭原则的解释是利用多态性从而轻松地依赖于一个抽象概念。让我们思考一个真实的例子。我们在编写一个软件能够计量系统性能信息并图形化计量结果。举个例子，我们有张图表描绘出计算机花了多久在用户空间，内核空间，和I/O执行上。我将会调用有具有显示指标功能的**MetericDataGraph**类。
一种设计**MetricDataGraph**类的方法是将每一种新指标从收集数据的代理中推入。所以，它的公共API看起来会像Example2-4。

_Example 2-4. MetricDataGraph 的公共API_
```java
class MetricDataGraph {
	public void updateUserTime(int value);
	public void updateSystemTime(int value);
	public void updateIoTime(int value);
}
```

但是这将意味着每次我们想要在图中添加一组新的时间点，都需要去修改**MetricDataGraph**类。我们可以通过引出一个抽象概念来解决这个问题，我将引入一个**TimeSeries**对象，它表示一系列的时间点。现在我们的**MetricDataGraph**的API变的精简，不依赖于需要显示的不同类型的指标，如Example 2-5所示。

_Example 2-5. 精简化的MetricDataGraph API_
```java
    class MetricDataGraph {
        public void addTimeSeries(TimeSeries values);
}
```

每组指标数据可以实现**TimeSeries**接口并导入。举个例子，我们有实现类叫**UserTimeSeries**，**SystemTimeSeries**，和**IoTimesSeries**。如果我们想要添加表示机器虚拟化所损耗的CPU时间量，就需要添加一个新的**TimeSeries**实现，叫做**StealTimeSeries**。这样，**MetricDataGraph**在没有被修改的情况下被修改了。

### 高阶函数
高阶函数也表现出与扩展开放相同的性质，尽管对于修改是封闭的。**TreadLocal**类中有个好例子。**ThreadLocal**类提供了一个特殊的变量，每个线程都有一个副本供它交互。它的静态**withInitial**方法是一个高阶函数，它接受一个的lambda表达式，该表达式表示一个用于生产初始值的工厂。
开闭原则的实现，是因为我们从**ThreadLOcal**获取了新的行为，而没有修改它。我们将不同的工厂方法输入到**winthInitial**并根据不同的行为获得一个**ThreadLocal**示例。举个例子，我们使用ThreadLocal来生成一个线程安全的**DataFormatter**，如Example 2-6所示。

_Example 2-6 一个ThreadLocal化的日期格式化工具_
```java
/* One implementation*/
ThreadLocal<DateFormat> localFormatter
    = ThreadLocal.withInitial(SimpleDateFormat::new);
/* Usage */
DateFormat formatter = localFormatter.get();
```

我们也可以通过输入不同的lambda表达式来生成完全不同的行为。比如在Example 2-7中，我们为每个Java线程按顺序创建一个独一无二的身份标识。
/Example 2-7 一个ThreadLocal 标识符 /
```java
/* Or... */
AtomicInteger threadId = new AtomicInteger();
ThreadLocal<Integer> localId
    = ThreadLocal.withInitial(
	 	() -> threadId.getAndIncrement());
/* Usage */
int idForThisThread = localId.get();

```

### 不可变性
另一种开闭原则的理解是不遵循面向对象原则，而使用不可变对象来实现开闭原则。不可变对象是一个被创建后不能被修改的对象。
“不可变”这个术语可以有两种潜在解释：_可观测的不可变_或者_实现不可变_。可观察的不变性意味着从任何其他对象的角度来看，类是不可变的；实现不变性意味着对象永远不会改变。 实现不变性表示可观察的不变性，但是逆向不一定是正确的。
举个例子，**java.lang.String**是一个宣称自己是不可变性但实际上只是可观测的不可变，因为它缓存了它第一次调用其 hashCode 方法时计算的哈希码。这在其他类看来这完全是安全的，因为没有办法让它们观察它在构造函数中每次被计算或被缓存之间的差异。
我在这份报告中提及不可变对象的原因是他们在函数式编程中是非常常见的概念。他们可以融洽地存在于我所说的编程风格中。
不可变对象通过内部状态不可被修改来实现了开闭原则，这对它们来说，添加新的方法非常安全。新的方法不能改变对象的内部状态，所以他们对修改是封闭的，但是他们可以添加行为，所以他们对扩展是开放的。当然，你仍然需要注意在程序中的其他地方避免修改状态。
此外，不可变对象比较有趣的地方是它们本质上是线程安全的。它们没有内在状态可以被修改，所以它们可以在不同的线程之间被共享。
如果我们想想这些方法，很明显，我们已经偏离了传统的开闭原则。事实上，当Bertrand Meyer第一次介绍该原则，他是这么定义的，如果一个类已经完成，那么就不能再去修改它。在现代敏捷开发环境中，很明显，完成一个类的想法是非常过时的。业务要求和应用的使用可能要求一个类被用于一些意料之外的地方。但是这并不是无视开闭原则的理由，这些原则应该被作为启发式的指导方针，而不是虔诚地遵循它，走向极端。我们不应该苛刻地评价原定义，不管如何，他被用在一个对软件有明确清晰需求的不同的年代。
最后一个我觉得值得思考的点是 Java 8 中的内容，将开闭原则解释为提倡一种我们可以插入多个类的抽象，或者以相同的做法提倡高阶函数。因为我们的抽象需要通过调用方法的接口来表示，这种开闭原则的做法实际上只是一种多态性的使用。
在 Java 8 中，一些lambda表达式能作为一个函数结构传入一个高阶函数。这个高阶函数调用它的单一方法，它能根据传入的lambda表达式而拥有不同的行为。再说一遍，在底层，我们使用多态去实现开闭原则。
