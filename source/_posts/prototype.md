title: javascript Prototype
date: 2017-07-15
tags: js
toc: true
categories: 原生js
---

> W3C对prototype的描述是：prototype 属性使您有能力向对象添加属性和方法。
> 语法：object.prototype.name=value

让很多刚刚开始了解OOP思想的人很是迷茫。

prototype 的出现主要是为了实现“继承”的设计理念

```
  function Fn(name){
    this.name = name
    this.type = 'string';
  }

  var foo1 = new Fn('one');
  var foo2 = new Fn('two');

  console.log(foo1.type) // => string
  console.log(foo2.type) // => string

  foo1.type = 'number';

  console.log(foo2.type) // => string

  // 同一个构造函数所生成的所有实例之间相互是没有影响的（this是构造函数所生成的实例）
```

> 这是我们通过 new 命令去创造一个实例的方法，但是 new 命令有一个很大的弊端，就是构造函数生成实例的时候会是属性和方法无法共享；这不仅是数据无法共享，也是资源的一种浪费；考虑到这个问题，也就在构造函数中出现了‘prototype’这个属性

```
  // 我们改写一下上面的方法

  function Fn(name){
    this.name = name
    this.fn = function(){};
  }

  Fn.prototype = {
    type: 'string'
  }

  var foo1 = new Fn('one');
  var foo2 = new Fn('two');

  console.log(foo1.name) // => one
  console.log(foo2.name) // => two

  Fn.prototype.type = 'number';

  console.log(foo2.type) // => number
```

> 我们发现 type 这个属性变成了两个实例的共有属性，而构造函数修改‘prototype’属性时会影响这实例的属性；

> 理解起来很像是构造函数内部this上的属性是实例的私有属性，而构造函数的‘prototype’上的属性是实例的共有属性；

> 对 prototype 有了一些了解 我们在说一下 \__proto__ 这个属性， 它可以称谓隐式原型；

> 我们先对这两个属性抽象理解一下:
 - 1) 每一个函数都天生自带一个prototype（对象数据类型）属性 指向自身的原型
 - 2) 每一个对象都天生自带一个\__proto__属性 指向所属构造函数（类）的原型
 - 3) 每一个原型都再带一个 constructor 属性 指向构造函数（类）（但是constructor这个属性是可以手动修改的）

js中的原型链也是由这俩个属性来实现的；

> 我们先了解一下函数的三种身份：
 - 1) 普通函数
 - 2) 构造函数
 - 3) 函数对象

> 普通函数 ： 是我们做常用的，不管是封装也谢方法还是回调函数，都属于普通函数范围
>
> 构造函数 ： new 命名后面跟的函数 归属于构造函数，并且我们为了更方便的区分普通函数还是构造函数，在声明函数的时候，构造函数通常会采用首字母大写的方式
>
> 函数对象 ： 函数自身也是一个对象，可以有自己的属性

这也不难理解为什么js中的函数可塑性如此的高;

既然说了prototype和\__proto__是为了共享属性和方法（继承）而诞生的属性，我们来看一看他们在实际中是怎么使用的

```
function Parent (name,age){
  this.name = name;
  this.age = age;
}

Parent.prototype = {
    getName : function(){
      return this.name;
    }
}

function Child (name,age,sex){
    Parent.call(this,name,age)
    this.sex = sex;
}

Child.prototype = new Parent();

var child = new Child('李雷',18,'男')

console.log(child.getName()); // 李雷
```
> 我们写了一个简单的继承方式，在这个例子中我们不难发现，在Child这个构造函数所产生的实例中，我们可以使用Parent上的方法，这就是继承的作用，当在一个已有的构造函数中已经写过我们需要的方法，我们在合适的需求下，我们完全可以将其拿来直接使用，而不是自己在写一段重复、冗余的代码，这样也就实现了资源的共享；
>
> \__proto__ 这个属性在实际开发中我们很少直接使用，是因为这个属性牵扯的东西太多，而且IE（所有开发者的痛苦）浏览器是屏蔽开发者对这个属性的操作的；并且这个属性也是一直不被推荐使用的；
>
> \__proto__ 默认是串联所有实例和类的原型

```
var ary = new Array(3);
console.dir(ary);

// 在输出台里你可以看见 \__proto__ 的指向顺序， ary -> Array -> Object（自身\__proto__为null，因为它已经是根源了）；这就是为什么我们声明一个数组后，就可以调用Array和Object上的方法；这也是js中为什么说万物皆对象的原因；

```

> 在ES6中给出了两个操作方法:
 - 1) [Object.getPrototypeOf()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/getPrototypeOf) 方法返回指定对象的原型
 - 2) [Object.setPrototypeOf()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/setPrototypeOf) 方法设置一个指定的对象的原型
>
> 尽管如此，设置对象的\__proto__是一个缓慢的操作，如果性能是一个问题，应该避免;

> 如果看过有关原型继承的书籍的话，我们会发现有很多继承的方式，在这就不一一举例了，我们直说一下寄生组合式继承，应该是目前最为理想解决继承的方法，解决了以前原型继承，组合式继承等等方法的一些小bug和缺陷

```
// 借助一下上面的例子，改造一下
function Parent (name,age){
  this.name = name;
  this.age = age;
}

Parent.prototype.getName = function(){ return this.name; }

// 在考虑Object.create 这个方法的兼容性不够，所以采用自己模拟这个功能的写法，如果不需要考虑IE9一下版本的话可以直接使用虑Object.create 更简单一些
function inherit(sub,sup){
  function Fn(){};
  Fn.prototype = sup.prototype;
  var fn = new Fn();
  Object.defineProperty(fn,'constructor',{value:sub}) // 增强原型
  sub.prototype = fn;
}

function Child (name,age,sex){
    Parent.call(this,name,age)
    this.sex = sex;
}

inherit(Child,Parent);

var child = new Child('李雷',18,'男')

console.log(child.getName()); // 李雷

```

希望对正在学习这方面只是的小伙伴们有帮助吧, 以上观点也是我总结的一些。

如果有任何疑问或者文章内有错误，可以发邮件到1043951234@qq.com 进行交流，如果有错误我会第一时间更正，感谢您的赐教

