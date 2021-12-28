# Traits

trait是Scala中除了class外的另一个面向对象编程特性，你可以像Java中的interface那样使用trait，仅仅把它当做一个接口; 同时，你也可以像Java中的抽象类(abstract class)那样使用trait, 在trait中定义完整的方法，而不仅仅只是声明方法. Scala的class也能继承一个或者多个trait.

## Using Scala Traits as Interfaces

当我们将`trait`当做接口时，我们可以在`trait`里仅仅定义一些功能的接口，而并不为这些功能实现任何的行为.

### A simple example

我们从一个简单的例子开始，想象一下你想要写一些代码为动物构建模型, 比如猫、狗, 我们可以先提取一些动物的通用特性, 比如动物都会叫，动物都会跑, 但是叫的声音不一样，跑的方式也不一样, 因此我们可以将叫和跑抽象成两个trait, 示例代码如下:

```scala
trait Speaker {
    def speak(): String
}

trait Runner {
    def run(): Unit
}
```

### Extending traits

上一节中的`Speaker`和`Runner`中仅仅是声明了功能，接下来我们将通过具体某一种动物来实现这些trait中定义的功能, 示例代码如下:

```scala
class Dog extends Speaker with Runner {
  override def speak() = "dog Woof!"

  override def run(): Unit = println("dog Run！")
}
```

如上代码所示就是Scala中`class`继承`trait`的方式了, 当需要继承多个trait时，使用`extends`关键字去继承第一个trait, 使用`with`关键字去继承其他的`trait`, 注意每继承一个新的`trait`都需要单独使用一个`with`关键字, 示例如下:

```scala
class Dog extends Speaker with Runner with OtherTrait {

}
```

如果class只需要继承一个`trait`, 那么直接使用`extends`关键字即可.

Tip:

- 继承`trait`的子类需要实现`trait`中所有已声明的方法

## Using Scala Traits like Abstract Classes

当我们像抽象类一样使用`trait`时, 我们即可以在`trait`中声明只包含签名的方法(没有方法体)，也可以声明带方法体的方法.

### A first example

上一节我们提到了猫、狗都属于动物，细分下来其实猫、狗可以划分为宠物, 我们可以将宠物的特性提取出来，抽象成一个trait，示例代码如下:

```scala
trait Pet {
  def eat() = "eat"

  def comeToMaster(): Unit
}
```

接下里我们使用`Pet`这个trait来丰富狗的模型, 示例代码如下:

```scala
class Dog(name: String) extends Speaker with Runner with Pet {
  override def speak() = "dog Woof!"

  override def run(): Unit = println("dog run")

  override def comeToMaster(): Unit = println(s"Woo-hoo, $name is coming!")
}
```

### Mixing traits in on the fly

Scala有一个有趣的特性，你可以动态的混合多个有实现方法的`trait`， 示例代码如下:

```scala
trait Speaker {
    def speak() = "speak"
}


trait Runner {
    def run(): Unit = println("I'm running~")
}


class Cat(name: String)


val cat= new Cat("Tom") with Speaker with Runner
```

代码中的`cat`对象拥有`speak`和`run`方法.

注意当`Speaker`包含一个没有实现的方法时, 将无法通过上述的方式创建`cat`对象, 因为我们必须实现所有的方法, 示例代码如下:

```scala
trait Speaker {
    def speak() = "speak"

    def wait(): Unit
}


trait Runner {
    def run(): Unit = println("I'm running~")
}


class Cat(name: String)


val cat= new Cat("Tom") with Speaker with Runner {
    override def wait(): Unit = println("Waiting 1 mins~")
  }
```

## Abstract Classes

Scala中也有抽象类的概念, 它与Java中的抽象类类似. 但因为`trait` 是如此的强大, 我们很少会使用抽象类. 实际上, 只有在如下场景下我们才会使用`trait`:

- 创建一个有构造参数的基础类

- 你的代码需要被Java代码调用

### Scala traits don’t allow constructor parameters

Scala的`trait`是不允许定义构造参数的, 如下代码将无法进行编译:

```scala
// this won’t compile
trait Animal(name: String)
```

如果你想要创建一个带有构造参数的基础类, 只能使用抽象类, 代码示例如下:

```scala
abstract class Animal(name: String)
```

Tip:

- 一个类只能继承一个抽象类

### When Scala code will be called from Java code

Java根本不认识Scala的`trait`, 因此如果你想在Java代码中调用Scala的代码, 那你只能使用抽象类而不是`trait`, 示例代码如下:

```scala
abstract class Pet(name: String) {
  def speak(): Unit = println("Yo") // concrete implementation

  def comeToMaster(): Unit // abstract method
}


class Dog(dogName: String) extends Pet(dogName) {
  override def speak(): Unit = println(s"$dogName Woof")

  def comeToMaster(): Unit = println(s"Here I come!")
}

val dog = new Dog("Tom")
dog.speak()           // Tom Woof
```

注意`Dog`的参数是如何传入到`Pet`的.
