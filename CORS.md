# SOP (Same Origin Policy)

- 동일 출처 정책
- SOP에 위반될 경우 접근을 제한함 (Cross Origin인 경우 접근을 제한, error)
- URL의 Protocol, Host, Port가 같은 경우 SOP에 위반되지 않는다 

### URL의 protocol, host, pot

https://github.com:443/abc?tab=repositortues#example

[protocol]://[Host]:[Port]/[Path]?[Query String]#[Fragment]


### https://localhost 랑 같은 url은? 

https://localhost   => protocol이 다름 ❌

htts://localhost:80 => :80은 기본포트 ⭕

http://127:0.0.1    => 같은 주소지만, browser는 string으로 비교하기 때문에 다르다고 판단함 ❌

http://localhost/api/cors => path는 다르지만 Protocol, Host, Port는 같음 ⭕

<br>
<br>
<br>

# 다른 출처의 자원을 사용하기 위해서는?
# CORS (Cross Origin Resource Sharing)

- 교차 출처 자원 공유
- HTTP 헤더를 사용해서 **실행중인 출처**에서 다른 출처에 접근할 수 있도록 **브라우저**에 알려주는 체제
- 브라우저에게 알려주는 거임

<br>
<br>
<br>

# CORS의 종류

- 사전 요청, 프리플라이트 요청 (Preflight Request)
- 단순 요청 (Simple Request)
- 인증정보 포함 요청 (Credentailed Request)

<br>
<br>
<br>

# Preflight Request

1. OPTIONS 메서드를 통해 다른 도메인의 리소스에 요청이 가능한 지 확인 작업 (GET, POST같은 메서드), (preflight라고 fetch란에 적힘)
- Actual Request(실체 요청)이 있기 전에 사전에 보내는 요청
2. 요청이 가능하면(res state 200) Actual request를 보냄, 그래서 처음 보내는 request가 두 개 임


### Preflight Request 구성

- Origin : 요청 출처
- Access-Control-Request-Method : 실제 요청의 메서드
- Access-Control-Request-Headers: 실제 요청의 추가 헤더

> REQUEST (Method : OPTIONS)
```
OPTIONS /doc HTTP/1.1
Origin : http://foo.example
Access-Control-Request-Method : POST
Access-Control-Request-Headers: X-PINGOTHER, Content-Type  
```

### Preflight Response 구성

- Access-Control-Allow-Origin : 서버 측 허가 출처
- Access-Control-Allow-Methods : 서버 측 허가 메서드
- Access-Control-Allow-Headers : 서버 측 허가 헤더
- Access-Control-Max-Age : Prefilght 응답 캐시 시간

> RESPONSE 

```
- Access-Control-Allow-Origin : http://foo.example
- Access-Control-Allow-Methods : POST, GET, OPTIONS
- Access-Control-Allow-Headers : X-PINGOTHER, Content-Type
- Access-Control-Max-Age : 86400
```
Access-Control-Max-Age : 86400 => 매 번 request마다 Preflight을 보내면 자원 낭비, 응답 캐시 확인 -> 바로 Actual Request를 보넴 

<br>
<br>
<br>

# Simple Request 

Preflight없이 바로 요청을 보냄

> 아래의 조건을 모두 만족해야 한다.
- GET, POST, HEAD 메서드 중 하나
- Content-type 아래 중 하나
  - application/x-www-form-urlencoded
  - mulipart/form-data
  - text/plain
- Header는 Accept, Aceept-language, Content-Language, Contnet-type만 허용

### Simpe Request Flow

```
Client -> (Get /doc HTTP/1.1, Origin: foo.example) -> Server
Client <- (HTTP/1.1 200 OK, Access-Control-Allow-Origin : *) <- Server
```
* : 와일드 카드 모두 허용

<br>
<br>
<br>

# Preflight가 왜 필요할까?

만약 Client가 Delete 명령을 내린다면

### Actual Request 요청만 보냄
아래 흐름에서 봤을 떄, 서버는 Delete명령을 수행하고 -> ❌CORS ERROR❌를 보냄

```
Client -> request(origin : foo.example) -> Browser -> request(origin : foo.example) -> Server -> CORS설정이 없음
Client <- ❌       CORS ERROR       ❌ <- Browser <- response(Allow-Origin : 없음) <- Server <- CORS설정이 없음
```

### Preflight 요청을 먼저 보내고 -> Actaul Request를 보낼 경우

```

Client -> preflight request(origin : foo.example) -> Browser -> preflight request(origin : foo.example) -> Server -> CORS설정이 없음
Client <-      ❌       CORS ERROR       ❌      <-  Browser <-       response(Allow-Origin : 없음)     <- Server <- CORS설정이 없음
```

CORS ERROR가 발생했지만 서버에는 Delete 명령을 수행하지 않음

<br>
<br>
<br>

# Credentailed Request

cookie, jwt token 등 인증을 서버로 보낼 떄 사용

서버에서 아래처럼 헤더를 response로 보내줘야, browser에서 에러가 안남
```
Access-Control-Allow-Credentials : true, 
Acces-Control-Allow-Origin : foo.example (정확한 origin을 기제해야 함)
```


# CORS 해결하기

1. 프론트 프록시 서버 설정 (개발 환경)
2. 직접 헤더에 설정
3. 스프링 부트를 이용

### 프론트 프록시 서버 설정

CORS에러는 브라우저에서 일어나니까 프론트에서 백엔드로 요청을 보낼떄, 출처를 변경함으로 CORS를 막을 수 있음 

```
Browser -> origin : localhost:8081 -> Front-end (npm run..) -> if(/api){ localhots :8080 } -> origin : localhost:8081 -> backend 
           target : localhost:8081                                                            target : localhost:8080
```

> 아래 해결 방법은 아직 java를 할 줄 모르니까 다음에..
### 직접 헤더에 설정
### 스프링 부트를 이용
 
