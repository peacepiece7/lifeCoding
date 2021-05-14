# http

http (hyper text transper protocol)

web breower, web server(client)가 content( image, html, video, file,,, )등을 주고 받기위한 통신 규칙

<br>
<br>

# http intro

### http 

request response 공통의 약속

node js 에서 request, response로 보내는, 받는 정보가 http header랑 같은 것 같음
정보를 보낼 땐 post 받을 떈 get 메서드를 사용
```pug
form(action="/" method="post")
  input(type="text" name="id" placeholder="enter a text")
```
```js
const export router = (req, res) => {
  console.log(req)
  res.render("main.pug")
}
```
<br>
<br>

browser에서는 아래와 같이 실행하여 request, response header를 볼 수 있음
```
inspect -> network tap를 켬
request headers -> view source
(browser가 client(server)에게 요청한 정보를 볼 수 있음)
response header -< view source
(client가 browser(server)에게 응답한 정보를 볼 수 있음)
```

<br>
<br>

# Request Message


[request, response 개념 vlog](https://velog.io/@rosewwross/Http-and-Request-and-Response-hok6exbnfb)

[requst, respone 상세내용 vlog](https://goddaehee.tistory.com/169)

[mdn http header ](https://developer.mozilla.org/ko/docs/Web/HTTP/Headers)에 들어가면 정말 잘 나와있다.

![image](https://user-images.githubusercontent.com/73880776/118213074-34e72100-b4a8-11eb-803d-fe3cfa893f83.png)

받아와야할 정보는 = body에 blank로 띄워서 적어 줌

GET /1.html HTTP/1.1
- GET : 주소에 입력하는 방식 메서드, post는 헤더에 데이터를 실어서
- /1.html : 이름
- HTTP/1.1 : HTTP 버전

Host : localhost: 8000
- 서버의 도메인 네임
- 웹사이트에 주소를 적는 것과 같음
- 하나의 서버가 여러게의 호스트를 운영한 경우 주소에 따라 다른 경로로 보낼 수 있음( 가상 호스트 ) 
- 8000 : port 번호



user-agent ; mozilla/5.0 ... Mac ...
- 사용하고 있는 브라우저, os 정보를 보여줌
- 통계를 낼 때 사용하기 좋다.
- browser를 보고 검색 robot을 차단할 수 있음

accept
- 클라이언트가 허용할 수 있는 파일 형식(MIME TYPE)
- accept : text/html, application/xhtml+xml,image/webp, */*;q-0.8
  - 와일드 카드로 텍스트면ok 라고 요청할 수도 있고(text/*), 콤마로 여러 타입 지정할 수있다.
  - generic에 content를 보면 accept를 요청했을 때 browser가 accept한 content가 나온다.
- accept-language : ko-KR;q=0, en-US;q=0.8, 
- accept-Encoding : gzip, deflaste, br
  - 브라우저가 할 수 있는 압축방식, 세 가지중 사용가능한 하나를 사용

if-modified-since : tue, 16 jul ...
- 언제 다운로드 했는지 보여 줌, 요청할 때마다 파일들을 다운받지 않도록 웹서버에게 알려 줌

Location
- 300번대 응답이나 201 Created 응답일 때 어느 페이지로 이동할지를 알려주는 헤더

Content-Security-Policy
- 외부 스를 불러올 때 차단, 허용 소스를 명시한다 (iframe, font, js,css)
- Content-Security-Policy: default-src 'self' (자신의 도메인의 파일들만 가져올 수 있다)
- Content-Security-Policy: default-src https: (https를 통해서만 파일을 가져올 수 있다)
- Content-Security-Policy: default-src 'none' (못가져 오게 만듬)
- 정책은 정말 다양하게 설정할 수 있음 (이거 보면 ptsd옴) 


# response message

![image (1)](https://user-images.githubusercontent.com/73880776/118214432-a58f3d00-b4aa-11eb-9247-ba401319d345.png)

### 주요 status codes 

```js
1xx : Information,
2xx : Success,
3xx : Redirect,
4xx : Client Error,
5xx : Server Error,
```
```
200 : ok,
201 : ok created,
301 : move to new URL,
304 : not modified,
400 : Bad request,
401 : unauthorized,
404 : not funnd,
500 : internal server error,
```

response를 보면 웹브라우저가 client에게 받은 정보를 어떻게 이해하고 있는지를 알 수 있다.
