---
title: node.js 实现简单接口
date: 2019-06-11 20:05:58
categories: 服务器端
tags: 软件开发
thumbnail: https://ss0.bdstatic.com/70cFvHSh_Q1YnxGkpoWK1HF6hhy/it/u=491865045,1211705331&fm=26&gp=0.jpg
---

## 安装 node 并引入 http 模块

```javascript
// 导入http模块:
var http = require('http');
// 创建http server，并传入回调函数:
var server = http.createServer(function (request, response) {
    // 回调函数接收request和response对象,
    // 获得HTTP请求的method和url:
    console.log(request.method + ': ' + request.url);
    // 将HTTP响应200写入response, 同时设置Content-Type: text/html:
    response.writeHead(200, {'Content-Type': 'text/html'});
    // 将HTTP响应的HTML内容写入response:
    response.end('Hello world!');
});

// 让服务器监听8080端口:
server.listen(8080);

console.log('Server is running at http://127.0.0.1:8080/');

```

## 运行 server.js

```bash
node server.js
```

## 打开 http://127.0.0.1:8080/
如出现下图则表示服务已启用成功
<div align=left>![node.png](node.png)

## 路由
在访问根路径时希望拿到当前目录的 index.html, 使用 request.url 进行判断即可。由于需要读取 index.html 文件，所以要用到 fs 读写模块
```
var http= require('http');
var fs= require('fs');
var server= http.createServer(function(request, response){
    var url = request.url;
    if(url === '/'){
        fs.readFile('./index.html', function(err, data){
          if(!err){
            response.writeHead(200, {"Content-Type": "text/html;charset=UTF-8"});
            response.end(data)
          }else{
              throw err;
          }
        });
    }else{
        console.log("错误");
    }
});
server.listen(8080, '127.0.0.1');
```

## 新建 index.html
写一个简单 ajax 请求
```
<div id="box">看不到看不到</div>
<button onclick="success()">我是按钮</button>
<script>
function success(){
    var http= new XMLHttpRequest();
    http.onreadystatechange= function(){
        if(http.status == 200 && http.readyState == 4){
            var msg= http.responseText;
            var box= document.getElementById('box');
            box.innerHTML= JSON.parse(msg).name;
        }
    }
    //发送请求
    http.open('GET', '/data');
    http.send();
}
</script>
```

## 实现一个简单的接口
这里使用 json 文件模拟数据库的读取操作，新建一个 data.json 文件，随意添加一些数据，然后在 server.js 中判断请求 url
```
if(url === '/data'){
    fs.readFile('./data.json', function(err, data){
        if(!err){
            response.writeHead(200, {"Content-Type": "application/json"});
            response.end(data);
        }else{
            throw err;
        }
    })
}
```

## 打开 http://127.0.0.1:8080/
<div align=left>![node2.png](node2.png)

至此，一个简单的接口实现就完成了。