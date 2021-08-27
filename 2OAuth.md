# Intro

User (resource owner = cumstomer) : logged user  
thier (resource server, auth server) : gogle, facebook. kakao talk...  
mine (server = client) : localhost:xxxx,,,   

- OAUTH

### OAth가 없이 service provider에게서 정보를 가져온다면?

- client의 id, password를 server에 저장 -> service provider에 접속해서 데이터를 읽고 씀 
- 신뢰성, 보안성에 문제가 있음 

### OAtuth를 사용한다면

- client가 server한테 ok 사인을 보냄 -> server는 service provider에게 ok사인을 access token으로 교환, 저장
- client가 요청을 보내면 sever는 client의 access token으로 service provider에게 접근할 수 있음


access token을 service provider에게서 얻는 것이 핵심 목표  


<br>
<br>

# role

User : resource server  
thier : resource owner, authorization server ( 데이터를 가지고 있는 서버, 권한 인증을 해주는 서버 )  
mine : client  

<br>
<br>

# register (서비스를 이용하기 위한 사전 등록)

create application

Client ID : 1
Client Secret : 2
Authorized redirect URls https://github/auth/callback

github -> settings -> oauth탭에서
homepageUrl, redirect Url, 이런 저런 거 입력하고 받아옴 

.env에 GIT_CLIENT_ID = 1adczc, GIT_CLIENT_SECRET = ASDJI1 이런식으로 저장함

<br>
<br>

# resource owner의 인증

<br>

1. github 로그인 하기 버튼을 클릭, 동의하면 아래의 링크로 resource owner 정보를 전송  
```
https://accounts.github.com/?client_id=1&scope=B,C&redirect_url=https://github/auth/callback
```

2. gitjub 로그인 하기 버튼 클릭 
여기로 이동
```
https://resource.server/github/auth
```
아래 url를 request하는 코드를 실행 
```
https://accounts.github.com/?client_id=1&scope=B,C&redirect_url=https://github/auth/callback
```

### 로그인이 안 되었다면 

github 로그인 창이 뜸 

### 로그인이 되었다면 -> 허용하시겠습니까 팝업을 띄움

resouce server를 아래의 주소로 접속하게 되고

`https://accounts.github.com/?client_id=1&scope=B,C&redirect_url=https://github/auth/callback`

client_id=1, redurect_url=https://github/auth/callback 이 두가지가 

사전에 client가 app을 등록했을 때 작성하고 받았던 client_id, redirect_url과 일치할 경우

Id, Email 정보를 client에게 허용하겠습니까? 라고 resource owner한테 묻는 팝업을 띄움,

허용시, resource server는 아래와 같이 정보를 저장해두고, client가 요청할 떄 불러올 수 있음

```js
// resource server

client_id = 1,
client_secret = 2,
redirect_url = "https://localhost:3000/github/auth/callback"
{user_id : 123, scope : B, C, authrization_code : 3 } // 추가된 정보

```

### authrization code로 이동하기 

resource server는 로그인 -> 허용 -> 유저정보 저장

authrization_code를 생성, 저장한 다음

resouce owner을 아래와 같이 code :3으로 이동시킴
`https:localhost:3000/github/auth/callback?code=3`

이제, client athurization_code : 3을 알게 됨


### token발급 요청

resouce owner login with github click -> 권한 확인 및 승인 ->resource server는 user의 정보 저장 auth_code 발급 ->
resource owner code로 이동, client는 resource owner의 request를 받고

`https://localhost:3000/token?grant_type=authorization_code&code=3&redirect_url=https://localhost:3000/github/auth/callback&client_id=1&client_secret=2`

이런 주소를 resource server에 전송함 

( client url로 접속하는데 어떻게 resource server에게 알리는지는 모르곘음..?? https://github.com?token?...일 거 같음)

Authorization_code, client_id, user_id, client_secret이 모두 같은 경우 access token을 발급함



<br>


4. access token

<br>

또 다시 인증하지 않게 authorization_code를 지우고 accessToken을 발급 (github이 request로 보내줌)
access token은 user_id : 1에 대한 scope : [B, C]의 접근을 허용함  

<br>
<br>

# API (application program interface)

Client가 resource server를 handling하는 방식 => API   
for instance : google platform, AWS,,,  

<br>

location : authorization이라 뜨면 access token을 발급 받아야 함  
google calender api로 예로 두 가지 방식이 있음  

<br>

1. URL redirection뒤에 access_token을 입력하는 방식  

```
https://www.googleapis.com/calender/v3/users/me/calenderList?access_token=askzkck12
```

<br>

2. 헤더 값으로 보냄  

curl이라는 프로그램을 사용  (curl은 html을 파싱, 화면에 출력해줌)  
`ex) curl https://opentutorials.org`   
cmd에 아래와 같이 입력하여 api를 호출(실제 access token은 훨~씬 더 김)  
```
curl -H "Authorization:Bearer askzkck12" https://www.googleapis.com/calender/v3/users/me/calenderList
```
> node js로 curl를 사용하려면 변환 과정을 거쳐야 함  

<br>
<br>

# refresh token


- refresh token
access token은 며칠, 몇 달씩 우효 기간이 있음   
다시 발급 받을 때 인증과정을 줄여주는 역할을 함  

[datatracker.ietf.org](https://datatracker.ietf.org/doc/html/rfc6749#section-1.5)  
[oauth 2.0](https://oauth.net/2/)  

APIs마다 refresh 방법을 제공하고 있음, 위의 문서를 읽고 관련 정보를 보내 주면 된다.

<br>
<br>

# federated identity technology

- login with facebook, login with google이 이에 해당  
- oauth의 궁극정 목적은 api를 다루는 것  
- 사회적 app을 만들기 위해 oauth는 필수적임  
- restful, json, xml 배경 지식은  

<br>
<br>

# bearer toekn usage

[생활코딩 bearer token usage](https://gist.github.com/egoing/cac3d6c8481062a7e7de327d3709505f)를 참조

