# 객체 지향 프로그래밍
실세계에 존재하는 사물이나 값들을 ```상태(변수)```와 ```행위(메서드)```를 가진```객체```로 나타내고, 이 객체간의 상호작용으로 프로그래밍 하는 것을 뜻한다.    
객체의 모듈화와 상속을 통해 효율적으로 코드를 재사용할 수 있고 유지보수에 용이하다.

## 객체 지향 프로그래밍의 특징

### 1. 추상화
객체에서 공통 속성이나 행위를 추출하여 나타내는 것
- 제어 추상화
    - 메서드의 내부 로직, 비즈니스 로직 등을 숨기는 것
    - 예를 들어, ```운전자가 엑셀을 밟는다 -> 차가 앞으로 간다.```   
     처럼 내부 기계공학적 원리는 모르더라도 차량을 운전하는데 지장이 전혀 없다.
- 데이터 추상화
    - 객체의 공통 속성을 통해 일반화하는 행위
    - 예를 들면, ```웰시코기, 닥스훈트, 도베르만``` 등은 ```개``` 라는 공통 개념으로 일반화할 수 있고, ```개```는 ```동물```이라는 개념으로 또 일반화할 수 있다.

### 2. 상속
객체의 상태나 행위를 다른 객체가 물려받는 것을 뜻한다. 공통되는 속성들을 효율적으로 재사용할 수 있다.
```
class Car {
    int displacement;
    String engine;

    move();
    stop();
}

class SportsCar extends Car {
    open();   
}

class Truck extends Car {
    load();
}   
```   
예시처럼 엔진, 배기량, 이동, 멈춤 같은 공통 속성은 재사용하고, 지붕이 열리거나 짐을 싣는 등의 추가 기능만 작성하면 된다.

### 3. 캡슐화
```특정한 목적```을 달성하기 위해 변수와 메서드를 하나로 묶는 것을 뜻한다. 캡슐화가 되어있지 않다면, 동일한 목적을 다른 곳에서도 사용해야 한다면 중복된 코드를 계속해서 작성해야 한다. 즉 객체 스스로 처리하도록 하는것이 캡슐화이고, 자연스레 정보은닉의 이점도 취할 수 있다.

캡슐화가 되어있지 않을 때, 판매 품목을 다른 할인율로 판매한다고 하자.   

```
class Product {
    int price;

    public int getPrice() {
        return price;
    }
}

public double discount1(Product product){
    return product.getPrice() * 0.8;
}

public double discount2(Product product){
    return product.getPrice() * 0.5;
}
```   
위와 같이 동일한 코드를 작성해야하고 할인율이 변경되기라도 하면 모든 코드를 찾아 고쳐야하는 번거로움이 존재한다.

캡슐화가 잘 지켜졌다면 다음과 같은 코드를 작성할 수 있다.   
```
class Product {
    int price;

    public int getPrice() {
        return price;
    }

    public double discount(Product product, double discountRate) {
        return product.getPrice() * discountRate;
    }
}
```   
객체 스스로 할인을 적용하도록 위와 같이 작성할 수 있다. 또 할인 로직이나 비율이 변경되어도 대응하기 용이하다.

### 4. 다형성
같은 코드가 다른 행위를 수행하는 것을 의미한다.
- Overriding 오버라이딩   
상위 클래스로부터 상속받은 메서드를 재정의하여 다른 기능을 구현한다.    
예를 들면, 전자 기기에서 ```전원 켜기```라는 기능이 존재할 때 ```TV, 컴퓨터, 휴대폰, 태블릿```등에서 모두 전기를 통해 기기를 가동시킨다는 목적이 있으나 내부 작동 방식은 모두 다르다.
- Overloading 오버로딩   
메서드의 인자를 다르게 하여 같은 이름의 메서드들을 하나의 클래스에서 여러 개 가질 수 있다.   
```
class PrintStream {
    
    ...

    public void print(boolean b) {
    write(String.valueOf(b));
    }

    public void print(char c) {
        write(String.valueOf(c));
    }

    public void print(int i) {
        write(String.valueOf(i));
    }

    public void print(long l) {
        write(String.valueOf(l));
    }

    public void print(float f) {
        write(String.valueOf(f));
    }
}

```   
위 예시처럼 ```print()```라는 공통의 함수를 정수, 실수, 문자 등 인자를 다르게 하여 여러 메서드를 하나의 클래스에서 가질 수 있다.