# Classes

为了支持面向对象编程，Scala提供了类结构，相比于Java和C#等语言，它的语法更为简洁, 但他仍然是易用且可读的.

## Basic Class Constructors

创建一个简单的类，并定义一个有两个参数的类构造器, 代码示例如下:

```scala
class Person(var firstName: String, var lastName: String)

// Use Class Person 
val person = new Person("Andrew", "Li")
println(person.firstName + " " + person.lastName)   // Andrew Li
```

在Scala类构造器中定义的所有参数，都会在编译期间自动在类中创建出对应的变量，如示例可见，构造器定义了两个`var`变量，因此他们都是可修改的，修改方式见如下代码示例:

```scala
person.firstName = "Shuai"

println(person.firstName + " " + person.lastName)   // Shuai Li
```

我们对比下在Scala和Java中创建一个简单类的区别，看看Scala的类是否足够简洁，代码示例如下:

```scala
// Create a class in Scala
class Person(var firstName: String, var lastName: String)
```

```java
// Create a class in Java
public class Person {

    private String firstName;
    private String lastName;
    
    public Person(String firstName, String lastName) {
        this.firstName = firstName;
        this.lastName = lastName;
    }
    
    public String getFirstName() {
        return this.firstName;
    }
    
    public void setFirstName(String firstName) {
        this.firstName = firstName;
    }
    
    public String getLastName() {
        return this.lastName;
    }
    
    public void setLastName(String lastName) {
        this.lastName = lastName;
    }
    
}
```

### val makes fileds read-only

前边提到，Person类中所有的字段都被定义成var, 这些字段是可变的.实际上你也可以将这些字段定义成val，这样所有的字段将是不可变的, 如下代码示例所见:

```scala
class Person(val firstName: String, val lastName: String)

// Use Person class
val person = new Person("Andrew", "Li")
person.firstName = "Shuai"    // error: reassignment to val
```

Tip:
当你使用Scala实践面向对象编程时，用var来定义class的变量显然更合适，因为这样你可以随意修改它们.
但是在使用Scala实践函数式编程时, 一般我们会使用case class而不在是class, case class适合于不可变得数据，函数式编程里数据的不可变是被提倡的，因为不可变会保证程序的健壮性.

### Class Structure

在Scala中, class主要有以下几个组成部分:

- class构造器的参数

- 类主体中定义的被调用的方法

- 类主体中能够在初始化(new)时被直接执行的语句和表达式

当然了，除了构造器中的变量，你可以在class中定义一些变量, 跟Java极其相似，这些变量将会在class初始化时被分配指定的值.

如下代码示例展示了你能在class中进行的一些操作:

```scala
class Person(var firstName: String, var lastName: String) {

  println("the constructor begins")

  // 'public' access by default
  var age = 0

  // some class fields
  private val AMOUNT_OF_EYES = 2

  // some methods
  override def toString(): String = s"$firstName $lastName is $age years old"

  def printAmountOfEyes(): Unit = println(s"AMOUNT_OF_EYES = $AMOUNT_OF_EYES")

  def printDescription(): Unit = println(this)

  printAmountOfEyes()
  printDescription()
  println("you've reached the end of the constructor")
}
```

```scala
var person = new Person("Andrew", "Li")

// When executing the above code to init person object, we can see those logs as follows:

// the constructor begins
// AMOUNT_OF_EYES = 2
// Andrew Li is 0 years old
// you've reached the end of the constructor
```

## Auxiliary Class Constructors

我们可以通过定义this方法来定义"补充类构造器", 在定义补充类构造器时需要注意一下几点:

- 每个补充类构造器必须有不同的方法签名(在这里主要是不同类构造器的的参数必须不同)
- 每个补充类构造器必须调用一个之前已经定义好的构造器

代码示例如下:

```scala
// the primary constructor
class Person (var name: String, var age: Int) {

  // one-arg auxiliary constructor
  def this(age: Int) = {
    this("default name", age)
  }

  // one-arg auxiliary constructor
  def this(name: String) = {
    this(name, 0)
  }

  // zero-arg auxiliary constructor
  def this() = {
    this("default name", 0)
  }

  override def toString = s"The person called $name is $age years old"

}
```

Tip:

- 补充类构造器是一个好的特性，但是因为Scala中我们可以给参数指定默认值，因此实际上补充类构造器使用的场景并不多,
在下一节中我们将学习如何给参数指定默认值.

## Supplying Default Values for Constructor Parameters

Scala支持我们给类构造器指定默认值, 代码示例如下:

```scala
class Socket(var timeout: Int = 2000, var linger: Int = 3000) {
  override def toString = s"timeout: $timeout, linger: $linger"
}
```

我们可以通过如下示例中的几种方式创建Socket对象:

```scala
new Socket()
new Socket(1000)
new Socket(4000, 6000)
```

Benefits:

- 你可以提供首选的, 默认的值为你的类构造器参数
- 这将允许使用你定义的类的开发者根据他们自己的要求覆盖那些默认值

### Bonus: Named parameters

Scala还有一个有用的特性，当你在创建类实例的时候可以你可以使用命名的参数，见如下代码示例:

```scala
val socket = new Socket(linger=3000, timeout=2000)

val socket = new Socket(timeout=2000)
val socket = new Socket(linger=3000)
```

很明显，如上的示例将比如下示例更具可读性:

```scala
val socket = new Socket(2000, 3000)
```

## Basic Scala Methods

在Scala中, 方法是被定义在类里的, 接下来我们将看一下常见的用法, 见如下代码示例:

```scala
def double(a: Int) = a * a

// Explicitly declare the return value
def double(a: Int): Int = a * a
```

方法的返回值类型是否显式声明式我们的自由，但是一般函数体逻辑简单且返回值类型比较容易看出来的情况下，我们可以不显式声明,
当返回值类型是我们的自定义类型并且方法体逻辑比较复杂无法一眼看出来返回值，推荐显式声明返回值类型，这样能显著提高代码的可读性.

方法的结构说明:

- `def` 是声明方法的关键字
- `double` 是方法的名称，尽量用业务含义为方法命名
- `a: Int` 是方法的参数, `a`是参数名, `Int` 是参数类型
- `: Int` 是显式声明了方法的返回值类型
- `a * a` 是方法体，单行方法体可以用直接用 `=` 定义，如上述代码示例, 多行方法体需要使用`{}`定义，代码示例如下:

```scala
def addThenDouble(a: Int, b: Int): Int = {
  val sum = a + b
  val doubled = sum * 2
  doubled
}
```
