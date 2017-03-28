# 接口隔离原则

_一个类对另外一个类的依赖是建立在最小的接口上。_
为了正确的理解接口隔离原则，让我们考虑一个工作的例子，我们有一个白天在工厂工作，晚上回家的人。我们可以定义我们的工人接口如下：

_Example 2-8. 解析文件中的标题_
```java
interface Worker {
	public void goHome();
	public void work();
}
```

起初我们的**AssemblyLine**需要两种类型的**Worker**：一个**AssemblyWorker**和一个**Manager**。它们都在晚上回家，但根据它们所做的内容，工作方法有不同的实现。
随着时间流逝，无论以何种方式，工厂开始现代化，它们开始引入机器人。我们的机器人也需要在工厂中工作，但它们在白天结束时不回家。我们可以看到我们的worker接口不适用ISP，除非**goHome()**方法不是最小接口的一部分。
现在关于这个例子的有趣的一点是这一切都涉及到子类型。大多数静态类型的面向对象语言，比如Java和C++，有所谓的名义子类型。这意味着**Foo**类扩展自**Bar**类时，你需要在你的代码中看到**Foo extends Bar**。这个关系时明确的，并建立于类的名称。这同样适用接口和类。在我们的工作例子中， 我们有类似 Example 2-9 的代码去让我们的编译器了解类之间的关系。

_Example 2-9. 解析文件中的标题_
```java
    class AssemblyWorker implements Worker
    class Manager implements Worker
    class Robot implements Worker
```

当编译器编译器检查一个已经完成类型检查的参数变量时，它可以识别父类型为Worker，并根据这些明确的命名关系进行检查。如Example 2-10所示。

_Example 2-10.  解析文件中的标题_
```java
public void addWorker(Worker worker) {
        workers.add(worker);
}
    public static AssemblyLine newLine() {
        AssemblyLine line = new AssemblyLine();
        line.addWorker(new Manager());
        line.addWorker(new AssemblyWorker());
        line.addWorker(new Robot());
        return line;
}
```

替代方法称为结构子类型，这里的隐含关系是基于shape/structure类型的之间。如果你在变量上调用一个名为**getFoo()** 的方法，那么那个变量只需要一个**getFoo**方法；它不需要实现实现一个接口或者扩展自其他的类。你在函数式编程或者在类似C++模版框架中系统中看到很多。像Ruby和Python语言中的鸭子类型就是结构子类的动态类型变体。
如果我们考虑假设语言中有一个使用结构子类型的例子，我们的例子会被重写为Example 2-11这样。

_Example 2-11.   解析文件中的标题_
```ruby
class StructuralWorker {
	def work(step:ProductionStep) {
  	  println("I'm working on: " + step.getName)
  }
}
def addWorker(worker) {
	workers += worker
}
def newLine() = {
	val line = new AssemblyLine
	line.addWorker(new Manager())
 	line.addWorker(new StructuralWorker())
	line.addWorker(new Robot())
	line
}
```

由于它去除了这些接口的显性性质，结构子类型不再需要接口隔离原则。使用这些参数，编译器就可以自动推断出最小化的接口。
