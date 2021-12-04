# Control Structures

Scala拥有你希望能在编程语言中找到的基本的控制结构，如下所示:

- if/then/else
- for loops
- try/catch/finally

Scala也有几个独特的控制结构，如下所示:

- match expressions
- for expressions

接下来详细讲解这些控制结构.

## "if/then/else" Structure

### 三种常见的代码示例

```scala
if (x == y) {
    doSomething()
}

// another style
if (x == y) doSomething()
```

```scala
if (x == y) {
    doSomething()
} else {
    doSomethingElse()
}
```

```scala
if (x == y) {
    doSomethingForXY()
} else if (x == z) {
    doSomethingForXZ()
} else {
    doSomethingElse()
}
```

### if 表达式总是会返回一个结果

Scala有一个好的特性就是if结构总是会返回一个结果，但是你可以忽略它返回的结果，就像如上代码示例中那样.
但是更加通用的做法是将它返回的结果指向一个变量，尤其是在函数式编程中, 如下为代码示例:

```scala
val minValue = if (x < y) x else y
```

看到这样的代码，我们很容易想到 "三元运算符", 是的，它实现了和三元运算符一样的功能，因此Scala没有再引入特殊的三元运算符

### Note: 面向表达式编程

当你写的每一个表达式都可以返回一个结果时, 我们称这种风格为 "面向表达式编程"(expression-oriented programming), 简称 EOP, 上一个代码示例就是这个风格.

相反的, 不返回结果的代码行被叫做 "语句", 这种语句的作用就是为了产生副作用<sup>1</sup>, 如下为代码示例:

```scala
if (a == b) doSomething() // This is a statements, is for side-effects
```

## "for loops" Structure

for循环最简单的使用场景就是用来遍历容器中的元素，如下是一个代码示例:

```scala
val nums = Seq(1, 2, 3, 4)          // given a sequence of integers
for (n <- nums) println(n)
```

我们也可以使用for循环进行累加运算，如下为求前100项累加的和的代码示例:

```scala
var i = 0
var sum = 0
for (i <- 1 to 100) sum += i
println(sum)                        // 5050
```

for循环也可以用来遍历Map容器，如下为代码示例:

```scala
val scores = Map(
  "Andrew" -> 100,
  "Tom" -> 95,
  "Pater" -> 60
)

for ((name, score) <- scores) println(s"Name: $name, Score: $score")
```

### The foreach method

出于遍历容器中元素并打印元素的目的，在Scala中我们同样可以使用 foreach 方法, foreach 方法在Scala大多数的容器类中都是可用的，甚至包括Map, 如下为常用的代码示例:

```scala
val nums = Seq(1, 2, 3, 4)
nums.foreach(println)
```

```scala
val scores = Map(
  "Andrew" -> 100,
  "Tom" -> 95,
  "Pater" -> 60
)

scores.foreach {
  case (name, score) => println(s"Name: $name, Score: $score")
}
```

## "for" Expressions

如何你还能想起我们在"if/then/else" Structure那一节中使用的"面向表达式编程"(EOP)以及表达式和语句之间的差异，你会发现我们上一节中使用 for 和 foreach 方法作为一种产生"副作用"的工具，我们利用他们和 println 方法将容器中元素的值打印出来.

一旦你开始用Scala工作，你会发现在函数式编程中，你能够使用相比for语句来说功能更为强大的for表达式，一定要注意区分，for语句与for表达式是不一样的，for语句用来产生副作用的场景(比如说打印输出), for表达式用来从现有容器产生容器的场景，可见如下for表达式的代码示例:

```scala
val nums = Seq(1, 2, 3, 4 )
val doubleNums = for (n <- nums) yield n * 2            // doubleNums: (2, 4, 6, 8)

// using a block of code
val doubleNums = for (n <- nums) yield { n * 2 }        // doubleNums: (2, 4, 6, 8)
```

Tips:

- `yield` 后边既可以是一个简短的表达式，也可以是一个复杂的代码块(用{}括住的部分)，一般推荐使用简单的表达式, 如果逻辑复杂非得写成多行的代码块，可以考虑将复杂的逻辑抽象出一个方法，这样`yield` 后边仍然是一个简单的表达式，只不过变成了一个方法的调用

- 注意此处的`yield`关键字, 他的意思是我要根据已有的容器产出一个新容器，这里是"产出"的意思

## "match" Expressions

Scala设计了一种"match"表达式, 它类似于Java中的`switch`语句, 如下为代码示例:

```scala
val i = 5
val numberDescription = i match {
  case 1 => "equals one"
  case 2 => "equals two"
  case 11 => "equals three"
  case _ => "else number"
}
println(numberDescription)            // else number
```

如代码示例可见, 当"i"匹配到某个case时，会返回case对应的字符串，最终将返回的字符串指向一个新的变量"numberDescription", "_" 代表的是任何其他值(除了1, 2, 11之外), 它是默认的case, 跟Java中的"default"是一个作用.

Tip:

- Scala中的"match"表达式也是有返回值的，而不仅仅是可以用作产生副作用, 这对函数式编程和友好

### 题外话 - 简单看下Scala中如何定义方法

如下代码示例定义了一个值转化的简单方法:

```scala
def convertBooleanToStringMessage(bool: Boolean): String = {
  if (bool) "true" else "false"        
}

val result = convertBooleanToStringMessage(true)    // true
```

这个方法是为了将入参的Boolean类型变量转换为对应的字符串变量

### 使用"match"表达式作为方法体

我们可以用"match"表达式重构下上一节中的"convertBooleanToStringMessage"方法，代码示例如下:

```scala
def convertBooleanToStringMessage(bool: Boolean): String = bool match {
  case true => "true"
  case false => "false"
}
```

另外提一点，Scala的"match"表达式的一个"case"语句其实可以定义多个匹配规则, 代码示例如下:

```scala
val evenOrOdd = i match {
  case 1 | 3 | 5 | 7 | 9 => println("odd")
  case 2 | 4 | 6 | 8 | 10 => println("even")
  case _ => println("some other number")
}
```

这里的"|"表示或者，用来组合多个"值"匹配规则，这么说是因为如果是多个"表达式"匹配规则的"或"，我们需要使用"||", 这一点后后续会在示例中展示.

### 在"case"语句中使用"if"表达式

if表达式和case语句的组合将会使"模式匹配"<sup>2</sup>更加的强大, 见如下代码示例:

```scala
count match {
  case 1 => println("one, a lonely number")
  case x if x == 2 || x == 3 => println("two's company, three's a crowd")
  case x if x > 3 => println("4+, that's a party")
  case _ => println("i'm guessing your number is zero or less")
}
```

这里的"||"表示或者, 用来组合多个"表达式"匹配规则, 在第二个case语句中我们可以看到，程序会将入参count指向变量x，然后使用if去判断x(这里实际上就是count的值)是否等于2或者3，这就是case语句中if表达式的用法.

对于匹配数字范围有这么一个代码示例:

```scala
i match {
  case a if 0 to 9 contains a => println("0-9 range: " + a)
  case b if 10 to 19 contains b => println("10-19 range: " + b)
  case c if 20 to 29 contains c => println("20-29 range: " + c)
  case _ => println("Hmmm...")
}
```

在实际工作中if表达式中使用引用字段是一个常见的场景, 代码示例如下:

```scala
i match {
  case x if (x.symbol == "XYZ" && x.price < 20) => buy(x)
  case x if (x.symbol == "XYZ" && x.price > 50) => sell(x)
  case x => doNothing(x)
}
```

## "try/catch/finally" Expressions

你可以使用"try/catch/finally"来捕获和管理异常. Scala中使用`case`语句来匹配可能发生的不同类型的异常, 如下是一个基本的代码示例:

```scala
var fileText = ""
try {
  fileText = openAndReadAFile(fileName)
} catch {
  case e: FileNotFoundException => println("Couldn't find that file.")
  case e: IOException           => println("Had an IOException trying to read that file")
}
```

在这个示例中，`openAndReadAFile` 方法用来打开并读取一个文件，然后将读取结果指向变量 `fileText`, Scala使用Java的`java.io.*`类操作文件，因此在这个过程中最可能抛出的异常就是`FileNotFoundException` 和 `IOException`, 这两个异常在示例的 `catch` 代码块中被捕获并处理.

在异常捕获和处理中,  `finally` 语句一般用于资源的关闭，比如文件资源、数据库连接等, 可见如下代码示例:

```scala
try {
  // your scala code here
} 
catch {
  case e: FooException => handleFooException(e)
  case e: BarException => handleBarException(e)
  case _: Throwable => println("Got some other kind of Throwable exception")
} finally {
  // your scala code here, such as closing a database connection
  // or file handle
}
```

在Scala的"try/catch/finally"语法中最棒的事情就是对于不同类型的异常处理，我们只需在`catch`代码块中使用`case`进行匹配即可，这种方式显得相当简洁，这将会使你的代码风格一致，也会提升代码的可读性.

## Referrences

- [1] Side Effects, <https://en.wikipedia.org/wiki/Side_effect_(computer_science)>
- [2] Pattern Matching, <https://en.wikipedia.org/wiki/Pattern_matching>