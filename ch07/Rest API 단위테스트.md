 ### spring-boot-starter-test는 스프링 부트로 단위 테스트를 하기 위한 의존성이다. 
  * spring-boot-starterrk 제공하는 단위 테스트 프레임워크
    * Junit : 가장 널리 사용되는 자바 단위 테스트 프레임워크는 단위 테스트 작성을 위한 기본 구조를 제공한다. 
    * 스프링 테스트 : 스프링 컨텍스트를 시작하기 위해 단일 테스트를 작성하는 기능을 제공한다. Rest API를 단위 테스트하는 데 사용할 수 있는 MOCK MVC 프레임워크도 제공한다.
    * mockito-core : mock로 단위 테스트를 작성하는 데 도움이 된다.
    * AssertJ-core : 간단하고 읽기 쉬운 assertion을 작성할 수 있다. ex) asertThat(numbersList).hasSize(15).contains(1,2,3).allMatch(x -> x<100)
    * Hamcrest-core : 훌륭한 assrtion을 작성할 수 있는 또 다른 프레임워크이다. ex) assertThat("XYZ", containsString("XY"))
    * json-path : XML용 XPath와 유사하게 JSONPath로 JSON 응답의 요소를 식별하고 단위 테스트를 할 수 있다.
    * JSONAssert : JSON Assert를 사용하면 예상되는 JSON을 지정하고 이를 사용해 JSON 응답의 특정요소를 체크할 수 있다. 

<br><br>
```
웹 레이어의 단위 테스트를 작성할 때 보통 비즈니스나 데이터 레이어를 걱정하지 않고 특정 컨트롤러에 집중하려고 한다.
스프링 테스트 프레임워크는 @WebMvcTest 프레임워크를 제공해 스프링 MVC 컴포넌트에만 초점을 맞춘 테스트를 작성한다.
웹 MVC와 관련이 없는 모든 자동 구성을 비활성화한다. @Component, @Service등
```
<br>
@WebMvcTest가 스캔할 빈 - @Controller, @ControllerAdvice, @JsonComponent, Converter, GenericConverter, Filter, WebMvcConfigurer, HandlerMethodArgumentResolver

<br><br>
```java
@RunWith(SpringRunner.class)
@WebMvcTest(value = BasicController.class, secure = false)
public class BasicControllerTest {
  @Autowired
  private MockMvc mvc;
```

* Runwith(SpringRunner.class) : SpringRunner는 SpringJUnit4ClassRunner 클래스의 바로가기이다. 단위 테스트를 위한 간단한 스프링 컨텍스트를 시작한다.
* @WebMvcTest(BasicController.class) : 어노테이션을 SpringRunner와 함께 사용해 스프링 MVC 컨트롤러에 간단한 테스트를 작성할 수 있다. 스프링 MVC 관련 어노테이션으로 어노테이션이 달린 빈만 로드한다. 
* @Autowired private MockMvc mvc : 요청하는 데 사용할 수 있는 MockMvc빈을 자동 연결한다.
* secure = false : 단위테스트에서 시큐리티를 사용하고 싶지 않을 경우


```java
@Test
public void welcome() throws Exception {
  mvc.perform(
    MockMvcRequestBuilders.get("/welcome")
      .accept(MediaType.APPLICATION_JSON))
    .andExpect(status.isOk())
    .andExpect(content().string(equalTo("Hello World")));
}
```

* mvc.perform(MockMvcRequestBuilders.get("/welcome").accept(MediaType.APPLICATION_JSON)) : Accept 헤더 값, application/json을 사용해 /welcome의 요청을 수행한다. 
* andExpect(status.isOk()) : 응답 상태는 200(success이다)
* andExpect(content().string(equalTo("Hello World")) : 응답 내용이 "Hello World"와 동일할 것으로 추정된다. 

* Test Double (Mockito)
* 스프링과 Junit을 이용해서 테스트 코드를 작성하다 보면 테스트 환경(database, api)을 구현하는 코드까지 작성해야 하고 실제 테스트할 코드보다 환경을 구현하는 코드가 훨씬 더 복잡해지게 됩니다. 이런 문제 영역을 해결하기 위해서 테스트 더블 이라는 것이 나왔고 Java진영에서는 대표적으로 Mockito가 있습니다.
* 테스트 더블을 사용하면 여러 장점이 있습니다.
  * 테스트 대상 코드 격리
  * 테스트 속도 개선
  * 예측 불가능한 실행 요소 제거
  * 특수한 상황 테스트 가능
  * 감춰진 정보를 확인 가능
    * @Mock, @MockBean, @Spy, @SpyBean, @InjectMocks
* @MockBean :  기존에 사용되던 Bean의 껍데기만 가져오고 내부의 구현 부분은 모두 사용자에게 위임한 형태. 즉, 해당 Bean의 어떤 메소드가 어떤 값이 입력 되면 어떤 값이 리턴 되어야 한다는 내용 모두 개발자 필요에 의해서 조작이 가능합니다.

```java
@Test
public void retreiveTodos() throws Exception {
  //GIVEN
  List<Todo> mockList = Arrays.asList(
    new Todo(1, "Jack", "Learn Spring MVC", new Date(), false),
    new Todo(2, "Jack", "Learn struts", new Date(), false));
    
 //WHEN
 MvcResult result = mvc.perform(
  MockMvcRequestBuilders.get("/users/Jack/todos")
  .accept(MediaType.APPLICATION_JSON))
  .andExpect(status.isOk()).andReturn();
  
  //Then
  String expected = "[ ... 생략...
```

<b> Given, When, Then 부분이 명확하게 표시된다. Given - 설정, When - 시나리오 설정, Then - 결과 </b>


* when(service.retrieveTodo(anyInt())).thenReturn(mockTodo) : mock todo를 return하기 위해 retrieveTodo 서비스 메소드를 mocking 한다.

  
  
  ```java
   @Test
    public void createOrderTest() {
        // given
        OrderRepository orderRepository = Mockito.mock(OrderRepository.class);
        NotificationClient notificationClient = Mockito.mock(NotificationClient.class);
        orderService = new OrderService(orderRepository, notificationClient);

        Mockito.when(orderRepository.findOrderList()).then(invocation -> {
            System.out.println("I'm mock orderRepository");
            return Collections.emptyList();
        });
        Mockito.doAnswer(invocation -> {
            System.out.println("I'm mock notificationclient");
            return null;
        }).when(notificationClient).notifyToMobile();


        // when
        orderService.createOrder(true);

        // then
        Mockito.verify(orderRepository, Mockito.times(1)).createOrder();
        Mockito.verify(notificationClient, Mockito.times(1)).notifyToMobile();
    }
  
  ```
  
  ### mock객체의 orderRepository.findOrderList()와 notificationClient.notifyToMobile()에 대한 기대 행위를 작성하여 테스트에서 원하는 상황을 설정할 수 있게 되었습니다. 이런 기대 행위를 작성하는것을 Stub이라 합니다.
  
```  
Mockito.mock()은 껍데기만있는 mock객체를 만들어주고 stub을 해야지만 원하는 상황대로 실행되는데 

OrderRepository의 createOrder()에 대해서는 아무 stub을 지정해주지 않았는데도 테스트는 별 무리 없이 성공했습니다. 이는 Mockito의 기본전략이 Answers.RETURNS_DEFAULTS 이기 때문입니다.

Mockito는 메소드의 type별로 정의된 DEFAULT 메소드가 있습니다.

Answers.RETURNS_DEFAULTS 은 stub 되지 않은 메소드들에 대해서는 Mockito에서 메소드 타입별로 정의된 메소드들을 실행하게 됩니다.

결국 orderRepository.createOrder() 는 따로 stub 되지 않았기 때문에 orderRepository.createOrder() 타입인 void에 맞는 Default 메소드가 실행되어서 아무일 없이 진행된 것입니다.

 ```
 
* @Mock을 이용해서 Mockito.mock() 코드를 대체할 수 있습니다.
  * 한가지 주의 할 점은 @ExtendWith(MockitoExtension.class)를 사용해야지만 테스트 시작전 어노테이션을 감지해서 mock 객체를 주입하기 때문에 꼭 함께 사용해야 합니다.
 
 
 
 
