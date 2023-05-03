# DB Connection Pool

### 정의
애플리케이션 서버로부터 데이터베이스로의 요청이 필요할 때마다 연결을 만드는 것이 아니라    
미리 만들어 두고 재사용할 수 있도록 관리되는 연결의 `캐시`이다.

<b>커넥션 풀 X</b>

<img src="https://user-images.githubusercontent.com/70866410/234161233-183a6866-9a7a-4785-8ed7-7c024610ba84.png">     

DB와의 통신은 신뢰성을 보장하는 `TCP` 프로토콜을 기반으로 동작한다.   
TCP 프토토콜은 데이터의 송수신이 일어나기 전에 `연결` 세션을 `생성`하고, 데이터의 송수신이 끝나면 `연결`을 `종료`하는 과정이 필요하다.   
연결이 생성될 때는 3-Way Handshake, 종료될 때는 4-Way Handshake로 진행된다.   
이는 결국 데이터를 주고받는 과정이 3회, 4회 있다는 것이므로 어느 정도의 시간을 소요한다.

커넥션 풀이 없는 경우, DB 조회가 필요한 요청이 들어올 때 마다 DB와의 연결을 생성하고 종료하는 과정을 거치며 백엔드 서버에는 수 많은 종류의 요청이 들어오므로 많은 시간을 필요로 한다.

<br><br>
<b>커넥션 풀 O</b>

<img src="https://user-images.githubusercontent.com/70866410/234165170-296de33d-c259-428b-bfdb-4604b82fc805.png">     

백엔드 서버와 DB 서버가 처음 메모리에 적재될 때, 미리 일정 수의 연결을 생성하여 저장해놓는다.
이 연결(Connection)들이 저장된 곳이 커넥션 풀(Connection Pool)이다.   
커넥션 풀이 있다면 DB 조회가 필요한 요청이 들어올 때 커넥션 풀에 여유가 있으면 해당 커넥션을 획득하여 DB와 통신하고 끝나면 커넥션을 <b>종료하지 않고</b> 반환한다.    
TCP 연결 생성, 종료 시간을 크게 절약할 수 있다.


### 장점
- Connection을 미리 메모리에 적재해 둠으로써 빠르게 접근이 가능하다.
- Connection의 수를 제한할 수 있어 많은 수의 요청이 들어와 서버 자원이 고갈되는 것을 막을 수 있다.   
요청의 수가 Connection Pool의 여유를 초과하면 나중에 들어온 요청들은 대기한다.

### Connection의 갯수 ?
커넥션 풀을 크게 설정하면 요청에 따른 대기시간이 줄어들겠지만, 미리 연결을 만들어 메모리상에 적재해두는 것이기 때문에 메모리 소모가 커진다.   
그래서 적절한 수의 커넥션을 생성하는 것이 중요하다.   
1. 모니터링 환경 구축 
2. 부하 테스트    
traffic을 늘려가며 백 엔드 시스템이 어떻게 작동하는지 확인한다. 
- Request Per Second : 단위시간(초) 당 몇 개의 요청을 처리하는가   
    <img src="https://user-images.githubusercontent.com/70866410/234167546-3f6dcab3-957d-4fde-b3c3-cbc0bf52581f.png">   
- Average Response Time : Request의 평균 응답 시간     
    <img src="https://user-images.githubusercontent.com/70866410/234167597-06b2b58d-461c-4271-aad2-9f47faaee620.png">
3. 두 그래프에서 값 `흐름이 변하는 구간`에서 모니터링을 실시한다. CPU, 메모리 사용률 등...

    - 백엔드 서버 사용률 높음 -> 백엔드 서버 확충
    - DB 서버 사용률 높음 -> DB 서버 확충
        - 백엔드 서버와 DB 서버 사이 Cache 레이어 확충
        - Sharding
    - 두 서버 모두 사용률 양호
        - 활성 상태 스레드를 확인하여 스레드 여유가 없다면 Thread 풀의 크기를 변경한다.
        - 활성 상태의 커넥션 수를 확인하여 Connection Pool의 크기를 변경한다.
4. 위 과정을 계속 반복🔁

5. 사용할 백엔드 서버의 수를 고려하여 커넥션 풀 설정

### 종류
- Apache Commons DBCP
- Tomcat JDBC
- `HikariCP` : Spring Boot 2.0부터 기본적으로 채택되었다.


### 참고자료
---
[DBCP : 쉬운 코드 Youtube ](https://youtu.be/zowzVqx3MQ4)   
[NAVER D2 : Commons DBCP 이해하기](https://d2.naver.com/helloworld/5102792)