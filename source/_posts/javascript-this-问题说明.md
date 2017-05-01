---
title: javascript this 问题说明
date: 2017-04-30 22:10:33
categories: 笔记
tags:
    javascript
---

> 最近有好多同事还在问我关于`this`的问题，那我就从函数调用模式的角度来说明一下`this`

## 首先this是什么

javascript中调用一个函数，这个函数除了声明时定义的形式参数外，每个函数还接收两个附加的参数：`this` 和 `arguments`。
参数 `this`在面向对象编程中非常重要，它的值取决与调用模式。

## 调用模式

javascript中的调用模式，一共有4种：
- 方法调用模式
- 函数调用模式
- 构造器调用模式
- apply / call 调用模式

### 方法调用模式

当一个函数被保存为对象的一个属性时，我们称它为一个方法。当一个方法被调用的时候
（即包含一个 . 点表达式或[subscript]下标表达式），`this`被绑定到该对象。

<script async src="//jsfiddle.net/asiareal/vq37nyxu/4/embed/js,html,result/"></script>

### 函数调用模式

当一个函数并非一个对象属性时，那么它就是被当作一个函数来调用的。
以此模式调用函数时，`this`被绑定到了全局对象。这是语言设计上的一个错误。倘若语言设计的正确，
那么当内部函数被调用时，`this`应该仍然绑定到外部函数的`this`变量。下面例子来演示这个错误。

> 例子

<script async src="//jsfiddle.net/asiareal/bsjdf9ut/embed/js,html,result/"></script>

针对这个有种很好解决的方案，定义一个变量并给它赋值`this`，那么内部函数就可以通过那个变量访问到`this`。
按照约定，命名为`that`。

> 例子

<script async src="//jsfiddle.net/asiareal/cjjrxcwj/embed/js,html,result/"></script>

### 构造器调用模式

javascript 是一门基于原型继承的语言。如果一个函数前面带上`new`来调用，那么背地里会创建一个连接到该函数的`prototype`
成员的新对象，同时`this`会被绑定到那个新对象上。

<script async src="//jsfiddle.net/asiareal/v7fw8ax2/embed/js,html,result/"></script>

### apply ／ call 调用模式

因为Javascript 是一门函数式的面向对象编程语言，所以函数是可以拥有方法的。

函数的`apply`方法允许我们选择`this`的值。`apply`方法接收两个参数，第一个是要绑定给`this`的值，第二个是参数数数组。

<script async src="//jsfiddle.net/asiareal/5y3p35e8/2/embed/js,html,result/"></script>

关于`apply`和 `call` 的区别，我暂时的理解就是参数的不同，`apply`方法接收两个参数，第一个是要绑定给`this`的值，第二个是参数数数组。
而`call`可以接收N个参数，第一个是要绑定给`this`的值，后面的参数是函数要接收传参。如果大家有不同见解，欢迎留言补充。

## 总结

1. 方法调用模式，构造器调用模式`this` 始终指向它的调用者
2. 函数调用模式`this`始终绑定到了全局对象上面，可以通过`that`来解决这个问题
3. apply 和 call 可以通过参数的方式指定`this` 



