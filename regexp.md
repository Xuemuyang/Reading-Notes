# JS正则表达式

## 参考链接

- [JS正则表达式完整教程---老姚](https://juejin.im/post/5965943ff265da6c30653879#heading-37)

## 速查

元字符 `( [ { \ ^ $ | ) ? * + . ] }`

code|detail|简介
---|---|---
`\d`|`[0-9]`|digit
`\D`|`[^0-9]`|除数字外的任意字符
`\w`|`[0-9a-zA-Z_]`|word，单词字符
`\W`|`[^0-9a-zA-Z_]`|非word
`\s`|`[ \t\v\n\r\f]`|space
`\S`|`[^ \t\v\n\r\f]`|非space
`.`|`[^\n\r\u2028\u2029]`|通配符

量词|detail|简介
`{m,}`|-|至少出现 m 次
`{m}`|`{m,m}`|出现 m 次
`?`|`{0,1}`|出现或者不出现
`+`|`{1,}`|至少出现一次
`*`|`{0,}`|出现任意次

量词加 `?` 表示贪婪

```js
{m,n}?
{m,}?
??
+?
*?
```

`|` 管道符表示任何之一 `(p1|p2|p3)`

位置|简介
---|---
`^`|行开头
`$`|行结尾
`\b`|单词边界，`\w` 和 `\W`、`\w` 和 `^`、`\w` 和 `$` 之间的位置
`\B`|非单词边界
`(?=p)`|`p` 是一个模式，这个位置之后内容需要匹配 `p` 这个模式，positive lookahead
`(?!=p)`|取反 negative lookahead
`(?<=p)`|positive lookbehind
`(?<!p)`|negative lookbehind

## 概述

正则表达式是匹配模式，要么匹配字符，要么匹配位置

## 字符匹配

模糊匹配：

- 横向模糊 字符串长度不固定
- 纵向模糊 某一位可以有多种可能

字符组 `[]`，one of，连字符 `-` 用来做简写，字符组第一位 `^` 表示反。

`[123456abcdefGHIJKLM]` 简写 `[1-6a-fG-M]`。

`[^abc]` 表示除 "a"、"b"、"c" 之外的任意一个字符。

`[\d\D]`、`[\w\W]`、`[\s\S]` 和 `[^]` 中的任意一个都可以表示任意字符。

### 重复

量词后面加问号实现非贪婪匹配，默认贪婪，尽可能匹配多的。

```js
{m,n}?
{m,}?
??
+?
*?
```

### 分支

一个模式可以实现横向和纵向模糊匹配，多选分支可以支持多个子模式任选其一。

分支结构与 `||` 操作符类似，短路

```js
var regex = /goodbye|good/g;
var string = "goodbye";
console.log( string.match(regex) );
// => ["goodbye"]
```

## 位置匹配

位置是相邻字符之间的位置

![](https://ws1.sinaimg.cn/large/006tNc79ly1g2s1bgkkkqj30g504dmx9.jpg)

```js
var result = "[JS] Lesson_01.mp4".replace(/\b/g, '#');
console.log(result);
// => "[#JS#] #Lesson_01#.#mp4#"
```

```js
var result = "[JS] Lesson_01.mp4".replace(/\B/g, '#');
console.log(result);
// => "#[J#S]# L#e#s#s#o#n#_#0#1.m#p#4"
```

`(?=p)` positive lookahead

```js
var result = "hello".replace(/(?=l)/g, '#');
console.log(result);
// => "he#l#lo"
```

`(?!p)` negative lookahead

```js
var result = "hello".replace(/(?!l)/g, '#');

console.log(result);
// => "#h#ell#o#"
```

ES6 中支持 `(?<=p)` positive lookbehind

```js
var result = "hello".replace(/(?<=l)/g, '#');
console.log(result);
// => "hel#l#o"
```

`(?<!p)` negative lookbehind

```js
var result = "hello".replace(/(?<!l)/g, '#');
console.log(result);
// => "#h#e#llo#"
```

再看一些 🌰

```js
'a2*3'.replace(/\w(?=\d)/g, 'X')
// => "X2*3"
'a2*34v8'.replace(/\w(?=\d)/g, 'X')
// => "X2*X4X8"
'a2*34vv'.replace(/\w(?=\d)/g, 'X')
// => "X2*X4vv"
'a2*34vv'.replace(/\w(?!=\d)/g, 'X')
// => "aX*3XXX"
```

## 括号

括号用作分组和分支结构，如果不需要捕获使用非捕获组 `(?:p)`

- 分组 让量词作用于括号内的整体
- 分支

反向引用，这里的 `\1` 表示引用第一个分组匹配到的内容

```js
var regex = /\d{4}(-|\/|\.)\d{2}\1\d{2}/;
var string1 = "2017-06-12";
var string2 = "2017/06/12";
var string3 = "2017.06.12";
var string4 = "2016-06/12";
console.log( regex.test(string1) ); // true
console.log( regex.test(string2) ); // true
console.log( regex.test(string3) ); // true
console.log( regex.test(string4) ); // false
```

括号嵌套以左括号为准

```js
var regex = /^((\d)(\d(\d)))\1\2\3\4$/;
var string = "1231231233";
console.log( regex.test(string) ); // true
console.log( RegExp.$1 ); // 123
console.log( RegExp.$2 ); // 1
console.log( RegExp.$3 ); // 23
console.log( RegExp.$4 ); // 3
```

## JavaScript 中的正则表达式操作

API

- String#search
- String#split
- String#match
- String#replace
- RegExp#test
- RegExp#exec

正则表达式的四种操作

- 验证
- 切分
- 提取
- 替换

### RegExp.prototype​.test

验证最常用的是 `test`，注意 `g` 也会对 `test` 有影响，当正则全局匹配的时候，每一次匹配完都会去修改 `lastIndex` 属性，而字符串方法则不存在这个特性。

```js
var regex = /a/g;
console.log( regex.test("a"), regex.lastIndex );
console.log( regex.test("aba"), regex.lastIndex );
console.log( regex.test("ababc"), regex.lastIndex );
// => true 1
// => true 3
// => false 0
```

`test` 是看目标字符串中是否有子串匹配正则，有部分匹配即可，如果要整体匹配，正则需要加上 `^` 和 `$`。

### String​.prototype​.match

[MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/String/match)

`match` 返回结果的格式与正则是否有 `g` 相关

```js
var string = "2017.06.27";
var regex1 = /\b(\d+)\b/;
var regex2 = /\b(\d+)\b/g;
console.log( string.match(regex1) );
console.log( string.match(regex2) );
// => ["2017", "2017", index: 0, input: "2017.06.27"]
// => ["2017", "06", "27"]
```

没有 `g`，数组的第一个元素是整体匹配的内容，接下来是分组捕获的内容，除了数组元素外还有两个对象属性 `input` 和 `index`。

- index 整体匹配的下标
- 目标字符串

有 `g`，返回的是所有匹配的子串，`match` 更适合用来一次获取所有结果

当没有匹配的时候，无论 `g`，都返回 `null`

### RegExp.prototype​.exec

[MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/RegExp/exec)

`exec` 是比 `match` 更强大的方法，在有 `g` 的情况下，返回的信息更多，可以接着上一次匹配后继续匹配，并将更新正则实例的属性如 `lastIndex`，代表下一次匹配的位置。

没有匹配文本返回 `null`。

否则是结果数组，数组的第一个元素是整体匹配的内容，接下来是分组捕获的内容，除了数组元素外还有两个对象属性 `input` 和 `index`。

```js
var string = "2017.06.27";
var regex2 = /\b(\d+)\b/g;
console.log( regex2.exec(string) );
console.log( regex2.lastIndex);
console.log( regex2.exec(string) );
console.log( regex2.lastIndex);
console.log( regex2.exec(string) );
console.log( regex2.lastIndex);
console.log( regex2.exec(string) );
console.log( regex2.lastIndex);
// => ["2017", "2017", index: 0, input: "2017.06.27"]
// => 4
// => ["06", "06", index: 5, input: "2017.06.27"]
// => 7
// => ["27", "27", index: 8, input: "2017.06.27"]
// => 10
// => null
// => 0
```

通常配合 `while` 循环使用：

```js
var string = "2017.06.27";
var regex2 = /\b(\d+)\b/g;
var result;
while ( result = regex2.exec(string) ) {
  console.log( result, regex2.lastIndex );
}
// => ["2017", "2017", index: 0, input: "2017.06.27"] 4
// => ["06", "06", index: 5, input: "2017.06.27"] 7
// => ["27", "27", index: 8, input: "2017.06.27"] 10
```
