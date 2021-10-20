# sbt
1. [What is sbt?](#what-is-sbt)

1. [Why sbt?](#why-sbt)

1. [The sbt project structure](#the-sbt-project-structure)

1. [Build definition](#build-definition)

   1. [Settings expression](#settings-expression)
   1. [Adding project description](#adding-project-description)
   1. [Adding library dependencies](#adding-library-dependencies)
   1. [Compiler options](#compiler-options)
   1. [Command alias](#command-alias)
   1. [Adding plugins](#adding-plugins)

1. [Build support files](#build-support-files)
   1. [Specifying the sbt version](#specifying-the-sbt-version)
   1. [Organize plugins](#organize-plugins) 

1. [How to run sbt?](#how-to-run-sbt) 
   1. [Installation](#installation)
   1. [Sbt shell and common commands](#sbt-shell-and-common-commands)
   1. [Batch mode](#batch-mode)
   1. [Continuous build and test](#continuous-build-and-test)

1. [Common plugins](#common-plugins)
   1. [Formatter](#formatter)
   1. [Package & Release](#package--release)
   1. [Test & Coverage](#test--coverage)

1. [Scopes](#scopes) 
   1. [There are three scope axes](#there-are-three-scope-axes)
   1. [Scoping by project axis](#scoping-by-project-axis)
   1. [Scoping by configuration axis](#scoping-by-configuration-axis)
   1. [Scoping by task axis](#scoping-by-task-axis)

1. [Referrences](#referrences) 

<br/>
<br/>

## What is sbt?

---

### A build tool for Scala (and Java)

- 当`Mark Harrah`创造sbt的时候, 它被称为"Simple Build Tool"
- 但它第一次公开宣布的时候，名字`sbt`不代表任何东西，它只是 "sbt"，它应该这样写<sup>[1]</sup>

---

### Other options of Scala build tool

- Java build tools, like ant, maven, gradle
- Mill
- Fury
- seed
- CBT
- ...

---

## Why sbt?

---

### The most used scala build tool

- Scala官方推荐
- 93.6% Scala开发者的选择<sup>[2]</sup>
- 使用Scala作为DSL定义build文件
- 市面上几乎所有的Scala指南都是基于`sbt`
- 可以混合构建Java和Scala项目
- 增量编译(优秀的增量编译设计)
- 通过触发执行(trigger execution)特性支持持续的编译与测试(file changes watch)
- 支持并行任务执行
- 支持大量的插件
- 可以重用Maven或者ivy的repository进行依赖管理

---

## The sbt project structure
  ```
  build.sbt              ------ build definition file
  project/               ------ build support files
  src/
  |-- main/
    |-- resources/
    |-- scala/
  |-- test/
    |-- resources/
    |-- scala/
  target/                ------ build products
  ```
---

## Build Definition

---

### Settings Expression

---

#### Introduction

```scala
{key} := {body}
```

配置表达式包含如下三部分:

- 操作符左侧被称为 _`key`_
- 操作符用 _`:=`_ 表示
- 操作符右侧被称为 _`body`_

---

#### Keys 

- sbt 预置的keys都被定义在 _`sbt.Keys`_ 里, 预置的Keys会被全部导入到 _`sbt.Keys`_ <sup>3</sup>

- 可以通过如下方式自定义keys

  ```scala
  lazy val customKey = settingKey[String]("The custom key")
  ```

---

### Adding project description

```scala
name := "scala-demo-project"
organization := "com.shuailli.demo"
version := "0.1.0-SNAPSHOT"
scalaVersion := "2.13.5"
```

---

### Adding library dependencies

```scala
// define libs version
val http4sVersion = "0.21.29"
val specs2Version = "4.11.0"
```

```scala
libraryDependencies ++= Seq(
  "org.http4s" %% "http4s-dsl" % http4sVersion,
  "org.http4s" %% "http4s-blaze-client" % http4sVersion,
  "org.http4s" %% "http4s-circe" % http4sVersion,
  "org.specs2" %% "specs2-core" % specs2Version % "test",
)
```

Tips
- Scala主次版本的特性差异较大，因此很多lib都会被编译给多个Scala版本 [可见示例](https://mvnrepository.com/artifact/org.http4s/http4s-core)

- 通过 %% 方法获取相应Scala版本的lib，确保lib对项目是二进制兼容的 

  如果你用的是 groupID %% artifactID % revision 而不是 groupID % artifactID % revision（区别在于 groupID 后面是 %%），sbt 会在 lib名称中加上项目的 Scala 版本号。 这只是一种快捷方法。你可以这样写不用 %%：
    ```scala
    libraryDependencies += "org.scala-tools" % "scala-stm_2.11" % "0.3"
    ```

  假设这个构建的 scalaVersion 是 2.11.1，下面这种方式是等效的（注意 "org.scala-tools" 后面是 %%）：
    ```scala
    libraryDependencies += "org.scala-tools" %% "scala-stm" % "0.3"
    ```
---

### Compiler Options

```scala
scalacOptions ++= Seq(
  "-encoding", "utf8", // Option and arguments on same line
  "-Xfatal-warnings",  // New lines for each options
  "-deprecation",
  "-unchecked",
  "-language:implicitConversions",
  "-language:higherKinds",
  "-language:existentials",
  "-language:postfixOps"
)
```

- 官方完整的compiler options可见: [链接](https://docs.scala-lang.org/overviews/compiler-options/index.html)

- 一份推荐的compiler options配置可见: [链接](https://tpolecat.github.io/2017/04/25/scalac-flags.html)

---

### Command Alias

```scala
addCommandAlias("checkAndTest", ";clean;scalafmtCheck;coverage;test;coverageReport;coverageOff")
```

---

### Adding plugins

```scala
// define lib version
val kindProjectorVersion = "0.13.0"

addCompilerPlugin("org.typelevel" %% "kind-projector" % kindProjectorVersion cross CrossVersion.full)
```

---

## Build Support Files

目录 _`project`_ 下任何的文件都可以被用在构建过程中.

---

### Specifying the sbt version

- 使用 _`project/build.properties`_ 文件指定 _`sbt`_ 版本

  ```properties
  sbt.version = 1.4.7
  ```
- 如果指定的 _`sbt`_ 版本在本地不存在, _`sbt launcher`_ 将会在构建开始前自动为你下载.
- 这个特性会保证本地预装了不同版本 _`sbt`_ 的开发者对同一项目的构建生成一致的构建产物, 因为它将最终使用指定版本的sbt

---

### Organize plugins

- 使用 _`project/plugins.sbt`_ 文件管理 _`sbt`_ 插件

  ```scala
  addSbtPlugin("com.typesafe.sbt" % "sbt-native-packager" % "1.8.0")
  addSbtPlugin("org.scoverage" % "sbt-scoverage" % "1.6.1")
  addSbtPlugin("org.scalameta" % "sbt-scalafmt" % "2.4.2")
  ```

---

## How to run sbt?

---

### Installation

- 安装 _`JDK`_ （推荐安装JDK8或者JDK11）

- 安装 _`sbt`_
  ```shell
  // In MacOS 
  $ brew install sbt
  ```

- 现在你可以开始Scala项目的开发

- Tips:
  - Scala不同于其他语言，Scala主次版本之间的特性差异较大，因此通常都是为每个项目安装特定版本(_`build.sbt`_)的Scala,而不是全局安装，_`sbt`_就可以用来管理每个Scala项目特定的Scala版本.
     
  - 安装 _`sbt`_ 后，默认情况下, _`sbt`_ 会使用和自身版本相应版本的Scala来构建项目

---

### sbt Shell and common commands

- 进入 _`sbt`_ 交互模式
  ```shell
  $ sbt
  ```
- 常用命令:
  ```shell
  > new
  > clean
  > compile 
  > test
  > testOnly
  > package
  > console
  > run <Main>
  ```
---

### Batch mode

你也可以用批处理模式来运行 sbt，可以以空格为分隔符指定参数。对于接受参数的 _`sbt`_ 命令，将命令和参数用引号引起来一起传给 _`sbt`_。例如：

```shell
$ sbt clean compile "testOnly TestA TestB"
```

在这个例子中，testOnly 有两个参数 TestA 和 TestB。这个命令会按顺序执行（clean， compile， 然后 testOnly）.

批处理模式下，构建将运行的非常慢.

---

### Continuous Build and Test

为了加快编辑-编译-测试循环，你可以让 sbt 在你保存源文件时自动重新编译或者跑测试。 在命令前面加上前缀 _`~`_ 后，每当有一个或多个源文件发生变化时就会自动运行该命令. 

- 交互模式下尝试

  ```shell
  > ~ compile
  ```

  ```shell
  > ~ run
  ```

  ```shell
  > ~ testQuick
  ```

  交互模式下按`回车键`停止监视变化，当然了交互模式或者批处理模式下均可使用 _`~`_ 前缀.

- 批处理模式下尝试

  ```shell
  $ sbt '~ run'
  ```

  为了区分类Unix系统下和 _`sbt`_ 下的 _`~`_ 前缀(Unix系统下代表当前用户根目录)，在批处理模式下需要使用 _`'`_ 将sbt的命令包起来

---

## Common plugins

---

### Formatter

- 在 _`project/plugins.sbt`_ 文件添加插件

  ```scala
  addSbtPlugin("org.scalameta" % "sbt-scalafmt" % "2.4.2")
  ```
- 项目根目录下添加配置文件 _`.scalafmt.conf`_ 
- 示例一个最简单的配置, 官方配置说明请见: [链接](https://scalameta.org/scalafmt/docs/configuration.html)
  
  ```conf
  version = 2.7.5

  maxColumn = 180

  align = more
  danglingParentheses = false
  continuationIndent.defnSite = 2
  ```
- 指定如下命令执行formatter
  ```shell
  $ sbt scalafmt
  // or
  $ sbt scalafmtCheck
  ```

---

### Package & Release

- 在 _`project/plugins.sbt`_ 文件添加打包插件（产出胖JAR包-一个包含代码和库中所有类文件的JAR文件）

  ```scala
  addSbtPlugin("com.eed3si9n" % "sbt-assembly" % "1.0.0")
  ```
- 执行如下命令生成 JAR 包
  ```shell
  $ sbt package
  ```

- 在 _`project/plugins.sbt`_ 文件添加打包插件（产出部署镜像）

  ```scala
  addSbtPlugin("com.typesafe.sbt" % "sbt-native-packager" % "1.7.5")
  ```
- 执行如下命令为不同系统构建部署镜像

  ```shell
  $ sbt debian:packageBin   # deb package
  $ sbt windows:packageBin  # msi package
  $ sbt docker:publishLocal # docker image
  ```

---

### Test & Coverage

- 在 _`project/plugins.sbt`_ 文件添加插件
  ```scala
  addSbtPlugin("org.scoverage" % "sbt-scoverage" % "1.6.1")
  ```

- 在 _`build.sbt`_ 文件添加测试覆盖率配置
  ```scala
  coverageEnabled in Test := true
  coverageMinimum := 90
  coverageFailOnMinimum := true
  coverageExcludedPackages := coverageExcludedClasses.mkString(";")
  ```
- 执行如下命令均可调用覆盖率检查
  ```shell
  $ sbt coverage
  // or 
  $ sbt test
  // or
  $ sbt coverageReport
  ```

---

## Scopes

之前提到了 _`key`_ , 实际上可以在不同的上下文中为同一个 _`key`_ 赋予不同的值，每个上下文称之为 _`scope`_

例如：如果在你的构建定义中有多个项目，在每个项目中的 _`version`_ 可以是不同的值.

---

### There are three scope axes:
Scope 轴是一种类型，该类型的每个实例都能定义自己的 scope（也就是说，每个实例的key可以有自己特定的值）.

- Projects
- Configurations
- Tasks

---

### Scoping by Project axis

如果你将多个项目放在同一个构建中，每个项目需要有属于自己的 _`settings`_。也就是说，_`keys`_ 可以根据项目被局限在不同的上下文中.

当一个项目没有定义项目层级的 _`settings`_ 的时候，构建层级的 _`settings`_ 通常作为默认的设置.

- 如下示例，利用 project 轴设置构建层级的 _`settings`_

  ```scala
  ThisBuild / scalaVersion     := "2.13.5"
  ThisBuild / version          := "0.1.0-SNAPSHOT"
  ThisBuild / organization     := "com.shuailli.demo"
  ```

  _`ThisBuild`_ 代表的就是`构建层级`

---

### Scoping by Configuration axis

一个 configuration 定义一种特定的构建，可能包含它自己的 classpath，源文件和生成的包等.

在 sbt 中你可以看到这些 configurations：
- _**`Compile`**_ 定义主构建 (src/main/scala).
- _**`Test`**_ 定义如何构建测试 (src/test/scala).
- _**`Runtime`**_ 为 _`run task`_ 定义 _`classpath`_.
- _**`Provide`**_ 为 _`compiling task`_ 和 _`test task`_ 定义 _`classpath`_.

默认情况下，和编译、打包、运行相关的所有 _`key`_ 都局限于一个 _`configuration`_，因此在不同的 _`configuration`_ 中可能产生不同的效果。最明显的例子就是 task key：compile，package 和 run； 然而能够 影响 到这些 _`key`_ 的所有其他 _`key`_（例如 sourceDirectories，scalacOptions 和 fullClasspath）也都局限于该 _`configuration`_.

---

### Scoping by Task axis

_`Settings`_ 可以影响一个 task 如何工作.

为了支持这种特性，一个 _`task key`_（例如 _`packageSrc`_）可以作为另一个 _`key`_（例如 _`packageOptions`_）的 scope.

一些和打包相关的 _`task`_（_`packageSrc`_, _`packageBin`_, _`packageDoc`_）可以共享和打包相关的 _`key`_，例如 _`artifactName`_ 和 _`packageOptions`_。这些 _`key`_ 对于每个打包的 _`task`_ 可以有唯一的值。

```scala
packageSrc / packageOptions := ???
```

```scala
packageBin / packageOptions := ???
```

```scala
packageDoc / packageOptions := ???
```

---

## Referrences

- [1] sbt Faq, https://www.scala-sbt.org/release/docs/Faq.html
- [2] 2019 Scala Developer Survey, https://scalacenter.github.io/scala-developer-survey-2019/#what-are-the-*other*-pain-points-to-get-started-in-scala
- [3] sbt Keys, https://www.scala-sbt.org/1.x/api/sbt/Keys$.html
          