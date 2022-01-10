# Case Classes And Case Objects

## Case Classes

Scala为函数式编程提供的特性之一为`case class`. `case class`拥有所有普通`class`的功能，同时还有更多的扩展功能. 当编译器检查到`class`前边有`case`关键字时，它会为你自动生成一些功能代码, 主要包含如下几种特性:

- `case class`的构造器参数默认为公共的`val`变量, 因此每个参数变量访问方法也会被自动生成.

- `apply` 方法将会在`case class`的伴生对象中被生成, 因此你不再需要使用`new`关键字来创建`case class`的实例.