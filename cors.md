# What is CORS?

서버의 리소스는 종종 다른 서버의 리소스를 요청한다.
이럴 때 서버의 보안 정책을 통헤 다른 서버에서 호스팅되는 자산 요청과 관련된 위험을 줄여 준다.

## Same-origin
먼저, same-origin 이라는 보안 정책을 알아 보자.  
동일 출처 정책은 문서나 스크립트가 다른 출처의 리소스와 통신하는 것을 제한하는 중요한 보안 방식이다.

same-origin 정책은 매우 제한적이다.  

서버 A에서 호스팅되는 문서(예: 웹 페이지)는 서버 A에 있는 다른 문서에만 상호 작용할 수 있다.

보안 정책이 없으면 위험 할 수 있지만 Same-origin 보안 정책은 너무 제한적이다. 

예를 들어 http://www.example.com/doc.html이 same-origin policy로 다른 리소스를 요청한다고 해보자.
- http://www.example.com/dir/page.html를 target으로 한다면 프로토콜, 도메인, 포트가 동일하므로 허용이 된다.  

    - 그러나 https://처럼 프로토콜이 다르거나,
    - www.abcd.com 처럼 도메인이 다르거나,
    - www.example.com:81 처럼 포트가 다를 경우에는 제한이 된다.

그러면 다른 서버에 있는 리소스를 요청할 수 있는 방법이 있을까?
### 그래서 사용 하는 것이 CORS

# Cross Origin Resource Sharing (CORS)
- origin 밖에 있는 곳에서 리소스 요청 하는 것을 cross-origin 요청이라고 한다.

cross-origin 요청을 할 때, request에 Origin header를 추가해서 보내면, response에서 Access-Control-Allow-Origin header를 보내줌으로써 서로간의 요청을 허용해주는 방식이다.

예를 들어, http://test.example.com 에서 http://test.target.com 으로의 엑세스를 시도한다면,  
Origin: http://test.target.com   
이라는 헤더를 실어 request를 보냈을 때, Access-Control-Allow-Origin: http://test.example.com 이라는 헤더와 함께 response를 보내게 된다.