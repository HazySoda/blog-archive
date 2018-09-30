---
title: "《深入理解ES6》读书笔记之六：Symbol和Symbol属性"
date: 2018-09-30T22:06:09+08:00
lastmod: 2018-09-30T22:06:09+08:00
draft: false
tags: ['读书笔记', 'JavaScript', 'ES6']
categories: ['前端']
---

除了之前的字符串型、数字型、布尔型、null 和 undefined，ES6 中引入了第 6 种原始类型：Symbol。

## 创建 Symbol

可以通过全局的 Symbol 函数创建一个 Symbol：

```javascript
let firstName = Symbol()
let lastName = Symbol('lastName')
let person = {}

person[firstName] = 'Nicholas'
person[lastName] = 'Zakas'

console.log(firstName) // Symbol()
console.log(lastName) // Symbol(lastName)
console.log(person[firstName]) // Nicholas
console.log(person[lastName]) // Zakas
```

在上面这段代码中，创建了名为 firstName 和 lastName 的两个 Symbol，用他们将两个新的属性赋给 person 对象，每当想访问这两个属性时必须要用到最初定义的 Symbol。

Symbol 函数接受一个可选参数，可以为即将创建的 Symbol 添加一段文本描述。

> 由于 Symbol 是原始类型，调用 new Symbol() 会导致抛出错误。

## Symbol 的使用方法

所有可以使用计算属性名的地方，都可以使用 Symbol，Symbol 也可以用于可计算对象字面量属性名、Object.defineProperty() 方法和 Object.defineProperties() 方法的调用过程中：

```javascript
let firstName = Symbol('firstName')

let person = {
  [firstName]: 'Nicholas'
}

Object.defineProperty(person, firstName, {
  writable: false
})
```

## Symbol 共享体系

如果想创建一个可共享的 Symbol，要使用 Symbol.for() 方法。它只接收一个参数，也就是即将创建的 Symbol 的字符串标识符，这个参数同样也被用于 Symbol 的描述：

```javascript
let uid = Symbol.for('uid')
let obj = {}

obj[uid] = 1

console.log(obj[uid]) // 1
console.log(uid) // Symbol(uid)
```

Symbol.for() 方法首先在全局 Symbol 注册表中搜索键为 uid 的 Symbol 是否存在，如果存在，直接返回已有的 Symbol，否则创建一个新的 Symbol，并使用这个键在 Symbol 全局注册表中注册，随后返回新创建的 Symbol。

可以使用 Symbol.keyFor() 方法在 Symbol 全局注册表中检索与 Symbol 有关的键：

```javascript
let uid = Symbol.for('uid')
let token = Symbol('token')


console.log(Symbol.keyFor(uid)) // uid
console.log(Symbol.keyFor(token)) // undefined
```

## Symbol 与强制类型转换

Symbol 可以被 String() 方法和 toString() 方法转换为字符串形式，但如果将 Symbol 和字符串进行拼接或将它强制转换成数字则会抛出错误。实际上，除了逻辑操作符之外，将 Symbol 与每一个数字操作符混合使用都会抛出错误。

## Symbol 属性检索

Object.getOwnPropertySymbols() 方法可以用来检测对象中所有的 Symbol 属性，它的返回值是一个包含所有 Symbol 自有属性的数组：

```javascript
let uid = Symbol.for('uid')
let obj = {
  [uid]: 1
}

let symbols = Object.getOwnPropertySymbols(obj)

console.log(symbols.length) // 1
console.log(symbols[0]) // Symbol(uid)
console.log(obj[symbols[0]]) // 1
```
