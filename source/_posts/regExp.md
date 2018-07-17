title: RegExp
date: 2017-11-26
tags: js
toc: true
categories: 正则
---

## 正则

> 释义： 具有全等正多边形各面的以及多面体的所有角均相等的
>
> 用途： 通常用于字符串的处理；指定一种规则，来验证字符串是否符合我们的要求；最多的应用在表单验证，路径解析，用户输入内容格式等；
>
> 语法： js中有两种方式创建一个正则表达式
 - 1、var reg = new RegExp('abc','g') 或者 var reg = new RegExp(/abc/g); // 构造函数 ES6之前是允许混用的，如：new RegExp(/abc/，'g'); ES6 修改了这种行为，如果 new RegExp(/abc/i，'g') 第二个参数会覆盖第一个参数中的修饰符
 - 2、var reg = /abc/g    // 字面量

### 使用方法

> String.prototype.search() 查找符合正则的字符串出现的位置 ，如果查询不到返回 -1
```
 'abcregexp'.search(/abc/); // => 0 首次出现的位置
```

> String.prototype.replace(n,m) 替换符合正则的字符串
```
 'abcregexp'.replace(/abc/,'def'); // => defregexp 如果第二个参数不传，会用undefined替代

 // 当replace 的第二个参数是func的时候，用法就改变了

 'abcregabcexp'.replace(/abc/,function(){
    console.log(arguments);
 }) // 虽然有两项符合条件，但是依然只会匹配第一个，这也是正则惰性一种表现，想取出这种惰性，需要加全局修饰符
 /* => Arguments(3) ["abc", 0, "abcregexp", callee: ƒ, Symbol(Symbol.iterator): ƒ]
       0 : "abc"  // 匹配结果
       1 : 0 // 匹配的开始索引
       2 : "abcregabcexp" // 原字符串
       callee : ƒ ()
       length : 3 // 数组长度
       ...
  */

  'abcregabcexp'.replace(/abc/g,function(){
      console.log(arguments);
   })
   // 不要以为是返回一次结果，依然是一次匹配只能返回一次结果，但是 replace 方法，会主动的去循环查找，知道没有符合条件后结束匹配。
```

> String.prototype.split(reg) 替换符合正则的字符串
```
 'abcaregaexp'.split(/a/); // => ['','bc','reg','exp'] 如果切割的字符串出现在了开始或者最后，数组中会出现一项为空字符串
```

> String.prototype.match(reg) 用来捕获字符串中符合正则的字符串到一个数组中。默认情况下只捕获第一个符合的结果到数组中(正则的惰性，下面我们会说到)，正则表达式有”全局捕获“的修饰符时(定义正则表达式的时候添加参数g)，会捕获所有结果到数组中
```
 'abcregabcexpabc'.match(/abc/); // => ['abc']
 'abcregabcexpabc'.match(/abc/g); // => ['abc','abc','abc']
```

> RegExp.prototype.test(reg) 用来测试字符串中是否含有子字符串
```
 /abc/.test('abcaregaexp'); // => true 是否存在，存在：true  不存在: false
```

> RegExp.prototype.exec(reg) 和match方法相似，也是从字符串中捕获满足条件的字符串到数组中，但是也有区别。
```
 /abc/.exec('abcaregaexp'); // => [["abc", index: 0, input: "abcaregaexp", groups: undefined]
```

> 当执行reg.exec(str) 先去验证当前字符串是否匹配，如果不匹配返回结果是null，如果匹配，从字符串最左边开始向右边查找匹配内容，并且把匹配内容返回。
> 捕获的返回结果格式：
  - 数组格式，数组中的第一项当前本次大正则在字符串中匹配的结果
  - index 记录的本次捕获到结果起始索引
  - input 当前正则操作的原始字符串
  - 如果当前正则中有分组，获取的数组中，从第二项开始都是每个小分组，本次匹配到的结果（通过exec可以把分组中的内容捕获到）
  - 执行一次exec只能把符合条件的第一项捕获到，如果还有其他符合规则的，需要在此执行exec才能有可能会捕获到
  - 正则捕获的懒惰型：执行一次exec捕获到第一个符合规则的内容，第一次执行exec，捕获到的依然是第一个匹配内容，后面匹配的内容不管执行多少次exec都无法捕获（解决正则捕获的懒惰型：在正则的末尾加修饰符g [全局匹配]）
>


#### 正则为什么会存在懒惰型呢？
> 正则本身有一个属性：lastIndex（下一次匹配的的起始位置）默认值是0， 默认不管指定多少遍exec方法，正则的lastIndex的值都不会改变（也就是为什么始终查找的都是第一个匹配内容）,并且程序不识别我们手动改变的值
>
> 为什么加上修饰符g就能解决懒惰型呢？
> 加了修饰符g之后，每次执行exec之后，浏览器会自动修改lastIndex的值，下一次从上一次结束的位置开始

> **所以如果想一次匹配到所有结果，需要自己封装一个方法，目的是执行一次这个方法，可以捕获所有符合匹配的内容**

----
### 元字符和修饰符

**修饰符**
> g（global）：全局匹配（查找所有匹配而非在找到第一个匹配后停止）
> i （ignoreCase）：忽略大小写匹配
> m（multiline）：多行匹配
> ES6 还推出了新的修饰符，在这里就不做介绍了，感兴趣的话自己去看吧

**元字符**
> [量词元字符]
  - +：让前面的元字符出现一到多次
  - ？：出现零到一次
  - *：出现零到多次
  - {n}：出现n次
  - {n,}：出现n到多次
  - {n,m}：出现n到m次
>
> [特殊意义的元字符]
  - \ ：转义字符（把一个普通字符转变成有特殊意义的字符，或者把一个有意义的字符转换为普通字符）
  - . ：除了\n（换行符）以外的任意字符
  - \d ： 匹配一个0-9之间的数字
  - \D ： 匹配一个任意非0-9之间的数字（大写字母和小写字母的组合正好是反向的）
  - \w ： 匹配一个0-9或者字母或者 _ 之间的字符
  - \W ： 非\w
  - \s ： 匹配一个任意空白字符串
  - \S ： 匹配一个非空白字符串
  - [\b]  匹配一个退格符
  - \b ： 匹配一个边界符
  - \B ： 匹配一个单词的非边界
  - \t ： 匹配一个制表符
  - \n ： 匹配一个换行符
  - \r ： 匹配一个回车符
  - \v ： 匹配一个重直制表符
  - x|y ：匹配x或者y中的一个
  - [a-z] ：匹配a-z之中的任意一个字符
  - [^a-z] ：匹配一个非a-z的字符
  - [xyz] ： 匹配x或者y或者z中的一个
  - [^xyz] ： 匹配除了xyz以外的任意字符
  - () ： 正则的小分组，匹配一个分组（小分组可以理解为一个大分组中的一个小分组）
  - ^ ：以某一个元字符开始
  - $ ：以某一个元字符结束
  - ?: ：只匹配不捕获（断言）
  - ?= ：正向预查
  - ?! ：负向预查

### 元字符详解

```
\  ：有一些符号在正则中室友特殊含义的，我们在想使用他本身意思的时候需要进行转义，例如 /./g（这个时候.所匹配的是除了\n（换行符）以外的任意字符），而我们只是希望匹配‘.’，写法：/\./g ；

() ：正则中的分组，也可以理解为一个大正则中的一个正则（包起来的部分是一个整体，在正则中我们可以使用小括号改变一些默认的优先级）

[] ：在其中的任意一个，具有消磁作用，特殊意义的符号，在[]中，恢复正常含义
  - 分组引用 ：\1,\2,……出现和第N个分组一模一样的内容
  - 分组捕获 ： 分组不仅可以匹配，而且以后捕获的时候，不仅可以把大正则匹配的结果捕获到，里面每一个小分组匹配的结果也可以单独的捕获到。

// 非断言的情况 /hello\s(world)/.exec('hello world hello today') =>  ["hello world", "world", index: 0, input: "hello world hello today", groups: undefined]

?: ：断言， 正则表达式 /hello\s(?:world)/.exec('hello world hello today') // => ["hello world", index: 0, input: "hello world hello today", groups: undefined] 但是不给此分组分配组号，也不会出现在返回结果中，

?= ：正则表达式 /hello\s(?=world)/.exec('hello world hello today') // => ["hello ", index: 0, input: "hello world hello today", groups: undefined]
     // 只会捕获 (?=world) 中world 之前符合匹配条件，同样不给此分组分配组号，也不会出现在返回结果中；

?! ：正则表达式 /hello\s(?!world)/.exec('hello world hello today') // => ["hello ", index: 12, input: "hello world hello today", groups: undefined]
     // 只会捕获 (?=world) 中world 后面符合匹配条件，同样不给此分组分配组号，也不会出现在返回结果中；
```

> 事实上 正则能做的事情远远超出你的想想，越研究你会越喜欢正则的
