# Know Scala

## What is Scala?

概括地讲，Scala是一种融合了面向对象编程和函数式编程两种编程范式的高级编程语言，Scala的静态类型系统有效地降低了复杂应用中Bug的出现率（构建健壮系统）.
Scala支持 `JVM` 和 `JavaScript` 两种运行环境，这个特性将使得我们基于现有的、庞大的类库生态去构建高性能系统更加容易.

## Scala Features

- Scala的创造深受Java, Ruby, Smalltalk, ML, Haskell, Erlang等语言影响

- 静态类型语言，变量的类型在编译期就已经确定，scala变量类型的确定依赖于其特有的类型推演系统

- 拥有复杂的类型推演系统. 通过使用类型推演，Scala 编写的代码能够和动态类型语言编写的代码一样精简.

- 简洁的语法，但仍然可读，使用 Scala 之后，Java 中冗长的表达式不见了，取而代之的是简洁的 Scala 方言.

- Scala 完全支持面向对象编程（OOP）. Scala 引入特征（trait）改进了 Java 的对象模型. trait 能通过使用混合结构（mixin composition）简洁地实现新的类型. 在 Scala 中，一切都是对象，即使是数值类型.

- Scala 完全支持函数式编程（FP），函数式编程已经被视为解决并发、大数据以及代码正确性问题的最佳工具. 使用不可变值、被视为一等公民的函数、无副作用的函数、高阶函数以及函数集合，有助于编写出简洁、强大而又正确的代码.

- Scala源代码能够被编译成`.class`文件，运行在`JVM`上.

- Scala将会让你的编程工作富有成效，但同时它也是一种`深入`的编程语言，随着你不断地使用Scala来编程，你会发现更好的编码方式. 有人说，Scala 会改变你的编程思维（这是一件好事情）.

## Comments

```scala
// 单行注释
```

```scala
/*
 多行注释
 多行注释
 */
```

```scala
/**
 * 多行注释
 * 多行注释
 */
```

## Naming conventions

Scala遵循驼峰命名规范.

- Class names: Person, StoreEmployee
- Variable names: name, firstName
- Method names: convertToInt, toUpper

## Hello, World

```scala
object Hello {
  def main(args: Array[String]): Unit = {
    println("Hello World!")
  }
}
```

- 创建一个名为`Hello.scala`的文件，写入如上代码并保存

- 使用`scalac`命令对源代码文件进行编译，我们可以得到如下两个文件
  - `Hello$.class`
  - `Hello.class`

```shell
$ scalac Hello.scala
```

- 使用`scala`命令运行上一步产出的字节码文件

```shell
$ scala Hello
```

Tip:

- `object`跟`class`是类似的, 当你仅仅想要这个类的一个实例时，你可以使用`object`
  - 跟Java相比较时, `object`里的`main`方法类似于Java类的静态方法

## Hello, World -- Version 2

```scala
object Hello2 extends App {
  println("Hello World!")
}
```

使用跟上一个Section同样的方式可以打印出同样的结果

Tip:

- `App` 是一个 `trait`, 这个Scala的特性，`App`包含了`main`方法，因此你无需自己实现它. `trait`类似于Java中的抽象类（abstract class）.
