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

cookie는 http protocol에서 제공하는 기능.

[MDN cookie](https://developer.mozilla.org/ko/docs/Web/HTTP/Cookies)

```js
const http : {
              cookie : { auth , check, independent },
            }
```

cookie.js 파일 생성

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

express, babel을 쓴다면 아래와 같을 듯
```js
import express from "express";

const app = express()

app.use('/', (req,res) => {
  res.setHeader("Set-Cookie" : ["yummy_cookie=choco, tasty_cookie=strawberry"]);
  res.send("cookie!!")
  })
  
app.listen(3000)
```

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


추가로

yummy_cookie, tasty..나 ninja는 예약어로 작동하지만 그외 단어는 보안상? 작동하지 않는 듯 하다. 

```js
response.setHeader('Set-Cookie', ['type=ninja', 'language=javascript']);
```
(nodeJs setHeader, writeHead)[https://nodejs.org/dist/latest-v8.x/docs/api/http.html#http_response_setheader_name_value]

<br>
<br>
<br>

# 쿠키 읽기 (cRud)

npm cookie-parser, cookie 도 있고 how to read cookie in nodejs검색 stackoverflow에서 찾을 수도 있고 다양함

아래와 같이 작성하여 cookie를 객체나 메서드로 호출할 수 있음
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

parser는 undifinded일 경우 에러가 나니까 위와 같이 if문을 작성

# 쿠키의 활용


(MDN HTTP cookies)[https://developer.mozilla.org/ko/docs/Web/HTTP/Cookies]

> MDN에서 en-US로 언어를 변경하고 다시 키면, inspect -> app -> stroage -> cookies에 en-US가 저장되면서 영어로 번역됨
> 다시 MDN에서 KO로 언어를 변경하고 다시 키면, inspect -> app -> stroage -> cookies에 KO가 저장되면서 한국어로 번역됨

MDN에 로그인 했을 떄, 
session id 생성 (adjzoxcji123ioasd)이런 해시 값

아래를 보면 로그인 후 user_session : { value : SKxkzc12ekasd... } 이런 값이 있고 DB에 일치하는 id가 있다면 로그인 됨

(비밀번호, 아아디는 쿠키에 저장하지 않음)

![로구인후 쿠키](https://user-images.githubusercontent.com/73880776/119075004-024ea280-ba2b-11eb-8b9b-581616a004b1.GIF)

만약 아래와 같이 logout된 상태에서 user_session : { value : SKxkzc12ekasd... }를 그대로 입력한다면? 
로그인이 된다...
만약 session id가 노출된다면 큰 보안사고로 이어진다..

![로그인전 쿠키](https://user-images.githubusercontent.com/73880776/119075206-607b8580-ba2b-11eb-845c-dbef245a97e8.GIF)




