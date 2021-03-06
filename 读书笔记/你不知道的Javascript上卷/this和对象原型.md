---
layout: default
title: {{ site.name }}
---
# this和对象原型
## this
具名函数我们可以在函数内部用名字来指向自己来进行递归的操作。而不具名函数我们做不到，我们只能使用arguments.callee来指向自己，但是这个已经被废弃了。(ES5的严格模式已经不被允许使用了)

this实际上是函数被调用时发生的绑定，它指向什么取决于函数在哪里被调用。

一般情况下调用函数，this都是默认绑定到window上的，但是严格模式下，全局对象不能用来默认绑定。

    但是混用严格模式和非严格模式会可能导致混乱。就是说不管你调用的时候是严格模式还是非严格模式，函数在哪个模式下声明的按照哪种规则来。

第三方的许多函数，包括js语言和宿主环境的许多新的内置函数，都有个额外的参数，是上下文，其实作用和bind一样，就是确保回调时的this。

### 优先级
new的优先级最高

其次是call，apply，bind函数

然后就是在某个上下文中调用的话，就是那个绑定的上下文

然后就是隐式绑定，就会绑在全局上。如果是严格模式就是undefined。

### 规则例外
#### 忽略的this
我们在apply和call和bind的时候，如果传入null，那就会被忽略，继续执行默认的绑定规则。

我们在有时将数组展开的时候需要用到这种用法。或者函数柯里化的时候。在ES6里面我们可以通过...来展开数组。但是柯里化ES6里面没有，所以我们还是会使用这种方式。

很多时候我们让他执行默认的绑定规则是不好的。因为很多第三方的库可能会导致全局对象遭到了修改。所以更安全的是传入一个特殊的对象。比如Object.create(null)。

#### 间接引用
比如`(p.foo=o.foo)()`实际上就是在全局环境在调用

#### 箭头函数
箭头函数是非常不讲道理的，他直接锁定了当时的this或者说是外层的作用域环境，并且没法再修改。和bind的效果是一致的，完全会锁死。和我们写了个self=this的效果时一致的。箭头函数内部没有constructor和prototype，不能被new。

## 对象
这里得再提一下基本类型咯：string，number，boolean，null，undefined，object

内置对象：string，number，boolean，date，function，RegExp，object，error

我们写一个字符串实际上只是一个字面量。但是我们操作他的时候，他就会变成一个string对象。

属性名这个东西最常用的是ES6的Symbol(这个东西是个创建了之后能保证唯一性的东西，他是一个基础的数据类型。就是说从6大基础数据类型变成7个了)

configurable为false的时候，我们还是可以将writable从true设置为false。其他情况都是不能设置的，然而浏览器好像没有报错....

### 不变性
Object.preventExtension：就是禁止添加新属性

Object.seal就是调用preventExtension，然后遍历属性，configuable为false

Object.freeze就是会调用一遍seal，然后遍历属性，全部的writable为false

const仅仅只是锁住了常量不可编辑，对象的话是锁住了引用，值还是可以修改的。

### 遍历
其实用的比较多的是for in，但是我们想要遍历值的话，我们可以使用ES6的for of。

但是使用for of的话得要求调用的得用迭代器接口。

我们可以通过Symbol.iterator来获得或者定义一个对象的迭代器方法。这个方法返回一个拥有next方法的对象。我们可以不断的调用这个对象的next来遍历。next方法的结果是{value:'',done:false}。

## 混合对象"类"
javascript不支持多重继承，因为多重继承可能会遇到钻石问题(菱形继承导致不知道具体继承的行为)。

这一章的各种模拟类都是一种类似复制对象的形式来做的

## 原型
我们添加一个值的时候其实判断逻辑是有3个的：

 - 如果原型链上有这个值，且这个值的writable为true，那么就会在obj上加一个新属性，赋值
 - 如果上层存在这个值，且他的writable为false，那么赋值语句会被忽略，如果是严格模式，会报错
 - 如果上层存在这个值，并且他是一个setter，那就会直接调用这个setter，而并不会被添加

当然，我们为了保证可以添加上的话，使用defineProperty时可以做到的。

### 继承
js之间的关系并不是类和类之间的关系，而是类和实例之间的关系。新的class加上了类和类之间的关系。

## 行为委托
