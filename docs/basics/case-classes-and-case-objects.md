# Case Classes And Case Objects

## Case Classes

Scala为函数式编程提供的特性之一为`case class`. `case class`拥有所有普通`class`的功能，同时还有更多的扩展功能. 当编译器检查到`class`前边有`case`关键字时，它会为你自动生成一些功能代码, 主要包含如下几种特性:

- `case class`的构造器参数默认为公共的`val`变量, 因此每个参数变量的访问方法也会被自动生成.

- `apply` 方法将会在`case class`的伴生对象中被生成, 因此你不再需要使用`new`关键字来创建`case class`的实例.

- 默认生成`unapply` 方法，你将可以在`match`表达式中有更多的方式去使用`case class`.

- 默认生成`copy`方法，你可能在Scala的OOP中不会使用它，但在Scala的FP中一定会用到它.

- 默认生成`equals`和`hashCode`方法，你不仅可以比较对象，同时将这类对象作为集合的`key`将更加第容易.

- 默认生成`toString`方法，这个特性在你debug的时候很有用.

接下来我们将通过实例更多地了解这些特性.

### With apply you don’t need new

当你定义一个`case class`，你将不再需要使用`new`关键字来创建新的实例, 代码示例如下:

```scala
case class Person(name: String, age: Int)
```

```scala
val person = Person("Shuai Li", 18)
println(s"${person.name}'s age is ${person.age} years old.")
```

不需要`new`关键字创建对象的实例是因为`apply`方法已经在`case class`的伴生对象中被生成, `apply`方法会用与生成类的实例对象.

### No mutator methods

`case class`的构造参数默认是val不可变的，因此编译器将不会为`case class`生成字段的修改方法, 我们用如下代码示例进行实验:

```scala
val person = Person("Shuai Li", 18)
person.age = 20 // compiler error: reassignment to val, can't mutate the `age` field
```

这是因为函数式编程从来不会允许修改对象的属性，因此`case class`构造参数默认为val的设计是有道理的.

### An unapply method

`case class`有一个好的特性就是它会在它的伴生对象中自动生成`unapply`方法，用于模式匹配. 如果没有`unapply`方法, 类将无法进行模式匹配, 关于`unapply`方法的写法可见如下代码示例:

```scala
sealed trait Pet

object Pet {
  final class Dog(val name: String) extends Pet {}

  final class Cat(val name: String) extends Pet {}

  object Dog {
    def unapply(arg: Dog): Option[String] = Some(arg.name)
  }

  object Cat {
    def unapply(arg: Cat): Option[String] = Some(arg.name)
  }
}
```

我们写一个实际用例来验证他的有效性:

```scala
object Main extends App {
  def printPetName(pet: Pet): Unit =
    pet match {
      case Dog(name) => println(s"The dog's name is $name")
      case Cat(name) => println(s"The cat's name is $name")
    }

  printPetName(new Dog("Tom"))
}
```

执行如上代码我们会得到这样的输出: "The dog's name is Tom", 我们可以看到上述实现过程手动定义了`unapply`方法，但是当我们使用`case class`就不再需要手动定义`unapply`方法就可以使用模式匹配, 代码示例如下:

```scala
sealed trait Pet

object Pet {
  final case class Dog(name: String) extends Pet {}
  final case class Cat(name: String) extends Pet {}
}
```

```scala
object Main extends App {
  def printPetName(pet: Pet): Unit =
    pet match {
      case Dog(name) => println(s"The dog's name is $name")
      case Cat(name) => println(s"The cat's name is $name")
    }

  printPetName(Dog("Tom"))
}
```

我们可以得到同样的输出: "The dog's name is Tom".

我们可以发现所有的`unapply`方法的类型签名符合某个标准。从技术角度讲，这些示例中显示的特定类型的模式匹配称为构造函数模式。

Scala标准是`unapply`方法返回一个Option元组，这个元祖中返回`case class`构造函数字段, 代码示例中的 `Option[String]`就是这样的元祖，只不过我们的`case class`构造函数只有一个字段而已.

### copy method

`copy`方法也是被自动生成的, 在如下场景中`copy`方法是非常有帮助的:

- 克隆一个对象
- 在克隆过程中更新一个或多个字段

可见如下代码示例:

```scala
val oldDog = Dog("Tom")
val newDog = oldDog.copy(name = "Peter")
```

大家可能疑惑为什么重点强调`copy`方法，这是因为在函数式编程中你永远不会修改对象的数据，所有的对象均为不可变对象, 因此我们需要使用`copy`来从一个现存的示例创建一个新的实例，这个过程也称为 "复制时更新".

### equals and hashCode methods

`case class`也有自动生成的`equals`和`hashCode`方法, 因此它的实例之间可以进行比较, 见如下代码示例:

```scala
scala> case class Person(name: String, relation: String)
defined class Person

scala> val christina = Person("Christina", "niece")
christina: Person = Person(Christina,niece)

scala> val hannah = Person("Hannah", "niece")
hannah: Person = Person(Hannah,niece)

scala> christina == hannah
res1: Boolean = false
```

这些方法会帮助你更容易地在容器中使用你的对象，比如`set`、`map`, 通常情况下你可以在函数式编程中直接将对象作为key.

### toString methods

`case class`也会有一个默认的`toString`方法实现，它至少能在Debug的时候帮助到你.

### Summary

尽管所有这些特性对函数式编程都有很大好处，正如他们在《Scala 编程（Odersky、Spoon 和 Venners）一书中所写的那样，“案例类的最大优势在于它们支持模式匹配。”模式匹配是函数式编程(FP)语言的一个主要特性，`Scala`的`case class`提供了一种在匹配表达式和其他领域实现模式匹配的简单方法.

## Case Objects

在了解`case object`之前，我们应该了解一些关于“常规”的Scala对象的背景知识. 一般情况下当我们想创建一个单例对象时，会使用Scala对象. "与类的单个实例无关的方法和值应该属于单例对象，使用关键字 object 而不是 class 表示."

一个常见的`object`使用场景是创建一个“工具集”对象时，代码示例如下：

```scala
object TimeOps {
  def convertDate2Timestamp(date: String, pattern: String): Long = ...
  def dayOfWeek(date: String): Int = ...
  def isReskDay(date: String): Boolean = ...
}
```

这是使用`object`结构的通用方式.

### Case objects

`case object`类似于`object`，就像`case class`比普通的`class`具有更多特性一样，`case object`比普通的`object`具有更多特性。其特性如下：

- 它是可序列化的
- 它具有默认的`hashCode`方法实现
- 它具有改进的`toString`方法实现

正是由于这些特性，`case object`主要在两个场景使用：

- 创建枚举时
- 为要在其他对象之间传递的"消息"创建容器时（例如使用 [Akka](https://akka.io/) actor 库）

### Creating enumerations with case objects

Scala中组织枚举的具体方式，见如下代码示例:

```scala
sealed trait Channel

object Channel {
  case object Sold     extends Channel
  case object Leased   extends Channel
}
```

我们可以同如下代码所示使用这些枚举:

```scala
case class House (
  price: BigDecimal,
  channel: Channel,
  area: Double
)
```

### Using case objects as messages

`case object`的另一个使用场景是，当你想要对"消息"的概念进行建模时. 例如，假设你正在编写一个像苹果Siri这样的应用程序，并且你希望能够传递"说话"消息，例如"说出随附的文本"，"停止说话"，"暂停"和"恢复"。在 Scala 中，你可以为这些消息创建单例对象，代码示例如下:

```scala
case class StartSpeakingMessage(textToSpeak: String)
case object StopSpeakingMessage
case object PauseSpeakingMessage
case object ResumeSpeakingMessage
```

注意，`StartSpeakMessage`被定义为`case class`而不是`case object`. 这是因为对象不能有任何构造函数参数.

给定这些消息，如果Siri是使用Akka库编写的，那么您将在"speak"类中找到这样的代码：

```scala
class Speak extends Actor {
  def receive = {
    case StartSpeakingMessage(textToSpeak) =>
        // code to speak the text
    case StopSpeakingMessage =>
        // code to stop speaking
    case PauseSpeakingMessage =>
        // code to pause speaking
    case ResumeSpeakingMessage =>
        // code to resume speaking
  }
}
```

如你所见, 这是在 Scala 应用程序中传递消息的好方法.
