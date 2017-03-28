# 策略模式


策略模式是基于运行时决策的软件改变算法行为的方式。根据你的实际情况决定如何实现策略模式，但百变不离其宗，其主要理念是定义一个能通用主题，它能被使用不同的算法解决，并将所有的算法抽象为同一个程序接口。
举一个压缩文件算法的例子。我们会让用户选择使用zip还是gzip算法来压缩文件，并实现通用的Compressor类来使用相应的算法。
首先，我们需要定义策略的API（见 Figure 3-2），命名为CompressionStrategy。每个压缩算法都会实现这个接口。它们有一个参数和返回值都为OutputStream实例的compress方法。其中返回的OutputStream对象是输入的压缩后版本。（见Example 3-9）

![](CHAPTER%203%20%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F/3AC1D462-0723-4581-ACDF-7922AD491226.png)
_Figure 3-2 策略模式_

_Example 3-9 定义压缩数据用的策略接口_
```java
interface CompressionStrategy {
	OutputStream compress(OutputStream data) throws IOException;
}
```

我们有该接口的两种具体实现，一种是gzip，一种是zip，使用内建的Java类来编写gzip（Example 3-10）和zip（Example 3-11）文件。

_Example 3-10. 使用gzip算法来压缩数据_
```java
class GzipCompressionStrategy implements CompressionStrategy {
    @Override
    public OutputStream compress(OutputStream data) throws IOException {
        return new GZIPOutputStream(data);
    }
}
```

_Example 3-11. 使用zip算法来压缩数据_
```java
class ZipCompressionStrategy implements CompressionStrategy {
	@Override
	public OutputStream compress(OutputStream data) throws IOException {
      return new ZipOutputStream(data);
	}
}
```

现在我们可以实现作为策略模式中_context_部分的**Compressor**类了。它有一个compress方法，能接收输入和输出文件，并将输入文件的压缩版本写入到输出文件中。它接收**CompressionStrategy**类型的实例作为构造函数的参数，可以在运行时选择使用哪种压缩策略并调用该参数——举个例子，获得将出决定的用户输入（见 Example 3-12）。

Example 3-12. 在构造压缩器时，需要提供压缩策略
```java
class Compressor {
	private final CompressionStrategy strategy;
	public Compressor(CompressionStrategy strategy) {
		this.strategy = 	strategy;
	}
	public void compress(Path inFile, File outFile) throws IOException {
		try (OutputStream outStream = new FileOutputStream(outFile)) {
			Files.copy(inFile, strategy.compress(outStream));
			}
		}
}
```

我们有策略模式的传统实现，然后就可以编写客户端代码，它能根据任何一个策略创建新Compressor。（见Example 3-13）。

Example 3-13. Instantiating the Compressor using concrete strategy classes
```java
Compressor gzipCompressor =
	new Compressor(new GzipCompressionStrategy());
gzipCompressor.compress(inFile, outFile);
Compressor zipCompressor = new Compressor(new ZipCompressionStrategy());
zipCompressor.compress(inFile, outFile);
```

与先前讨论的命令模式相同，使用lambda或者方法引用可以省却该模式中的整个模版代码层。这里，我们可以删去每个策略的具体实现并指向显示该算法的方法。这里的算法由相关的 OutputStream 实现的构造函数表示。当采取这种方法，我们可以摒弃掉GzipCompressionStrategy和ZipCompressionStrategy类。Example 3-14 就是使用方法引用后代码的样子。

Example 3-14.使用方法引用代替Compressor
```java
Compressor gzipCompressor = new Compressor(GZIPOutputStream::new);
gzipCompressor.compress(inFile, outFile);
Compressor zipCompressor = new Compressor(ZipOutputStream::new);
zipCompressor.compress(inFile, outFile);
```

再用更加函数的方式思考——根据函数而不是类和对象建模——能帮助我们减少模版并简化已有的设计模式。能够组合函数式和面向对象世界观是一次伟大的胜利：你可以针对不同的情况选择适当的解决方法。
