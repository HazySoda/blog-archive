---
title: "《深入理解ES6》读书笔记之一：块级作用域绑定"
date: 2018-09-15T04:15:18+08:00
lastmod: 2018-09-15T04:15:18+08:00
draft: false
tags: ['读书笔记', 'JavaScript', 'ES6']
categories: ['前端']
---

## 块级声明

块级声明用于声明在指定的作用域之外无法访问的变量，块级作用域（也称词法作用域）存在于：

- 函数内部
- 代码块内部

## var 声明

- 会被提升至作用域顶部
- 允许重复声明同名变量

## let 声明

- 不会被提升至作用域顶部
- 不允许重复声明同名变量
- 在所属代码块或函数中的代码执行完毕后会立即销毁

## const 声明

- 不会被提升至作用域顶部
- 不允许重复声明同名变量
- 在所属代码块或函数中的代码执行完毕后会立即销毁
- 声明时必须进行初始化（赋值）
- 声明后不允许再次赋值
- 如果常量的值是引用类型，引用类型中的值可以修改（`const` 实际指向的是内存地址的引用）

## 临时死区（TDZ）

由于 `let` 和 `const` 声明的变量不会被提升至作用域顶部，所以如果在声明之前访问变量，即使相对安全的 typeof 操作符也会报错：

```javascript
if (condition) {
  console.log(typeof value) // 报错
  let value = 'foo'
}
```

但如果在该变量声明作用域外对其进行访问则不会报错：

```javascript
console.log(typeof value) // undefined
if (condition) {
  let value = 'foo'
}
```

## 循环中的块作用域绑定

老生常谈的例子：

```javascript
var funcs = []

for (var i = 0; i < 10; i++) {
  funcs.push(function () {
    console.log(i)
  })
}

funcs.forEach(function (func) {
  func() // 输出 10 次 10
})

console.log(i) // 10
```

我们期望的行为是循环调用 `funcs` 中的函数后依次输出 0 到 9 ，然而由于使用 `var` 声明的变量会被提示至作用域顶部，导致变量 `i` 在循环结束后仍可以访问，想解决这个问题，可以借助立即执行函数（IIFE）：

```javascript
var funcs = []

for (var i = 0; i < 10; i++) {
  funcs.push((function (i) {
    return function () {
      console.log(i)
    }
  })(i))
}

funcs.forEach(function (func) {
  func() // 依次输出 0 到 9
})

console.log(i) // 10
```

现在有了块级绑定，只需将 `var` 替换为 `let` 即可：

```javascript
var funcs = []

for (let i = 0; i < 10; i++) {
  funcs.push(function () {
    console.log(i)
  })
}

funcs.forEach(function (func) {
  func() // 依次输出 0 到 9
})

console.log(i) // 10
```

如果在循环中使用了 `let`，每次迭代循环都会创建一个新变量，并以之前迭代中同名变量的值将其初始化（在 `for-in` 和 `for-of` 循环中也是一样的）。

> `let` 声明在循环中的行为是标准中专门定义的，其不一定与 let 的不提升特性相关。

在 `for-in` 和 `for-of` 循环中，用于循环的变量不会被重新赋值，因此可以使用 `const` ：

```javascript
var obj = {
  foo: 'foo',
  bar: 'bar'
}

for (const key in obj) {
  console.log(key) // foo bar
}
```

每次迭代不会像 `let` 一样修改已有绑定，而是会创建一个新绑定。

## 全局块作用域绑定

`let` 和 `const` 可以遮蔽全局变量，但不能覆盖全局变量：

```javascript
window.foo = 'foo'

let foo = 'bar'

console.log(foo) // bar

console.log(window.foo) // foo
```

## 最佳实践

不再使用 `var` ，默认使用 `const` ，只在确实需要改变变量的值时使用 `let` 。因为大部分变量的值在初始化后不应该再改变，且变量值在预料之外发生改变是很多 BUG 的源头。
