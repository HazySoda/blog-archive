---
title: "《深入理解ES6》读书笔记之三：函数"
date: 2018-09-15T04:15:25+08:00
lastmod: 2018-09-15T04:15:25+08:00
draft: false
tags: ['读书笔记', 'JavaScript', 'ES6']
categories: ['前端']
---

## 函数形参的默认值

ES6 中，定义函数时为形参赋值即可为其提供一个默认值：

```javascript
function add (a, b = 1) {
  return a + b
}

add(1) // 2

add(1, 2) // 3
```

## 参数默认值对 arguments 对象的影响

ES6 中，如果一个函数使用了默认参数值，则其行为将与 ES5 严格模式下保持一致，无论参数如何变化，arguments 对象不再随之改变：

```javascript
function add (a, b = 1) {
  console.log(arguments.length) // 1
  console.log(a === arguments[0]) // true
  console.log(b === arguments[1]) // false
  a = 5
  b = 10
  console.log(a === arguments[0]) // false
  console.log(b === arguments[1]) // false
}

add(1)
```

在上例中，由于使用了默认参数值，在函数中改变参数的值并不会影响 arguments 对象。

## 默认参数表达式

默认参数的值可以为一个 JavaScript 表达式：

```javascript
function getValue () {
  return 5
}

function add (a, b = getValue()) {
  return a + b
}

add(1) // 6
```

在初次解析函数声明时作为参数默认值的表达式不会执行，只有在调用函数且不传入带有默认值的参数时才会进行调用，例如：

```javascript
let value = 5

function getValue () {
  return value++
}

function add (a, b = getValue()) {
  return a + b
}

add(1, 1) // 2

add(1) // 6

add(1) // 7
```

## 默认参数的临时死区

因为默认参数是在函数调用时求值的，所以可以使用先定义的参数作为后定义参数的默认值：

```javascript
function add (a, b = a) {
  return a + b
}

add(2) // 4
```

但如果用后定义参数作为先定义参数的默认值，则会报错：

```javascript
function add (a = b, b) {
  return a + b
}

add(undefined, 1) // 报错
```

原因是当参数 a 初始化时，参数 b 还处在临时死区中，所有引用临时死区中变量的行为都会抛出错误。

> 函数参数有自己的作用域和临时死区，其与函数体的作用域是相互独立的，所以参数默认值不能访问函数体内声明的变量。

## 不定参数

ES6 中，在函数的命名参数前加三个点（`...`）就表明这是一个不定参数，该参数为一个数组，包含着自它之后传入的所有参数：

```javascript
function restParams (a, ...rest) {
  console.log(rest)
}

restParams(1, 2, 3, 4, 5) // [2, 3, 4, 5]
```

不定参数有两条使用限制：

- 每个函数最多只能声明一个不定参数，且一定要放在所有参数的末尾
- 不定参数不能用于对象字面量 setter 中，因为对象字面量 setter 的参数有且只能有一个

另外，无论是否使用不定参数，arguments 对象总是包含所有传入函数的参数。

## 展开运算符

展开运算符可以让你指定一个数组，将它打散后作为各自独立的参数传入函数，以取数组中最大数字为例：

```javascript
const arr = [1, 2, 3, 4, 5]

// ES5
Math.max.apply(Math, arr) // 5

// ES6
Math.max(...arr) // 5
```

另外，可以将展开运算符和其他正常传入的参数混合使用：

```javascript
const arr = [1, 2, 3, 4, 5]

Math.max(...arr, 6, 7) // 7
```

## 明确函数的多重用途

JavaScript 函数有两个不同的内部方法：[[Call]] 和 [[Construct]] ，当通过 new 关键字调用函数时，执行的是 [[Construct]] 函数，它负责创建一个通常被称作实例的新对象，然后再执行函数体，将 this 绑定到实例上。如果不通过 new 关键字调用函数，则执行 [[Call]] 函数，直接执行函数体中的代码。具有 [[Construct]] 方法的函数被统称为构造函数。

> 不是每个函数都有 [[Construct]] 方法，比如箭头函数。因此不是所有函数都可以通过 new 关键字调用。

## 元属性

元属性是指非对象的属性，其可以提供非对象目标的补充信息。

### new.target

为了解决判断函数是否通过 new 关键字调用的问题，ES6 引入了 new.target 这个元属性。当调用函数的 [[Construct]] 方法时，new.target 被赋值为 new 操作符的目标，通常是新创建的实例，也就是函数体内 this 的构造函数。如果调用的是 [[Call]] 方法，则 new.target 的值为 undefined。

```javascript
function Person (name) {
  if (typeof new.target !== 'undefined') {
    this.name = name
  } else {
    throw new Error('必须通过 new 关键字调用 Person')
  }
}

const newPerson = new Person('Nicholas') // OK

const notPerson = Person.call(Person, 'Michael') // 报错
```

也可以检查 new.target 是否被某个特定的构造函数所调用：

```javascript
function Person (name) {
  if (typeof new.target !== 'undefined') {
    this.name = name
  } else {
    throw new Error('必须通过 new 关键字调用 Person')
  }
}

function AnotherPerson (name) {
  Person.call(this, name)
}

const newPerson = new Person('Nicholas') // OK

const notPerson = new AnotherPerson('Michael') // 报错
```

## 块级函数

ES6中，在块级作用域内，如果使用函数声明来定义函数，函数会被提升至顶部，因此即使你再函数定义的位置之前调用该函数，也能返回正确结果。但如果使用 let 或 const 关键字定义函数表达式，函数则不会被提升，且代码块中的代码一旦执行完毕，函数将立刻被销毁。

> 如果处于非严格模式，块级函数将不再提升至代码块顶部，而是提升至外围函数或全局作用域的顶部。

## 箭头函数

箭头函数是 ES6 中新增的一个特性，顾名思义，箭头函数是使用箭头（`=>`）定义函数的新语法，他与传统的函数有以下几点区别：

- 没有 this, super, arguments 和 new.target 绑定，这些值由外围最近一层非箭头函数决定
- 不能通过 new 关键字调用，因为箭头函数没有 [[Construct]] 方法
- 没有原型，因为不能通过 new 调用，因此没有也无需拥有 prototype 属性
- 不支持 arguments 对象，只能通过命名参数和不定参数两种形式访问函数的参数
- 不支持重复的命名参数（传统函数只有在严格模式下才不支持重复的命名参数）

### 箭头函数语法

无参数：

```javascript
const func = () => {
  return 'Arrow Function'
}
```

一个参数：

```javascript
const func = param => {
  return param
}
```

多个参数：

```javascript
const func = (a, b, c) => {
  return [a, b, c]
}
```

返回值简写：

```javascript
const func = (a, b) => a + b
```

返回对象字面量，需要将对象字面量包裹在小括号中，从而与函数体区分开来：

```javascript
const func = () => ({
  foo: 'foo',
  bar: 'bar'
})
```

### 创建立即执行函数

使用箭头函数创建立即执行函数十分方便且整洁：

```javascript
const person = (name => ({
  getName: () => {
    return name
  }
}))('Nicholas')

person.getName() // Nicholas
```

## 尾调用优化

在 ES5中，尾调用的实现与其他函数的实现类似：创建一个新的栈帧（stack frame），将其推入调用栈来表示函数调用。也就是说，在循环调用中，每一个未用完的栈帧都会被保存在内存中，当调用栈变得过大时会造成程序问题。

ES6 在**严格模式**中对尾调用系统做了优化，如果满足以下条件，尾调用不再创建新的栈帧，而是清除并重用当前栈帧：

- 尾调用不访问当前栈帧的变量（也就是说函数不是一个闭包）
- 在函数内部，尾调用是最后一条语句
- 尾调用的结果作为函数值返回

> 递归是尾调用优化最主要的应用场景，此时尾调用优化的效果也最显著。
