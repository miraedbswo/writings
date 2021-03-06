# Introduction
토큰 기반 인증은 모던한 웹 서비스에서 정말 많이 사용 됩니다.  

API를 사용하는 웹 서비스에서, Token을 사용한다면 쉽게 인증 작업을 처리 할 수 있을 것입니다.  
Json Web Token을 설명하기 이전에, Token이란 무엇인가, 사용하는 주요 이유들을 설명 드리겠습니다.
## Stateless

먼저 Stateful에 대해 알아봅시다.  
Stateful 서버는 클라이언트에게서 요청을 받을 때 마다, 클라이언트의 상태를 계속해서 유지하고, 이 정보를 서비스 제공에 이용합니다.   

유저가 로그인을 할 때, stateful 상태가 되는 쿠키랑 세션을 예로 들어보겠습니다. 쿠키를 사용하면 로그인 한 정보를 쿠키에 담아 클라이언트에서 저장 하면서 다음의 각 request마다 cookie를 주는 형식이고, 세션은 정보들을 서버 측에서 저장하면서 요청을 받을 때마다, 전의 클라이언트의 상태를 유지시킵니다.   

자, 그러면 Stateless 란 클라이언트의 상태를 서버에 저장하지 않습니다. 상태정보를 저장하지 않으면, 서버는 클라이언트측에서 들어오는 요청만으로만 작업을 처리할 수 있습니다.

## 모바일 환경에서 적합하다.
만약 Androld/iOS 환경에서 쿠키같은 인증 시스템을 사용하려면 쿠키 컨테이너 등의 기술을 사용해야 하므로 이상적이지 않습니다. 토큰을 사용한다면 이 문제를 쉽게 해결할 수 있습니다.

## 그 외
- 인증 정보를 다른 어플리케이션으로 전달 가능
- 보안을 조금 높일 수 있다.

# 왜 토큰을 사용하는가 ?
## 서버 기반 인증의 문제점
- ### 세션  
 
 세션을 사용 할 때에는, 인증을 할 때, 서버에 저장을 해야합니다.  
 대부분의 경우엔 메모리에 이를 저장하는데, 로그인 중인 유저의 수가 늘어난다면 서버의 램의 자리 차지를 하게 되고, 자원을 소비하게 됩니다.   
 이를 피하기 위해서, 세션을 데이터베이스에 시스템에 저장하는 방식도 있지만,   
 이 또한 유저의 수가 많으면 데이터베이스의 성능에 무리를 줄 수 있습니다.

- ### 확장성  

세션을 사용하면 서버를 확장하는것이 어려워집니다.   
여기서 서버의 확장이란, 단순히 서버의 사양을 업그레이드 하는것이 아니라, 더 많은 트래픽을 감당하기 위하여 여러개의 프로세스를 돌리거나, 여러대의 서버 컴퓨터를 추가 하는것을 의미합니다.   
세션을 사용하면서 분산된 시스템을 설계하는건 불가능한것은 아니지만 과정이 매우 복잡해집니다.

- ### CORS (Cross-Origin Resource Sharing)

먼저 CORS란, 처음 전송되는 리소스의 도메인과 다른 도메인으로부터 리소스가 요청될 경우 해당 리소스는 cross-origin http 요청에 의해 요청됩니다.   
cors에 대한 더 자세한 내용은     

https://github.com/miraedbswo/writings/blob/master/cors.md

- 웹 어플리케이션에서 세션을 관리 할 때 자주 사용되는 쿠키는 단일 도메인 및 서브 도메인에서만 작동하도록 설계되어있습니다. 
따라서 쿠키를 여러 도메인에서 관리하는것은 굉장히 번거롭습니다.   
 ------------------------------------------
이러한 이유로 Stateless한 서버를 만들기 위해,   
확장성을 갖춘 서버를 만들기 위해,   
Cross-Origin 요청을 위해.   
많은 사람들은 Token 인증 방식을 택합니다. 

# JWT ( Json Web Token )
## json web token 이란 뭘까 ?
- Json Web Token 은 두 객체에서 JSON을 사용하여 가볍고 자가수용적인(self-contained) 방식으로 정보를 안전성 있게 전달할 수 있다.

### 자가 수용적 ( Self - contained )
JWT 는 필요한 모든 정보를 자체적으로 지니고 있다.   
JWT 시스템에서 발급된 토큰은, 토큰에 대한 기본정보, 전달 할 정보,  
그리고 토큰이 검증됐다는것을 증명해주는 signature 를 포함합니다.

### 쉽게 전달 가능
JWT는 자가 수용적이므로, 손 쉽게 전달할 수 있다.
예를 들어 HTTP 에서는 헤더에 넣어 적용할 수도 있고, URL의 Parameter 로 전달할 수 있다.

## 장점
- 서버에서 json web token을 사용한다면, 서버측에서는 유저의 정보를 세션으로 관리할 필요가 사라진다. 즉 토큰만 확인하면 되니 세션 관리가 필요 없어서 서버 자원을 많이 아낄 수 있습니다.
- 정보가 sign 되어 있기 때문에, 정보가 조작되었는지는 않았는지, 검증할 수 있다.

# JWT 의 생김새
JWT는 ```.```을 기준으로 3가지의 문자열로 되어있다.
- aaaaaa.bbbbbb.cccccc

각 문자열은 어떤 의미인지, 어떤 내용을 포함 하고 있는지 알아보자.

## Header 
Header는 ```.```을 기준으로 맨 처음에 위치하고 있으며, 두 가지의 정보를 지니고 있습니다.
- ### typ
토큰의 타입을 지정합니다. JWT 이죠.
- ### alg
해싱 알고리즘을 지정합니다.  
 해싱 알고리즘으로는 보통 HMAC SHA256 혹은 RSA 가 사용되며, 이 알고리즘은, 토큰을 검증 할 때 사용되는 signature 부분에서 사용됩니다.

 ```py
 header = {"alg":"HS256","typ":"JWT"}
 result = "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9" # example
 ```

## Payload
 Header는 ```.```을 기준으로 중간에 위치하고 있습니다.   

 Payload 부분에는 토큰에 담을 정보가 들어있습니다.    
 여기에 담는 정보의 한 ‘조각’ 을 클레임(claim) 이라고 부르고, 이는 name / value 의 한 쌍으로 이뤄져있습니다.    
 토큰에는 여러개의 클레임 들을 넣을 수 있습니다.

 - 클레임의 종류는 3가지 입니다.
   - ### 등록된 (registered) 클레임,
     - 서비스에서 필요한 정보들이 아닌, 토큰에 대한 정보들을     담기위하여 이름이 이미 정해진 클레임들이다.
   - ### 공개 (public) 클레임,
     - 공개 클레임들은 충돌이 방지된 (collision-resistant) 이름을 가지고 있어야 합니다. 충돌을 방지하기 위해서는, 클레임 이름을 URI 형식으로 짓습니다.
   - ### 비공개 (private) 클레임
     - 양 측간에 (보통 클라이언트 <->서버) 협의하에 사용되는 클레임 이름들입니다   

    ```py
    {
    "iss": "velopert.com",
    "exp": "1485270000000",
    "https://velopert.com/jwt_claims/is_admin": true,
    "userId": "11028373727102",
    "username": "velopert"
    }
    # example
    ``` 

## Signature
JSON Web Token 의 마지막 부분은 signature 입니다.   
이 서명은 헤더의 인코딩값과, 정보의 인코딩값을 합친후 주어진 비밀키로 해쉬를 하여 생성합니다.   

따라서 JWT를 만들기 위해서는 미리 비밀 키를 지정을 하고, 그 비밀키로 해쉬를 하고 Token을 생성합니다.

```py
"eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJ2ZWxvcGVydC5jb20iLCJleHAiOiIxNDg1MjcwMDAwMDAwIiwiaHR0cHM6Ly92ZWxvcGVydC5jb20vand0X2NsYWltcy9pc19hZG1pbiI6dHJ1ZSwidXNlcklkIjoiMTEwMjgzNzM3MjcxMDIiLCJ1c2VybmFtZSI6InZlbG9wZXJ0In0.WE5fMufM0NDSVGJ8cAolXGkyB5RmYwCto1pQwDIqo2w"
# example
```

이 값을  https://jwt.io/ 에 넣고 debug 해보세요. 
- 브라우저 상에서 JWT 토큰을 검증하고 생성 할 수 있게 해주는 디버거 서비스입니다

## Access-Token & Refresh Token
보통 인증에 성공하면 서버는 Access-Token과 Refresh-Token 을 반환합니다. Access-Toekn 과 Refresh-Token의 차이점이 뭘까요 ?
   

최근 뉴스를 보면, 페이스북의 Access Token으로 인한 해킹 피해가 화제가 되기도 했습니다. IT(정보기술) 업계에 따르면 엑세스 토큰은 페이스북 계정의 보안 권한을 제3자에게 넘겨준다는 암호문 형태의 '증서'다.

Access-Token은 성공적으로 로그인 했음을 증명하는 암호문 형태의 증서입니다. 그래서 이 액세스 토큰을 이용해서 많은 일들을 할 수 있습니다.

이로 인한 피해를 최소화 하기 위해 Access-Token의 인증 기한을 최소화하는 방식을 사용합니다. Access-Token이 짧다면, 시간이 지날 때마다 계속해서 로그인을 해 줘야 할까요 ?  

아닙니다.   

이를 방지하기 위해서 Refresh-Token을 사용합니다.
Access Token이 만료됐으나 Refresh token이 만료되지 않은 경우, Access Token을 갱신하여 유효하게 하는 역할을 합니다.
대신에 Refresh-Token의 만료 기한을 길게 해 주는 방식을 사용합니다.

만약 Refresh-Token이 유출 된다면 어떨까요? 
만약 리프레시 토큰이 노출되더라도 이는 쓸모가 없습니다.  
 왜냐하면 액세스 토큰을 얻기 위해 해커는 클라이언트 id와 secret이 추가로 필요하기 때문입니다.

# JWT 사용 방법
서버 측에서 로그인을 했을 때 토큰 값이 "aaa"인 토큰을 을 발행한다고 합시다.
클라이언트는 user Authorization이 필요한 api를 사용할 때, 
Header로 
- Authorization: Basic aaa,   
- Authorization: Bearer aaa
- 또는 JWT: aaa

## 가상 이상적인 Authorization header
The best HTTP header for your client to send an access token (JWT or any other token) is the Authorization header with the Bearer authentication scheme.

- JWT나 다른 Token을 사용할 때 access token을 보내기 위해 사용하는 가장 이상적인 HTTP header는 Bearer authentication 스키마입니다.

This scheme is described by the RFC6750.

- 이 스키마는 RFC6750에 상세 설명 되어 있다.

Unlike the custom JWT scheme you mention in your question, the Bearer one is registered at the IANA.

- 당신이 질문에서 언급했던 JWT scheme과 달리, Bearer는 IANA에 등록되어 있다.

Concerning the Basic and Digest authentication schemes, they are dedicated to authentication using a username and a secret (see RFC7616 and RFC7617) so not applicable in that context

- Basic과 Digest 인증 스키마들을 고려하자면, 사용자 이름과 secret(RFC7616 및 RFC7617 참조)을 사용하는 인증에만 사용됩니다. 따라서 이 context와는 알맞지 않습니다.



