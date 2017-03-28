# 依赖反转原则


_抽象接口不应该依赖于具体实现。而具体实现则应该依赖于抽象接口。_
一种会使得程序僵化脆弱难以修改的做法是将高级的业务逻辑和低级的代码设计粘合为一个模块。因为这两个是会随着时间而改变的不同的隐患。
依赖反转原则的目标是允许程序员编写高级业务逻辑且独立于底层的胶水代码。这允许我们使用它所依赖实现的抽象去重用高层代码。模块化和重用是双向的：我们可以替换不同的具体实现来重用高层代码，并且我们可以通过将顶层的业务逻辑分层替换来重用具体实现。
我们来看一下具体的例子，通过考虑实现构建地址簿应用所涉及到的高级分解来说明如何传统地使用依赖关系反转原理。我们的应用获取一组电子名片作为输入并将地址簿聚集存储到一些硬盘装置。
显而易见，我们可以将代码分离到单个基础模块中：
* 解析电子名片格式的名片读取器。
* 存储数据到一个文本文件的地址簿存储器。
* 从名片中拾取有用信息并将其置入地址簿的聚集模块。
我们可以图形化模块之间的关系，如下Figure 2-1所示。

![](CHAPTER%202%20SOLID%20%E5%8E%9F%E5%88%99/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202017-03-24%20%E4%B8%8B%E5%8D%884.24.42.png)
_Figure 2-1. 依赖关系图_

在这个系统中，聚集模型的重用更加复杂，名片读取器和地址簿存储器不依赖其他的组件。所以我们可以轻松地在其他系统中重用它们。我们也可以修改它们；举个例子，我们会想要试用一个不同的读取器，比如读取人们的Twitter个人信息；或者我们会想存储我们的地址簿到其他非文本文件，比如数据库。
为了在修改系统中的组件时的给予我们更多的灵活性，我们需要确保聚集模块的实现不依赖任何名片阅读器和地址簿存储器的具体细节。所以我们引入了名片阅读器和写入信息的抽象。聚集模块的实现依赖这些抽象。我们可以在运行时注入这些实现的具体细节。这就是依赖反转原则的工作。
在lambda表达式中，我们有遇到很多高阶函数可以应用依赖反转。比如**map**方法允许我们进行一般意义上的代码重用，根据不同的特定转化器转换数值流。**map**方法不依赖任何具体转化器的细节，而是依赖于一个抽象。在这种情况下，这个抽象就是函数接口**Function**。
一个关于依赖反转的更复杂例子是资源管理。很明显，有很多需要被管理的资源，比如数据库连接，线程循环，文件和网络连接。我将使用文件作为例子，因为它们是一种相对简单的资源，但是原理可以简单被应用到你应用中更复杂的资源。
来看一些代码，从假设的标记语言中提取标题，每个标题的位置被设计为以冒号为后缀。我们的方法是通过解析文件，从文件中提取标题，一行接着一行筛选出头部，然后关闭文件。我们将一些I/O相关的异常封装进一个友好的领域异常**HeadingLookupException**。代码看起来像Example 2-12。

_Example 2-12 解析文件中的标题_
```java
public List<String> findHeadings(Reader input) {
    try (BufferedReader reader = new BufferedReader(input)) {
        return reader.lines()
                     .filter(line -> line.endsWith(":"))
                     .map(line -> line.substring(0, line.length() - 1))
                     .collect(toList());
    } catch (IOException e) {
        throw new HeadingLookupException(e);
    }
}
```

不幸的是，我们的标题查找代码把资源管理和标题处理代码组合在了一起。我们真正想写的代码是查找标题，并把文件的实现委托给另一个方法。我们可以使用**Stream<String>**作为抽象进行依赖，而不是一个特定文件。**Stream**更加安全且不会被滥用。针对文件问题，我们也想能够注入一个方法来创建我们领域异常。解决方法如Example 2-13，允许我们将领域级别的错误处理从资源管理级别的错误处理分离。

_Example 2-13 将领域逻辑和文件处理分离_
```java
public List<String> findHeadings(Reader input) {
  return withLinesOf(
    input,
    lines -> lines.filter(line -> line.endsWith(":"))
                  .map(line -> line.substring(0, line.length()-1))
                  .collect(toList()),
    HeadingLookupException::new);
}
```

我猜你一定对**withLinesOf**一定很疑惑，请看Example 2-14.

Example 2-14 withLinesOf的定义
```java
private <T> T withLinesOf(
   Reader input,
   Function<Stream<String>, T> handler,
   Function<IOException, RuntimeException> error) {
   try (BufferedReader reader = new BufferedReader(input)) {
       return handler.apply(reader.lines());
   } catch (IOException e) {
       throw error.apply(e);
    }
}
```

**withLinesOf**以一个reader作为参数来处理底层的文件I/O。再将其传入**BufferedReader**，以让我们能够一行接着一行地读取文件。**handler**函数是任何想要使用的代码的主体。它需要一行行文本为元素的Stream作为参数。我们还需要另一个名为**error**的处理函数，它会在当I /O代码发生异常时被调用。这是构建任何领域异常都需要的。这个异常会在发生问题事件后被抛出。
总而言之，高阶函数提供了一种控制反转，这是依赖反转的一种形式。我们通过lambda表达式轻松的使用它们。其他关于依赖反转原则需要注意的地方是我们所依赖的对象不一定是一个**借口**。上面，我们通过已有的**Stream**作为原reader和文件处理的抽象。这种方法也适用于以函数式语言执行资源管理的方式——通常一个管理资源的高阶函数，传入一个应用一个开放资源的回调函数，然后关闭。实际上，如果 lambda 表达式当时可用，那么可以说 Java 7 的 try-with-resources 特性可以用一个库函数来实现的。
