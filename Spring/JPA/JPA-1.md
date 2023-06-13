# Inflern - 자바 ORM 표준 JPA 프로그래밍 

### 영속성 컨텍스트 (Persistence Context)
- JPA를 이해하는 데 가장 중요한 개념 중 하나로 엔티티를 영구적으로 저장하는 환경    
(엔티티를 DB에 저장하는 것이 아니고, 엔티티를 영속성 컨텍스트 안에 저장)
- 논리적인 개념
- 엔티티 매니저를 통해 영속성 컨텍스트에 접근

### 엔티티의 생명 주기 (Entity Life Cycle)
- 비영속 (new/transient) : 최초의 객체를 생성한 상태
- 영속 (managed) : ```persist()```메서드 이후 영속성 컨텍스트에 관리되는 상태
- 준영속 (detached) : 영속성 컨텍스트에서 다시 분리하는 상태 
- 삭제 (removed) : 실제 DB에 삭제를 요청하는 상태
    ```java
    EntityManagerFactory emf = Persistence.createEntityManagerFactory("hello");

    // `비영속` 상태
    Member member = new Member("A", "John");

    EntityManager em = emf.createEntityManager();
    em.getTransaction().begin();

    // 이후부터 '영속' 상태
    // But, Query가 날아가는 단계는 아님
    em.persist(member); 

    // detach() 메서드를 사용하면 '준영속'상태
    em.detach(member);
    ```
### 영속성 컨텍스트의 장점
- 1차 캐시(조회) : 영속성 컨텍스트 내부에 존재하며, DB의 PK를 Key값으로 갖고, 엔티티를 Value로 갖는 임시 저장공간
    ```java
    Member member = new Member("A", "John");

    // 생성한 Member 객체가 1차 캐시에 저장
    em.persist(member); 

    // 1차 캐시 공간을 먼저 확인
    Member findMember1 = em.find(Member.class, "A");

    // 1차 캐시에 없다면, DB로부터 조회하고 1차 캐시에 저장
    Member findMember2 = em.find(Member.class, "B");
    ```
- 동일성 보장
    ```java
    Member findMember1 = em.find(Member.class, "A");
    Member findMember2 = em.find(Member.class, "A");

    boolean result = findMember1 == findMember2; // true
    ```
- 쓰기 지연 : Insert SQL 구문을 작성하되, 커밋 전까지 DB에 날리지 않고 영속성 컨텍스트 내부 SQL 저장소에 저장
    ```java 
    em.persist(member1);    
    em.persist(member2);

    // Insert Query가 DB에 전달되는 순간
    em.transaction().commit();
    ```
    다음의 옵션을 통해 SQL 저장소에 쌓인 Query를 한번에 보낼 수 있다.

    ```xml
    <property name="hibernate.jdbc.batch_size" value="10"/>
    ```
- 변경 감지 : 1차 캐시에서 Entity의 값과 스냅샷을 비교(변경 유무 확인)하여 Update Query를 SQL 저장소에 저정한다. 이 Query 역시 Commit 시점에 전달된다.
    ```java
    Member findMember = em.find(Member.class, "A");
    findMember.setName("Mike");

    // 필요 없음
    // em.persist(findMember);
    // em.update(findMember); 

    em.transaction().commit(); 
    ```
- 삭제
    ```java
    Member findMember = em.find(Member.class, "A");
    
    em.remove(findMember);

    em.transaction().commit();
    ```