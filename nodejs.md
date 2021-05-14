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

# URl을 통해서 입력된 값 사용하기

express 없이 라우터 구성

query data id 값을 변경함으로 url을 변경할 수 있음
response.end("string") 
=> string 을 browser에 보냄

```js
var http = require('http');
var fs = require('fs');
var url = require('url');


var app = http.createServer(function(request,response){
    var _url = request.url;
    var queryData = url.parse(_url, true).query;
    // queryData = { id : 'HTML' }
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


# app 동적 웹페이지 만들기


아래와 같이 templete을 페이지로 보낼 수 있음,

<a href="/?id=HRML">

=> https://localhost:9000/HTML 이런식으로 URL이 나옴

```js
var http = require('http');
var fs = require('fs');
var url = require('url');


var app = http.createServer(function(request,response){
    var _url = request.url;
    var queryData = url.parse(_url, true).query;
    // queryData = { id : 'HTML' }
    if(request.url == '/'){
      url = '/index.html';
    }
    if(request.url == '/favicon.ico'){
      return response.writeHead(404);
    }
    response.writeHead(200);
    var tenplate = `
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <link rel="stylesheet" href="CSS/styles.css">
    <title>index Cocoa Talk</title>
</head>

<body>
    <div class="status-bar">
        <div class="status-bar__column">
            <span>No Service</span>
            <i class="fas fa-wifi"></i>
        </div>
        <div class="status-bar__column">
            <span>18:43</span>
        </div>
        <div class="status-bar__column">
            <span>110%</span>
            <i class="fas fa-battery-full fa-lg"></i>
            <i class="fas fa-bolt"></i>
        </div>
        <!-- fa-lg는 fontawsome에서도 안 나옴 fa-1x, fa-2x, fa-lg같은 추가 속성으로 사이즈 조절 -->
    </div>
    <header class="welcome-header">
        <h1 class="welcome-header__title">Welcom to CocoaTalk</h1>
        <p class="welcome-header__text">if you have a Cocoa Account log in with your email or phone number</p>
    </header>
    <form action="freinds.html" method="GET" id="login-form">
        <input name="username" type="text" required="please Enater a ID" placeholder="Email or phone number">
        <input name="password" type="text" required="Please Enater a Password" placeholder="Password">
        <input type="submit" value="Log In">
        <a href="#"> Find Cocoa Account or Password</a>
    </form>

    <div id="no-mobile">
        <span>
            your screen is too big
        </span>
    </div>

    <script src="https://kit.fontawesome.com/64854b007a.js" crossorigin="anonymous">
    </script>
</body>

</html>
    `
    response.end(template);
});
app.listen(3000);
```

# 파일 읽기 기능







