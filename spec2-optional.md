# Scala Specs2 测试入门教程（7）：Spec2 可选的 Matcher（匹配运算）

除了之前介绍的 Spec2 内置的 Matcher, 针对不同的功能，Spec2 还提供了一些可选的 Matcher，比如：

## Result

如果你需要其它 Matcher 的结果：

```
// you need to extend the ResultMatchers trait
class MatchersSpec extends Specification with matcher.ResultMatchers { def is =
  "beMatching is using a regexp" ! {
    ("Hello" must beMatching("h.*")) must beSuccessful
  }
}
```

## Xml

用于检测 XML 的 Matcher

```
beEqualToIgnoringSpace compares 2 Nodes, without considering spaces
<a><b/></a> must ==/(<a><b/></a>)
<a><b/></a> must beEqualToIgnoringSpace(<a><b/></a>)
beEqualToIgnoringSpace can also do an ordered comparison
must ==/().ordered
on the other hand beEqualToIgnoringSpace will not check attributes order
must ==/()
\ is an XPath-like matcher matching if a node is a direct child of another
must \("b")
You can also check attribute names
must \("b","name")
And attribute names and values as well (values are checked using a regular expression, use the quote method if you want an exact match)
must \("b","n"->"v","n2"->"v\d")
Or the content of a Text node
hello must \("a")\>"hello" (alias textIs)
hello must \("a")\>~"h.*" (alias textMatches)
The equivalent of \ for a “deep” match is simply \\
must \\("c")
```

## Json

用来检测 Json 字符串的 Matcher

```
/(value) checks if a value is present at the root of the document. This can only be the case if that document is an Array
/(regex) checks if a value matching the regex is present at the root of the document. This can only be the case if that document is an Array
/(key -> value) checks if a pair is present at the root of the document. This can only be the case if that document is a Map
*/(value) checks if a value is present anywhere in the document, either as an entry in an Array, or as the value for a key in a Map
*/(key -> value) checks if a pair is present anywhere in a Map of the document
/#(i) selects the ith element in a 0-based indexed Array or a Map and allow further checks on that element
```

Now the interesting part comes from the fact that those matchers can be chained to search specific paths in the Json document. For example, for the following document:

```
// taken from an example in the Lift project
val person ="""{
  "person": {
    "name": "Joe",
    "age": 35,
    "spouse": {
      "person": {
        "name": "Marilyn",
        "age": 33
      }
    }
  }
}
"""
```

You can use these combinations:

```
person must /("person")*/("person") /("age"->33.0)// by default numbers are parsed as Doubles
```

You can as well use regular expressions or String matchers instead of values to verify the presence of keys or elements. For example:

```
person must /("p.*".r)*/".*on".r /("age"->"\\d+\\.\\d".r)
person must /("p.*".r)*/".*on".r /("age"-> startWith("3"))
person must /("p.*".r)*/".*on".r /("age"->(be_>(30)^^((_:String).toInt)))
Finally you can access some records by their index:

person must /("person")/# 2 / "person"
```

## File

检测文件或是路径

```
beEqualToIgnoringSep checks if 2 paths are the same regardless of their separators
"c:\temp\hello" must beEqualToIgnoringSep("c:/temp/hello")
beAnExistingPath checks if a path exists
beAReadablePath checks if a path is readable
beAWritablePath checks if a path is writable
beAnAbsolutePath checks if a path is absolute
beAHiddenPath checks if a path is hidden
beAFilePath checks if a path is a file
beADirectoryPath checks if a path is a directory
havePathName checks if a path has a given name
haveAsAbsolutePath checks if a path has a given absolute path
haveAsCanonicalPath checks if a path has a given canonical path
haveParentPath checks if a path has a given parent path
listPaths checks if a path has a given list of children
exist checks if a file exists
beReadable checks if a file is readable
beWritable checks if a file is writable
beAbsolute checks if a file is absolute
beHidden checks if a file is hidden
beAFile checks if a file is a file
beADirectory checks if a file is a directory
haveName checks if a file has a given name
haveAbsolutePath checks if a file has a given absolute path
haveCanonicalPath checks if afile has a given canonical path
haveParent checks if a file has a given parent path
haveList checks if a file has a given list of children
```

等等，如果 Spec2 内置或是可选的 Matcher 还是不满足你的要求，你还是自定义 Matcher，本系列就不介绍了。