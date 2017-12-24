---
title: 第一章  你好lambda 表达式
tags: 翻译,java,函数式
grammar_cjkRuby: true
---


我们java编程的方式正在为一个非凡的大转变做准备。
我们每天的工作将会变得简单，轻松，易于表达出来，这种java新的编程方式在其他编程语言出现了数十年。在java中有了这些工具，我们将会以更少的错误代价，更容易的写出简洁，优雅，富有表达力的程序。我们可以利用这个增强某些策略，只用简单的几行代码就可以实现普通的设计模式。
在这本书中，我们将使用平常作为开发人员日常用到的编程例子来探索函数式编程的内容。在我们跳入优雅的方式之前，这种新的设计和编码方式之前，让我们来讨论为什么函数式更好。
### 改变你思考的方式

命令步骤式的方式-这也是Java最初提供给我们的方式。以这样的方式，我们告诉Java每一步我们想让他做什么，然后我们忠实的看他运行完每一步。这种方式完成的非常好，但是有一点低端。这样做代码将会变得非常啰嗦，我们经常希望语言可以变得更智能一些。
我们希望这样，告诉编程语言，声明一些东西，告诉他我们想要什么，而不是我们告诉他如何一步一步的做。万幸的是，现在的Java可以帮我们做了。让我们通过几个例子来看看他带来的好处，还有不同实现方式的区别吧。

#### 习以为常的方式
让我们以两个简单的案例来开始吧，这是一个命令式的程序，从一个citys集合中找出是不是含有chicago，别忘记了书末尾的如何阅读代码的提示。

``` java
boolean found = false;
for(String city : cities) {
    if(city.equals("Chicago")) {
        found = true;
        break;
    }
}
System.out.println("Found chicago?:" + found);
```
这个命令式的版本非常的脏乱和低层次，它有几个移动的部分。我们首先初始化一个有异味的boolean标记叫做found，然后遍历集合的每个元素，如果我们找到了我们要找的元素，我们就把标记设置为true，并且退出循环体，最后我们打印我们的结果。
#### 一个更好的方式
作为善于观察的Java程序员，我们一看到这段代码，我们可以快速的将它转化为简明，易于阅读的代码，像这样：

```java
System.out.println("Found chicago?:" + cities.contains("Chicago"));
```
这是声明式代码的一个例子，contains()函数帮我们解决了我们的业务逻辑。
#### 微弱的改进
这种变化在一下几个方面改善了我们的代码:

 - 变量周围没有异味
 - 迭代过程是包含在内部的
 - 更加整洁
 - 更清晰的表达：专注我们需要注意的
 - 更少的额阻碍：代码紧密的贴合我们的业务目的
 - 更少的错误倾向
 - 更容易理解和掌握
#### 比简单更进一步
 就这么简单，声明式的函数来检测一个集合中是否包含一个元素在Java中是由来已久的。现在想象不使用命令式的编程方法来使用更更先进的操作。比如解析文件，操作数据库，对web应用发起一个请求，并发编程等等。Java现在不但可以为小例子写出简洁，优雅，少错误的代码，甚至是我们的整个应用。
 
#### 老方式
让我们看另外的一个例子。我们先定义一个价格的集合，然后试着打折后的价格总数是多少。
``` java
final List<BigDecimal> prices = Arrays.asList(
	new BigDecimal("10"), new BigDecimal("30"), new BigDecimal("17"),
	new BigDecimal("20"), new BigDecimal("15"), new BigDecimal("18"),
	new BigDecimal("45"), new BigDecimal("12"));

```
假设我们被要求假如价格大于20刀，就可以打九折。首先，看看我们习惯性的用Java怎么做的。
``` java
BigDecimal totalOfDiscountedPrices = BigDecimal.ZERO;
for(BigDecimal price : prices) {
	if(price.compareTo(BigDecimal.valueOf(20)) > 0)
	totalOfDiscountedPrices =totalOfDiscountedPrices.add(price.multiply(BigDecimal.valueOf(0.9)));
}
System.out.println("Total of discounted prices: " + totalOfDiscountedPrices);
```
似曾相识的代码，我们首先用一个可变的变量来表示可以打折的价格数量。然后遍历整个价格数，取出每一个大于20$的价格，计算每一个打折后的价格，把他们加起来，最后打印出最后得到的价格总数。这是最后得出的数字：
``` java
Total of discounted prices: 67.5
```
是的，它达到我们的要求了。但是代码还是有的’脏‘。我们的代码逻辑没有错，我们不得不用我们拥有的，但是这个代码是相当的低端，它饱受原始的困扰（低端），并且挑战着第一职责准则（一种软件模块构建准则）。那些我们在家工作，必须让这些代码远离那些渴望成为程序员的孩子们的眼睛，对于他们也许会惊愕并叹息道，这就是你赖以为生做的东西？233

#### 一种更好的方式again
现在我们可以做的更好，更好的多。我们的代码像是硬性要求的说明书一样，这会有助于减少业务需求和实现它的代码间的差距，更进一步的减少需求被误解的可能。比起Java创建一个可变变量，然后重复的去声明它，让我们以一种更高层次的抽象来讨论他，下面就是代码。
``` java
final BigDecimal totalOfDiscountedPrices =
prices.stream()
	.filter(price -> price.compareTo(BigDecimal.valueOf(20)) > 0)
	.map(price -> price.multiply(BigDecimal.valueOf(0.9)))
	.reduce(BigDecimal.ZERO, BigDecimal::add);
System.out.println("Total of discounted prices: " + totalOfDiscountedPrices);
```
让我们大声的读出来，过滤出大于20$的价格，将这些价格通过函数映射为打折后的价格，最后把他们加起来。代码以一种我们要求的方式和逻辑一起流动。这个代码是简洁的，但是我们从Java8中应用了非常多的东西。我们在价格列表上调用了stream()函数，这打开了拥有丰富功能的迭代器的门，我们将在后面讨论。与明确的迭代价格列表相比，我们使用了非常少的方法，比如filter() 和 map(),不想我们在Java和jdk中使用的方法，这些方法要求匿名函数（lambda表达式）作为参数，我们调用reduce()方法来计算map出来的价格。

这个循环看上去更像是在contains()下的函数，但是filter和map函数是要更复杂的。对于在价格列表中的价格，它调用了lambda表达式，然后把他们加入到一个新的集合，然后在这个新的集合上调用reduce，得到最后的结果。这是这个版本的代码得到的结果。
```java
Total of discounted prices: 67.5 
```
#### 改善点
对比习惯性的写法有很大的改善：

 - 非常漂亮的结构，而不是杂乱无章的
 - 没有低层次的操作
 - 非常容易增强和改变代码逻辑
 - 高效，惰性计算迭代
 - 在需要并行的地方非常容易实现
 之后让我们讨论，Java是怎样提供这些改进的。

#### Lambda来拯救
lambda表达式是将我们从命令式编程的激战中解救出来的功能点，通过改变我们编程的方式，通过Java中一个已有的特色，我们写出的代码不但优雅简洁，而且很少的错误倾向，更高效，容易优化，容易进行并行化计算。
### 函数式代码的巨大好处
