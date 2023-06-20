# JPQL 객체지향 쿼리 언어
JPA를 사용하면 테이블 중심이 아니라 객체를 중심으로 개발하게 됨.
-> 조회 쿼리시 모든 데이터를 객체로 변환해서 검색하는 것은 불가능하다. 필요한 데이터만 불러오기위해서 조건이 포함된 쿼리가 필요!!

```java
String JPQL = "select o from Object o where o.name like '%jeon%'";
entityManager.createQuery(JPQL, Object.class);
```
위와 같이 ```createQuery(qlString, Class)```를 통해 만들 수 있으며 ```getResultList()```등의 메서드를 추가하여 결과를 반환받을 수 있다. 
- 테이블이 아닌 객체를 대상으로 검색한다.
- SQL을 추상화 하기때문에 특정 데이터베이스에 종속되지 않는다.

### JPA Criteria 
```java
CriteriaBuilder cb = em.getCriteriaBuilder();
CriteriaQuery<Member> query = cb.createQuery(Member.class);
Root<Member> m = query.from(Member.class);

CriteriaQuery<Member> cq = query.select(m).where(cb.equal(m.get("username"), “kim”));
List<Member> resultList = em.createQuery(cq).getResultList();
```
- Java 표준에서 지원하는 기능
- 쿼리를 메서드로 작성함 => 동적 쿼리 작성이 좀 더 용이함
- 문자열이 아닌 코드이기때문에 오탈자 발생을 컴파일 시에 알 수 있다.
- But 너무 복잡하고 실용성이 없음 -> 실무에서 사용 안함..

### QueryDSL
```java
JPAFactoryQuery query = new JPAQueryFactory(em);
QMember m = QMember.member;
List<Member> list = query.selectFrom(m)
                            .where(m.age.gt(18))
                            .orderBy(m.name.desc())
                            .fetch();
```
- Java 코드로 쿼리 작성 가능
- 역시 컴파일 시점에 오류 발생을 알 수 있음
- 단순하고 쉽게 동적 쿼리 작성 가능 -> 실무 사용 권장!!

### 네이티브 SQL
- SQL을 직접 사용하는 기능
- JPQL로 해결할 수 없는 특정 데이터베이스를 위한 기능
```java
String sql = "SELECT ID, AGE, TEAM_ID, NAME FROM MEMBER WHERE NAME = ‘kim’";
List<Member> resultList = em.createNativeQuery(sql, Member.class).getResultList();
```
위 예시처럼 ```createNativeQuery()``` 메서드를 사용하면 된다.

### JPQL 기본 문법
SQL과 거의 비슷하지만 테이블이 아니라 엔티티가 들어간다는 점이 다르다.
```java
String jpql = "select m from Member as m where m.age > 18";
```
- 엔티티와 속성은 각각 대소문자를 구분한다.
- JPQL 키워드는 대소문자를 구분하지 않는다. (SELECT == select)
- 별칭은 필수이다. (as 는 생략 가능)
- 집합과 정렬
    - ```count(), sum(), avg(), max(), min()``` 등 기본적 집계함수 모두 존재한다.

- TypeQuery & Query
    - TypeQuery는 반환타입이 명확할 때 사용
    - Query : 반환타입이 명확하지 않을 때

- 결과 조회 API
    - ```getResultList()``` : 반환형이 컬렉션일 때
        - 결과가 없으면 빈 리스트 반환 -> NPE 에서 비교적 자유로움
    - ```getSingleResult()``` : 반환값이 단 하나일 때
        - 결과가 없으면 ```NoResultException```, 둘 이상이면 ```NonUniqueResultException``` 발생

### 파라미터 바인딩
- 명칭 기준
    ```java
    em.createQuery("SELECT m FROM Member m where m.username=:username", Member.class);
    query.setParameter("username", usernameParam);
    ```
- 위치 기준 -> 위치가 바뀌면 오류가 발생할 수 있으므로 사용 지양
    ```java
    em.createQuery("SELECT m FROM Member m where m.username=?1", Member.class);
    query.setParameter(1, usernameParam);
    ```
> 물론 메서드 체이닝이 가능함.