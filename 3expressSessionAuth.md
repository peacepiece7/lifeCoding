# Express Session Auth

만들어 보는 건 좋지만 

보안에 인생을 갈아 넣을 생각이 없다면, 전문가가 만들어 준 session auth을 사용하자

[express-session github page](https://github.com/expressjs/session)

<br>
<br>
<br>

# Session object

아래와 같이 작성하여 session 객체를 console.log에 찍어보면
```
import express from "express"
import session from "express-session"


const app = express();

app.use(session({
  secret : "aasSazcA22Sa1sSSsd22d4bng",
  resave : false,
  saveUnitalized : ture
})
);

app.get("/", (req, res, next) => {
  console.log(req.session)
  res.send("Hello, world!");
})
```
아래와 같은 Session 객체가 콘솔에 찍힌다.

```
Session {
  cookie : {
    path : "/",
    expires : null,
    originalMaxAge : null,
    httpOnly : true
    }
}
```

아래처럼 적으면 해당되는 라우터 접속시 값이 1이 증가함
```
if (req.session.num = undifined) {
  req.session.num = 1
}else {
  req.session.num += 1
}
```
<br>
<br>
<br>

# Session Store 

Compatible Session Store 리스트에 보면

각 db마다 plugin하는 방법이 나와 있음

```js
import session from "express-session";
var FileStore = require('session-file-store')(session);
// import FileStore from "sessopm-file-store";

app.use(session({
  secret: 'asadlfkj!@#!@#dfgasdg',
  resave: false,
  saveUninitialized: true,
  store:new FileStore()
}))
```
```js
router.post('/login_process', function (request, response) {
  var post = request.body;
  var email = post.email;
  var password = post.pwd;
  if(email === authData.email && password === authData.password){
    response.send('Welcome!');
    request.session.is_logined = true;
    request.session.nickname = authData.nickname;
    request.session.save(); // async await처럼 session store에 session값 저장을 비동기로 저장 함 
    response.redirect(`/`);
  } else {
    response.send('Who?');
  }
  ```

sessions 폴더에 cookie가 생김!

세션을 db에 저장할 떄, 이런 방식으로 해보고 저장하는 것도 좋을듯 

<br>
<br>
<br>

# 더 공부할 것들

secure : true 설정으로 https에서면 session정보를 이동하도록 만들어 줌, ( session id 보호) 

httpOnly : true 사용자가 `<script>alret(${req.session})</script>` 처럼 textarea에 js코드를 사용할 수 없게 httpOnly설정을 해줘야 함

secret key는 .env 파일에 저장해서 관리하기

```js
app.use(session({
  secure : true,
  httpOnly : true,
  secret: prosess.env.sessionSceret,
  resave: false,
  saveUninitialized: true,
  store:new FileStore()
}))

```
