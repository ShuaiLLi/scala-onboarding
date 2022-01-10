# Companion Object

Scala中类和对象可以共享同一个名称, 在这种场景下的类称之为伴生类，对象称之为伴生对象, 这是Scala的特性.

## Meaning

1. 考虑到与Java的兼容性,引入了`伴生`的特性. 实际上Java中`static variables`的设计破坏了面向对象规范的完整性<sup>1</sup>, 因此Scala在设计之初就放弃了`static`特性, 设计了`伴生`来保证兼容性.

2. 考虑到JVM的优化. 我们知道Java中静态属性会在整个JVM中仅开辟一块内存空间, 相比于实例属性节省了大量内存资源，而Scala中的`伴生对象`承载的主要就是静态属性，因此`伴生`会节省大量的内存资源.

## How to use

创建一对简单的伴生类和伴生对象, 代码示例如下:

```scala
class Demo {
  private val class_var = 10;

  def init(): Unit = {
    // codes
  }
}

object Demo {
  private val object_var = "demo";

  def access(): Unit = {
    // codes
  }
}
```

伴生类和伴生对象需要遵循如下规则:

- 伴生类中实现所有的实例成员

- 伴生对象中实现所有的静态变量

- 伴生类与伴生对象同名同文件

## Features

`伴生`特性有如下两个特点:

- 在同一个类文件既可定义实例成员，又可定义静态成员

- 伴生类和伴生对象可以互相访问对方的私有变量及方法

私有变量和方法的互相访问方式可见如下代码示例:

```scala
class Demo {
  private val class_var = 10;

  def init(): Unit = {
    println("variable from class:" + class_var)
    println("variable from object:" + Demo.object_var)
  }
}

object Demo {
  private val object_var = "demo";

  def access(): Unit = {
    println("variable from object:" + object_var)
    val demo = new Demo()
    println("variable from class:" + demo.class_var)
  }
}

object Main extends App {
  println("variable from object:" + Demo.object_var);
  println("variable from class:" + (new Demo()).class_var);
}
```

总结起来，伴生类和伴生对象互相访问私有属性主要遵循如下规则:

- 伴生类中通过`object名`的`.`操作符来访问伴生对象的私有属性

- 伴生对象中通过`class实例对象`的`.`操作符号访问伴生类的私有属性

## Referrences

- [1] Why are static variables considered evil?, <https://stackoverflow.com/questions/7026507/why-are-static-variables-considered-evil>
