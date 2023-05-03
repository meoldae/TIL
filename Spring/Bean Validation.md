# Bean Validation

필드에 대한 검증 로직을 어노테이션 하나로 편리하게 적용할 수 있다.

> Bean Validation은 구현체가 아니라 JPA 처럼 기술 표준으로 인터페이스의 모음이다. 일반적으로 구현체는 하이버네이트 Validator를 사용한다. ❗ JPA 구현체와 관련 없다!!

- 예제 코드
    ```java
    public class Item {

        private Long id;

        @NotBlank
        private String itemName;

        @NotNull
        @Range(min = 1000, max = 100000)
        private Integer price;

        @NotNull
        @Max(9999)
        private Integer quantity;
    }
    ```
    - 코드 블럭처럼 여러 어노테이션을 활용해 검증 로직을 수행할 수 있다!<br>
    [다른 애노테이션 더 보기..](https://docs.jboss.org/hibernate/validator/6.2/reference/en-US/html_single/#validator-defineconstraints-spec)

### 검증 ?

```java
ValidatorFactory factory = Validation.buildDefaultValidatorFactory();
Validator validator = factory.getValidator();
```
위 코드와 같은 형태로 검증기를 직접 생성하여 사용할 수도 있다. 그러나 Spring 통합시 이런 코드를 작성할 필요 없다!

스프링 부트는 자동으로 Global한 Validator를 등록한다. 때문에 별도의 검증기 작성 없이 객체의 어노테이션을 보고 검증을 수행한다.

- 예제 코드
    ```java
    @PostMapping("/add")
    public String addItem(@Validated @ModelAttribute Item item, BindingResult bindingResult, RedirectAttributes redirectAttributes) {
        if (bindingResult.hasErrors()) {
            log.info("errors={}", bindingResult);
            return "validation/v3/addForm";
        }
        ... 
    }

    ```
    - `@ModelAttribute`의 앞에 `@Validated` 혹은 `@Valid` 를 붙임으로써 검증기가 작동한다.
        - `@Validated` : 스프링 전용 검증
        - `@Valid` : 자바 표준 검증
    - 바인딩에 성공한 필드에 대해서만 검증이 적용된다. 바인딩 실패시 `typeMismatch` 에러가 생성되고, `bindingResult` 에 담기게 된다.
- 에러 코드
    - Bean Validation이 적용된 후 오류가 발생하면 해당 어노테이션 이름으로 오류코드가 등록된다.

        > @NotBlank
        > - NotBlank.item.itemName
        > - NotBlank.itemName
        > - NotBlank.java.lang.String
        > - NotBlank

- `Object Error`
    - Object Error 검증은 `@ScriptAssert()`를 사용할 수 있다.
    - 예제 코드
        ```java
            @ScriptAssert(lang = "javascript", script = "_this.price * _this.quantity >= 10000")
            public class Item {
                ...
            }
        ```
        - 실 사용시 제약이 많고 복잡하므로, 필드 에러 이외의 검증에는 직접 자바 코드로 작성하는 것을 권장한다.

### Groups 
동일한 객체에 대해 요구사항이 변경될 수 있다.   
예를 들어, `Item`에 대해 등록시에는 최대 재고가 9999개 이지만, 수정시에는 재고 제한을 없앨 수 있다.
- groups

    ```java
    @NotNull(groups = UpdateCheck.class) 
    private Long id;

    @NotBlank(groups = {SaveCheck.class, UpdateCheck.class})
    private String itemName;
    ```   

    ```java
    @PostMapping("/add")
    public String addItemV2(@Validated(SaveCheck.class) @ModelAttribute Item item, BindingResult bindingResult, RedirectAttributes redirectAttributes) {
        ...
    }

    ```
    - 위의 코드처럼 별도의 인터페이스를 만들어 해당되는 경우에만 검증이 되도록 할 수 있다.
    - 그러나 실무에서는 복잡도가 커져서 잘 사용하지 않으며 용도에 따라 별도의 객체로 `분리해서` 사용한다.

### API (JSON) 방식에서의 검증

`@ModelAttribute`는 QueryString, Form을 통해서 정보가 전달될 때 사용하였고, JSON 형식으로 전달되는 `@RequestBody`에도 동일하게 사용이 가능하다.

- 예제 코드    
    ```java
    @PostMapping("/add")
    public Object addItem(@RequestBody @Validated ItemSaveForm form, BindingResult bindingResult) {
        ...
    }
    ```
    - 동일하게 `@Validated` 어노테이션을 붙임으로서 검증할 수 있다.
    - 단, API 방식의 경우 Binding 자체가 실패하면 Controller의 호출도 일어나지 않는다.

