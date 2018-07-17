title: Express
date: 2017-10-28
tags: node
toc: true
categories: node
---

### 什么是Express

> [Express](http://www.expressjs.com.cn/4x/api.html) 是一个简洁、灵活的 node.js Web 应用开发框架,它提供一系列强大的特性，并且提供一些供客户端调用的api接口（支持中间件、支持路由处理等）,还有一个框架和它类似，叫做koa(有时间我也会写一篇);

```
 /*
 * express：express核心框架
 * body-parser：用来快速解析请求主体中内容的
 * express-session / cookie-parser：方便我们在express中操作session的
 */
 $ npm install express body-parser express-session cookie-parser
```

#### 创建服务，监听端口

```javascript
// 基于NODE内置模块，使用原生JS代码实现WEB服务
let http = require('http'),
    fs = require('fs'),
    url = require('url');
http.createServer((req, res)=> {
    let {url:reqURL, method, headers}=req,
        {pathname, query}= url.parse(reqURL, true);
    if (pathname === '/login' && method=='GET') {

    }
    if(pathname==='/register' && method=='POST'){

    }
}).listen(8888, ()=> {
    console.log(`server is success~`);
});

<*------------*>

// 基于express框架完成
// express就是把我们自己用原生写的封装好了，我们以后直接调取使用即可（方便开发，有点类似于JQ）

let express = require('express'),
    app = express();
app.listen(8888, ()=> {
    console.log(`server is success~`);
});

app.get(`/login`, (req, res)=> {
  //=>等价于原生
  //req.url  请求的地址+问号传参
  //req.method 请求的方式(大写)
  //req.headers 请求头(小写)

  //=>EXPRESS新提供的
  //req.path 相当于我们原生中通过url.parse方法解析出来的pathname
  //req.query 相当于解析出来的query

  //=>等价于原生
  //res.writeHead 重写响应头
  //res.write 响应主体
  //res.end 结束响应(响应主体) //=>只能返回字符串或者Buffer格式数据

  //=>EXPRESS独有的
      //res.sendFile('./index.html',{root:__dirname}); 读取指定的文件，并且把内容响应给客户端
      //res.json({"id":1,"name":"李雷"}); 我们可以直接放JSON,EXPRESS帮我们把它转换为字符串返回给客户端（并且设置好了响应头信息）
      //res.send() 综合体
      //res.statusCode = 200; 设置状态码
      //res.redirect() 重定向(对应状态码中的302/301)
      //res.sendStatus(404); 返回状态码并且附带一些状态码的描述信息
      //...

});

app.post(`/register`, (req, res)=> {

});
```

----
### Express中间件

> 使用中间件的两大作用：
 - 1、想要提取部分公共代码
 - 2、想要在主体API逻辑处理前，做一些特殊处理（把传递的参数做特殊的处理）

```javascript
let express = require('express'),
    app = express();
app.listen(8888, ()=> {
    console.log(`server is success~`);
});

//=>所有的客户端请求都会先执行这个中间件(然后执行NEXT后在进入下一个处理程序)
app.use((req, res, next)=> {
    //next:执行下一个处理(当前这个中间件根据情况，如果需要走到下一个操作，必须执行NEXT，如果不需要，直接响应内容即可)
    if (req.query.name) {
        req.userName = req.query.name;//=>把获取的内容写在了userName自定义属性上
        next();
    } else {
        res.send(`name必须传递`);
    }
});
//=>只有请求的地址中包含`/user`的才会进入这个中间件
app.use(`/user`, (req, res, next)=> {
    req.userName += '@';
    next();
});
app.get(`/user`, (req, res)=> {
    res.send(`My name is ${req.userName}`);
});
app.get(`/article`, (req, res)=> {
    res.send(`Article author is ${req.userName}`);
});
```

###Express中的路由router

> 当请求变得越来越多的时候，我们为了方便后期的管理和维护，我们会把相同功能体系的接口进行归类；在实现的时候，也会分类实现（分组管理）；
> 例如：
> /user/signin
> /user/signup
>
> /product/query
> /product/search
>
> /log/info
>   GET获取
>   POST请求设置
>   同一个地址，因为请求方式不一样，实现不一样的功能
> ...
> 对于上面的操作我们可以基于Express中的路由来完成

1、我们创建一个routers文件夹，在这个文件夹中存储所有功能模块的接口信息（分类存储）
> 格式 ： routers
          - user.js
          - product.js
          - login.js

2、在每一个路由模块中完成相关API接口的编写（举例：user模块）

```
let express = require('express'),
    router = express.Router(); //=>router和app其实差不多
router.use((req, res, next)=> {
    console.log(`ok`);
    next();
});
router.post(`/signin`, (req, res)=> {
    res.send('login success');
});
router.post(`/signup`, (req, res)=> {
    res.send('register success');
});
module.exports = router; //=>把创建的路由导出,方便后续调取使用
```

3、在server中需要调取使用
```
let express = require('express'),
    app = express();
...

app.use(`/user`, require('./routers/user'));
```

### Express 中获取客户端传递的信息

> 客户端把信息传递给服务器：
> - 1、问号传参（GET请求）
> - 2、设置请求主体（POST请求）
> - 3、设置请求头（cookie等信息传送）
> ...

**问号传参 & 请求头**

```javascript
app.get(`/query`, (req, res)=> {
    console.log(req.headers);//=> 通过 req 的 header 可以直接的获取请求头信息(想获取其中的某一个:req.headers.host...)

    res.send(req.query);//=>通过 req 中的 query 就可以获取问号传递的参数值(并且存储的方式是 KEY : VALUE)

});
```

**请求主体**

```javascript
//=>基于传统的内置模块自己实现
app.use(`/reg`, (req, res, next)=> {
    //=>POST请求获取请求主体内容,需要基于事件完成
    let str = ``;
    req.on('data', chunk=> {
        //=>正在接收请求主体中的内容(一般内容偏多)
        str += chunk;
    });
    req.on('end', ()=> {
        //=>接收结束,此时STR存储的就是传递进来的信息
        //=>客户端传递给服务器的信息一般都是字符串格式的(JSON字符串或者普通的FORMAT-DATA字符串[xxx=xxx&xxx=xxx])

        let data = require('querystring').parse(str);//=>这个内置模块就是把FORMAT-DATA变为对象键值对的方式存储
        req.body = data;//=>把解析后的数据放到REQ的自定义属性上,在其它的方法中可以获取使用
        next();
    });
});
app.post(`/reg`, (req, res)=> {
    res.send(req.body);
});
```

> 但是这种方式太麻烦了，我们可以使用第三方插件搞定刚才处理的事情：`body-parser`

```javascript
let bodyParser = require('body-parser');
app.use(bodyParser.urlencoded({extended: false}));
app.use(bodyParser.json());
app.use(bodyParser.raw());
app.use(bodyParser.text());
app.post(`/reg`, (req, res)=> {
    res.send(req.body);
});
```