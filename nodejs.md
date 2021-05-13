# 수업의 목적

CRUD (create read update Delete)를 제공하고자 함

node.js의 주 목적은 **사용자의 참여**라고 생각함

<br>
<br>

# node.js application

**node.js runtime** 이라는 app을 다운 받고 
js를 통해 node.js runtime을 제어(실행)함으로 
node.js apllication을 만듬

이는 **web browser**에서 동작함

<br>
<br>

# web bowser <=> web server

web server 는 engin, apache의 기능을 가지고 있음,  

<br>
<br>

express 없이 라우터 구성

```js
var http = require('http');
var fs = require('fs');
var app = http.createServer(function(request,response){
    var url = request.url;
    if(request.url == '/'){
      url = '/index.html';
    }
    if(request.url == '/favicon.ico'){
      response.writeHead(404);
      response.end()
      return
    }
    response.writeHead(200);
    response.end(fs.readFileSync(__dirname + url));
});
app.listen(3000);
```



