# Scala Specs2 测试入门教程（3）：测试结果 Results

在第一篇文章我们说过 Specs 可以有两种风格的测试规范：单元测试规范和验收测试规范，我们来看其中的一个例子：

```
def is =                     s2"""

  this is my specification
    and example 1            $e1
    and example 2            $e2
                             """

def e1 = success
def e2 = success
```

这段代码从 S2 字符串创建一组 Fragments（测试用例）对象，这个例子创建了一个 Text 对象和两个 Example 对象（它们都是 Fragment 的子类）。

在 Specs2 的 Example 对象为一个文字加上任意可以转换成 Result 对象（org.specs2.execute.Result）的对象，它可以是：

- 一个标准测试结果（Success,failure,pending 等）
- 一个Matcher（匹配）结果
- 一个布尔值
- 一个 ScalaCheck 属性
 

## 标准测试结果

最简单的 Result 值由 StandardResults Trait 定义，可以有如下几种值：

- success: 这个测试结果正常
- failure: 这个测试结果不满足预期
- anError: 测试出现异常
- skipped: 该测试被跳过（不满足某些条件）
- pending: 通常只这个测试用例还没实现
 
此外还有两种情况：

- done: 这个测试结果正常并显示 “DONE”
- todo: 测试用例还没实现显示“TODO”

## 匹配

通常 Example 定义体使用 Matcher 定义期望值：

```
def e1 = 1 must_== 1
```

Matcher 我们在后面继续详细介绍。