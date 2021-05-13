# Intro

<br>
<br>

User(cumstomer) : logged user  
thier(service provider) : gogle, facebook. kakao talk...  
mine(server) : localhost:xxxx,,,   

- OAUTH

access token을 thier(servie provider)에게 얻는 것이 핵심 목표  


<br>
<br>

# role

<br>
<br>

thier : resource server  
User : resource owner, authorization server ( 데이터를 가지고 있는 서버, 권한 인증을 해주는 서버 )  
thier : client  

<br>
<br>

# register

<br>
<br>

- create application

Client ID : 1
Client Secret : 2
Authorized redirect URls https://github/auth/callback

예시로 github은 아래와 같이 작성

```js
// passport.js

import GithubStrategy from "passport-github";

passport.use(
  new GithubStrategy(
    {
      clientID: process.env.GH_ID,
      clientSecret: process.env.GH_SECRET,
      redirect_uri: `http://localhost:8889${routes.githubCallback}`,
    },
    githubLoginCallback
  )
);
```

<br>

```js
export const githubLoginCallback = async (
  accessToken,
  refreshToken,
  profile,
  cb
) => {
  const {
    _json: { id, avatar_url: avatarUrl, name, email },
  } = profile;
  try {
    const user = await User.findOne({ email });
    if (user) {
      user.githubId = id;
      user.name = name;
      user.avatarUrl = avatarUrl;
      user.save();
      return cb(null, user);
    }
    const newUser = await User.create({
      email,
      name,
      githubId: id,
      avatarUrl,
    });
    return cb(null, newUser);
  } catch (error) {
    return cb(error);
  }
};

// 참고 routes
// routes.js

const GITHUB ="/auth/github";
const GITHUB_CALLBACK = "/auth/github/callback";
```
<br>
<br>

# resource owner의 승인

<br>
<br>


1. create app을 한다.  

<br>

아래와 같은 정보를 client, resouce server가 공유하게 됨
redirect_URL을 client가 구현해 놔야 한다.
```js
let Client : {
  Client_Id : 1,
  Client_Secret : 2
}

let Resouce_Server : {
  Client_Id : 1,
  Client_Secret : 2,
  Redirect_URL : "https://client/github/auth/callback"
}
```

<br>

2. resource owner가 login 동의 인증

<br>

resourve_server에서 id, avatarUrl을 받아올 것임 이를 B,C라 가정
github 로그인 하기 버튼을 클릭, 동의하면 아래의 링크로 resourve owner 정보를 전송 
```
https://resourve.server/?client_id=1&scope=B,C&redirect_url=https://github/auth/callback
```
resouce server는 resourve owner의 ID와 scope : B, C에 동의 했음을 알게 됨 

```js
// resourve server

let Resouce_Server : {
  Client_Id : 1,
  Client_Secret : 2,
  Redirect_URL : "https://client/github/auth/callback",
  User_Id : 1,
  Scope : [B, C]
}
```

<br>

3. resource server의 동의

<br>

임시 비밀번호 authorization code : 3 을 resouce owner에서 보냄
client는 은밀하게 code =3 이라는 주소로 리다이렉트함

```
// redirect address

location : https://client/callback?code=3
```

<br>
 
client는 authorization code = 3을 알게 됨 
네 가지 방식이 더 있다고 함

<br>

```
// client

let Client : {
  Client_Id : 1,
  Client_Secret : 2,
  Authorization_Code : 3
}

```

client는 Authorization_code와 client 정보를 조합해서 resouce server에게 보냄  


resouce server는 Authorization_code와 client_Id, Client_Secret이 일치하는 resouce owner에 대한 access token을 
client에게 발급해줌

<br>

4. access token

<br>

또 다시 인증하지 않게 authorization_code를 지우고 accessToken을 발급  
access token은 user_id : 1에 대한 scope : [B, C]의 접근을 허용함

```js
// client
let Client : {
  Client_Id : 1,
  Client_Secret : 2,
  **AccessToken : 4**
}

// resouce server
let Resouce_Server : {
  Client_Id : 1,
  Client_Secret : 2,
  Redirect_URL : "https://client/github/auth/callback",
  User_Id : 1,
  scope : [B, C],
  **AccessToken : 4**
}
```

<br>
<br>

# API (application program interface)

<br>
<br>

Client가 resource server를 handling하는 방식 => API  
for instance : google platform, AWS,,,

<br>

location : authorization이라 뜨면 access token을 발급 받아야 함 

google calender api로 예로 두 가지 방식이 있음  

<br>

1. URL redirection뒤에 access_token을 입력하는 방식

<br>

```
https://www.googleapis.com/calender/v3/users/me/calenderList?access_token=askzkck12
```

<br>

2. 헤더 값으로 보냄

<br>

curl이라는 프로그램을 사용  (curl은 html을 파싱, 화면에 출력해줌)  
`ex) curl https://opentutorials.org`

<br>

cmd에 아래와 같이 입력하여 api를 호출(실제 access token은 훨~씬 더 김)
```
curl -H "Authorization:Bearer askzkck12" https://www.googleapis.com/calender/v3/users/me/calenderList
```
> node js로 curl를 사용하려면 변환 과정을 거쳐야 함

<br>
<br>

# refresh token

<br>
<br>

- refresh token
access token은 며칠, 몇 달씩 우효 기간이 있음  
다시 발급 받을 때 인증과정을 줄여주는 역할을 함  

[datatracker.ietf.org](https://datatracker.ietf.org/doc/html/rfc6749#section-1.5)
[oauth 2.0](https://oauth.net/2/)

APIs마다 refresh 방법을 제공하고 있음, 위의 문서를 읽고 관련 정보를 보내 주면 된다.

<br>
<br>

# federated identity technology

<br>
<br>

- login with facebook, login with google이 이에 해당  
- oauth의 궁극정 목적은 api를 다루는 것
- 사회적 app을 만들기 위해 oauth는 필수적임
- restful, json, xml 배경 지식은

