# Inflern - 자바 ORM 표준 JPA 프로그래밍 

### 엔티티 매핑

- ```@Entity``` : 어노테이션이 붙은 클래스는 JPA가 관리하고, 엔티티라고 칭한다.
    - 기본 생성자가 필수적
    - ```final, enum, interface, inner class```에서 사용 불가
    - 객체 필드에 ```final``` 사용 불가
    - name  
        - JPA에서 사용할 엔티티 이름 지정
        - 기본적으로 클래스 이름을 그대로 사용   
- ```@table``` : 엔티티와 매핑될 테이블을 지정
    - ```name``` : 매핑될 테이블 이름 (기본값은 엔티티 이름)
    - ```catalog, schema, uniqueConstraints``` 등.. 여러 속성 존재

### 데이터 스키마 자동 생성
JPA에서는 애플리케이션 로딩 시점에 DB 테이블 생성 기능 제공   
DDL은 선택한 데이터베이스에 맞게 생성되며 운영시점이 아니라 개발 시점에서만 사용해야한다!
```xml
<property name="hibernate.hbm2ddl.auto" value="create" />
```
JPA 설정파일 옵션을 통해 설정 가능
- 옵션
    - create : 기존 테이블 삭제 후 생성
    - create-drop : create 이후 종료시점에 테이블 삭제
    - update : 변경된 것만 반영 (운영시 절대 사용 X)
    - validate : 엔티티와 테이블이 정상적으로 매핑가능한지만 확인
    - none : 사용X
- 제약조건 추가 가능
    - ```@Column``` 어노테이션을 붙이고 속성으로 제약조건 추가 가능
        - ```unique``` : ```true || false```
        - ```nullable``` : ```true || false```
        - ```length``` : 정수값 입력..

### 기본 키 맵핑
- ```@Id``` : 직접 할당 (문자열)
- ```@GeneratedValue``` : 자동 생성 ( GenerationType.옵션 )
    - IDENTITY : DB에 위임 ex.) Auto Increment 
    - SEQUENCE : 주로 Oracle DB에서 사용하며 시퀀스 오브젝트를 가져와서 사용
        - ```SequenceGenerator, sequenceName```을 통해 테이블마다 다른 시퀀스를 매핑할 수 있음
    - TABLE : 키 전용 테이블을 만들어 시퀀스 흉내 (성능↓)
    - AUTO : DB에 맞춰서 자동 생성

### 기본키 전략
- 제약조건 : NULL X, Unique, ```불변```
- 미래까지 불변하는 자연적인 키는 찾기 어려움
- Long + 대체키 + 키 생성 전략 사용 권장