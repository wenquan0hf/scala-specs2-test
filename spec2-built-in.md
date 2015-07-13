# Scala Specs2 测试入门教程（6）：Spec2 内置的 Matcher（匹配运算）

前面的两篇博客  
[Scala Specs2 测试入门教程（3）：测试结果 Result](http://www.imobilebbs.com/wordpress/archives/5005) 和 [Scala Specs2 测试入门教程（4）：期望结果](http://www.imobilebbs.com/wordpress/archives/5009)简要介绍了 Spec 的预期结果，其中说明

在 Specs2 的 Example对象为一个文字加上任意可以转换成 Result 对象（org.specs2.execute.Result）的对象，它可以是：

- 一个标准测试结果（Success,failure,pending 等）
- 一个 Matcher（匹配）结果
- 一个布尔值
- 一一个 ScalaCheck 属性

## 标准测试结果

最简单的 Result 值由 StandardResults Trait 定义，可以有如下几种值：

- success: 这个测试结果正常
- failure: 这个测试结果不满足预期
- anError: 测试出现异常
- skipped: 该测试被跳过（不满足某些條件）
- pending: 通常只这个测试用例还没实现

此外还有两种情况：

- done: 这个测试结果正常显示「DONE」
- todo:测试结果还没实现显示」TODO」

其中 Matcher 是用来判断测试结果和預期的结果是否符合的主要方法.

本篇介绍 Spec2内置的一些 Matcher类型， 它可以分为比较，选项， 字元串比较，数字比较，捕获异常等种类：

###Equalit

比较，该类最常用的 Matcher 为 beEqualTo 来测试是否相等

|Matcher |描述 |
|:-------|:----|
|1 must beEqualTo(1)  |   the normal way |
|1 must be_==(1)  | with a shorter matcher  | 
|1 must_==1    |   my favorite! |
|1 mustEqual 1    |   if you dislike underscores  |   
|1 should_==1  |  for should lovers |
|1===1 |   the ultimate shortcut   | 
|1 must be equalTo(1)    |   with a literate style   |

| 否定形式  |
|:-------|
|1 must not be equalTo(2) |
|1 must_!=2 |
|1 mustNotEqual 2 |
|1 must be_!=(2)| 
|1!==2 |	

此外还有其它形式的比较:

|Matcher| 说明|
|:-------|:-------| 
| be_=== | same as be_== but can be used with some combinators like ^^^ or toSeq because the parameter type is kept | 
| be_==~ | checks if (a:A)==(b:A) when there is an implicit conversion from B (the type of b) to A (the type of a) | 
| beTheSameAs|  checks if a eq b (a must be(b) also works) | 
| beTrue, beFalse | shortcuts for Boolean equality | 
| a ==== b|  similar to a === b but will not typecheck if a and b don’t have the same type | 

### Any

下面的 Matcher 可以应用到 Scala 的 Any 类型的对象。

```
•beLike {case exp => ok }: to check if an object is like a given pattern (ok is a predefined value, ko is the opposite)
•beLike {case exp => exp must beXXX }: to check if an object is like a given pattern, and verifies a condition
•beNull
•beAsNullAs: when 2 objects must be null at the same time if one of them is null
•beOneOf(a, b, c): to check if an object is one of a given list
•haveClass: to check the class of an object
•haveSuperclass: to check if the class of an object as another class as one of its ancestors
•haveInterface: to check if an object is implementing a given interface
•beAssignableFrom: to check if a class is assignable from another
•beAnInstanceOf[T]: to check if an object is an instance of type T
Option/Either
```
 
下面的 Matcher 用来检查 Option 和 Either 类型：

```
•beSome checks if an element is Some(_)
•beSome(exp) checks if an element is Some(exp)
•beSome(matcher) checks if an element is Some(a) where a satisfies the matcher
•beSome(function: A =>AsResult[B]) checks if an element is Some(a) where function(a) returns a successful Result
(note that a Seq[A] is also a function Int=> A so if you want to check that a sequence is contained in Some you need to use a matcher: beSome(===(Seq(1)))
•beSome.which(function) checks if an element is Some(_) and satisfies a function returning a boolean
•beSome.like(partial function) checks if an element is Some(_) and satisfies a partial function returning a MatchResult
•beNone checks if an element is None
•beAsNoneAs checks if 2 values are equal to None at the same time
•beRight checks if an element is Right(_)
•beRight(exp) checks if an element is `Right(exp)
•beRight(matcher) checks if an element is Right(a) where a satisfies the matcher
•beRight(function: A =>AsResult[B]) checks if an element is Right(a) where function(a) returns a successful Result
(note that a Seq[A] is also a function Int=> A so if you want to check that a sequence is contained in Right you need to use a matcher: beRight(===(Seq(1)))
•beRight.like(partial function) checks if an element is Right(_) and satisfies a partial function returning a MatchResult
•beLeft checks if an element is Left(_)
•beLeft(exp) checks if an element is Left(exp)
•beLeft(matcher) checks if an element is Left(a) where a satisfies the matcher
•beLeft(function: A =>AsResult[B]) checks if an element is Left(a) where function(a) returns a successful Result
(note that a Seq[A] is also a function Int=> A so if you want to check that a sequence is contained in Left you need to use a matcher: beLeft(===(Seq(1)))
•beLeft.like(partial function) checks if an element is Left(_) and satisfies a partial function returning a MatchResult
```

### Try

下面用来测试 Try 类型对象：

```
•beSuccessfulTry checks if an element is Success(_)
•beSuccessfulTry.withValue(exp) checks if an element is Success(_)
•beSuccessfulTry.withValue(matcher) checks if an element is Success(a) where a satisfies the matcher
•beSuccessfulTry.withValue(function: A =>AsResult[B]) checks if an element is Success(a) where function(a) returns a successful Result
(note that a Seq[A] is also a function Int=> A so if you want to check that a sequence is contained in Success you need to use a matcher: beSuccessfulTry.withValue(===(Seq(1)))
•beSuccessfulTry.which(function) checks if an element is Success(_) and satisfies a function returning a boolean
•beSuccessfulTry.like(partial function) checks if an element is Success(_) and satisfies a partial function returning a MatchResult
•beFailedTry checks if an element is Failure(_)
•beFailedTry.withThrowable[T] checks if an element is Failure(t: T)
•beFailedTry.withThrowable[T](pattern) checks if an element is Failure(t: T) and t.getMessage matches pattern
```

### String

匹配字元串非常常见，下面为字元串匹配：

```
•beMatching (or be matching) checks if a string matches a regular expression
•=~(s) is a shortcut for beMatching("(.|\\s)*"+s+"(.|\\s)*")
•find(exp).withGroups(a, b, c) checks if some groups are found in a string
•have length checks the length of a string
•have size checks the size of a string (seen as an Iterable[Char])
•be empty checks if a string is empty
•beEqualTo(b).ignoreCase checks if 2 strings are equal regardless of casing
•beEqualTo(b).ignoreSpace checks if 2 strings are equal when you replaceAll("\\s","")
•beEqualTo(b).trimmed checks if 2 strings are equal when trimmed
•beEqualTo(b).ignoreSpace.ignoreCase you can compose them
•contain(b) checks if a string contains another one
•startWith(b) checks if a string starts with another one
•endWith(b) checks if a string ends with another one
```

### Numeric

下面的用来测试数值：

```
•beLessThanOrEqualTo compares any Ordered type with <=
1 must be_<=(2)
1 must beLessThanOrEqualTo(2)
•beLessThan compares any Ordered type with <
1 must be_<(2)
1 must beLessThan(2)
•beGreaterThanOrEqualTo compares any Ordered type with >=
2 must be_>=(1)
2 must beGreaterThanOrEqualTo(1)
•beGreaterThan compares any Ordered type with >
2 must be_>(1)
2 must beGreaterThan(1)
•beCloseTo checks if 2 Numerics are close to each other
1.0 must beCloseTo(1,0.5)
4 must be ~(5+/- 2)
•beBetween checks if a value is between 2 others
5 must beBetween(3,6)
5 must beBetween(3,6).excludingEnd
5 must beBetween(4,6).excludingStart
5 must beBetween(4,6).excludingBounds
// with brackets notation
5 must (be[(4,7)])
```

### Exception

下面用来检查是否拋出异常：

```
•throwA[ExceptionType] checks if a block of code throws an exception of the given type
•throwA[ExceptionType](message ="boom") additionally checks if the exception message is as expected
•throwA(exception) or throwAn(exception) checks if a block of code throws an exception of the same type, with the
same message
•throwA[ExceptionType].like {case e => e must matchSomething } or
throwA(exception).like {case e => e must matchSomething } allow to verify that the thrown exception satisfies a property
•throwA[ExceptionType](me.like {case e => e must matchSomething } or
throwA(exception).like {case e => e must matchSomething } allow to verify that the thrown exception satisfies a property
```

### Traversable

下面的 Matcher 用来检查 Traversables 对象类型：

If you want to check the size of a Traversable

```
•to check if it is empty
Seq() must be empty
Seq(1,2,3) must not be empty
•to check its size
Seq(1,2) must have size(2)
Seq(1,2) must have length(2) // equivalent to size
•to check its ordering (works with any type T which has an Ordering)
Seq(1,2,3) must beSorted
```

Then you can check the elements which are contained in the Traversable

```
•if a simple value is contained
Seq(1,2,3) must contain(2)
•if a value matching a specific matcher is contained
Seq(1,2,3) must contain(be_>=(2))
•if a value passing a function returning a Result is contained (MatchResult, ScalaCheck Prop,…)
Seq(1,2,3) must contain((i:Int)=> i must be_>=(2))
•note that a Seq[A] is also a function Int=> A so if you want to check that a sequence is contained in another you need to use a matcher
Seq(Seq(1)) must contain(===(Seq(1)))
•there are also 2 specialized matchers to check the string representation of the elements
Seq(1234,6237) must containMatch("23") // matches with ".*23.*"
Seq(1234,6234) must containPattern(".*234") // matches with !.*234"
```

For each of the checks above you can indicate how many times the check should be satisfied:

```
•Seq(1,2,3) must contain(be_>(0)).forall // this will stop after the first failure
•Seq(1,2,3) must contain(be_>(0)).foreach // this will report all failures
•Seq(1,2,3) must contain(be_>(0)).atLeastOnce
•Seq(1,2,3) must contain(be_>(2)).atMostOnce
•Seq(1,2,3) must contain(be_>(2)).exactly(1.times)
•Seq(1,2,3) must contain(be_>(2)).exactly(1)
•Seq(1,2,3) must contain(be_>(1)).between(1.times,2.times)
•Seq(1,2,3) must contain(be_>(1)).between(1,2)
```

The other types of checks involve comparing the Traversable elements to other elements (values, matchers, function returning a Result)

```
•with a set of values
Seq(1,2,3,4) must contain(2,4)
which is the same thing as
Seq(1,2,3,4) must contain(allOf(2,4))
•with a set of matchers
Seq(1,2,3,4) must contain(allOf(be_>(0), be_>(1)))
•checking that the order is satisfied
Seq(1,2,3,4) must contain(allOf(be_>(0), be_>(1)).inOrder)
```

Note that allOf tries to make each check at least successful once, even if that on the same value. If, on the other hand, you want to specify that each check must succeed on a differentvalue you should use onDistinctValues. For example this will fail:

```
Seq(1) must contain(allOf(1,1)).onDistinctValues
```

The eachOf method does the same thing (and this example will fail as well):

```
Seq(1) must contain(eachOf(1,1))
```

Another frequent use of Traversable matchers is to check if the Traversable have the right number of elements. For this you can use:

```
•atLeast, which is actually another name for allOf, where the traversable can contain more elements than required
Seq(1,2,3,4) must contain(atLeast(2,4))
•atMost where the traversable can not contain more elements than required
Seq(2,3) must contain(atMost(2,3,4))
•exactly where the traversable must contain exactly the specified number of elements
Seq(1,2) must contain(exactly(2,1))
```

The atLeast/atMost/exactly operators work on distinct values by default (because this is easier for counting the correspondance between actual values and expected ones). However you can use onDistinctValues(false) if you don’t care.

Finally, if you want to get the differences between 2 traversables:

```
Seq(2,4,1) must containTheSameElementsAs(Seq(1,4,2))
```

### Map

下面为检查 Map 类型的对象：

```
•haveKey checks if a Map has a given key
Map(1->"1") must haveKey(1)
•haveKeys checks if a Map has several keys
Map(1->"1",2->"2") must haveKeys(1,2)
•haveValue checks if a Map has a given value
Map(1->"1") must haveValue("1")
•haveValues checks if a Map has several values
Map(1->"1",2->"2") must haveValue("1","2")
•havePair checks if a Map has a given pair of values
Map(1->"1") must havePair(1->"1")
•havePairs checks if a Map has some pairs of values
Map(1->"1",2->"2",3->"3") must havePairs(1->"1",2->"2")
```

But Maps are also Partial Functions, so:

```
•beDefinedAt checks if a PartialFunction is defined for a given value
partial must beDefinedAt(1)
•beDefinedBy checks if a PartialFunction is defined for a given value
and returns another one
partial must beDefinedBy(1 -> true)
```

这些内置的 Matcher 具有下面的特点：

- 这些Matcher的一般形式为 a must matcher
- 你可以使用 should 代替 must
- 对于大部分的 Matcher 来说，如果使用 be,have 時，be 和 have 可以省略
- 你可以在 Matcher 前后使用 not 来反向匹配

比如

```
def e1 = "Hello world" must be size(11)
def e1 = "Hello world" must have size(11)

def e1 = "Hello world" must  size(11)
```

是等效的。

