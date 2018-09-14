---
title: "《深入理解ES6》读书笔记之二：字符串和正则表达式"
date: 2018-09-15T04:15:22+08:00
lastmod: 2018-09-15T04:15:22+08:00
draft: false
tags: ['读书笔记', 'JavaScript', 'ES6']
categories: ['前端']
---

## 字符串中的子串识别

我们一直使用 indexOf 方法来在一段字符串中检测另一段字符串，ES6 中提供了一下三个新方法：

- includes() 方法，如果在字符串中检测到指定文本则返回 true ，否则返回 false
- startsWith() 方法，在字符串起始部分检测到指定文本则返回 true ，否则返回 false
- endsWith() 方法，在字符串结尾部分检测到指定文本则返回 true ，否则返回 false

以上三个方法都接收两个参数，第一个参数指定要搜索的文本，第二个参数是可选的，指定一个开始搜索的位置的索引值，如果指定了第二个参数，includes() 和 startWith() 方法会从这个索引值的位置开始匹配，而 endsWith() 方法则会从这个索引值减去欲搜索文本长度的位置开始正向匹配。

```javascript
const str = 'Hello ES6!'

console.log(str.includes('ES6')) // true
console.log(str.startsWith('Hello')) // true
console.log(str.endsWith('!')) // true

console.log(str.includes('ES5')) // false
console.log(str.startsWith('e')) // false
console.log(str.endsWith('6')) // false

console.log(str.includes('ES6', 6)) // true
console.log(str.startsWith('ES6', 6)) // true
console.log(str.endsWith('ES6', 9)) // true

```

## 字符串模板

### 基础语法

使用 反引号`` ` ``代替单引号或双引号：

```javascript
const str1 = `Hello ES6!`

// 在字符串模板中使用反引号(用反斜杠转义)
const str2 = `Hello \`ES6\``
```

### 多行字符串

在字符串模板中，直接换行书写即可生成多行字符串：

```javascript
const str = `
This is first line
This is second line
This is third line
`

// 在浏览器中输出结果为
"
This is first line
This is second line
This is third line
"
```

在反引号中的所有空白字符都属于字符串的一部分，所以上面例子中第一行会有多余的空白，如果需要去掉空白，可以使用字符串的 trim() 方法。

### 字符串占位符

占位符由一个左侧的 `${` 和一个右侧的 `}` 组成，中间可以包含任意的变量和 JavaScript 表达式：

```javascript
const version = 6

function getVersion () {
  return version
}

const str1 = `Hello ES${version}!` // Hello ES6!

const str2 = `Hello ES${version + 1}!` // Hello ES7!

const str3 = `Hello ES${getVersion()}!` // Hello ES6!

```

> 在占位符中嵌入未定义的变量会导致抛出错误。
