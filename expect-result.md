# Scala Specs2 测试入门教程（4）：期望结果

书写测试用例一个步骤是书写测试的预期结果。

## 函数化

Spec2 中缺省 Specification Trait 是函数化的，也就是说 Example 的 Result 值为代码中的最后一条语句提供。比如下面的示例，永远不会失败，这是本例的第一个测试的结果给丢掉了。

```
"my example on strings" ! e1           // will never fail!

def e1 = {
  "hello" must have size(10000)        // because this expectation will not be returned,...
  "hello" must startWith("hell")
}
```

因此正确的写法为：

```
"my example on strings" ! e1           // will fail

def e1 = "hello" must have size(10000) and
                           startWith("hell")

```

## Thrown

上面的函数化需要仔细指明所有的期望，有时你可能觉得这样很麻烦，比如还是用什么的那个不会失败的例子：

```
import org.specs2._


class HelloWorldAcceptanceSpec extends Specification  { def is = s2"""

 This is a specification to check the 'Hello world' string

  "my example on strings"    $e1
                                                                 """

  def e1 = {
    "hello" must have size(10000)        // because this expectation will not be returned,...
    "hello" must startWith("hell")
  }
}
```

这个例子来执行不会报失败，我们希望在执行“hello” must have size(10000）报错，不继续执行下面的测试，此时我们可以使用 org.specs2.matcher.ThrownExpectations，此时如果将这个 Trait 混合到定义的规范中，所有没有达到期望值的测试都会抛出 FailureException 异常，Example 之后的测试也不执行，比如修改后代码：

```
import org.specs2._
import org.specs2.matcher.ThrownExpectations

class HelloWorldAcceptanceSpec extends Specification with ThrownExpectations { def is = s2"""

 This is a specification to check the 'Hello world' string

  "my example on strings"    $e1
                                                                 """

  def e1 = {
    "hello" must have size(10000)        // because this expectation will not be returned,...
    "hello" must startWith("hell")
  }
}
```

这个测试的第一个检测“hello” must have size(10000)失败，整个 Example 失败，后续的测试也不会执行。