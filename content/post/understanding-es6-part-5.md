---
title: "《深入理解ES6》读书笔记之五：解构"
date: 2018-09-18T22:36:35+08:00
lastmod: 2018-09-18T22:36:35+08:00
draft: false
tags: ['读书笔记', 'JavaScript', 'ES6']
categories: ['前端']
---

对象和数组字面量是 JavaScript 中两种最常用的数据结构，我们经常定义许多对象和数组，然后有组织地从中提取相关的信息片段。ES6 中添加了可以简化这种任务的新特性：解构。解构是一种打破数据结构，将其拆分为更小部分的过程。

## 对象解构

对象解构的语法形式是在一个赋值操作符左边放置一个对象字面量：

```javascript
let node = {
  type: 'Identifier',
  name: 'foo'
}

let { type, name } = node

console.log(type) // Identifier

console.log(name) // foo
```

> 如果使用 var、let 或 const 解构声明变量，则必须要提供初始化程序（也就是等号右侧的值），否则会抛出语法错误。

### 解构赋值

```javascript
let type = 'Literal'
let name = 5

let node = {
  type: 'Identifier',
  name: 'foo'
}

({ type, name } = node)

console.log(type) // Identifier

console.log(name) // foo
```

在上面的代码中，通过解构赋值的方法，从 node 对象读取相应的值并重新为 type、name 两个变量赋值。注意，一定要用小括号包裹解构赋值语句，因为 JavaScript 引擎将一对开放的花括号视为一个代码块，而语法规定，代码块语句不允许出现在赋值语句左侧，添加小括号可以将块语句转化为一个表达式，从而实现解构赋值。

> 解构赋值表达式（也就是等号右侧的表达式）如果为 null 或 undefined 会导致抛出错误，因为任何尝试读取 null 或 undefined 的属性的行为都会导致错误。

### 默认值

使用解构赋值表达式时，如果指定的局部变量名称在对象中不存在，那么这个局部变量会被赋值为 undefined ，为了防止这种情况发生，也可以主动定义一个默认值：

```javascript
let node = {
  type: 'Identifier',
  name: 'foo'
}

let { type, value, status = true } = node)

console.log(type) // Identifier

console.log(value) // undefined

console.log(status) // true
```

### 为非同名局部变量赋值

为非同名局部变量赋值，其实也可以理解为给用于解构的局部变量定义一个别名：

```javascript
let node = {
  type: 'Identifier',
  name: 'foo'
}

let { type: localType, value: localValue, status: localStatus = true } = node)

console.log(localType) // Identifier

console.log(localValue) // undefined

console.log(localStatus) // true
```

### 嵌套对象解构

解构嵌套对象仍然与对象字面量的语法类似：

```javascript
let node = {
  type: 'Identifier',
  name: 'foo',
  loc: {
    start: {
      line: 1,
      column: 1
    },
    end: {
      line: 1,
      column: 4
    }
  }
}

let { loc: { start }} = node

console.log(start.line) // 1

console.log(start.column) // 1
```

我们在解构模式中使用了花括号，其含义为在找到 node 对象中的 loc 属性后，再深入一层继续查找 start 属性。所有冒号前的标识符都代表在对象中的检索位置，其右侧为被赋值的变量名。如果冒号后面是花括号，则意味着要赋予的最终值嵌套在对象内部更深的层级中。

> 如果对嵌套过深的对象进行解构，代码可读性很容易受到影响，应斟酌后使用。

## 数组解构

```javascript
let colors = ['red', 'green', 'blue']

let [ firstColor, secondColor ] = colors

console.log(firstColor) // red

console.log(secondColor) // green
```

在数组解构语法中，我们通过值在数组中的位置进行选取，且可以将其存储在任意变量中，未显式声明的元素都会直接被忽略，在这个过程中，数组本身不会发生任何变化。

> 和对象解构类似，使用 var、let 或 const 声明数组解构的绑定时，必须要提供一个初始化程序。

### 解构赋值

数组解构也可用于赋值上下文，但不需要用小括号包裹表达式：

```javascript
let firstColor = 'black'

let secondColor = 'purple'

let colors = ['red', 'green', 'blue']

[ firstColor, secondColor ] = colors

console.log(firstColor) // red

console.log(secondColor) // green
```

数组解构还有一个独特的用法，那就是交换两个变量的值：

```javascript
let a = 1

let b = 2

[ a, b ] = [ b, a ]

console.log(a) // 2

console.log(b) // 1
```

> 和对象解构类似，如果数组解构表达式右侧的值为 null 或 undefined，会导致抛出错误。

### 默认值

也可以在数组解构赋值表达式中为数组的任意位置添加默认值：

```javascript
let colors = ['red']

[ firstColor, secondColor = 'green' ] = colors

console.log(firstColor) // red

console.log(secondColor) // green
```

### 嵌套数组解构

嵌套数组解构与嵌套对象解构的语法类似，在原有的数组模式中插入另一个数组模式，即可将解构过程深入到下一个层级：

```javascript
let colors = ['red', ['green', 'lightgreen'], 'blue']

let[ firstColor, [ secondColor ] ] = colors

console.log(firstColor) // red

console.log(secondColor) // green
```

> 和对象中一样，在数组中也可以无限深入去解构。

### 不定元素

在数组中，可以通过展开运算符语法将数组中的其余元素赋值给另一个特定的变量：

```javascript
let colors = ['red', 'green', 'blue']

let[ firstColor, ...restColors ] = colors

console.log(restColors) // ['green', 'blue']
```

不定元素还有一种有趣的应用，那就是克隆数组：

```javascript
let colors = ['red', 'green', 'blue']

let [ ...clonedColors ] = colors

console.log(clonedColord) // ['red', 'green', 'blue']
```

## 混合解构

可以混合使用对象解构和数组解构来创建更多复杂的表达式，如此便可以从任何混杂着对象和数组的数据结构中提取想要的信息：

```javascript
let node = {
  type: 'Identifier',
  name: 'foo',
  loc: {
    start: {
      line: 1,
      column: 1
    },
    end: {
      line: 1,
      column: 4
    },
    range: [0, 3]
  }
}

let {
  loc: { start },
  range: [ startIndex, endIndex ]
} = node

console.log(start.line) // 1

console.log(start.column) // 1

console.log(startIndex) // 0

console.log(endIndex) // 3
```

## 解构参数

定义解构参数，可以更清晰地了解函数预期传入的参数，解构需要用对象或数组解构模式来代替命名参数：

```javascript
function setCookie (name, value, { secure, path, domain, expires }) {
  // 函数内部代码
}

setCookie('type', 'js', {
  secure: true,
  expires: 60000
})
```

### 必须传值的解构参数

之前我们已经知道，如果对象或数组解构表达式右侧的值为 null 或 undefined，程序会抛出错误，那么我们就必须为解构参数传递默认值来避免这种情况的发生：

```javascript
function setCookie (name, value, { secure, path, domain, expires } = {}) {
  console.log(secure) // undefined
  console.log(path) // undefined
}

setCookie('type', 'js')
```

这样一来如果我们在实际调用中不传递解构参数，解构参数中包含的变量都会被赋值为 undefined，如果我们不希望这样，就要为解构参数中的变量也设置一个默认值：

```javascript
function setCookie (name, value, { secure = true, path, domain, expires } = {}) {
  console.log(secure) // true
  console.log(path) // undefined
}

setCookie('type', 'js')
```

但是这样做会导致代码看起来很冗余，因此我们可以将默认值提取到一个独立的对象中，并使用这个对象作为解构参数的默认值，从而消除这些冗余：

```javascript
const defaultParams = {
  secure: true,
  path: '/',
  domain: 'example.com',
  expires: 60000
}

function setCookie (name, value, { secure, path, domain, expires } = defaultParams) {
  console.log(secure) // true
  console.log(domain) // example.com
}

setCookie('type', 'js')
```
