# 单一功能原则
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
	long tally = 0;
	for (int i = 1; i < upTo; i++) {
		boolean isPrime = true;
		for (int j = 2; j < i; j++) {
			if (i % j == 0) {
				isPrime = false;
			}
		}
		if (isPrime) {
			tally++;
		}
	}
	return tally;
}
```

很明显，我们真的把两个不同的职责放进的Example 2-1中：我们使用某个属性统计数字，并检查一个数字是否是素数。就像 Example 2-2中所示，我们可以轻易地重构去分离这两个职责。
Example 2-2 统计素数——重构出isPrime方法。
```java
public long countPrimes(int upTo) {
	long tally = 0;
	for (int i = 1; i < upTo; i++) {
		if (isPrime(i)) {
			tally++;
		}
	}
	return tally;
}
private boolean isPrime(int number) {
    for (int i = 2; i < number; i++) {
		if (number % i == 0) {
			return false;
		}
	}
	return true;
}
```

不幸的是，我们仍然处于一种情况，我们的代码有两个职责。大多数情况下，我们的代码仍在处理重复（looping over）的数字。如果我们遵循单一职责原则，那么迭代应该被囊括到其他地方。这也是一个很好的改进代码的时机原因。如果我们想去统计一个非常巨大的**upTo**值的素数的数量，然后我们想使这个操作并行运行。是的，线程模型是代码的一个职责。
我们使用Java 8的streams类库来重构我们的代码（看 Exmaple 2-3），它将控制循环的责任委托给库本身。这里我们使用**Range**方法来统计从0到**upTo**之间的数字，将它们筛选出检查为素数的数字。然后**count**结果。

_Example 2-3. 统计素数——使用Java 8 streams API_
```java
public long countPrimes(int upTo) {
    return IntStream.range(1, upTo)
                    .filter(this::isPrime)
                    .count();
}
private boolean isPrime(int number) {
    return IntStream.range(2, number)
                    .allMatch(x -> (number % x) != 0);
}
```

所以，我们可以使用高阶函数来帮助我们轻易地实现单一功能原则。
