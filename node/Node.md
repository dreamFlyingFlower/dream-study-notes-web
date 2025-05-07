# Node



# NPM,YARN



* 要使用yarn,需要先安装:npm install -g yarn
* npm/yarn init:初始化包
* npm install/yarn install:安装所有依赖
* npm install xxx@version:安装指定版本的依赖
* npm install -g xxx/yarn global add xxx:全局安装
* npm install xxx --save/yarn add xxx:安装包
* npm install xxx --save-dev/yarn add xxx --dev:安装开发依赖的包
* npm uninstall xxx/yarn remove xxx:移除包
* npm update xxx/yarn upgrade xxx:更新包
* npm config set registry url/yarn config set registry url:设置下载镜像的地址
* npm run/yarn run:执行包



# 全局成员



* `global`:全局成员对象,类似于浏览器的windows,使用全局成员时可省略

* `__filename`:获取当前文件的全路径的绝对路径
* `__dirname`:获取当前文件的父路径的绝对路径



## process



* `process.argv`:一个数组,默认前两项数据分别是Node.js环境的路径和当前执行的js文件的全路径,从第三个参数开始表示命令行执行命令时输入的参数
* `process.arch`:打印当前系统的架构(64位或者32位)



## exports



* 将一个JS文件中的属性,方法等导出,使其他JS文件可以使用import引用



## require



* 引入其他文件内容,可以是JS,JSON,NODE后缀的文件
* 引入文件时若不加文件后缀,则根据JS->JSON->NODE后缀依次寻找,找到之后不再找其他后缀的文件
* JS需要是exports或module,global导出的内容,JSON和NODE文件不需要exports等关键字
* JSON一般是直接数据,NODE一般是C调用
* 多次引入同一个文件不会多次执行,会从缓存中取该文件,方法也不会多次执行
* 加载第三方模块时,使用的是包名,不带路径,npm保证包名不会重复.加载时先从node_modules找该包名,找到该包名之后再找package.json文件,查看文件里的main属性,找到入口文件.如果main是空或指定文件找不到,会默认加载index.js.如果都没有,会找到node_modules的上一级,直到根目录,最后抛异常



# 模块导出引入



* Node中,每个模块内部都有一个自己的module对象,该对象有一个成员exports,类似如下:

  ```typescript
  let module ={
  	exports:{
  	
  	}
  }
  return module.exports;
  ```

  

## exports



```javascript
// 导出a.js,类似于一个对象
function sum(arg1,arg2){
	return arg1+arg2;
}

// 导出,该语句实际上完整的是module.exports.sum=sum;
exports.sum=sum;

// 引入a.js并使用
let a = require('./a.js');
console.log(a.sum(1,2));
```



## module



```javascript
// 导出a.js,是一个单独的方法
function sum(arg1,arg2){
	return arg1+arg2;
}

module.exports=sum;

// 引入a.js并使用
let a = require('./a.js');
console.log(a(1,2));

// 导出多个属性或方法
module.exports={
    sum:sum,
    a:123
};

let a = require('./a.js');
console.log(a.sum(1,2));
```



## global



```javascript
// 导出文件a.js
let num = 123;
global.num = num;

// 引入,不需要返回值
require('./a.js');
console.log(global.num);
```





# Path



* const path=require('path'):固定写法,引入路径模块,可以使用一个变量存储该值

* path.basename('/app/test.html'):获取路径的最后一部分

* path.basename('/app/test.html', '.html'):获得路径最后一部分,同时去掉文件后缀

* path.dirname('/app/test.html'):获得指定路径的父路径

* path.extname('test.html'):获得文件的扩展名,结果带点

* path.parse('/app/test.html'):根据指定文件路径获得该文件的文件对象

  ```javascript
  console.log(path.parse(__filename));
  {
  	root:'文件根路径',
  	dir:'文件的全路径',
  	base:'文件的名称',
  	ext:'文件的扩展名,带点',
  	name:'文件的名称,不带扩展名'
  }
  ```

* path.format(obj):将一个文件对象转为字符串,输出的是文件的全路径

* path.join(...):拼接路径,会处理特殊字符串:`.`以及`..`,在连接路径的时候会格式化路径

  ```javascript
  // 输出/foo/bar/baz/asdf/quux的上2级目录,即/foo/bar/baz/
  console.log(path.join('/foo', 'bar', 'baz/asdf', 'quux', '../../'));
  ```

* path.normalize('path'):规范化路径,去除路径多于的/,点等

  ```javascript
  // /foo/bar/baz/asdf
  console.log(path.normalize('/foo/bar//baz/asdf/quux/..'));
  // C:\temp\foo\
  console.log(path.normalize('C:\\temp\\\\foo\\bar\\..\\'));
  ```

* path.relative(from,to):计算从from到to的相对路径

  ```js
  // ../../impl/bbb
  console.log(path.relative('/data/orandea/test/aaa', '/data/orandea/impl/bbb'));
  // ../../impl/bbb
  console.log(path.relative('C:\\orandea\\test\\aaa', 'C:\\orandea\\impl\\bbb'));
  ```

* path.resolve(...):路径解析,相当于cd命令,会根据相对路径和绝对路径计算,返回计算后的路径

  ```js
  // /app/test1
  console.log(path.resolve('/app/test', '/app/test1'));
  // /app/test/test1
  console.log(path.resolve('/app/test', './test1'));
  // 假设当前路径为/app/test,输出为/app/test/wwwroot/static_files/gif/image.gif
  console.log(path.resolve('wwwroot', 'static_files/png/', '../gif/image.gif'));
  ```

* path.delimiter:环境变量分隔符,windows中使用;

* path.sep:linux中使用:,路径分隔符,windows是\,Linux是/



# File



* `const fs=require('fs')`:固定写法,引入文件模块,可以使用一个变量存储该值

* fs.stat(path,callback):查看文件状态,包括文件名,大小,访问时间等.该方法为异步操作

* fs.statSync(path):同步查看文件状态

  ```js
  const fs = require('fs');
  fs.stat('./abc',(err,stat) => {
      // 一般回调函数的第一个参数是错误对象,如果err为null,表示没有错误,否则表示报错了
      if(err) {
          return;
      }
      if(stat.isFile()){
          console.log('文件');
      }else if(stat.isDirectory()){
          console.log('目录');
      }
      // atime:文件访问时间;ctime:文件的状态信息发生变化的时间(比如文件的权限)
      // mtime:文件数据发生变化的时间,birthtime:文件创建的时间
      console.log(stat);
  });
  
  let ret = fs.statSync('./data.txt');
  console.log(ret);
  ```

* fs.readFile(path,enc,callback):异步读文件.enc是编码,callback是回调

* fs.readFileSync(path,enc):同步读文件

  ```js
  const fs = require('fs');
  const path = require('path');
  
  let strpath = path.join(__dirname,'data.txt');
  fs.readFile(strpath,(err,data)=>{
      if(err) return;
      console.log(data.toString());
  });
  
  // 如果有第二个参数,回调函数获取到的数据就是字符串;如果没有第二个参数,得到的就是Buffer实例
  fs.readFile(strpath,'utf8',(err,data)=>{
      if(err) return;
      // console.log(data.toString());
      console.log(data);
  });
  
  // 同步操作
  let ret = fs.readFileSync(strpath,'utf8');
  console.log(ret);
  ```

* fs.writeFile(path,content[,option],callback):异步写文件,option包括编码,权限,读写模式,见官网

* fs.writeFileSync(path,content[,option]):同步写文件

  ```js
  const fs = require('fs');
  const path = require('path');
  
  let strpath = path.join(__dirname,'data.txt');
  fs.writeFile(strpath,'hello nihao','utf8',(err)=>{
      if(!err){
          console.log('文件写入成功');
      }
  });
  
  let buf = Buffer.from('hi');
  fs.writeFile(strpath,buf,'utf8',(err)=>{
      if(!err){
          console.log('文件写入成功');
      }
  });
  
  // 同步操作
  fs.writeFileSync(strpath,'tom and jerry');
  ```

* fs.createReadStream(path[,options]):流式操作读大文件

* fs.createWriteStream(path[, options]):流式操作写大文件

  ```js
  const path = require('path');
  const fs = require('fs');
  
  let spath = path.join(__dirname,'../03-source','file.zip');
  let dpath = path.join('C:\\Users\\www\\Desktop','file.zip');
  
  let readStream = fs.createReadStream(spath);
  let writeStream = fs.createWriteStream(dpath);
  
  let num = 1;
  // data是固定写法,chunk是块数据,读取一部分数据就调用一次
  readStream.on('data',(chunk)=>{
      num++;
      writeStream.write(chunk);
  });
  
  // 文件处理完毕操作
  readStream.on('end',()=>{
      console.log('文件处理完成:'+num);
  });
  
  // 第二种方式:pipe的作用直接把输入流和输出流,相当于将data和end一步完成
  readStream.pipe(writeStream);
  // 第三种方式:直接将文件从一个文件读取之后复制到另一个文件
  fs.createReadStream(spath).pipe(fs.createWriteStream(dpath));
  ```

* fs.mkdir(path[, mode], callback):创建目录

* fs.mkdirSync(path[, mode]):同步创建目录

* fs.readdir(path[, options], callback):读取目录

* fs.readdirSync(path[, options]):同步读取目录

* fs.rmdir(path, callback):删除目录

* fs.rmdirSync(path):同步删除目录

* fs.watchFile(path,(newVal,oldVal)=>{}):监视文件内容

  ```js
  const path = require('path');
  const fs = require('fs');
  // 创建目录
  fs.mkdir(path.join(__dirname,'abc'),(err)=>{
      console.log(err);
  });
  
  fs.mkdirSync(path.join(__dirname,'hello'));
  
  // 读取目录
  fs.readdir(__dirname,(err,files)=>{
      files.forEach((item,index)=>{
          fs.stat(path.join(__dirname,item),(err,stat)=>{
              if(stat.isFile()){
                  console.log(item,'文件');
              }else if(stat.isDirectory()){
                  console.log(item,'目录');
              }
          });
      });
  });
  
  let files = fs.readdirSync(__dirname);
  files.forEach((item,index)=>{
      fs.stat(path.join(__dirname,item),(err,stat)=>{
          if(stat.isFile()){
              console.log(item,'文件');
          }else if(stat.isDirectory()){
              console.log(item,'目录');
         }
      });
  });
  
  // 删除目录
  fs.rmdir(path.join(__dirname,'abc'),(err)=>{
      console.log(err);
  });
  
  fs.rmdirSync(path.join(__dirname,'qqq'));
  ```

* 创建文件

```javascript
// 文件操作案例,初始化目录结构
const path = require('path');
const fs = require('fs');

// 文件根目录
let root = 'D:\\test';
// 写入文件的内容
let fileContent = `
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
</head>
<body>
    <div>welcome to this</div>
</body>
</html>
`;

// 需要初始化的目录和文件
let initData = {
    projectName : 'mydemo',
    data : [{
        name : 'img',
        type : 'dir'
    },{
        name : 'css',
        type : 'dir'
    },{
        name : 'js',
        type : 'dir'
    },{
        name : 'index.html',
        type : 'file'
    }]
}

// 创建项目根路径
fs.mkdir(path.join(root,initData.projectName),(err)=>{
    if(err) {
        return;
    }
    // 创建子目录和文件
    initData.data.forEach((item)=>{
        if(item.type == 'dir'){
            // 异步创建子目录
            // fs.mkdir(path.join(root,initData.proejctName,item.name));
            // 同步创建子目录
            fs.mkdirSync(path.join(root,initData.projectName,item.name));
        }else if(item.type == 'file'){
            // 同步创建文件并写入内容,异步使用writeFile
            fs.writeFileSync(path.join(root,initData.projectName,item.name),
                             fileContent);
        }
    });
});
```



# Buffer



* 字节数组操作

* Buffer.alloc(5):创建一个长度为5的空字节数组

* Buffer.from('hello','utf8'):根据字符串创建一个字节数组,得到的字节数组值以16进制表示

* Buffer.from([0x62...]):根据16进制字节数组创建一个字节数组,调用`toString()`输出字符串

* Buffer.isEncoding('utf8'):判断字节数组的编码

* Buffer.isBuffer(buf):判断是否为Buffer

* Buffer.byteLength(buf,enc):返回指定编码的字节长度,默认utf8

* Buffer.concat(buf1,buf2...):将一组Buffer合并为一个Buffer

* buf.write(str[,offset[,length,[enc]]]):向buf中写入字符串,返回被写入字节的长度

  * offset:从str的什么位置开始写入
  * length:写入多少字节
  * enc:编码

* buf.slice([start,[end]]):截取新的buffer对象,若不指定参数相当于复制,和源对象不是同一个对象

* buf.toString():将Buffer对象转为字符串

* buf.toJson():将Buffer对象转为JSON字符串

  ```js
  // toJSON()不需要显式调用,当JSON.stringify方法调用的时候会自动调用toJSON()
  const buf = Buffer.from([0x1, 0x2, 0x3, 0x4, 0x5]);
  // 结果为JSON字符串:{"type":"Buffer","data":[1,2,3,4,5]}
  console.log(JSON.stringify(buf));
  
  const buf1 = Buffer.from('aello');
  // 输入字符串时,结果是ASCII字符串数组:{"type":"Buffer","data":[97,101,108,108,111]}
  console.log(JSON.stringify(buf1));
  ```





# 类



* 同Java和其他强语言类似,可单继承,每个类只能有一个构造方法,父子类构造可以不相同

* 若继承了某个类,则子类构造必须先调用父类构造

  ```js
  class Person{
  	constructor(){
  	}
  	getInfo(){
  		return "person";
  	}
      static test(){
          return "static function";
      }
  }
  
  class Student extends Person{
  	constructor(name,age){
  		super();
  		this.name = name;
  		this.age = age;
  	}
  	getInfo(){
  		return super.getInfo() + ",student," + this.name + "," + this.age;
  	}
  }
  
  let student = new Student("aaa",12);
  console.log(student.getInfo());
  // 错误,不能使用调用静态方法,只能使用类名调用
  // console.log(student.test());
  console.log(Student.test());
  ```





# Http



## 入门



```js
const http = require('http');
// 创建服务器实例对象
let server = http.createServer();
// 绑定请求事件,on('request')是固定写法
server.on('request',(req,res)=>{
    res.end('hello');
});
// 监听端口
server.listen(3000);

// 链式调用,一步完成上述操作.req,res是请求和响应
http.createServer((req,res)=>{
    // 结束请求
    res.end('ok');
})
	// 端口,IP,请求最大数,监听成功的回调事件
    .listen(3000,'192.168.0.150',1000,()=>{
    console.log('running...');
});
```



## GET参数处理



```js
const url = require('url');

// parse()将URL字符串转化为对象
let str = 'http://www.baidu.com/abc/qqq?flag=123&keyword=java';
// 第2个参数决定是否将格式化后的obj.query输出为对象.默认false字符串
let ret = url.parse(str,true);
console.log(ret.query.keyword);
// 输出格式数据如下:
let obj = {
    // 协议
    protocol: 'http:',
    // 协议后面的2个斜杠
    slashes: true,
    // 权限校验
    auth: null,
    // 域名,包括端口
    host: 'www.baidu.com',
    // 端口
    port: null,
    // 域名,不包括端口
    hostname: 'www.baidu.com',
    // 锚点,类似于单页面应用
    hash: null,
    // URL地址后直接拼接的参数,带?
    search: '?flag=123&keyword=java',
    // URL地址后直接拼接的参数,不带?
    query: 'flag=123&keyword=java',
    // URI资源地址,访问路径
    pathname: '/abc/qqq',
    // 资源地址加请求参数
    path: '/abc/qqq?flag=123&keyword=java',
    // 完整请求地址
    href: 'http://www.baidu.com/abc/qqq?flag=123&keyword=java' 
};
// format()将对象转化为标准的URL字符串
let ret1 = url.format(obj);
console.log(ret1);
```



## POST参数处理



```js
const querystring = require('querystring');
const http = require('http');

// parse()将字符串转成对象.如果有key值一样,会自动转成数组
let param = 'foo=bar&abc=xyz&abc=123';
let obj = querystring.parse(param);
console.log(obj);
// stringify()将对象转成字符串
let obj1 = {
    flag : '123',
    abc : ['hello','hi']
}
let str1 = querystring.stringify(obj1);
console.log(str1);

http.createServer((req,res)=>{
    if(req.url.startsWith('/login')){
        let pdata = '';
        req.on('data',(chunk)=>{
            // 每次获取一部分数据
            pdata += chunk;
        });

        req.on('end',()=>{
            // 得到完整的数据,POST提交的方式应该是表单
            console.log(pdata);
            let obj = querystring.parse(pdata);
            res.end(obj.username+'-----'+obj.password);
        });
    }
}).listen(3000,()=>{
    console.log('running...');
})
```



## 请求路径转发



```js
const http = require('http');

// req对象是Class:http.IncomingMessage的实例对象
// res对象是Class:http.ServerResponse的实例对象
http.createServer((req,res)=>{
    // req.url可以获取URL中的路径(端口之后部分)
    if(req.url.startsWith('/index')){
        // write向客户端响应内容,可以写多次
        res.write('hello');
        res.write('hi');
        // end方法用来完成响应,只能执行一次
        res.end();
    }else if(req.url.startsWith('/about')){
        res.end('about');
    }else{
        res.end('no content');
    }
}).listen(3000,'192.168.0.150',()=>{
    console.log('running...');
});
```



## 响应完整的页面



```js
const http = require('http');
const path = require('path');
const fs = require('fs');

// 根据路径读取文件的内容,并且响应到浏览器端.该方法必须在写createServer前面
let readFile = (url,res) => {
    fs.readFile(path.join(__dirname,'view',url),'utf8',(err,fileContent)=>{
        if(err){
            res.end('server error');
        }else{
            res.end(fileContent);
        }
    });
}

http.createServer((req,res)=>{
    // 处理路径的分发
    if(req.url.startsWith('/index')){
        readFile('index.html',res);
    }else if(req.url.startsWith('/about')){
        readFile('about.html',res);
    }else{
        res.writeHead(200,{
            'Content-Type':'text/plain; charset=utf8'
        });
        // 重定向
        // res.statusCode= 302;
        // res.setHeader("Location","/");
        res.end('页面被狗狗叼走了');
    }
}).listen(3000,'192.168.0.150',()=>{
    console.log('running...');
});
```



* index.html

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>主页</title>
</head>
<body>
    <div>欢迎访问</div>
</body>
</html>
```



* about.html

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>关于我们</title>
</head>
<body>
    <div>关于我们</div>
</body>
</html>
```





## 动态网站开发



```js
// 简单的成绩查询
const http = require('http');
const path = require('path');
const fs = require('fs');
const querystring = require('querystring');
const scoreData = require('./scores.json');

http.createServer((req,res)=>{
    if(req.url.startsWith('/query') && req.method == 'GET'){
        // 查询成绩的入口地址/query,请求方式是GET
        // 读取view/index.tpl模板文件,将读取玩的结果直接返回给前端网页
        fs.readFile(path.join(__dirname,'view','index.tpl'),'utf8',(err,content)=>{
            if(err){
                // 设置响应码和请求头
                res.writeHead(500,{
                    'Content-Type':'text/plain; charset=utf8'
                });
                res.end('服务器错误,请与管理员联系');
            }
            // 将读取的字符串返回给前端
            res.end(content);
        });
    }else if(req.url.startsWith('/score') && req.method == 'POST'){
        // 获取成绩的结果/score,请求方式是POST
        let pdata = '';
        // 读取req中的参数拼接成字符串,on('data')是固定写法
        req.on('data',(chunk)=>{
            pdata += chunk;
        });
        // 读取完请求中的数据之后的操作,on('end')是固定写法
        req.on('end',()=>{
            // 将参数字符串转对象
            let obj = querystring.parse(pdata);
            // 获取输入的学号
            let result = scoreData[obj.code];
            // 读取文件并渲染
            fs.readFile(path.join(__dirname,'view','result.tpl'),'utf8',
                        (err,content)=>{
                if(err){
                    res.writeHead(500,{
                        'Content-Type':'text/plain; charset=utf8'
                    });
                    res.end('服务器错误，请与管理员联系');
                }
                // 返回内容之前进行数据渲染
                content = content.replace('$$chinese$$',result.chinese);
                content = content.replace('$$math$$',result.math);
                content = content.replace('$$english$$',result.english);
                content = content.replace('$$summary$$',result.summary);
                res.end(content);
            });
        });
    }
}).listen(3000,()=>{
    console.log('running....');
});
```

* index.tpl

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>查询成绩</title>
</head>
<body>
    <form action="http://localhost:3000/score" method="post">
        请输入考号：<input type="text" name="code">
        <input type="submit" value="查询">
    </form>
</body>
</html>
```

* result.tpl

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>成绩结果</title>
</head>
<body>
    <div>
        <ul>
            <li>语文：$$chinese$$</li>
            <li>数学：$$math$$</li>
            <li>外语：$$english$$</li>
            <li>综合：$$summary$$</li>
        </ul>
    </div>
</body>
</html>
```

* scores.json

```json
{
    "no123" : {
        "chinese" : "100",
        "math" : "140",
        "english" : "149",
        "summary" : "289"
    },
    "no124" : {
        "chinese" : "120",
        "math" : "120",
        "english" : "119",
        "summary" : "239"
    },
    "no125" : {
        "chinese" : "130",
        "math" : "110",
        "english" : "139",
        "summary" : "269"
    }
}
```



# Socket



```js
// 服务端
const socket = require('net');
// 创建一个Socket服务器,回调函数
let server = socket.createServer(socketConnect);
// 客户端连接事件
function socketConnect(socket){
    console.log("有客户端进行连接..."+socket.remoteAddress());
    // 监听流数据事件,data是固定写法,chunk是Buffer卡类型
    socket.on("data",(chunk)=>{
        console.log(chunk.toString());
    });
}
// 监听
server.listen(665,(error)=>{
    if(error){
        console.log("端口被占用,请先关闭该端口");
        return false;
    }
});

// 客户端
const socket = require('net');
// 创建一个Socket客户端
let client = socket.connect(665,()=>{
    // 发送数据给服务器
    client.write("this is a info");
});
```



# Nodemon



* 一个热部署的第三方工具
* 将启动时的node换成nodemon启动即可完成使用



# Express



* 第三方工具,对http进行封装,require后使用



## 入门



```js
let express = require('express');
let app = express();

// 绑定路由,GET方式监听根目录
app.get('/', function(req, res) {
    // 响应请求
    res.send('Hello World!');
});

app.use((req,res)=>{
    // use分发可以处理所有的路由请求
    res.send("ok");
})

var server = app.listen(3000, "localhost", function() {
    // 监听的域名或者IP
    let host = server.address().address;
    // 监听的端口
    let port = server.address().port;
    console.log('Example app listening at http://%s:%s', host, port);
});
```



## 托管静态文件



```js
const express = require('express');
const app = express();

// use的第一个参数指定一个虚拟路径,第2个参数指定静态资源根目录,虚拟路径可不指定
// Web访问时的地址为:ip:port/虚拟路径/静态文件名,若无虚拟路径,则直接访问ip:port/静态文件名
let server = app.use('/abc',express.static('public'));
// 可指定多个目录作为静态资源目录
app.use('/nihao',express.static('hello'));
server.listen(3000,()=>{
    console.log('running...');
});

// 同上,更简洁
app.use('/abc',express.static('public'));
app.use('/nihao',express.static('hello'));
app.listen(3000,()=>{
    console.log('running...');
});
```



## 请求传递



```js
const express = require('express');
const app = express();
let total = 0;

// next():将请求传递到下一个请求,从书写的顺序,依次往下传递.若不写next(),则请求到不了send(),会超时
// 不写访问路径时,所有的请求都要通过该方法,相当于全局路径监听
app.use((req,res,next)=>{
    console.log('有人访问');
    next();
});

app.use('/user',(req,res,next)=>{
    // 记录访问时间
    console.log(Date.now());
    next();
});

app.use('/user',(req,res,next)=>{
    // 记录访问日志
    console.log('访问了/user');
    // 只会跳到/user的下一个路由,直到遇到res.send();
    next();
    // 会跳过本次路由后是其他路由,跳到
});

app.use('/user',(req,res)=>{
    total++;
    console.log(total);
    res.send('result');
});

app.use('/login',(req,res)=>{
    total++;
    console.log(total);
    // 不会走本方法的send,而是直接跳过所有的路由到本次send的路由,跳到下一次的send
    next("route");
},(req,res)={
    res.send("result");
});

// login的next("route")会跳到本方法
app.use("/login",(req,res)=>{
    res.send("result1");
})

app.listen(3000,()=>{
    console.log('running...');
});
```



```js
var cb0 = function (req, res, next) {
  console.log('CB0');
  next();
}

var cb1 = function (req, res, next) {
  console.log('CB1');
  next();
}

var cb2 = function (req, res) {
  res.send('Hello from C!');
}

// 同上,依次指定路由
app.get('/example', [cb0, cb1, cb2]);

app.listen(3000,()=>{
    console.log('running...');
});
```



## body-parser



```js
const express = require('express');
const app = express();
// 对参数进行解析的组件
const bodyParser = require('body-parser')
// 挂载内置中间件
app.use(express.static('public'));

// 挂载参数处理中间件,处理post,application/x-www-form-urlencoede
app.use(bodyParser.urlencoded({ extended: false }));
// 处理application/json
// app.use(bodyParse.json())

// 处理get提交参数
app.get('/login',(req,res)=>{
    let data = req.query;
    console.log(data);
    res.send('get data');
});

// 处理post提交参数
app.post('/login',(req,res)=>{
    let data = req.body;
    // console.log(data);
    if(data.username == 'admin' && data.password == '123'){
        res.send('success');
    }else{
        res.send('failure');
    }
});

app.listen(3000,()=>{
    console.log('running...');
});
```



## 路由



```js
// 单独的路由文件
const express = require('express');
const router = express.Router();
router.get('index',(req,resp)=>{
    resp.send("success");
})
module.exports = router;

// 其他需要使用路由的文件
const express = require('express');
const app = express();
// 引入body-parser,必须在app.use(router)之前,否则会有问题
const bodyParser = require('body-parser');
// parse application/x-www-form-urlencoded
app.use(bodyParser.urlEncoded({extends:false}));
// parse application/json
app.use(bodyParser.json());
// 引入路由文件,根据实际情况修改路由文件的路径
const router=require("./router");
app.use(router)
```



# Koa



* [官网](https://koa.bootcss.com/),由Express原班人马打造的框架,更小,更轻量级



# Art-template



* 一个模板引擎,可以单独使用,也可以配合express-art-template使用



## Express-art-template



```js
let express = require("express");
let app = express();
// 配置使用art-template模板引擎
// 第一个参数指定渲染以.html结尾的文件时,使用art-template模板引擎.html可修改为其他后缀
app.engine('html',require('express-art-template'));
// 指定运行时的参数,该参数需参考官网修改
app.set('view options',{
	debug: process.env.NODE_ENV !== 'production'
});

app.get('/login',(req,res)=>{
    // render()默认是不可以使用的,但是如果配置了模板引擎就可以使用
    // 参数:模板路径;模板数据
    // 注意:模板路径不可以写绝对路径,且默认在当前运行的js文件同级目录的views目录中查找
    // 以下会渲染JS同级目录的views/login.html
	res.render('login.html',{
        // 在模板中使用数据时,可参考官网
		user:{
			name:'admin',
			tags:['art','template','nodejs']
		}
	})
});

// 修改默认渲染的文件根路径,若不设置,默认为views目录
// 参数:views为固定写法,之修改的运行参数名;渲染目录的根目录
app.set("views","/app");
```



## 表达式语法



```js
// 数据
{
    user:"admin",
    userinfo:{
		age:18,
		sex:"m"
	},
	roles:[
		{roleName:"role1"},
		{roleName:"role2"}
	]
}
// 单属性和对象可直接使用
{{user}}
{{userinfo.age}}
// 列表使用each,中间是空格
{{ each roles }}
// 列表中的每一项用$value
{{$value.roleName}}
{{ /each }}
```



## Include



* 包含组件,相当于将将其他html嵌入到使用该标签的位置



```html
<!DOCTYPE html>
<html>
<head>
	<title>header.html</title>
</head>
<body>
    <h1>header部分</h1>
</body>
</html>
```



```html
<!DOCTYPE html>
<html>
<head>
	<title>index.html</title>
</head>
<body>
    <!-- 引入header.html,并渲染在当前位置 -->
	{{ include './header.html'}}
</body>
</html>
```



## Block,Extend



```html
<!DOCTYPE html>
<html>
<head>
	<title>block.html</title>
</head>
<body>
    <!-- block相当于占位符,其他模块可以继承当前html,只修改block部分的内容 -->
    <!-- 父类包含公共内容,子类个性化内容,html中,head中都可以使用 -->
	{{ block 'content'}}
    <h1>默认内容,可被继承的html覆盖</h1>
	{{ /block }}
</body>
</html>
```



```html
// 继承block.html,如果什么都不改,内容相当于block.html
{{ extend 'block.html' }}
// 重写block中content的内容
<div>
    this is the personal content
</div>
```





# MySQL



* mysql模块,使用node操作mysql,也可以使用mongdb



```js
const mysql = require("mysql");

// 创建连接
const connection = mysql.createConnection({
    // 数据库名,不需要设置driver
	database:"test",
	host:"localhost",
	user:"root",
	password:"123456"
});

// 建立连接
connection.connect();

// 执行数据库操作
connection.query("select * from user",(error,results,fields)=>{
    if(error){
        throw error;
    }
    console.log(results);
    console.log(fields);
})

// 关闭连接
connection.end();
```



# Promise



* 一个容器,容器中存放一个异步任务.本身是同步的,但是任务是异步的
* 主要是用来处理回调函数,只有成功和失败两种状态,且处理回调时只会是其中一种状态
* ES6和Node中都有Promise,作用基本相同



```js
const express = require("express");
const app = express();

// 参数:成功的回调函数,失败的回调函数
const ret = new Promise((resolve,reject)=>{
    app.use("login",(req,resp)=>{
        let flag = true;
        if(req.method === "GET"){
            // 处理成功的函数,将数据传递给下一个方法
            resolve("成功的数据");
        }else{
            // 处理失败的方法
            reject("失败的数据或异常");
        }
    })
});

// then()就是接收resolve和reject的方法.可以进行链式调用
// 参数:第一个方法接收reslove,参数是resolve的数据;error就是reject的数据
ret.then((data)=>{
    // 再次返回一个Promise,error处也同样可以再次返回一个Promise
    // return new Promise();
    return 123;
},(error)=>{
    return 456;
})
// 可以继续链式调用,同前2个方法是一样的,第一个接收的上一个then中成功的数据,第2个接收上一个失败的数据
.then((data1)=>{
    // 此处的data1就是上一个then返回的123
    // 上一个then中成功的返回值可以仍然是一个Promise实例
},(error)=>{
    // 此处的error是上一个then返回的456
})
```



# Node爬虫



* 使用Express,Request,Cheerio3个模块,公用作用即可



# Loopback



* 一种比较健全的搭建用户系统的框架,包含鉴权,发邮件等功能
