---
title: TOML： 简体中文 v1_0_0
date: 2024-09-10T16:41:06Z
lastmod: 2024-09-10T16:41:06Z
---

> 原文地址 [https://toml.io/cn/v1.0.0](https://toml.io/cn/v1.0.0)

发布于 1/11/2021 – [文本版本](https://raw.githubusercontent.com/toml-lang/toml.io/main/specs/cn/v1.0.0.md)

全称：Tom 的（语义）明显、（配置）最小化的语言。（Tom's Obvious, Minimal Language）
作者：Tom Preston-Werner、Pradyun Gedam 等人。

## [宗旨](#宗旨)

TOML 旨在成为一个语义明显且易于阅读的最小化配置文件格式。
TOML 被设计成可以无歧义地映射为哈希表。
TOML 应该能很容易地被解析成各种语言中的数据结构。

## [规格](#规格)

* TOML 是大小写敏感的。
* TOML 文件必须是合法的 UTF-8 编码的 Unicode 文档。
* 空白是指制表符（0x09）或空格（0x20）。
* 换行是指 LF（0x0A）或 CRLF（0x0D 0x0A）。

## [注释](#注释)

井字符将该行余下的部分标记为注释，除非它在字符串中。

```
key = "value"  
another = "# 这不是一个注释"

```

除制表符以外的控制字符（U+0000 至 U+0008，U+000A 至 U+001F，U+007F）不允许出现在注释中。

## [键值对](#键值对)

TOML 文档最基本的构成区块是键值对。

键名在等号的左边而值在右边。
键名和键值周围的空白会被忽略。
键、等号和值必须在同一行（不过有些值可以跨多行）。

```
key = "value"

```

值必须是下述类型之一。

* [字符串](#%E5%AD%97%E7%AC%A6%E4%B8%B2)
* [整数](#%E6%95%B4%E6%95%B0)
* [浮点数](#%E6%B5%AE%E7%82%B9%E6%95%B0)
* [布尔值](#%E5%B8%83%E5%B0%94%E5%80%BC)
* [坐标日期时刻](#%E5%9D%90%E6%A0%87%E6%97%A5%E6%9C%9F%E6%97%B6%E5%88%BB)
* [各地日期时刻](#%E5%90%84%E5%9C%B0%E6%97%A5%E6%9C%9F%E6%97%B6%E5%88%BB)
* [各地日期](#%E5%90%84%E5%9C%B0%E6%97%A5%E6%9C%9F)
* [各地时刻](#%E5%90%84%E5%9C%B0%E6%97%B6%E5%88%BB)
* [数组](#%E6%95%B0%E7%BB%84)
* [内联表](#%E5%86%85%E8%81%94%E8%A1%A8)

不指定值是非法的。

```
key = 

```

键值对后必须换行（或结束文件）。
（例外见[内联表](#%E5%86%85%E8%81%94%E8%A1%A8)）

```
first = "Tom" last = "Preston-Werner" 

```

## [键名](#键名)

键名可以是裸露的，引号引起来的，或点分隔的。

**裸键**只能包含 ASCII 字母，ASCII 数字，下划线和短横线（`A-Za-z0-9_-`​）。
注意裸键允许仅由纯 ASCII 数字构成，例如 `1234`​，但总是被解释为字符串。

```
key = "value"
bare_key = "value"
bare-key = "value"
1234 = "value"

```

**引号键**遵循与基本字符串或字面量字符串相同的规则并允许你使用更为广泛的键名。
除非明显必要，使用裸键方为最佳实践。

```
"127.0.0.1" = "value"
"character encoding" = "value"
"ʎǝʞ" = "value"
'key2' = "value"
'quoted "value"' = "value"

```

裸键不能为空，但空引号键是允许的（虽然不建议如此）。

```
= "no key name"  
"" = "blank"   
'' = 'blank'   

```

**点分隔键**是一系列通过点相连的裸键或引号键。
这允许了你将相近属性放在一起：

```
name = "Orange"
physical.color = "orange"
physical.shape = "round"
site."google.com" = true

```

等价于 JSON 的如下结构：

```
{
  "name": "Orange",
  "physical": {
    "color": "orange",
    "shape": "round"
  },
  "site": {
    "google.com": true
  }
}

```

有关点分隔键定义表的详细信息，请参阅后文[表](#%E8%A1%A8)一节。

点分隔符周围的空白会被忽略。
不过，最佳实践是不要使用任何不必要的空白。

```
fruit.name = "banana"   
fruit. color = "yellow"  
fruit . flavor = "banana"   

```

缩进被作为空白对待而被忽略。

多次定义同一个键是非法的。

```
name = "Tom"
name = "Pradyun"

```

注意裸键和引号键是等价的：

```
spelling = "favorite"
"spelling" = "favourite"

```

只要一个键还没有被直接定义过，你就仍可以对它和它下属的键名赋值。

```
fruit.apple.smooth = true


fruit.orange = 2

```

```
fruit.apple = 1



fruit.apple.smooth = true

```

不鼓励跳跃式地定义点分隔键。

```
apple.type = "水果"
orange.type = "水果"

apple.skin = "薄"
orange.skin = "厚"

apple.color = "红"
orange.color = "橙"

```

```
apple.type = "水果"
apple.skin = "薄"
apple.color = "红"

orange.type = "水果"
orange.skin = "厚"
orange.color = "红"

```

由于裸键可以仅由 ASCII 整数组成，所以可能写出看起来像浮点数、但实际上是两部分的点分隔键。
除非你有充分的理由（基本不太会），否则不要这样做。

```
3.14159 = "派"

```

上面的 TOML 对应以下 JSON。

```
{ "3": { "14159": "派" } }

```

## [字符串](#字符串)

共有四种方式来表示字符串：基本字符串、多行基本字符串、字面量和多行字面量。
所有字符串都只能包含有效的 UTF-8 字符。

**基本字符串**由引号（`"`​）包裹。
任何 Unicode 字符都可以使用，除了那些必须转义的：引号，反斜杠，以及除制表符外的控制字符（U+0000 至 U+0008，U+000A 至 U+001F，U+007F）。

```
str = "我是一个字符串。\"你可以把我引起来\"。姓名\tJos\u00E9\n位置\t旧金山。"

```

为了方便，一些流行的字符有其简便转义写法。

```
\b         - backspace       (U+0008)
\t         - tab             (U+0009)
\n         - linefeed        (U+000A)
\f         - form feed       (U+000C)
\r         - carriage return (U+000D)
\"         - quote           (U+0022)
\\         - backslash       (U+005C)
\uXXXX     - unicode         (U+XXXX)
\UXXXXXXXX - unicode         (U+XXXXXXXX)

```

任何 Unicode 字符都可以用 `\uXXXX`​ 或 `\UXXXXXXXX`​ 的形式来转义。
转义码必须是有效的 Unicode [标量值](https://unicode.org/glossary/#unicode_scalar_value)。

所有上面未列出的其它转义序列都是保留的；如果用了，TOML 应当产生错误。

有时你需要表示一小篇文本（例如译文）或者想要对非常长的字符串进行折行。
TOML 对此进行了简化。

**多行基本字符串**由三个引号包裹，允许折行。
紧随开头引号的那个换行会被去除。
其它空白和换行会被原样保留。

```
str1 = """
Roses are red
Violets are blue"""

```

TOML 解析器可以相对灵活地解析成对所在平台有效的换行字符。

```
str2 = "Roses are red\nViolets are blue"


str3 = "Roses are red\r\nViolets are blue"

```

想书写长字符串却不想引入无关空白，可以用 “行末反斜杠”。
当一行的最后一个非空白字符是未被转义的 `\`​ 时，它会连同它后面的所有空白（包括换行）一起被去除，直到下一个非空白字符或结束引号为止。
所有对基本字符串有效的转义序列，对多行基本字符串也同样适用。

```
str1 = "The quick brown fox jumps over the lazy dog."

str2 = """
The quick brown \


  fox jumps over \
    the lazy dog."""

str3 = """\
       The quick brown \
       fox jumps over \
       the lazy dog.\
       """

```

任何 Unicode 字符都可以使用，除了那些必须被转义的：反斜杠和除制表符、换行符、回车符外的控制字符（U+0000 至 U+0008，U+000B，U+000C，U+000E 至 U+001F，U+007F）。

你可以在多行基本字符串内的任何地方写一个引号或两个毗连的引号。
它们也可以写在紧邻界分符内的位置。

```
str4 = """这有两个引号：""。够简单。"""

str5 = """这有三个引号：""\"。"""
str6 = """这有十五个引号：""\"""\"""\"""\"""\"。"""


str7 = """"这，"她说，"只是个无意义的条款。""""

```

如果你常常要指定 Windows 路径或正则表达式，那么必须转义反斜杠就马上成为啰嗦而易错的了。
为了帮助搞定这点，TOML 支持字面量字符串，它完全不允许转义。

**字面量字符串**由单引号包裹。
类似于基本字符串，他们只能表现为单行：

```
winpath  = 'C:\Users\nodejs\templates'
winpath2 = '\\ServerX\admin$\system32\'
quoted   = 'Tom "Dubs" Preston-Werner'
regex    = '<\i\c*\s*>'

```

由于没有转义，无法在由单引号包裹的字面量字符串中写入单引号。
万幸，TOML 支持一种多行版本的字面量字符串来解决这个问题。

**多行字面量字符串**两侧各有三个单引号来包裹，允许换行。
类似于字面量字符串，无论任何转义都不存在。
紧随开始标记的那个换行会被剔除。
开始结束标记之间的所有其它内容会原样对待。

```
regex2 = '''I [dw]on't need \d{2} apples'''
lines  = '''
原始字符串中的
第一个换行被剔除了。
   所有其它空白
   都保留了。
'''

```

你可以在多行字面量字符串中的任何位置写一个或两个单引号，但三个以上的单引号序列不可以。

```
quot15 = '''这有十五个引号："""""""""""""""'''


apos15 = "这有十五个撇号：'''''''''''''''"


str = ''''那，'她说，'仍然没有意义。''''

```

除制表符以外的所有控制字符都不允许出现在字面量字符串中。
因此，对于二进制数据，建议你使用 Base64 或其它合适的 ASCII 或 UTF-8 编码。
对那些编码的处理方式，将交由应用程序自己来确定。

## [整数](#整数)

整数是纯数字。
正数可以以加号为前缀。
负数以减号为前缀。

```
int1 = +99
int2 = 42
int3 = 0
int4 = -17

```

对于大数，你可以在数字之间用下划线来增强可读性。
每个下划线两侧必须至少有一个数字。

```
int5 = 1_000
int6 = 5_349_221
int7 = 53_49_221  
int8 = 1_2_3_4_5  

```

前导零是不允许的。
整数值 `-0`​ 与 `+0`​ 是有效的，并等同于无前缀的零。

非负整数值也可以用十六进制、八进制或二进制来表示。
在这些格式中，`+`​ 不被允许，而（前缀后的）前导零是允许的。
十六进制值大小写不敏感。
数字间的下划线是允许的（但不能存在于前缀和值之间）。

```
hex1 = 0xDEADBEEF
hex2 = 0xdeadbeef
hex3 = 0xdead_beef


oct1 = 0o01234567
oct2 = 0o755 


bin1 = 0b11010110

```

任何 64 位有符号整数（从 −2^63 到 2^63−1）都应当被接受并无损处理。
如果无法无损表现某个整数，则必须抛出错误。

## [浮点数](#浮点数)

浮点数应当被实现为 IEEE 754 binary64 值。

一个浮点数由一个整数部分（遵从与十进制整数值相同的规则）后跟上一个小数部分和 / 或一个指数部分组成。
如果小数部分和指数部分兼有，那小数部分必须在指数部分前面。

```
flt1 = +1.0
flt2 = 3.1415
flt3 = -0.01


flt4 = 5e+22
flt5 = 1e06
flt6 = -2E-2


flt7 = 6.626e-34

```

小数部分是一个小数点后跟一个或多个数字。

一个指数部分是一个 E（大小写均可）后跟一个整数部分（遵从与十进制整数值相同的规则，但可以包含前导零）。

小数点，如果有用到的话，每侧必须紧邻至少一个数字。

```
invalid_float_1 = .7
invalid_float_2 = 7.
invalid_float_3 = 3.e+20

```

与整数相似，你可以使用下划线来增强可读性。
每个下划线必须被至少一个数字围绕。

```
flt8 = 224_617.445_991_228

```

浮点数值 `-0.0`​ 与 `+0.0`​ 是有效的，并且应当遵从 IEEE 754。

特殊浮点值也能够表示。
它们是小写的。

```
sf1 = inf  
sf2 = +inf 
sf3 = -inf 


sf4 = nan  
sf5 = +nan 
sf6 = -nan 

```

## [布尔值](#布尔值)

布尔值就是你所惯用的那样。
要小写。

```
bool1 = true
bool2 = false

```

## [坐标日期时刻](#坐标日期时刻)

要准确地表示世上的一个特定时间，你可以使用指定了时区偏移量的 [RFC 3339](https://tools.ietf.org/html/rfc3339) 格式的日期时刻。

```
odt1 = 1979-05-27T07:32:00Z
odt2 = 1979-05-27T00:32:00-07:00
odt3 = 1979-05-27T00:32:00.999999-07:00

```

出于可读性的目的，你可以用一个空格字符替代日期和时刻之间的 T（RFC 3339 的第 5.6 节中允许了这样做）。

```
odt4 = 1979-05-27 07:32:00Z

```

毫秒级的精度是必须的。
更高精度的小数秒取决于实现。
如果它的值超出了实现所支持的精度，那超出的部分必须被舍弃，而不能四舍五入。

## [各地日期时刻](#各地日期时刻)

如果你省略了 [RFC 3339](https://tools.ietf.org/html/rfc3339) 日期时刻中的时区偏移量，这表示该日期时刻的使用并不涉及时区偏移。
在没有其它信息的情况下，并不知道它究竟该被转化成世上的哪一刻。
如果仍被要求转化，那结果将取决于实现。

```
ldt1 = 1979-05-27T07:32:00
ldt2 = 1979-05-27T00:32:00.999999

```

毫秒级的精度是必须的。
更高精度的小数秒取决于实现。
如果它的值超出了实现所支持的精度，那多余的部分必须被舍弃，而不能四舍五入。

## [各地日期](#各地日期)

如果你只写了 [RFC 3339](https://tools.ietf.org/html/rfc3339) 日期时刻中的日期部分，那它表示一整天，同时也不涉及时区偏移。

```
ld1 = 1979-05-27

```

## [各地时刻](#各地时刻)

如果你只写了 [RFC 3339](https://tools.ietf.org/html/rfc3339) 日期时刻中的时刻部分，它将只表示一天之中的那个时刻，而与任何特定的日期无关、亦不涉及时区偏移。

```
lt1 = 07:32:00
lt2 = 00:32:00.999999

```

毫秒级的精度是必须的。
更高精度的小数秒取决于实现。
如果它的值超出了实现所支持的精度，那多余的部分必须被舍弃，而不能四舍五入。

## [数组](#数组)

数组是内含值的方括号。
空白会被忽略。
子元素由逗号分隔。
数组可以包含与键值对所允许的相同数据类型的值。
可以混合不同类型的值。

```
integers = [ 1, 2, 3 ]
colors = [ "红", "黄", "绿" ]
nested_array_of_ints = [ [ 1, 2 ], [3, 4, 5] ]
nested_mixed_array = [ [ 1, 2 ], ["a", "b", "c"] ]
string_array = [ "所有的", '字符串', """是相同的""", '''类型''' ]


numbers = [ 0.1, 0.2, 0.5, 1, 2, 5 ]
contributors = [
  "Foo Bar <foo@example.com>",
  { name = "Baz Qux", email = "bazqux@example.com", url = "https://example.com/bazqux" }
]

```

数组可以跨行。
数组的最后一个值后面可以有终逗号（也称为尾逗号）。
值、逗号、结束括号前可以存在任意数量的换行和注释。
数组值和逗号之间的缩进被作为空白对待而被忽略。

```
integers2 = [
  1, 2, 3
]

integers3 = [
  1,
  2, 
]

```

## [表](#表)

表（也被称为哈希表或字典）是键值对的集合。
它们由表头定义，连同方括号作为单独的行出现。
看得出表头不同于数组，因为数组只有值。

```
[table]

```

在它下方，直至下一个表头或文件结束，都是这个表的键值对。
表不保证保持键值对的指定顺序。

```
[table-1]
key1 = "some string"
key2 = 123

[table-2]
key1 = "another string"
key2 = 456

```

表名的规则与键名相同（见前文[键名](#%E9%94%AE%E5%90%8D)定义）。

```
[dog."tater.man"]
type.name = "pug"

```

等价于 JSON 的如下结构：

```
{ "dog": { "tater.man": { "type": { "name": "pug" } } } }

```

键名周围的空格会被忽略。
然而，最佳实践还是不要有任何多余的空白。

```
[a.b.c]          
[ d.e.f ]        
[ g .  h  . i ]  
[ j . "ʞ" . 'l' ]  

```

缩进被作为空白对待而被忽略。

你不必层层完整地写出你不想写的所有途径的父表。
TOML 知道该怎么办。

```
[x.y.z.w] 

[x] 

```

空表是允许的，只要里面没有键值对就行了。

类似于键名，你不能重复定义一个表。
这样做是非法的。

```
[fruit]
apple = "红"

[fruit]
orange = "橙"

```

```
[fruit]
apple = "红"

[fruit.apple]
texture = "光滑"

```

不鼓励无序地定义表。

```
[fruit.apple]
[animal]
[fruit.orange]

```

```
[fruit.apple]
[fruit.orange]
[animal]

```

顶层表，又被称为根表，于文档开始处开始并在第一个表头（或文件结束处）前结束。
不同于其它表，它没有名字且无法后置。

```
name = "Fido"
breed = "pug"


[owner]
name = "Regina Dogman"
member_since = 1999-08-04

```

点分隔键为最后一个键名前的每个键名创建并定义一个表，倘若这些表尚未被创建的话。

```
fruit.apple.color = "red"



fruit.apple.taste.sweet = true



```

由于表不能定义多于一次，不允许使用 `[table]`​ 头重定义这样的表。
同样地，使用点分隔键来重定义已经以 `[table]`​ 形式定义过的表也是不允许的。
不过，`[table]`​ 形式可以被用来定义通过点分隔键定义的表中的子表。

```
[fruit]
apple.color = "红"
apple.taste.sweet = true




[fruit.apple.texture]  
smooth = true

```

## [内联表](#内联表)

内联表提供了一种更为紧凑的语法来表示表。
它们对于分组数据特别有用，否则这些数据很快就会变得冗长。
内联表被完整地定义在花括号之中：`{`​ 和 `}`​。 括号中，可以出现零或更多个以逗号分隔的键值对。
键值对采取与标准表中的键值对相同的形式。
什么类型的值都可以，包括内联表。

内联表得出现在同一行内。
内联表中，最后一对键值对后不允许终逗号（也称为尾逗号）。
不允许花括号中出现任何换行，除非在值中它们合法。
即便如此，也强烈不建议把一个内联表搞成纵跨多行的样子。
如果你发现自己真的需要，那意味着你应该使用标准表。

```
name = { first = "Tom", last = "Preston-Werner" }
point = { x = 1, y = 2 }
animal = { type.name = "pug" }

```

上述内联表等同于下面的标准表定义：

```
[name]
first = "Tom"
last = "Preston-Werner"

[point]
x = 1
y = 2

[animal]
type.name = "pug"

```

内联表是独立自足的，在内部定义全部的键与子表。
不能在括号以外的地方，再添加键与子表。

```
[product]
type = { name = "Nail" }


```

类似地，内联表不能被用于向一个已定义的表添加键或子表。

```
[product]
type.name = "Nail"


```

## [表数组](#表数组)

最后一个还没讲到的语法允许你写表数组。
这可以通过把表名写在双方括号里的表头来表示。
表头的第一例定义了这个数组及其首个表元素，而后续的每个则在该数组中创建并定义一个新的表元素。
这些表按出现顺序插入该数组。

```
[[products]]
name = "Hammer"
sku = 738594937

[[products]]  

[[products]]
name = "Nail"
sku = 284758393

color = "gray"

```

等价于 JSON 的如下结构。

```
{
  "products": [
    { "name": "Hammer", "sku": 738594937 },
    { },
    { "name": "Nail", "sku": 284758393, "color": "gray" }
  ]
}

```

任何对表数组的引用都指向该数组里最近定义的表元素。
这允许你在最近的表内定义子表，甚至子表数组。

```
[[fruits]]
name = "apple"

[fruits.physical]  
color = "red"
shape = "round"

[[fruits.varieties]]  
name = "red delicious"

[[fruits.varieties]]
name = "granny smith"

[[fruits]]
name = "banana"

[[fruits.varieties]]
name = "plantain"

```

上述 TOML 等价于 JSON 的如下结构。

```
{
  "fruits": [
    {
      "name": "apple",
      "physical": {
        "color": "red",
        "shape": "round"
      },
      "varieties": [
        { "name": "red delicious" },
        { "name": "granny smith" }
      ]
    },
    {
      "name": "banana",
      "varieties": [
        { "name": "plantain" }
      ]
    }
  ]
}

```

如果一个表或表数组的父级是一个数组元素，该元素必须在定义子级前先定义。
顺序颠倒的行为，必须在解析时报错。

```
[fruit.physical]  
color = "red"
shape = "round"

[[fruit]]  
name = "apple"

```

若试图向一个静态定义的数组追加内容，即便数组尚且为空，也必须在解析时报错。

```
fruits = []

[[fruits]] 

```

若试图用已经确定为数组的名称定义表，必须在解析时报错。
将数组重定义为普通表的行为，也必须在解析时报错。

```
[[fruits]]
name = "apple"

[[fruits.varieties]]
name = "red delicious"


[fruits.varieties]
name = "granny smith"

[fruits.physical]
color = "red"
shape = "round"


[[fruits.physical]]
color = "green"

```

你也可以适当使用内联表：

```
points = [ { x = 1, y = 2, z = 3 },
           { x = 7, y = 8, z = 9 },
           { x = 2, y = 4, z = 8 } ]

```

## [文件扩展名](#文件扩展名)

TOML 文件应当使用 `.toml`​ 扩展名。

## [MIME 类型](#mime-类型)

在互联网上传输 TOML 文件时，恰当的 MIME 类型是 `application/toml`​。

## [ABNF 语法](#abnf-语法)

TOML 语法的严谨说明，由一个 [ABNF 文件](https://github.com/toml-lang/toml/blob/1.0.0/toml.abnf)另行提供。
