# 개인화

**"웹 브라우져의 개인화" = 쿠키**

장바구니, 로그인 유지

웹 브라우저는 웹 서버로 "cookie"를 보냄으로 사용자를 식별할 수 있게 됨

<br>
<br>
<br>

# 실습 준비

nodeJs로 실습

[여기서 다운받기](https://github.com/web-n/Nodejs)

<br>
<br>
<br>

# 쿠키의 생성 (Crud)

**cookie는 http protocol에서 제공하는 기능**

[MDN cookie](https://developer.mozilla.org/ko/docs/Web/HTTP/Cookies)

cookie.js 파일 생성 아래와 같이 작성

```js
// cookie.js

var http = require('http');
http.createServer(function(request, response) {
  response.writeHead(200, {
    'Set-Cookie: ['Yummycookie = choco', 'Tastycookie = strowberry']
  });
  response.end("Cookie!!")
}).listen(3000);
```

🍗🍖 express, babel을 쓴다면 아래와 같을 듯 이 부분은 나중에 직접 실습해보자!! 🍗🍖

```js
import express from "express";

const app = express()

app.use('/', (req,res) => {
  res.setHeader("Set-Cookie" : ["yummy_cookie=choco, tasty_cookie=strawberry"]);
  res.send("cookie!!")
  })
  
app.listen(3000)
```

<br>
<br>

### Developer Tool 

위의 코드를 실행한 후
inspect에 들어가서 network tap을 보면 

Response Header 에 아래와 같이 쿠기가 생성됨

(server에서 browser에게 주는 데이터, browser의 응답)
```
Cookie : yummy_cookie=choco
Cookie : tasty_cookie=strawbery
```

weiteHead를 지우고 다시 실행하면 request Header 에 아래와 같이 쿠키가 생성됨

(browser에서 server에게 요청하는 데이터, browser의 요청)
```
Cookie : yummy_cookie=choco
Cookie : tasty_cookie=strawbery
```

> 1. server에서 (root URL) browser로 cookie를 보냈고 browser가 이를 저장 (응답, response)
> 2, browser가 root URL에서 실행될 때, 저장한 쿠키를 server로 보냄, 요청함 (요청, request)
> 3. browser에 저장된 cookie를 지운다면?
>  > browser가 server에 쿠키를 요청하지 않음
> 4. server에서 cookie를 지우면?
>  > server가 cookie를 응답하지 않고 browser가 저장한 쿠기가 있다면 요청하지만, 없다면 요청하지 않음

<br>
<br>
<br>

# 쿠키 읽기 (cRud)

`npm install cookie` 하고 아래와 같이 작성하여 cookie를 객체나 메서드로 호출할 수 있음

parser는 undifinded일 경우 에러가 나니까 if문으로 작성

```js
var http = require('http');
var cookie = require('cookie');
http.createServer(function(request, response){
    console.log(request.headers.cookie);
    var cookies = {};
    if(request.headers.cookie !== undefined){
        cookies = cookie.parse(request.headers.cookie);
    }
    console.log(cookies.yummy_cookie);
    response.writeHead(200, {
        'Set-Cookie':['yummy_cookie=choco', 'tasty_cookie=strawberry']
    });
    response.end('Cookie!!');
}).listen(3000);
```



<br>
<br>
<br>

# 쿠키의 활용

(MDN HTTP cookies)[https://developer.mozilla.org/ko/docs/Web/HTTP/Cookies]

<br>
<br>

### en-US cookie <-> KO cookie

> MDN에서 en-US로 언어를 변경하면 영어로 번역됨
>  > inspect -> app -> stroage -> cookies에 **en-US cookie**가 저장되어 있슴
> MDN에서 KO로 언어를 변경하면 한국어로 번역됨
>  > inspect -> app -> stroage -> cookies에 **KO cookie**가 저장되면서

<br>
<br>

### user_session, 보안이 중요한 이유

MDN에 로그인 했을 떄  

`user_session : adjzoxcji123ioasd...`이런 값(value)이 생성 DB에 일치하는 value가 있다면 로그인 됨

(비밀번호, 아아디는 쿠키에 저장하지 않음)

![로구인후 쿠키](https://user-images.githubusercontent.com/73880776/119075004-024ea280-ba2b-11eb-8b9b-581616a004b1.GIF)

만약 아래와 같이 logout된 상태에서 user_session : { value : adjzoxcji123ioasd... }를 만들어 그대로 입력한다면?   

**로그인이 된다**

만약 user_session이 노출된다면 큰 보안사고로 이어진다

![로그인전 쿠키](https://user-images.githubusercontent.com/73880776/119075206-607b8580-ba2b-11eb-845c-dbef245a97e8.GIF)

<br>
<br>
<br>

# Permanent Cookie & Session Cookie


**permanenet cookie**
- language change
- 껏다 켜도 설정이 유지
- session cookie에 Expires, Max-Age 값을 설정해주면 permancent cookie가 됨

**Session Cookie**
- login 
- 브라우저를 종료하면 쿠키가 사라짐

> 매번 request로 요청을 보내야 함 -> templete change
> seesion cookie -> login(일회성)
> permanent cookie -> lnaguage change(translator), cart list



```js
var http = require('http');
var cookie = require('cookie');
http.createServer(function(request, response){
    console.log(request.headers.cookie);
    var cookies = {};
    if(request.headers.cookie !== undefined){
        cookies = cookie.parse(request.headers.cookie);
    }
    console.log(cookies.yummy_cookie);
    response.writeHead(200, {
        'Set-Cookie':[
            // session cookie
            'yummy_cookie=choco',
            'tasty_cookie=strawberry',
            // Permanent & Max-Age
            `Permanent=cookies; Max-Age=${60*60*24*30}`
        ]
    });
    response.end('Cookie!!');
}).listen(3000);
```

+ express 쓸 때, nodejs 보면
res.setHeader
res.write(...) 로 Set-Cookie할 수 있음 이건 직접 해보자

<br>
<br>
<br>

# 쿠키 옵션 - Secure & HttpOnly

**Secure** : https에서만 cookie를 response함 ( https가 아니면 inspect에 cookie가 없음  

**HttpOnly** : http을 통해서만 cookie를 request함 ( inspect에 cookie가 있음 )

```js
// cookie.js

var http = require('http');
var cookie = require('cookie');
http.createServer(function(request, response){
    console.log(request.headers.cookie);
    var cookies = {};
    if(request.headers.cookie !== undefined){
        cookies = cookie.parse(request.headers.cookie);
    }
    console.log(cookies.yummy_cookie);
    response.writeHead(200, {
        'Set-Cookie':[
            'yummy_cookie=choco', 
            'tasty_cookie=strawberry',
            `Permanent=cookies; Max-Age=${60*60*24*30}`,
            // Secure 
            'Secure=Secure; Secure',
            // HttpOnly
            'HttpOnly=HttpOnly; HttpOnly'
        ]
    });
    response.end('Cookie!!');
}).listen(3000);
```

<br>
<br>
<br>

# 쿠키 옵션 path, & domain

<br>
<br>

### path 옵션

path 옵션은 해당 디렉터리 + 하위 디렉터리에서 쿠키를 request하는 옵션

writeHead에 `path=path; path=/cookie` 입력
(inspect->network tap확인)

> 1. http:localhost:3000/로 접속
>  >  "path" cookie는 없음
> 2. http:localhost:3000/cookie로 접속
>  >  "path" cookie가 있음
> 3. http:localhost:3000/cookie/api로 접속
>  >  "path" cookie가 있음
 
<br>
<br>
 
## domain 옵션 
 
domain 옵션은 해당 root domain이 어디까지 인지를 의미함

writeHead에 `domain=domain; domain=o2.org` 입력

> 1. o2.org:3000 으로 접속
>   >  디렉토리가 지정이 안된 쿠기들 ( path : / )은 모두 살아 있고 o2.org라고 표시
>   >  domain cookie는 .o2.org라고 표시됨
> 2. add.o2.org:3000 으로 접속
>   > domain cookie 하나만 살아 있음
>   > .o2.org앞에 어떤 문자가 오면 이걸 **sub domain**이라 부름

```js
var http = require('http');
var cookie = require('cookie');
http.createServer(function(request, response){
    console.log(request.headers.cookie);
    var cookies = {};
    if(request.headers.cookie !== undefined){
        cookies = cookie.parse(request.headers.cookie);
    }
    console.log(cookies.yummy_cookie);
    response.writeHead(200, {
        'Set-Cookie':[
            'yummy_cookie=choco', 
            'tasty_cookie=strawberry',
            `Permanent=cookies; Max-Age=${60*60*24*30}`,
            'Secure=Secure; Secure',
            'HttpOnly=HttpOnly; HttpOnly',
            // path option
            // alive cookie in root & child root
            'Path=Path; Path=/cookie',
            // Domain option
            // Specify root domain
            'Domain=Domain; Domain=test.o2.org'
        ]
    });
    response.end('Cookie!!');
}).listen(3000);
```






