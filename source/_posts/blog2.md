---
title: es6规范
date: 2018-03-19 17:25:22
tags: js
---
es6 es7 es8 typescript 这些都是为了让一些语法逻辑更为简洁

### 常量

```
const a = xxx
const obj ={}
```
<!--more-->
### 变量

```javascript
let a = xxx
```

### 模版字符串

传统的JavaScript语言，输出模板通常是这样写的。

```javascript
const welcome = 'You have logged in as ' + first + ' ' + last + '.'
const db = 'http://' + host + ':' + port + '/' + database;
```
ES6可以使用反引号和${}简写：

```javascript
const welcome = `You have logged in as ${first} ${last}`;

const db = `http://${host}:${port}/${database}`;
```
### 解构赋值简写方法

```javascript
const observable = require('mobx/observable');
const action = require('mobx/action');
const runInAction = require('mobx/runInAction');

const store = this.props.store;
const form = this.props.form;
const loading = this.props.loading;
const errors = this.props.errors;
const entity = this.props.entity;
```
简写:
```javascript
import { observable, action, runInAction } from 'mobx';

const { store, form, loading, errors, entity } = this.props;
```
### 扩展运算符简写

扩展运算符有几种用例让JavaScript代码更加有效使用，可以用来代替某个数组函数。
```javascript
// joining arrays
const odd = [1, 3, 5];
const nums = [2 ,4 , 6].concat(odd);

// cloning arrays
const arr = [1, 2, 3, 4];
const arr2 = arr.slice()
```
简写：

```javascript
const odd = [1, 3, 5 ];
const nums = [2 ,4 , 6, ...odd];
console.log(nums); // [ 2, 4, 6, 1, 3, 5 ]

// cloning arrays
const arr = [1, 2, 3, 4];
const arr2 = [...arr];
```
也可以使用扩展运算符解构：

```javascript
const { a, b, ...z } = { a: 1, b: 2, c: 3, d: 4 };
console.log(a) // 1
console.log(b) // 2
console.log(z) // { c: 3, d: 4 }

```
### 对象的扩展

Object.assign方法用于对象的合并，将源对象（source）的所有可枚举属性，复制到目标对象（target）。
Object.assign方法的第一个参数是目标对象，后面的参数都是源对象。

```javascript
const target = { a: 1 };

const source1 = { b: 2 };
const source2 = { c: 3 };

Object.assign(target, source1, source2);
target // {a:1, b:2, c:3}
```
注意，如果目标对象与源对象有同名属性，或多个源对象有同名属性，则后面的属性会覆盖前面的属性。

```javascript
const target = { a: 1, b: 1 };

const source1 = { b: 2, c: 2 };
const source2 = { c: 3 };

Object.assign(target, source1, source2);
target // {a:1, b:2, c:3}
```
### 数组方法

Map filter forEach (es5常用)

find方法  扩展符

想从数组中查找某个值，则需要循环。在ES6中，find()函数能实现同样效果。

```javascript
const pets = [
  { type: 'Dog', name: 'Max'},
  { type: 'Cat', name: 'Karl'},
  { type: 'Dog', name: 'Tommy'},
]

function findDog(name) {
  for(let i = 0; i<pets.length; ++i) {
    if(pets[i].type === 'Dog' && pets[i].name === name) {
      return pets[i];
    }
  }
}
```
简写：
```javascript
pet = pets.find(pet => pet.type ==='Dog' && pet.name === 'Tommy');
console.log(pet); // { type: 'Dog', name: 'Tommy' }
```

### 异步操作

promise用法

链接   http://es6.ruanyifeng.com/#docs/promise

async await

链接  http://es6.ruanyifeng.com/#docs/async