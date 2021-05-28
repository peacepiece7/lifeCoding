# Express Session Auth

만들어 보는 건 좋지만 

보안에 인생을 갈아 넣을 생각이 없다면, 전문가가 만들어 준 session auth을 사용하자

[express-session github page](https://github.com/expressjs/session)


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




