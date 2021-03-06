---
title: 正则表达式
date: 2020-1-18 14:11:00
categories:
 - 技术探索
tags: 
 - Linux
 - Shell
 - 正则表达式
mathjax: false
---

正则表达式(Regular Expression)是一种文本模式，包括普通字符（例如，a 到 z 之间的字母）和特殊字符（称为"元字符"）。正则表达式使用单个字符串来描述、匹配一系列匹配某个句法规则的字符串。

## 为什么使用正则表达式

在Linux终端和Vim等文本编辑器中经常会使用到正则表达式来匹配期望的文本，通过使用正则表达式可以：

+ 测试字符串内的模式， 例如查看字符串内是否出现电话号码或信用卡号码等；
+ 替换文本， 识别特定文本并进行相关操作；
+ 基于模式匹配从字符串中提取子字符串，可以查找文档内或输入域内特定的文本。

例如，可以使用`data(\w)?\.dat`可以匹配以下内容：

```txt
    data.dat
    data1.dat
    datax.dat
    dataN.dat
```

`?`可以匹配文件名中的0个或1个字符，而`*`通配符匹配0个或多个字符。`data.*\.dat`可以匹配以下内容：

```txt
    data.dat
    data2.dat
    data12.dat
    dataxYZ.dat
```

## 语法

正则表达式描述了一种字符串匹配的模式（pattern），可以用来检查一个串是否含有某种子串、将匹配的子串替换或者从某个串中取出符合某个条件的子串等。

例如，

+ `runoo+b`，可以匹配 runoob、runooob、runoooooob 等，`+` 号代表前面的字符必须至少出现一次（1次或多次）。

+ `runoo*b`，可以匹配 runob、runoob、runoooooob 等，`*` 号代表字符可以不出现，也可以出现一次或者多次（0次、或1次、或多次）。

+ `colou?r` 可以匹配 color 或者 colour，`?` 问号代表前面的字符最多只可以出现一次（0次、或1次）。

### 普通字符

普通字符包括没有显式指定为元字符的所有可打印和不可打印字符。这包括所有*大写*和*小写*字母、所有*数字*、所有*标点符号*和一些其他符号。

### 非打印字符

| 字符    |       描述      |
| :--- | :--- |
| `\cx` | 匹配由x指明的控制字符。例如， \cM 匹配一个 Control-M 或回车符。x 的值必须为 A-Z 或 a-z 之一。否则，将 c 视为一个原义的 'c' 字符 |
| `\f` | 匹配一个换页符。等价于 \x0c 和 \c  |
| `\n`  | 匹配一个换行符。等价于 \x0a 和 \cJ |
| `\r`  | 匹配一个回车符。等价于 \x0d 和 \cM |
| `\s`  | 匹配任何空白字符，包括空格、制表符、换页符等等。等价于 [ \f\n\r\t\v]。注意 Unicode 正则表达式会匹配全角空格符 |
| `\S`  | 匹配任何非空白字符。等价于 [^ \f\n\r\t\v] |
| `\t`  | 匹配一个制表符。等价于 \x09 和 \cI |
| `\v`  | 匹配一个垂直制表符。等价于 \x0b 和 \cK |

### 特殊字符

所谓特殊字符，就是一些有特殊含义的字符，如上面说的 `runoo*b` 中的 `*`，简单的说就是表示任何字符串的意思。如果要查找字符串中的 \* 符号，则需要对 \* 进行转义，即在其前加一个 `\`: `runo\*ob` 匹配 runo\*ob。

许多元字符要求在试图匹配它们时特别对待。若要匹配这些特殊字符，必须首先使字符"转义"，即，将反斜杠字符`\`放在它们前面。下表列出了正则表达式中的特殊字符：

| 字符    |       描述      |
| :--- | :--- |
| `$` | 匹配输入字符串的结尾位置。如果设置了 RegExp 对象的 Multiline 属性，则 $ 也匹配 '\n' 或 '\r'。要匹配 $ 字符本身，请使用 \$ |
| `()` | 标记一个子表达式的开始和结束位置。子表达式可以获取供以后使用。要匹配这些字符，请使用 `\(` 和 `\)` |
| `*` | 匹配前面的子表达式0次或多次。要匹配 * 字符，请使用 `\*` |
| `+` | 匹配前面的子表达式1次或多次。要匹配 + 字符，请使用 `\+` |
| `.` | 匹配除换行符 `\n` 之外的任何单字符。要匹配 . ，请使用 `\.` |
| `[` | 标记一个中括号表达式的开始。要匹配 [，请使用 `\[` |
| `?` | 匹配前面的子表达式0次或1次，或指明一个**非贪婪**限定符。要匹配 ? 字符，请使用 `\?` |
| `\` | 将下一个字符标记为或特殊字符、或原义字符、或向后引用、或八进制转义符。例如， `n` 匹配字符 n。`\n` 匹配换行符。序列 `\\` 匹配 \、，而 `\(` 则匹配 ( |
| `^` | 匹配输入字符串的开始位置，除非在方括号表达式中使用，当该符号在方括号表达式中使用时，表示不接受该方括号表达式中的字符集合。要匹配 ^ 字符本身，请使用 `\^` |
| `{` | 标记限定符表达式的开始。要匹配 {，请使用`\{`  |
| &#124;  | 指明两项之间的一个选择。要匹配 &#124;， 请使用 \&#124; |

### 限定符

限定符用来指定正则表达式的一个给定组件必须要出现多少次才能满足匹配。有 `*`或 `+` 或 `?` 或 `{n}` 或 `{n,}` 或 `{n,m}` 共6种。正则表达式的限定符有：

| 字符    |       描述      |
| :--- | :--- |
| `*`  | 匹配前面的子表达式0次或多次。例如，`zo*` 能匹配 z 以及 zoo。`*` 等价于`{0,}` |
| `+`  | 匹配前面的子表达式1次或多次。例如，`zo+` 能匹配 zo 以及 zoo，但不能匹配 z。`+` 等价于 `{1,}` |
| `?`  | 匹配前面的子表达式0次或1次。例如，`do(es)?` 可以匹配 do 、 does 中的 does 、 doxy 中的 do 。`?` 等价于 `{0,1}` |
| `{n}`| n 是一个非负整数。匹配确定的 n 次。例如，`o{2}` 不能匹配 Bob 中的 o，但是能匹配 food 中的2个 o |
| `{n,}` | n 是一个非负整数。至少匹配n 次。例如，`o{2,}` 不能匹配 Bob 中的 o，但能匹配 foooood 中的所有 o。`o{1,}` 等价于 `o+`。`o{0,}` 则等价于 `o*` |
| `{n,m}` | m 和 n 均为非负整数，其中n <= m。最少匹配 n 次且最多匹配 m 次。例如，`o{1,3}` 将匹配 fooooood 中的前三个 o。`o{0,1}` 等价于 `o?`。请注意在逗号和两个数之间不能有空格 |

以下正则表达式匹配一个正整数， `[1-9]`设置第一个数字不是0， `[0-9]*`表示任意多个数字：

```regexp
/[1-9][0-9]*/
```

请注意，限定符出现在范围表达式之后。因此，它应用于整个范围表达式，在本例中，只指定从 0 到 9 的数字（包括 0 和 9）。这里不使用 + 限定符，因为在第二个位置或后面的位置不一定需要有一个数字。也不使用 ? 字符，因为使用 ? 会将整数限制到只有两位数。如果你想设置 0~99 的两位数，可以使用下面的表达式来至少指定1位但至多2位数字：

```regexp
/[0-9]{1,2}/
```

上面的表达式的缺点是，只能匹配两位数字，而且可以匹配 0、00、01、10 99 的章节编号仍只匹配开头两位数字。改进下，匹配 1~99 的正整数表达式如下：

```regexp
/[1-9][0-9]?/
```

`*` 、`+` 限定符都是**贪婪**的，因为它们会**尽可能多**的匹配文字，只有在它们的后面加上一个 `?` 就可以实现**非贪婪**或**最小匹配**。

例如，您可能搜索 HTML 文档，以查找在 h1 标签内的内容。HTML 代码如下：

```xml
<h1>RUNOOB-菜鸟教程</h1>
```

**贪婪**：下面的表达式匹配从开始小于符号 (<) 到关闭 h1 标记的大于符号 (>) 之间的所有内容。

```regexp
/<.*>/
```

**非贪婪**：如果您只需要匹配开始和结束 h1 标签，下面的非贪婪表达式只匹配 \<h1\>。

```regexp
/<.*?>/
```

也可以使用以下正则表达式来匹配 h1 标签，表达式则是：

```regexp
/<\w+?>/
```

通过在 `*` 、`+` 或 `?` 限定符之后放置 `?`，该表达式从**贪婪**表达式转换为**非贪婪**表达式或者**最小匹配**。

未完待续。
