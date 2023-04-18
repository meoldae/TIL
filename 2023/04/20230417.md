# 의존 관계와 의존성 주입

Spring에서 가장 중요한 개념인 DI (Dependency Injection)에 대해 제대로 이해하지 못하고 쓰는 것 같아 정리하고자 한다.   

### 의존관계 Dependency
A, B 두 객체가 있을 때 B가 변경됨으로써 A에 영향을 미친다면 `A는 B에 의존한다.`라고 표현할 수 있다.   

객체지향 프로그래밍에서는 어떠한 클래스가 다른 클래스를 사용하기 위해서는 반드시 필요한 관계이다. 예를 들면 다음과 같다.
```java
public class Lunch {
    private DaejeonCampus daejeonCam;

    public Lunch() {
        this.daejeonCam = new DaejeonCampus();
    }
}
```
`Lunch` 클래스는 대전 캠퍼스만을 의존할 수 있다.    
다른 캠퍼스의 점심을 먹기 위해서는 캠퍼스와 생성자를 변경해줘야 하고, 캠퍼스가 바뀔때 마다 변경해야 한다.     
`Lunch`는 `DaejeonCampus`라는 구현체에 의존하고 있으며 이를 `강하게 결합되어있다(Tight Coupling)` 라고 표현한다.

다른 캠퍼스의 점심도 유연하게 받으려면 `인터페이스`를 사용해야 한다.   
객체지향의 5 원칙 (SOLID) 중 `의존성 역전의 원칙 : DIP (Dependency Inversion Principle)`에 해당한다. `추상화`를 매개체로 하여 결합도를 낮추는 원칙이다.   

```java
public class Lunch {
    private Campus campus;

    public Lunch() {
        this.campus = new DaejeonCampus();
        // this.campus = new GumiCampus();
        // this.campus = new BUKCampus();
        ...
    }
}
```
추상화하고 나면 여러 캠퍼스의 점심에 접근이 가능하게 되고 이를 `느슨한 결합(Loose Coupling)`이라고 한다.

### DI (Dependency Injection)
추상화 이후에도 `Lunch`클래스가 어떤 캠퍼스에 접근할지 생성자에서 직접 결정하고 있다. 이를 `의존성 주입`을 통해 결합도를 더 낮출 수 있다.   
의존성 주입이란, 의존 관계가 외부에서 결정되는 것을 의미한다.

나아가 토비님은 아래와 같은 세 가지 조건을 덧붙였다.
- 클래스 모델이나 코드에서는 런타임 시점의 의존관계가 드러나지 않아야 한다. (인터페이스에 의존해야 한다.)
- 런타임 시점의 의존관계는 컨테이너나 팩토리 같은 제3의 존재가 결정한다.
- 의존관계는 사용할 객체에 대한 레퍼런스를 외부에서 주입해줌으로써 만들어진다.

DI를 통해 얻을 수 있는 장점은 다음과 같다.
- 느슨한 결합   
    대상 객체가 변하더라도 유연하게 대처가 가능하다.
- 재사용성 증가   
    예시에서의 `Campus`를 용도에 따라 다른 클래스에서 재사용 할 수 있다.

### 의존관계 주입 방법
Spring에서는 네가지 방법으로 의존성을 주입할 수 있다.
- 생성자 (권장되는 방법)
    - 생성자 주입 시 `final`로 변수 선언이 가능하다. 
    - 테스트 코드 작성 시 테스트용 클래스의 주입이 가능하다.
    - 순환참조를 예방할 수 있다. (Spring 한정)   <br><br>
        > 순환참조란? 닭이 먼저냐, 계란이 먼저냐 같은 문제라고 볼 수 있다.   
        > 닭이 있기 위해서는 계란이 있어야 하고, 계란이 있기 위해서는 닭이 있어야 한다..
- 수정자 
    - Setter 메소드를 통해 의존성을 주입할 수 있다.
    - ❗ 단, Setter를 통해 주입받기 전에 객체가 생성될 수 있다. 이 때 메소드에 접근하게되면 `NullPointerException`이 발생할 수 있다.
- 필드
    - `@Autowired` 어노테이션을 통한 주입이 가능하다.
    - ❗ 필드 주입 역시 주입받기 전에 객체가 생성될 수 있다. 때문에 수정자와 동일하게 `NullPointerException`이 발생할 수 있다.
- 메서드
    - 여러 파라미터들을 받을 수 있다는 장점이 있으나 거의 사용되지 않는다.
<br>
<br>
<br>

### 참고
---
- [DI 쉽게 이해하기](https://tecoble.techcourse.co.kr/post/2021-04-27-dependency-injection/)   
- [생성자 주입을 사용해야 하는 이유](https://yaboong.github.io/spring/2019/08/29/why-field-injection-is-bad/)