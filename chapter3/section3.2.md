# 命令模式

_命令对象_是一个对象，该对象封装了以后以后调用另一个方法所需要所有的信息。命令模式利用该对象， _旨在编写基于运行时决策的序列与执行方法的通用代码_ 。命令模式由四个类型组成，如Figure 3-1所示：
* _Receiver_
执行实际工作。
* _Command_
封装所有请求调用Receiver的信息。
* _Invoker_
控制一个或多个指令的顺序和执行。
* _Client_
创建具体的指令实例。

![](CHAPTER%203%20%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F/0EB300F3-D910-46A1-AE93-96CCB6972FAC.png)
_Figure 3-1. 命令模式_

观察命令模式的具体例子并注意它是如何利用lambda表达式进行改进的。假设我们有一个图形界面的编辑器组件，当对其进行任何操作时会通知我们，比如**open**或者**save**，如Example 3-1所示。我们想要实现宏功能——它可以记录一组操作封装为一个单独的操作供以后运行。这就是receiver。
_Example 3-1. 一个文字编辑器会有的通用函数_
```java
interface Editor {
	void save();
	void open();
	void close();
}
```

在这个例子中，每一个操作，比如**open**或者**save**都是指令。 我们需要一个通用的命令接口去接受不同操作。我把这个接口称为**Action**，它描述了在区域内执行单一操作。所有命令对象需要实现的接口如下（Example 3-2）。

_Example 3-2.  所有actions都需要实现的Action接口_
```java
interface Action {
    void perform();
}
```

我们现在可以为每一个操作实现**Action**接口了。这些类都需要在我们的Editor上调用单一方法并将该方法的调用封装**Action**接口中。在封装操作之后，用合适的类型命名惯例来命名这些类——所以，与save方法相对应的类是Save。Example 3-3 和 Example 3-4 是我们的命令对象。

_Example 3-3 将save操作委托给编辑器底层的方法调用_
```java
class Save implements Action {
    private final Editor editor;
    public Save(Editor editor) {
        this.editor = editor;
    }
    @Override
    public void perform() {
        editor.save();
    }
}
```

_Example 3-4 将open操作委托给编辑器底层的方法调用。_
```java
class Open implements Action {
    private final Editor editor;
    public Open(Editor editor) {
        this.editor = editor;
    }
    @Override
    public void perform() {
        editor.open();
    }
}
```

现在我们可以实现Macro类了。该类可以record操作，并作为group运行。我们使用List来存储一组操作，然后调用ForEach方法依次执行每一个方法。Example 3-5 实现了invoker。

_Example 3-5 一个由一组可以依次调用的操作组成的宏_
```java
class Macro {
    private final List<Action> actions;
    public Macro() {
        actions = new ArrayList<>();
	  }
    public void record(Action action) {
        actions.add(action);
	  }
    public void run() {
        actions.forEach(Action::perform);
	  }
}
```

当我们用代码构建一个宏时，我们添加了每个命令的实例，它们都被记录到 Macro 对象中。我们立即运行该宏，它会依次调用每一个命令。作为一个懒癌程序员，我喜欢使用宏定义命令工作流。我说了“懒？”我的意思是专注于提高生产力。**Macro**对象是我们的client的代码，如Example 3-6 所示。

_Example 3-6 使用命令模式构建宏_
```java
Macro macro = new Macro();
macro.record(new Open(editor));
macro.record(new Save(editor));
macro.record(new Close(editor));
macro.run();
```

lambda表达式有什么帮助？事实上，所有的命令类，比如Save和Open，都可以利用lambda表达式剥离它们的外壳。它们是为了传递而创建的类的行为块。整个模式会因为使用lambda表达式可以完全抛开这些类而变得很简单。Example 3-7 展示了Macro类如何使用lambda表达式来代替这些命令类。

_Example 3-7 使用lambda表达式来构建宏_
```java
Macro macro = new Macro();
macro.record(() -> editor.open());
macro.record(() -> editor.save());
macro.record(() -> editor.close());
macro.run();
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
