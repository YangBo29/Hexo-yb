title: gulp
date: 2018-06-17
tags: js
toc: true
categories: gulp
---

## gulp

### glup 语法

> gulp.src(globs[, options])  输出（Emits）符合所提供的匹配模式（glob）或者匹配模式的数组（array of globs）的文件。 将返回一个 Vinyl files 的 stream 它可以被 piped 到别的插件中
>  - globs  :  String 或 Array
>  - options ：Object
>   + options.buffer ： Boolean 默认值： true
>   + options.read   ：   Boolean 默认值： true
>   + options.base   ：  String

> gulp.dest(path[, options]) 能被 pipe 进来，并且将会写文件。并且重新输出（emits）所有数据，因此你可以将它 pipe 到多个文件夹。如果某文件夹不存在，将会自动创建它
>  - path : String or Function 文件将被写入的路径（输出目录）
>  - options : Object 
>  + options.cwd : String 默认值： process.cwd() 输出目录的 cwd 参数，只在所给的输出目录是相对路径时候有效
>  + options.mode : String 默认值： 0777 八进制权限字符，用以定义所有在输出目录中所创建的目录的权限。

> gulp.task(name[, deps], fn) 定义一个使用 编排器 实现的任务（task）
> - name ：String  任务的名字，如果你需要在命令行中运行你的某些任务，那么，请不要在名字中使用空格
> - deps ： Array 一个包含任务列表的数组，这些任务会在你当前任务运行之前完成【前置依赖】
> - fn ： func 该函数定义任务所要执行的一些操作【支持异步任务】
> ```
> gulp.task('somename', function() {
  var stream = gulp.src('client/**/*.js')
    .pipe(minify())
    .pipe(gulp.dest('build'));
  return stream;
})
> gulp.task('somename', function() {
  var deferred = Q.defer();
  // 执行异步的操作
  setTimeout(function() {
    deferred.resolve();
  }, 1);
  return deferred.promise;
});
> // 在gulp 执行任务的时候，task会以最大的并发数执行，也就是gulp 会一次性的执行所有的task 并且不会等待，所以如果你想创建一个序列化的task队列，并想按顺序执行，你需要做两步：
> // 1、给出一个提示，告诉task什么时候执行完毕
> // 2、给出一个提示，告知task 一来另外一个task 的完成
> 
> // 返回一个 callback，因此系统可以知道它什么时候完成
gulp.task('one', function(cb) {
    // 做一些事 -- 异步的或者其他的
    cb(err); // 如果 err 不是 null 或 undefined，则会停止执行，且注意，这样代表执行失败了
});
// 定义一个所依赖的 task 必须在这个 task 执行之前完成
gulp.task('two', ['one'], function() {
    // 'one' 完成后
});
gulp.task('default', ['one', 'two']);  // 创建一个有序执行的任务
> ```

> gulp.watch(glob [, opts], tasks) 或 gulp.watch(glob [, opts, cb])
>> gulp.watch(glob[, opts], tasks) 
> - glob :  String or Array  用来指定具体监控哪些文件的变动
> - opts :  Object  传给 监视者 的参数
> - task :  Array  需要在文件变动后执行的一个或者多个通过 gulp.task() 创建的 task 的名字
> 
>> gulp.watch(glob [, opts, cb]) 
> - glob :  String or Array  用来指定具体监控哪些文件的变动
> - opts :  Object  传给 监视者 的参数
> - cb(event) :  每次变动需要执行的 callback
>  + callback 会被传入一个名为 event 的对象。这个对象描述了所监控到的变动
>  + event.type ：  String  发生的变动的类型：added, changed 或者 deleted
>  + event.path ：  String  触发了该事件的文件的路径

### 使用技巧  https://www.gulpjs.com.cn/docs/recipes/ （官网）

### gulp 在项目中用到的一些插件

#### gulp-stylelint （代码校验）
> 参数
> failAfterError ： 当设置为true时，如果出现错误级别的警告，进程将以非零错误代码结束。默认值为true
> reportOutputDir ：用于将lint结果写入文件系统的基本目录。当前工作目录的默认值
> reporters ：配置对象列表。默认为空数组
>  - formatter ：结果格式化程序
>  - save ：将格式化的结果保存到一个文件中
>  - console  ：格式化结果到控制台
> debug ：当设置为true时，错误处理程序将打印错误堆栈跟踪。默认值为假
> fix :  为true选项指示stylelint尽可能多地解决问题。这些修正被应用到gulp流中。可以将固定的内容保存到文件中
```
const stylelint = require('gulp-stylelint')
// stylelint检测 

// 作用： 统一代码风格，检查语法错误； 减少错误语法的出现率和git管理中代码冲突的情况

gulp.task('lintCss', () => {
    return gulp
        .src(['assets/css/*.scss', 'assets/css/*/*.scss']) // 单路径：String，多路径：Array
        .pipe(stylelint({ // 调用 有点想node express 里面的use
	        failAfterError: true,
	        reportOutputDir: 'reports/lint',
	        fix: true,
            reporters: [
                {formatter: 'string', console: true},
                {formatter: 'json', save:'text.txt', console:true}           
            ]，
            debug: true
        }))
        .pipe(gulp.dest('src')); // 只有在fix 设置为true 的时候加这项操作
})

```

#### gulp-sass 和 gulp-sourcemaps （sass编译，源映射）

```
var sass = require('gulp-sass'); // 编译sass -> css
var sourcemaps = require('gulp-sourcemaps'); // 将编译、压缩后的 css 显示成原始代码，映射到调试工具中

// sass处理
gulp.task('sass', ['lintCss'], () => { // 第二个参数是依赖进程
    return gulp.src('assets/css/*.scss')
	    .pipe(sourcemaps.init()) //初始化
        .pipe(sass().on('error', sass.logError)) // .pipe(sass。sync().on('error', sass.logError)) 同步编译
        .pipe(sourcemaps.write('')) // 编写源映射
        .pipe(gulp.dest('public/css'))
})
```

#### gulp-clean-css （css压缩）

> 用于压缩css文件，减少文件大小，并给引用的url添加版本号避免缓存

```
cleanCSS= require('gulp-clean-css')
cssver = require('gulp-make-css-url-version'); // 给css文件中的引用文件添加版本号（MD5），避免缓存
参数：useDate : true 使用时间戳制作
     assetsDir: __dirname + '/public' 在某些特定的情况下，指定公共目录来正确地计算MD5

gulp.task('minifyCss', function () {
    gulp.src('src/css/*.css')
	    .pipe(cssver())
        .pipe(cleanCSS({
            advanced: false,//类型：Boolean 默认：true [是否开启高级优化（合并选择器等）]
            compatibility: 'ie7',//保留ie7及以下兼容写法 类型：String 默认：''or'*' [启用兼容模式； 'ie7'：IE7兼容模式，'ie8'：IE8兼容模式，'*'：IE9+兼容模式]
            keepBreaks: true,//类型：Boolean 默认：false [是否保留换行]
            keepSpecialComments: '*'
            //保留所有特殊前缀 当你用autoprefixer生成的浏览器前缀，如果不加这个参数，有可能将会删除你的部分前缀
        }))
        
        .pipe(gulp.dest('dist/css'));
});
```

#### gulp-imagemin （图片压缩）

```
const imagemin = require('gulp-imagemin')
const pngquant = require('imagemin-pngquant') // 深度压缩png格式图片

gulp.task('minifyImg', () => {
    return gulp.src(['assets/img/*', 'assets/img/*/*'])
        .pipe(imagemin({
            optimizationLevel: 5, //类型：Number  默认：3  取值范围：0-7（优化等级）
            progressive: true, //类型：Boolean 默认：false 无损压缩jpg图片 
            interlaced: true, //类型：Boolean 默认：false 隔行扫描gif进行渲染
            multipass: true //类型：Boolean 默认：false 多次优化svg直到完全优化
            svgoPlugins: [{removeViewBox: false}], //不要移除svg的viewbox属性 深度压缩时参数
            use: [pngquant()] //使用pngquant深度压缩png图片的imagemin插件 深度压缩时参数
        }))
        .pipe(gulp.dest('public/img'))
        .pipe(connect.reload()) // 配合gulp-connect使用， 可以捕获根目录下的子目录变化
})
```

####  gulp-clean （清除文件/文件夹）

```
const clean = require('gulp-clean')

// 删除文件及文件夹，在执行打包的时候，一般都需要先清理目标文件夹，以保证每次打包时，都是最新的文件
gulp.task('cleanPublic', () => {
    return gulp.src(['public/css', 'public/js', 'public/img'], {read: false}) // read:false 阻止gulp读取文件的内容，并使这个任务更快。如果在同一条流中清除后需要文件及其内容，则不要将read选项设置为false
        .pipe(clean({force: true})) //对于安全文件和文件夹，在当前工作目录之外，只能用option force设置为true
})
```

#### gulp-connect （动态监控文件变化）

```
const connect = require('gulp-connect') // 可以开启多个服务

gulp.task('connect1', () => {
    connect.server({
        root: ['public','img'],  // 需要监控的根目录
        host: 192.168.90.188, // IP地址
        port: 3000, // 端口号
        livereload: true   // 重新加载
    })
})

gulp.task('connect2', () => {
    connect.server({
        root: 'css',  // 需要监控的根目录
        host: 192.168.90.188, // IP地址
        port: 3001, // 端口号
        livereload: true   // 重新加载
    })
})

// 监听
gulp.task('watch', () => {
    gulp.watch(['assets/css/*.scss', 'assets/css/*/*.scss'], ['minifyCss']) // 参数1 ： 监听文件 ， 参数2 ： 所监听的文件发生改变后执行的任务 （原理很像发布订阅）
    gulp.watch(['assets/js/*.js', 'assets/js/*/*.js'], ['buildJs'])
    gulp.watch(['assets/img/*', 'assets/img/*/*'], ['minifyImg'])
})
```

#### gulp-postcss (插件平台) [类似于webpack中的plugins]

```
const postcss = require('gulp-postcss')
const autoprefixer = require('autoprefixer') // 前缀补充

gulp.task('postcss', ['sass'], () => {
    const plugins = [
        autoprefixer({browsers: ['last 2 versions', 'ie >= 8', '> 5% in CN']}) // 参数: [主流浏览器的最近2个版本，IE8以上，中国市场占有量大于5%]
    ]
    return gulp.src('public/css/*.css')
        .pipe(sourcemaps.init())
        .pipe(postcss(plugins)) // 参数是个Array
        .pipe(sourcemaps.write(''))
        .pipe(gulp.dest('public/css'))
        .pipe(connect.reload())
})
```