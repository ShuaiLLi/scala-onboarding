# Variables

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
