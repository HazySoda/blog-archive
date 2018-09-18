---
title: "《深入理解ES6》读书笔记之四：扩展对象的功能性"
date: 2018-09-18T22:36:33+08:00
lastmod: 2018-09-18T22:36:33+08:00
draft: false
tags: ['读书笔记', 'JavaScript', 'ES6']
categories: ['前端']
---

## 对象的类别

ES6 标准清晰定义了每一个类别的对象：

- 普通对象（Ordinary）：具有 JavaScript 对象所有的默认内部行为。
- 特异对象（Exotic）：具有某些与默认行为不符的内部行为。
- 标准对象（Standard）：ES6 标准中定义的对象，例如 Array、Date 等，标准对象既可以是普通对象，也可以是特异对象。
- 内建对象：脚本开始执行时存在于 JavaScript 执行环境中的对象，所有标准对象都是内建对象。

## 对象字面量语法扩展

ES6 扩展了对象字面量语法，让对象字面量变得更强大、更简洁。

### 属性初始值的简写

当一个对象的属性与本地变量同名时，不必再写冒号和值，简单地只写属性名即可：

```javascript
function createPerson (name, age) {
  return {
    name,
    age
  }
}

createPerson('Nicholas', 30) // {name: 'Nicholas', age: 30}
```

### 对象方法的简写语法

ES6 中，对象方法的语法更简洁，消除了冒号和 function 关键字：

```javascript
var person = {
  name: 'Nicholas',
  sayName () {
    console.log(this.name)
  }
}

person.sayName() // Nicholas
```

> 简写语法和正常写法的唯一区别是使用简写语法的方法可以使用 super 关键字。

### 可计算属性名

ES6 中，可以在对象字面量中使用可计算属性名，其语法和引用对象实例的可计算属性名相同，也是使用方括号：

```javascript
let suffix = 'Name'

let person = {
  firstName: 'Nicholas',
  ['Last' + suffix]: 'Zakas'
}

console.log(person) // {firstName: 'Nicholas', LastName: 'Zakas'}
```

## 新增方法

### Object.is()

ES6 引入了 Object.is() 方法来弥补全等运算符的不准确运算，这个方法接收两个参数，如果这两个参数类型相同且具有相同的值，则返回 true。

```javascript
console.log(+0 === -0) // true
console.log(Object.is(+0, -0)) // false

console.log(NaN === NaN) // false
console.log(Object.is(NaN, NaN)) // true
```

### Object.assign()

Object.assign() 方法可以接收任意数量的源对象，并按照指定的顺序将属性复制到接收对象中，如果多个源对象具有同名属性，则排位靠后的源对象会覆盖排位靠前的。

```javascript
var receiver = {}

Object.assign(receiver,
  {
    type: 'js',
    name: 'file.js'
  },
  {
    type: 'css'
  }
)

console.log(receiver.type) // css

console.log(receiver.name) // file.js
```

> Object.assign() 方法不能复制访问器属性，但由于执行了赋值操作，因此提供者的访问器属性会转变为接受者对象中的一个数据属性。

## 重复的对象字面量属性

ES6 中移除了对象重复属性的检查，对于每一组重复属性，都会选取最后一个取值：

```javascript
var person = {
  name: 'Greg',
  name: 'Nicholas'
}

console.log(person.name) // Nicholas
```

## 自有属性枚举顺序

ES6 中严格规定了对象的自有属性被枚举时的返回顺序，自有属性枚举顺序的基本规则是：

- 所有数字键按升序排序
- 所有字符串键按它们被加入对象的顺序排序
- 所有 Symbol() 键按它们被加入对象的顺序排序

> 由于浏览器厂商的实现方式不同，部分方法的枚举顺序目前尚不清晰。

## 增强对象原型

### 改变对象的原型

对象原型的真实值被存储在内部专用属性 [[Prototype]] 中，调用 Object.getPrototypeOf() 方法返回存储在其中的值，调用 Object.setPrototypeOf() 方法改变其中的值，然而这并不是操作 [[Prototype]] 值的唯一方法。

### 简化原型访问的 Super 引用

为了更便捷的访问对象原型，ES6 中引入了 super 关键字，Super 引用相当于指向对象原型的指针，实际上也就是 Object.getPrototypeOf(this) 的值。

Super 引用不是动态变化的，在多重继承中，它总是指向正确的对象。

> 必须在使用简写方法的对象中使用 Super 引用，否则会导致语法错误。

### 正式的方法定义

在 ES6 以前从未定义过方法的概念，方法仅仅是一个具有功能而非数据的对象属性。而在 ES6 中正式将方法定义为一个函数，它会有一个内部的 [[HomeObject]] 属性来容纳这个方法丛属的对象。

```javascript
let person = {
  // 是方法
  getGreeting () {
    return 'Hello!'
  }
}

// 不是方法
function shareGreeting () {
  return 'Hi!'
}
```

在上面的代码中，getGreeting() 的 [[HomeObject]] 属性值为 person，而创建 shareGreeting() 函数时，由于未将其赋给一个对象，因而该方法没有明确定义 [[HomeObject]] 属性，当使用 Super 引用时，这一点非常重要。Super 的所有引用都通过 [[HomeObject]] 属性来确定后续的运行过程，第一步是在 [[HomeObject]] 属性上调用 Object.getPrototypeOf() 方法来检索原型的引用，然后搜寻原型找到同名函数，最后，设置 this 绑定并调用相应的方法。
