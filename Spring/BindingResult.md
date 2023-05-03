# 2023-04-05 TIL

## Binding Result
- Spring이 제공하는 검증 오류 보관 객체
- 파라미터의 위치는 반드시 검증할 대상 `바로 뒤에` 위치해야 한다.
    ```java
        @PostMapping("/add")
        public String addItem(@ModelAttribute Item item, BindingResult bindingResult, RedirectAttributes redirectAttributes, Model model) ...
    ```
- BindingResult가 존재하면 타입오류가 발생해 데이터 바인딩이 되지 않더라도 400 오류가 발생하지 않고, `컨트롤러를 정상 호출`한다.  
- ```Model```에 담지 않아도 Spring이 View에 제공한다.
- 담을 수 있는 오류 객체는 `FieldError, ObjectError`가 있다.

## Field Error
- Field에 관한 오류를 담는 객체
```java
public FieldError(String objectName, String field, String defaultMessage) {}

public FieldError(String objectName, String field, @Nullable Object rejectedValue, boolean bindingFailure, @Nullable String[] codes, @Nullable Object[] arguments, @Nullable String defaultMessage) {}
```
- 두 개의 생성자가 제공된다.
    - objectName : 오류의 대상 객체 이름
    - field : 오류가 발생한 필드
    - <b>rejectedValue : 사용자가 입력한(오류가 발생한) 값</b>
    - bindingFailure : 바인딩 자체가 실패했는지(타입 오류), 검증 실패인지 구분하는 값
    - codes : 메시지 코드
    - arguments : 메시지에서 사용할 인자
    - defaultMessage : 기본 오류 메시지
- 예제 코드 
    ```java
        // 가격의 범위가 잘못 입력되었을 때
        if (item.getPrice() == null || item.getPrice() < 1000 || item.getPrice() > 10000000 ) {
            bindingResult.addError(
                new FieldError("item", "price", item.getPrice(), false, null, null, "가격은 1,000 ~ 1,000,000 원 까지 허용됩니다.")
            );
        }
    ```
    - objectName : ```@ModelAttribute Item item``` 이므로 ```item```. 반드시 일치해야 한다!
    - field : 가격에 대한 오류가 발생했으므로 ```price```
    - rejectedValue : 사용자가 입력한 범위 밖의 값
    - bindingFailure : 값이 입력되어 바인딩 자체는 되었으므로 ```false```
    - codes : 메시지 코드는 없으므로 ```null```
    - arguments : 사용할 메시지 코드 자체가 null이므로 이것도 ```null```
    - defaultMessage : 가격 오류가 발생하면 ```"가격은 1,000 ~ 1,000,000 원 까지 허용됩니다."``` 라는 문구를 출력한다.

## Object Error
- Field Error 이외의 모든 오류를 담는 객체
```java
public ObjectError(String objectName, String defaultMessage) {}

public ObjectError(String objectName, @Nullable String[] codes, @Nullable Object[] arguments, @Nullable String defaultMessage) {}
```
- Object Error 역시 두 가지 생성자를 제공한다. FieldError와 비슷하므로 자세한 설명은 생략한다.
- 예제 코드 
    ```java
        // 어느 Field 하나에 대한 오류가 아닌 복합적 오류 검증
        if (item.getPrice() != null && item.getQuantity() != null) {
                int resultPrice = item.getPrice() * item.getQuantity();
                if (resultPrice < 10000) {
                bindingResult.addError(new ObjectError("item", null, null, "가격 * 수량의 합은 10,000원 이상이어야 합니다.");
                }
            }
    ```
