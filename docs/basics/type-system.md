# Type System

## Two Types of Variables

`Scala` 中有两种变量类型（非数据类型）

- `val` 用来创建不可变类型的变量，类似于Java中`final`修饰符的作用
- `var` 用来创建可变类型的变量

如下为`Scala`中变量声明的示例代码：

```scala
// immutable
val s = "hello"
s = "hello, world" // compile error: reassignment to val

val dog = new Dog("Tom")

// mutable
var i = 123
i = 456            // compile pass
```

## The Type is Optional

上一个Section的示例表明`Scala`编译器足够智能，在编译期间可以通过 `=` 右侧的变量值推断出变量的数据类型.当然你也可以显式地声明变量的数据类型，示例代码如下：

```scala
val s: String = "hello"
var i: Int = 123
```

一般来说，对于`Scala`编译器在编译期间对变量数据类型的推断，是不需要参考变量显式声明的数据类型的，不显式声明数据类型是首选，因为显式的方式将让代码变得冗余. 但如果你觉得显式声明变量的数据类型会增加代码的`可读性`，你可以这样做.

Tip:

实际上，当我们使用第三方库中的方法时，显式声明变量的数据类型会给代码的`可读性`带来显著提升，尤其是非常见库或者其方法名称没有明确数据类型的场景. 当然你也可以使用`注释`来阐明数据类型, 但是代码的`见名知意`是更加推荐的做法，毫不夸张地说：`好代码不需要注释`

## A few build-in Data Types

`Scala` 内置了很多基础数据类型供开发者使用，所有的这些数据类型都被封装成成熟的`object`，并非原始数据类型(int, float等)

如下为定义这些基础数据类型的示例:

```scala
val b: Byte = 1
val i: Int = 1
val l: Long = 1
val s: Short = 1
```

上述 `Byte`, `Int`, `Long`, `Short`四个数据类型均为 **整数型** ，整数型默认的数据类型为 `Int`, 如果你想将整数型变量设置为其他整数型数据类型，你将需要使用类似如上的 **显式声明数据类型**，也即在变量名后边跟上 "`: {数据类型}`".

小数型变量默认的数据类型为 `Double`, 同样地如果你想将小数型变量设置为其他小数型数据类型，也需要 **显式声明数据类型**, 如下为内置小数型数据类型示例:

```scala
val d = 2.0          // default "Double" type

val f: Float = 3.0   // set to `Float` type
```

当然了 `Scala` 也内置了 `Boolean`, `String`, `Char` 三种基本数据类型, 示例如下:

```scala
val b = true         // default "Boolean" type

val s = "hello"      // default "String" type

val c = 'a'          // default "Char" type
```

Tip

如下附上 `Scala` 官方对 `9` 种基本数据类型 **值范围** 的说明:

| Data Type | Possible Values |
| --------- | --------------- |
| Boolean | true or false |
| Byte    | 8-bit signed two’s complement integer (-2^7 to 2^7-1, inclusive) |
| Short   | 16-bit signed two’s complement integer (-2^15 to 2^15-1, inclusive) |
| Int     | 32-bit two’s complement integer (-2^31 to 2^31-1, inclusive) |
| Long    | 64-bit two’s complement integer (-2^63 to 2^63-1, inclusive) |
| Float   | 32-bit IEEE 754 single-precision float (1.40129846432481707e-45 to 3.40282346638528860e+38) |
| Double  | 64-bit IEEE 754 double-precision float (4.94065645841246544e-324d to 1.79769313486231570e+308d) |
| Char    | 16-bit unsigned Unicode character (0 to 2^16-1, inclusive) |
| String  | a sequence of Char |

### BigInt and BigDecimal

针对大数处理场景，`Scala` 提供了两种数据类型:

- `BigInt` 大整数

- `BigDecimal` 大十进制数(小数)

不同于其在Java中对应的类，`Scala` 中这两种大数数据类型支持所有你习惯使用的数字类型的操作符，一个字: **妙**, 让我们看几个`REPL`环境下的示例:

```shell
scala> val bi = BigInt(100000001)
val bi: scala.math.BigInt = 100000001

scala> bi + bi
val res2: scala.math.BigInt = 200000002

scala> bi - bi
val res3: scala.math.BigInt = 0

scala> bi * bi
val res4: scala.math.BigInt = 10000000200000001
```

```shell
scala> val bd = BigDecimal(10000.123)
val bd: scala.math.BigDecimal = 10000.123

scala> bd + bd
val res0: scala.math.BigDecimal = 20000.246

scala> bd * bd
val res1: scala.math.BigDecimal = 100002460.01512
```
