title: Node 基本语法
date: 2017-10-14
tags: node
toc: true
categories: node
---

## Node 的特点
> NODE只是一个工具（或者环境），我们可以把NODE安装在服务器上，NODE提供的环境可以运行JS代码，这样我们就可以在服务器端使用JavaScript编写一些处理服务器相关操作的程序，也可以理解为JS变为了后台编程语言。
 - 1、NODE是基于V8引擎来渲染JS的（V8是谷歌的引擎）
   + 渲染JS的速度会很快
   + 我们在使用JS开发后台程序的时候，不需要考虑浏览器兼容了，使用JS的最新标准即可（ECMAScript）
 - 2、单线程、无阻塞I/O操作、事件驱动（event-driven）

> 首先要安装node，下载地址[Node.js](http://nodejs.cn/) 下载最新的即可，因为node也在不断更新语法和处理方式；

> Node和客户端的主要区别在于全局对象
 - 浏览器全局对象: window
 - NODE全局对象: global

### NODE天生就是基于模块化开发的
> NODE中模块这个概念非常强（强到我们每创建一个新的JS，都相当于创建了一个新的模块）
>
> NODE是基于commonjs规范来完成模块设计的（如何创建模块、如何调取模块、如何导出模块 这些就是规范里提及的内容）
>  - CMD模块开发思想：seajs  （按需导入：用到的时候在导入进来使用）
>  - AMD模块开发思想：requirejs （提前导入所有需要的依赖）
>
> 1、自定义模块：自己创建的
> 2、第三方模块：别人写好的我来用
> 3、内置模块：天生自带的

```
//=>NODE为了实现模块之间的独立，会自动包一层闭包，而且给每一个模块传递五个值
(function (exports, require, module, __filename, __dirname) {
   /*
    * module模块:NODE模块管理的对象
    * exports叫做导出，等同于module.exports，用来把模块进行导出的
    * require是导入的方法，通过这个方法可以把一些模块导入到当前模块中使用
   */

    //=>内部代码中有一个类似于这样的操作，可以把一些想要供别人使用的属性和方法暴露出来
    module.exports=exports=this={
        fn:()=>{}
    };
})();
```

**module.exports**

> 把当前模块中的某些属性和方法导出

```
module.exports.fn=fn;
module.exports.a=12;
//=>上面写起来麻烦（上面属于向原有内置对象的堆内存中增加属性和方法，下面属于导出一个新的堆内存）
module.exports={
  fn:fn,
  a:12
};

---
//=>直接使用exports也可以，但是这种模式只能使用原有内置内容，自己开辟新内存不行
exports.fn=fn;
exports={};//=>这种方式不行
```

**require**

> 导入某个模块，目的是使用其它模块中的属性和方法。在导入自定义模块和第三方模块语法上是有一些区别需要注意的

```
require('./xxx') ： 指定目录的情况下，都是为了要导入自己定义的模块

require('xxx')：不指定目录情况下，可能是导入第三方安装的模块，也可能是导入内置模块。
// 查找规则: 首先到node_modules文件夹中找第三方模块，有的话，导入的就是第三方模块，没有的话，看内置模块中有没有，内置中这个模块，导入的就是内置模块，内置中没有就会报错。
```

----

#### Path 模块

> 用于处理文件路径
 - path.normalize(p) 路径解析，得到规范路径
 - path.join 路径合并
 - path.resolve([from …], to) 获取绝对路径
 - path.relative(from, to) 获取相对路径
 - path.isAbsolute(path) 判定是否为绝对路径
 - path.dirname(p) 返回路径中文件夹的名称
 - path.basename(p, [ext]) 返回路径中的最后一部分
 - path.extname(p) 返回路径中文件的扩展名, 在从最后一部分中的最后一个’.’到字符串的末尾。 如果在路径的最后一部分没有’.’，或者第一个字符是’.’，就返回一个 空字符串
 - path.parse(pathString) 解析路径 (返回一个对象：{ root : "/", dir : "/home/user/dir", base : "file.txt", ext : ".txt", name : "file" })

----

#### Http 模块

> 用于和服务器交互
 - http.createServer(function(req,res){}); 创建服务
 - http.get('path',callback); 发送get请求。
 - http.request(options,callback); 发送请求。

```
var http=require('http');
var querystring=require('querystring');

//启动服务
http.createServer((req,res)=>{
    console.log("request already come");
    var post = "";
    req.on('data',(chunk)=>{
        post += chunk;
    });
    req.on('end',()=>{
        //querystring.parse  将字符串转换为json的格式
        post =  querystring.parse(post);
        console.log('complete complished');
        //返回请求者一个信息
        res.write(post.name);
        res.end();
    });
}).listen(3000);
```


#### FS 模块

> 主要作用就是进行I/O操作（对服务器端的文件进行增删改查等操作）

```
// 导入fs模块
let fs = require('fs');
//=>接下来就可以使用它里面提供的方法了
```

> 我们发现FS中提供的方法一般都是两套：同步操作和异步操作各一套
>
> 例如：
> fs.readFile 异步读取文件中的内容
> fs.readFileSync 同步读取文件中的内容
>
> 同步和异步的区别在于：同步读取文件，文件内容没有读取完成，后面的任务无法处理，而异步不是，数据没有读取完成，下面的任务先去执行（我们也把这个特点叫做 `“无阻塞的I/O操作”`）

----

**readFile && readFileSync**

```
//=>同步读取
let con = fs.readFileSync('./index.html');
console.log(con); //=>BUFFER

con = fs.readFileSync('./index.html', 'utf8');
console.log(con); //=>STRING 设置UTF8后,会自动把BUFFER格式的数据转换为字符串格式的数据

console.log('ok');//=>最后执行:同步读取,需要等内容读取完成才会执行下面的任务
```

```
//=>异步读取
console.time('readFile');
let con = fs.readFile('./index2.html', 'utf8', (err, result)=> {
    //=>回调函数就是NODE的事件驱动机制:当文件读取成功或者失败的时候,会触发回调函数执行(并且传递两个实参值)
    //=>err(error):当读取出错,信息保存在err中,如果没有出错,err为null
    //=>result:当读取成功,信息保存在result中(第二个参数不设置utf8,获取的结果依然是Buffer格式的数据)
    console.timeEnd('readFile');
    if (err) {
        //=>出错了:真实项目中我们会把错误信息记录在错误日志中
        console.log(err);
        return;
    }
    console.log(result);
});

console.log('ok');//=>OK是先输出的
```

----

**fs.writeFile && fs.writeFileSync**
> 同步或者异步向某个文件中写入内容
>> 特点：
 - 1、如果当前文件没有，我们会自动创建文件，然后再写入内容（但是并不是万能的：如果指定的地址中不存在这个文件夹，NODE无法自动帮你创建文件夹，需要我们自己手动检测并创建）
 - 2、文件写入属于覆盖式写入（新写入的内容会覆盖原有的内容）
 - 3、写入的内容需要是字符串或者BUFFER格式的数据

```
// 同步写入
let res = fs.writeFileSync('./TEMP.txt', 'HELLO', 'utf8');
console.log(res); //=>undefined 写入的方法没有返回值

// 异步写入
fs.writeFile('./TEMP.txt', '珠峰培训', 'utf8', (error)=> {
    //=>这里面只有ERROR一个参数:代表写入成功还是失败
    console.log(error);
});
```

----

**appendFile && appendFileSync**

> 用法和writeFile类似,不一样的地方在于：appendFile写入属于追加式写入（原有内容不改变），而writeFile输入覆盖式写入（覆盖原有的内容）

```
fs.appendFileSync('./TEMP.txt', 'HELLO', 'utf8');

fs.appendFile('./TEMP.txt', 'HELLO', 'utf8', error=> {
    if (error) {
        console.log(error);
        return;
    }
    console.log('success');
});
```

----

**readdir && readdirSync**

> 同步或者异步读取某一个目录下所有的文件和文件夹信息

```
let dirList = fs.readdirSync('./');
console.log(dirList);

fs.readdir('./', (error, result)=> {
    if (error) {
        console.log(error);
        return;
    }
    console.log(result); //=>获取一个数组集合,集合中包含当前目录中(./)所有的文件及文件夹信息
});
```

----

**mkdir && mkdirSync**

> 创建文件夹
 - 特点:
  + 1、可以创建文件夹，如果当前文件夹已经存在，返回的是错误的信息，不会重新的创建
  + 2、不能一次创建多级结构目录，例如：./TEMP/DAY1/CSS 这样的多级目录无法一次创建，需要先创建TEMP，然后再创建DAY1...

```
fs.mkdir('./TEMP', (error)=> {
    if (error) {
        console.log('error');
        return;
    }
    console.log('success');
});
```

> 自己实现一个方法，可以一次创建多级结构目录

```
/*
 * 创建文件夹
 *   path: './TEMP' 、'./TEMP/DAY1' 、'./TEMP/DAY1/CSS' ...
 */

let makeDir = function (path) {
    let pathAry = path.split('/'),
        [root,...arg]=pathAry;
    root = root + '/';

    let make = n=> {
        if (n >= arg.length) return;
        let curPath = arg[n];
        fs.mkdir(root + curPath, error=> {
            root += curPath + '/';
            make(n + 1);
        });
    };
    make(0);
};
makeDir('./TEMP/DAY1/CSS/LESS');
```

> 除了上述方法以外，还有很多其它的方法，例如：copyFile、rmdir... ，大家可以观看NODE中文文档（http://nodejs.cn/api/）