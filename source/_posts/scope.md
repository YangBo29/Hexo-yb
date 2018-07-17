title: javascript scope
date: 2017-07-30
tags: js
toc: true
categories: 原生js
---

对作用域和作用域链概念的模糊是我们在开发中导致一些‘意外’状况发生的常见原因

我们随便写一个例子来看一下

```
var name = 'bird';
function fn(){
  console.log(name); // 1、undefined
  var name = 'dog'

  function temp(){
    var name = 'cat'
    console.log(name); // 2、cat
  }

  function item(){
    console.log(name); // 3、dog
  }

  temp();
  item();

  console.log(name); // 4、dog

  return function(){
    console.log(name); // 5、dog
  }
}

var fn1 = fn();

fn1();
```

如果的这些结果你还会产生疑问的话，那说明你对作用域和作用域链的概念很模糊

我们先说一下在js中的作用域的概念：作用域指的是变量的适用范围； 基本分为全局作用域（window）和局部作用域（函数）

作用域的特点: 1、js中是不存在动态作用域的; 2、es6之前是不存在块级作用域的;

我们来了分析一下刚才的代码

位置1 这个位置输出 undefined 是因为js中变量提升机制所导致的 当进入一个作用域范围的时候js会预先查询在当前作用域中的var和function关键字所创建的变量，然后将这些变量的声明提升到当前作用域的顶部，但是var只能提升声明，不能赋值；而function 是提升声明并且赋值；

位置2 在当前作用域（temp函数）中可以查询到name属性；

位置3 在当前作用域（item函数）中无法查询到name属性，js机制会继续向外层查询，如果查询到了即输出；如果依旧没有找到，则继续向上级查询，直到查找到全局作用域，如果仍然查找不到，就会报错。

位置4 跟2同理

位置5 这也是证明了js中没有动态作用域；因为我们已经将fn函数的返回的函数赋值到了fn1，如果存在动态作用域的话，那输出的结果应该是‘bird’，然而他依旧是输出的‘dog’，也就是说，作用域链的查找规则是根据函数声明的位置，为不是看函数的调用位置

如果有任何疑问或者文章内有错误，可以发邮件到1043951234@qq.com 进行交流，如果有错误我会第一时间更正，感谢您的赐教



