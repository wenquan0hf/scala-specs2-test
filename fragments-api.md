# Scala Specs2 测试入门教程（5）：Fragments API 简介

前面的例子

```
import org.specs2._

class HelloWorldAcceptanceSpec extends Specification { def is = s2"""

 This is a specification to check the 'Hello world' string

 The 'Hello world' string should
   contain 11 characters                                         $e1
   start with 'Hello'                                            $e2
   end with 'world'                                              $e3
                                                                 """

  def e1 = "Hello world" must have size(11)
  def e2 = "Hello world" must startWith("Hello")
  def e3 = "Hello world" must endWith("world")
}
```

实际上是使用 Fragment API，我们使用 Fragment API 重写什么例子，它是一系列 Fragment 对象通过^运算符连接而成：

```
import org.specs2._

class HelloWorldAcceptanceSpec extends Specification { def is =

 "This is a specification to check the 'Hello world' string" ^
 "The 'Hello world' string should" ^
   "contain 11 characters"                                         ! e1 ^
   "start with 'Hello'"                                            ! e2 ^
   "end with 'world'"                                              ! e3


  def e1 = "Hello world" must have size(11)
  def e2 = "Hello world" must startWith("Hello")
  def e3 = "Hello world" must endWith("world")
}
```

其中的 Example 对象的格式为 “description” ! body. body 返回一个 Result 对象，当你直接使用 Fragment API 使用 ^ 运算符构建 Text 和 Example 对象时有些规则需要注意，这些规则影响到结果的显示格式。

## 规则

spec2 的结果显示的布局缺省情况是自动完成，其显示和源码显示类似。
其显示规则如下：

- 当一个 Example 跟在一个 Text 对象后面，它缩进显示。
- 两个连续的 Example 对象，缩进层次相同。
- 但一个 Text 对象跟在一个 Example 对象后面时，意味着你想显示一个“辅助文字（子文字）”，因此这个 Text 对象缩进一级。
 
比如下面的例子：

```
"this is some presentation text"      ^
  "and the first example"             ! success^
  "and the second example"            ! success
}
```

显示如下：

```
 this is some presentation text
 + and the first example
 + and the second example
```

如果你指定一个“子文字” ，则显示如下：

```
this is some presentation text
+ and the first example
+ and the second example
  and in this specific context
  + one more example
```
