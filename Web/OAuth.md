# 인증과 인가

<b> 인증 Authentication </b> <br>
인터넷 환경에서 사용자의 신원을 확인하는 행위   

<b> 인가 Authorization </b> <br>
사용자에게 특정한 자원이나 기능에 접근할 수 있는 ```권한```을 허가하는 행위

### OAuth 1.0
OAuth 1.0 버전에는 세 가지 역할이 존재한다.
- Resource Owner : A 서비스를 통해 사진을 올리는 등 여러 작업을 하는 유저
- OAuth Client : A 서비스의 자원에 접근해 가공하거나 활용하려는 Third-Party Application
- OAuth Server : Resource Owner의 신분을 ```인증```하고 상호작용하여 OAuth Client에게 사용 ```권한을 부여```

<b> 흐름 </b>   
1. OAuth Client는 사용자의 자원을 활용하고 싶다 라는 요청을 OAuth Server에 호출
2. OAuth Server는 OAuth Client에게 인증과 인가를 위한 주소를 응답
3. OAuth Client는 Resource Owner에게 리다이렉션 방식을 통해 Owner를 OAuth Server가 호스팅하는 웹 주소로 보냄
4. Resource Owner는 해당 웹 주소에서 인증과 인가 과정을 거침
5. OAuth Server는 Resource Owner에게 리다이렉션 방식으로 응답하여 인증값을 OAuth Client에 전달
6. OAuth Client는 인증값을 통해 OAuth Server로부터 인가 토큰을 획득
7. OAuth Client는 획득한 토큰으로 자원 / 기능에 접근     

![image](https://github.com/meoldae/TIL/assets/70866410/75191495-a78e-4e23-92a8-941645bd9447)

<b> 문제점 </b>
- Scope 개념의 부재
- Client 측의 복잡한 구현 난이도
- 토큰의 유효기간 문제
- 사용환경이 제한적
- 각각의 역할이 확실하게 나누어지지 않음

### OAuth 2.0
- Scope 개념의 추가   
OAuth 1.0에서는 인가 토큰만 있다면 사용자의 모든 자원에 접근할 수 있었다. OAuth 2.0에서는 Scope 개념이 추가되어 원하는 자원에만 접근할 수 있게 되었다.
- Client 복잡성 간소화   
OAuth 1.0의 보안은 서명을 위해 Method, URI, Parameter, Parameter의 순서 등.. 복잡한 과정이 존재   
OAuth 2.0은 Bearer Token과 TLS(HTTPS)를 통해 보안을 유지
> Bearer Token?    
> Bearer란 [ 소유자 ] 라는 의미로 다른 암호학적 장치들을 일절 배제하고 토큰을 소유하고 있는 것만으로 사용 권한을 부여하는 토큰   
[RFC 6750 표준 명세](https://datatracker.ietf.org/doc/html/rfc6750)

> TLS (Transport Layer Security)   
> 웹 상에서의 정보를 암호화 해서 주고받는 프로토콜
- OAuth Server의 역할 분리
    - OAuth 1.0에서 OAuth Server의 역할
        - Resource Owner 인증
        - 인가 토큰 발급
        - Resource 관리
    - OAuth 2.0에서는 Authorization Server와 Resource Server로 역할을 분리
        - Authorization Server : Resource Owner 인증, 인가 토큰 발급
        - Resource Server : Resource 관리
    - OAuth 2.0에서의 흐름
    ![image](https://github.com/meoldae/TIL/assets/70866410/93edab93-af9b-469c-9759-73b3046d5f0f)
- 토큰 유효기간 문제 개선   
RefreshToken개념을 도입하여 토큰 탈취 문제를 해결
- Grant 개념의 도입   
Grant란, 정적 리소스 반환만을 하는 웹이 아니라 모바일, JS등 여러가지 사용 환경에 대한 인증 방식
    - Authorization Code : OAuth 1.0과 거의 비슷한 흐름으로 Server 간 통신을 통해 인증값을 가지고 토큰을 발급받는 방식
    - Implicit : Web Browser의 JavaScript 기반 Client나 모바일 환경에서 인증값을 받는 대신, Resource Server에 직접적으로 접근하는 방식 
    - Resource Owner Password Credentials : Client가 Owner의 ID/PW를 직접 받아서 요청하는 방식
    - Client Credentials : Resource Owner와 Client가 사실상 동일할 때, Client가 관리하는 리소스를 접근하기 위한 경우 사용하는 방식


### Open ID Connect ( OIDC ) 
OAuth 2.0을 기반으로 하여 OAuth(인가) 를 통해 인증을 처리하는 방식   
기존의 토큰을 발급받던 흐름에서 Json Web Token 기반의 ID 토큰을 추가로 획득   
ID Token에는 Token 발급자, 발급시간, 만료시간, 사용자 신원 정보 등이 담겨있어 API 호출을 한번 더 하지 않아도 인증할 수 있음