---
layout:     post
title:      【正则表达式系列】基本用法
category: blog
tags: 正则表达式
favour: 正则表达式
description: 正则表达式的一些基本用法，match，test，exec，replace等
---

## 前言

本文属于 正则表达式系列文章之一，更多请前往  [正则表达式系列](http://www.jianshu.com/p/d04be43416fb)

在`快速入门`一文中，示例都是通过`test()`方法演示，本文会继续介绍正则的其它用法(以`JS`语言为例)

## 大纲

- 正则对象

  - 生成正则对象

  - 可选项标记

  - 子表达式
  
  - 正则的`test`方法

  - 正则的`compile`方法

  - 正则的`exec`方法

- 字符串的正则应用

  - 字符串的`match`方法

  - 字符串的`replace`方法

  - 字符串的`search`方法

  - 字符串的`split`方法

## 正则对象

### 生成正则对象

有两种方法可以创建并得到一个正则表达式对象

- 字面量声明方式

  ```js
  var reg = /abc/g; 
  ```

- 显式创建

  ```js
  var reg =new RegExp("abc", "g");
  ```

### 可选项标记

上文生成正则对象中最后有用到`g`，其实这就是生成正则的可选项标记，可以组合使用，如下

- `g` 全文匹配，即匹配一个成功后，如果没有结束，会继续匹配

- `i` 忽略大小写匹配

- `m` 多行匹配

可以通过如下示例进一步了解

__`/g`的用法__

加上`/g`后会全局搜索所有匹配结果

```
var str = 'abcabcabc';

// ['abc', index: 0, input: "abcabcabc"]
str.match(/abc/);

// ['abc', 'abc', 'abc']
str.match(/abc/g);
```

__`/i`的用法__

加上`/i`后大小写一视同仁，很多时候，如果不加`/i`，很容易出现大小写不匹配的错误(比如去除字符串中`script`标签时)

```
var str = 'Script';

// null
str.match(/script/);

// ["Script", index: 0, input: "Script"]
str.match(/script/i);
```

__`/m`的用法__

多行匹配比较少用到，但在一些场景下不得不用，比如下例中，只有`\m`多行匹配的情况下才能正常的将每一行的行尾(`\n`形成不同的行)的数字替换成`#`，否则默认会认为只有一行

```
var str = 'a1\nb1\nc1\nd1\ne1\nf1';

/**
 * a1
 * b1
 * c1
 * d1
 * e1
 * f#
 */
str.replace(/\d+$/g, '#')

/**
 * a#
 * b#
 * c#
 * d#
 * e#
 * f#
 */
str.replace(/\d+$/mg, '#')
```

### 子表达式

正则表达式中，用圆括号包围的就是子表达式(子模式)

一般，在`match`的非全局匹配中或`exec`中，在匹配完整个正则表达表达式后，都会再次匹配子表达式

另外，在字符串的`replace`与`split`中，子表达式也会经常用到

```js
var str = 'a1.b2.c3.d4';

// 第一个结果是 整个正则表达式的匹配，之后则分别是子表达式的匹配
/(\w)(\d)[.]/.exec(str); // ["a1.", "a", "1", index: 0, input: "a1.b2.c3.d4"]
```

### 正则的test() 

```js
pattern.test(str);
```

检索字符串中是否存在指定模式，匹配成功则返回`true`，否则返回`false`

```js
var str = 'abcdefg';

/^abc/.test(str); // true

/^abce/.test(str); // false
```

### 正则的compile()

```js
reg.compile(pattern);
```

编译正则表达式，编译之后正则的执行速度会提高

编译的时候也可以改变检索模式或者改变可选项标识

```js
var str = 'abcdabcdabc';
var reg = /abc/;

reg.test(str)； // true

reg.compile();

reg.test(str)； // true，仅仅是被编译，没有被改变

reg.compile(/aB/); 

reg.test(str)； // false，匹配式改成了aB，因此不匹配

reg.compile(/aB/i); 

reg.test(str)； // true，改成了aB并且忽略大小的形式，因此匹配
```

### 正则的exec()

```js
pattern.exec(str);
```

在字符串中检索特定的模式，匹配成功则返回找到的值，否则返回`null`

有两种情况

__第一种:非全局匹配__

如果没有找到，则返回`null`

找到则返回一个数组，`arr[0]`是匹配结果，余下元素是arr[0]中匹配圆括号中子表达式的结果，以及最后的`index`和`input`

而且非全局模式中，不会保存`index`，也就是说不管匹配多少次，结果都是一样的

```js
var str = 'a1.b2.c3.d4';
var reg1 = /(\w)(\d)[.]/;

reg1.exec(str); // ["a1.", "a", "1", index: 0, input: "a1.b2.c3.d4"]
reg1.exec(str); // ["a1.", "a", "1", index: 0, input: "a1.b2.c3.d4"]
reg1.exec(str); // ["a1.", "a", "1", index: 0, input: "a1.b2.c3.d4"]

/abc/.exec(str); // null
```

__第二种:`g`全局匹配__

正则的`exec`全局匹配可以保存`index`，并且下一次继续匹配时，将不会是重新从`0`开始，而是从保存的`index`开始

```js
var str = 'a1.b2.c3.d4';
var reg2 = /(\w)(\d)[.]/g;

reg2.exec(str); // ["a1.", "a", "1", index: 0, input: "a1.b2.c3.d4"]
reg2.exec(str); // ["b2.", "b", "2", index: 3, input: "a1.b2.c3.d4"]
reg2.exec(str); // ["c3.", "c", "3", index: 6, input: "a1.b2.c3.d4"]

/abc/.exec(str); // null
```

## 字符串的正则应用

上文中提到的都是正则对象上的方法，但实际上，`JS`的`String`对象也支持正则表达式

### 字符串的match()

`match`是字符串中最常用的方法

```js
str.match(pattern);
```

如果`pattern`中有`g`，代表全局匹配，则返回的数组包含所有匹配结果

如果无`g`，则返回的数组的第`1`个元素(`arr[0]`)是第一个匹配结果，余下元素是`arr[0]`中匹配圆括号中子表达式的结果

```js
var str = 'a.b2.c3.d445.e';

str.match(/\d[.]/); // ["2.", index: 3, input: "a.b2.c3.d445.e"]

// 非全局匹配下，并且有圆括号子表达式，先匹配整个正则表达式一次
// 然后在匹配结果中再匹配子表达式
str.match(/(\d)[.]/); // ["2.", "2", index: 3, input: "a.b2.c3.d445.e"]

// g 模式下是对整个正则表达式(包括圆括号子表达式)进行全局匹配
str.match(/(\d)[.]/g); // ["2.", "3.", "5."]
```

### 字符串的replace()

字符串中用来快速替换的方法，有多种用法

__第一种情况__

```js
str.replace(str1, str2);
```

第一个参数是字符串，那么返回的结果只有`str1`被替换成`str2`了

```js
var str = 'a.b2.c3.d4';

// 只会替换 . 一次
str.replace('.', '#'); // a#b2.c3.d445.e
```

__第二种情况__

```js
str.replace(pattern, str2);
```

第一个参数是正则表达式，此时如果是`g`全局匹配模式，会替换所有的匹配结果为`str2`，否则只会替换一个匹配结果

```js
var str = 'a.b2.c3.d4';

str.replace(/[.]/, '#'); // a#b2.c3.d445.e

str.replace(/[.]/g, '#'); // a#b2#c3#d445#e
```

另外此模式下，`str2`还可以使用一些有特殊含义的特殊字符，例如

```js
var str = 'a1.b2.c3.d4';

// $2和$1分别代表第2个,第1个子表达式
str.replace(/(\w)(\d)[.]*/g, '$2$1~'); // 1a~2b~3c~4d~
```

`str2`中可用的特殊字符表

| 字符| 替换|
| :------------- |:-------------|
| $1,$2,...,$99 | 匹配第1~99个pattern中的圆括号子表达式的文本 |
| $& | 匹配pattern的子串 |
| $` | 匹配子串的左边文本 |
| $' | 匹配子串的右边文本 |
| $$ | 美元符号 |

__第三种情况__

```js
str.replace(pattern, func);
```

这种模式下，第二个参数为一个函数，`func`将会在每一个匹配结果中调用，`func`返回的字符串将作为替换文本，`func`接收的参数，第一个是匹配`pattern`的字符串，之后的参数(可能是多个)是匹配该`pattern`中某个圆括号子表达式的字符串，在这之后的参数就是`index`(匹配结果的位置)，再之后就是完整的`input`

```js
var str = a1.b2.c3.d4;

// 最终结果为: 1a~2b~3c~4d~
str.replace(/(\w)(\d)[.]*/g, function(word, child1, child2, index, input) {
    
    console.log(word); // 依次打印的是a1. b2. c3. d4
    console.log(child1); // 依次打印的是a b c d
    console.log(child2); // 依次打印的是1 2 3 4
    console.log(index); // 依次打印的是0 3 6 9
    console.log(input); // 每次都是打印 a1.b2.c3.d4
    
    return child2 + child1 + '~';
});
```

### 字符串的search()

返回第`1`个与patten匹配的字符串子串的起始位置，如果找不到，则返回`-1`，不支持全局检索，也就是说，会省略`g`

```js
var str = 'abcdefg1234567';

str.search(/efg/); // 4
str.search(/efg/g); // 4
str.search(/aabc/g); // -1
```

### 字符串的split()

`split`方法可以让一个字符串分割成数组，同样忽略`g`

```js
str.split(pattern, limit); // pattern为字符串或正则
```

将`str`拆分为子串组成的数组，子串中不包括`pattern`（特例除外）。`limit`是可选参数，指定返回的数组的最大长度

特例: 如果`pattern`包含圆括号子表达式，则匹配这个圆括号子表达式的子串(不是匹配整个正则)会包含在返回数组中

```js
var str = 'a1.b2.c3.d4';

str.split(/\d[.]/); // ["a", "b", "c", "d4"]

// 包含了圆括号的子串，返回数组中出现了匹配圆括号的子串
str.split(/(\d)[.]/); // ["a", "1", "b", "2", "c", "3", "d4"]

var str2 = '.b2.c3.';

// 3.后面没有字符了，但是由于它符合匹配，所以后续结果中会多一个""
str2.split(/\d[.]/); // [".b", "c", ""]

// 同上，只不过圆括号中的内容也在返回结果中
str.split(/(\d)[.]/); // [".b", "2", "c", "3", ""]
```

## 附录

### 参考资料

* [正则表达式元字符](http://www.runoob.com/regexp/regexp-metachar.html)

* [js中的正则表达式入门](http://www.cnblogs.com/chenmeng0818/p/6370819.html)

* [js中正则表达式的模式匹配](http://www.cnblogs.com/seven7seven/p/4778726.html)

* [精通 JS正则表达式](http://www.cnblogs.com/aaronjs/archive/2012/06/30/2570970.html)