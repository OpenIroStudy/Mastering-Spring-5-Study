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
 
 
 
 
 
스프링 부트 애플리케이션은 웹, 비즈니스, 데이터와 같은 여러 레이어로 구축된다. <br>
단위 테스트에서는 각 레이어를 독립적으로 테스트한다. <br>
모든 레이어가 결합돼 제대로 동작하는지 테스트 하는 것은 '통합테스트' 이다. <br>


### 스프링 부트 테스트는 통합 테스트를 빠르게 시작하기 위해 @SpringBootTest 어노테이션을 제공한다. 
* Mock : 웹 환경에서 컨트롤러를 단위 테스트 한다. 앞에서 단위 테스트에서 한 것과 유사하다.
* RANDOM_PORT : 사용 가능한 랜덤 포트의 임베디드 서버에서 다른 레이어를 포함한 전체 웹 컨텍스트를 시작한다. 
* DEFINED_PORT : 포트 번호를 하드 코딩한다는 점을 제외하고는 RAMDOM_PORT와 유사하다.
* NONE : 웹 컨텍스트 없이 스프링 컨텍스트를 로드한다. 

필자는 원하는 기능을 제공하는 Mock 라이브러리를 찾지 못해 (또는 완전히 학습하지 못해?) 필요한 기능을 제공하는 간단한 라이브러리를 직접 만들어서 사용한 적도 있다. 그런데, 매우 맘에 드는 Mock 라이브러리를 하나 찾게 되었고, 몇 달 정도 사용해 본 결과 이전의 몇몇 Mock 라이브러리도 사용성 면에서 편리하였기에 본 글에서 사용방법을 소개하고자 한다. 그 Mock 라이브러리는 바로 Mockito 라는 것이다.

Mockito는 http://code.google.com/p/mockito/ 사이트에서 mockito-all-1.6.jar 파일을 다운로드 받을 수 있다. Maven을 사용한다면 아래의 의존을 pom.xml 파일에 추가해주면 된다.

<dependency>
    <groupId>org.mockito</groupId>
    <artifactId>mockito-all</artifactId>
    <version>1.6</version>
    <scope>test</scope>
</dependency>

Mock 객체를 만드는 이유가 크게 1) 협업하는 클래스의 완성 여부에 상관없이 내가 만든 클래스를 테스트 할 수 있고 2) 내가 만든 클래스가 연관된 클래스와 올바르게 협업하는 지 확인할 수 있기 때문이므로 이 두 가지 상황을 연출하기 위해 다음과 같은 클래스에 대한 테스트를 만들어 봄으로써 Mockito의 사용법을 설명해 나가도록 하겠다.

public class WriteArticleServiceImpl {
    private IdGenerator idGenerator;
    private ArticleDao articleDao;

    public Article writeArticle(Article article) {
        Integer id = idGenerator.getNextId();
        article.setId(id);
        articleDao.insert(article);
        return article;
    }
   
    // idGenerator와 articleDao에 대한 setter
    ...
}

위 코드에서 WriteArticleServiceImpl 클래스를 테스트 하려면 IdGenerator 인터페이스와 ArticleDao 인터페이스의 구현 객체를 필요로 한다. 하지만, 아직 IdGenerator와 ArticleDao를 구현한 클래스가 완전히 구현되지 않았다. 또한, 테스트 DB의 구축도 완전하지 않은 상황이라고 하자. 따라서 현재로서는 통합 테스트는 진행할 수 없고 WriteArticleServceImpl에 대한 단위 테스트만 진행할 수 있다.

Mockito를 이용한 Mock 객체 생성

WriteArticleServiceImpl 클래스를 테스트하는 코드는 다음과 같을 것이다.

    @Test
    public void writeArticle() {
        WriteArticleServiceImpl writeArticleService = new WriteArticleServiceImpl();
        Article article = new Article();
        Article writtenArticle = writeArticleService.writeArticle(article);
       
        assertNotNull(writtenArticle);
        assertNotNull(writtenArticle.getId());
    }

하지만 위 테스트를 실행하면 NullPointerException이 발생하는 데, 그 이유는 WriteArticleServiceImpl.writeArticle() 메서드에서 IdGenerator와 ArticleDao을 구현한 객체를 사용하기 때문이다. 따라서, WriteArticleServiceImpl 클래스를 테스트 하려면 IdGenerator와 ArticleDao를 가짜로 구현한 Mock 객체를 전달해 주어야 한다.

Mockito를 이용할 경우 이는 다음과 같이 Mockito.mock() 이라는 메서드를 이용해서 생성할 수 있다. 아래는 Mock 객체 생성 예이다.

import static org.junit.Assert.*;
import static org.mockito.Mockito.*;

import org.junit.Test;

public class WriteArticleServiceImplTest {

    @Test
    public void writeArticle() {
        // mock 객체 생성
        ArticleDao mockedDao = mock(ArticleDao.class);
        IdGenerator mockedGenerator = mock(IdGenerator.class);
       
        WriteArticleServiceImpl writeArticleService = new WriteArticleServiceImpl();
        writeArticleService.setArticleDao(mockedDao);
        writeArticleService.setIdGenerator(mockedGenerator);
       
        Article article = new Article();
        Article writtenArticle = writeArticleService.writeArticle(article);
       
        assertNotNull(writtenArticle);
    }
}


Mock 객체의 메서드 호출 검증하기

Mock 객체를 사용하는 이유는 테스트 하려는 클래스가 연관된 객체와 올바르게 협업하는 지를 테스트 하기 위함도 있다. 따라서, Mock 객체의 메서드가 올바르게 실행되는 지 확인해볼 필요가 있다. Mock 객체의 특정 메서드가 호출되었는 지 확인하려면 Mockito.verify() 메서드와 Mock 객체의 메서드를 함께 사용하면 된다. 아래는 사용 예이다.

    @Test
    public void writeArticle() {
        // mock 객체 생성
        ArticleDao mockedDao = mock(ArticleDao.class);
        IdGenerator mockedGenerator = mock(IdGenerator.class);
       
        WriteArticleServiceImpl writeArticleService = new WriteArticleServiceImpl();
        writeArticleService.setArticleDao(mockedDao);
        writeArticleService.setIdGenerator(mockedGenerator);
       
        Article article = new Article();
        Article writtenArticle = writeArticleService.writeArticle(article);
       
        assertNotNull(writtenArticle);
        verify(mockedGenerator).getNextId();
        verify(mockedDao).insert(article);
    }

위 코드에서 verify(mockedGenerator).getNextId() 메서드는 mockedGenerator 객체의 getNextId() 메서드가 호출되었는 지의 여부를 확인한다. verify(mockedDao).insert(article) 메서드의 경우 mockedDao 객체의 insert() 메서드 호출 중에서 article 객체를 인자로 전달받는 호출이 있었는 지 여부를 확인한다.

일단 Mock 객체가 만들어지면 해당 Mock 객체는 메서드 호출을 모두 기억하기 때문에, 어떤 메서드 호출이든 검증할 수 있다.

원하는 값을 리턴하는 스텁 만들기

앞서 테스트에서 다음과 같이 writeArticleService.writeArticle(aritlce)이 리턴한 객체가 알맞은 ID 값을 갖는 지 확인하는 검증 코드를 넣었다고 하자.

Article writtenArticle = writeArticleService.writeArticle(article);
assertNotNull(writtenArticle);
assertNotNull(writtenArticle.getId()); // 에러 발생
위 코드에서 assertNotNull(writtenArticle.getId()) 메서드는 검증에 실패한다. 그 이유는 WriteArticleServiceImpl.writeArticle() 메서드가 IdGenerator.nextId() 메서드를 이용해서 ID 값을 가져온 뒤 aritlcle 객체에 저장하기 때문이다. (아래 코드 참조)


    public Article writeArticle(Article article) {
        Integer id = idGenerator.getNextId();
        article.setId(id);
        articleDao.insert(article);
        return article;
    }
테스트 코드에서는 IdGenerator로 Mock 객체를 전달했는데, Mockito.mock()을 이용해서 생성한 객체의 메서드는 리턴 타입이 객체인 경우 null을 리턴하고 기본 데이터 타입인 경우 기본 값을 리턴한다. 따라서, 리턴 타입이 Integer인 (즉, 객체인) IdGenerator.getNextId()에 대해서는 null을 리턴하고 따라서 assertNotNull(writtenArticle.getId()) 코드에서 writtenArticle.getId() 메서드가 null을 리턴하게 되어 검증에 실패하는 것이다.

Mockito는 Mock 객체의 메서드가 알맞은 값을 리턴하는 스텁을 만들 수 있는 기능을 제공하고 있다. 이 메서드는 when - then의 형식을 띄고 있는데, 아래 코드는 실제 사용 예를 보여주고 있다.

...
IdGenerator mockedGenerator = mock(IdGenerator.class);
when(mockedGenerator.getNextId()).thenReturn(new Integer(1));

WriteArticleServiceImpl writeArticleService = new WriteArticleServiceImpl();
writeArticleService.setIdGenerator(mockedGenerator);

Article article = new Article();
Article writtenArticle = writeArticleService.writeArticle(article);

assertNotNull(writtenArticle);
assertNotNull(writtenArticle.getId());
verify(mockedGenerator).getNextId();
Mockito.when() 메서드는 메서드 호출 조건을 그리고 thenReturn()은 그 조건을 충족할 때 리턴할 값을 지정한다. 위 코드의 경우 mockedGenerator.getNextId() 메서드가 호출되면 Integer(1)을 리턴하라는 의미를 갖는다.

Mock 객체의 메서드 호출 시 전달되는 인자 값에 따라서 리턴 값을 다르게 지정할 수도 있다. 아래 코드는 예를 보여주고 있다.

Article article = new Article();
when(mockedArticleDao.insert(article)).thenReturn(article));

Argument Matcher를 이용한 인자 매칭

보통, when()으로 스텁을 생성하거나 verify()로 메서드 호출 여부를 확인할 때는 특졍한 값을 지정한다.

// 인자 값이 1인 경우 스텁 생성
when(mockedListService.getArticles(1)).thenReturn(someList);
// 인자 값으로 1을 전달하여 getArticles() 메서드를 호출했는 지의 여부
verify(mockedListService).getArticles(1);
하지만, 특정한 값이 아닌 임의의 값에 대해서 when() 메서드와 verify() 메서드를 실행하고 싶을 때가 있다. 이런 경우에는 Argument Matcher를 이용해서 인자 값을 지정하면 된다. 예를 들어, 임의의 정수 값을 인자로 전달받은 메서드 호출을 when()과 verify()에서 표현하고 싶다면 다음과 같이 Matchers.anyInt() 메서드를 사용하면 된다.


when(mockedListService.getArticles(anyInt())).thenReturn(someList);
...
verify(mockedListService).getArticles(anyInt());
Matchers 클래스는 anyInt() 뿐만 아니라 anyString(), anyDouble(), anyLong(), anyList(), anyMap() 등의 메서드를 제공하는데, 이들 메서드에 대한 자세한 내용은 http://mockito.googlecode.com/svn/branches/1.6/javadoc/org/mockito/Matchers.html 사이트를 참고하기 바란다.

인자 중 한가지라도 Argument Matcher를 사용하면 나머지 인자에 대해서도 Matcher를 사용해야 한다. 예를 들어, 아래 코드는 예외를 발생한다.


Authenticator authenticator = mock(Authenticator.class);
when(authenticator.authenticate(anyString(), "password")).thenReturn(authObj);
만약 여러 인자 중 특정 값을 명시해야 하는 경우가 필요하다면 eq() Matcher를 사용하면 된다. 아래는 위 코드를 eq()를 이용해서 수정한 코드를 보여주고 있다.

Authenticator authenticator = mock(Authenticator.class);
when(authenticator.authenticate(anyString(), eq("password"))).thenReturn(authObj);
Mockito 클래스는 Matchers 클래스를 상속받고 있기 때문에 Mockito 클래스의 static 메서드를 static import 하면 Matchers 클래스에 정의된 메서드를 사용할 수 있다.

thenThrow()를 이용한 예외 발생

Mock 객체의 메서드 호출시 예외를 발생시키고 싶을 때가 있는데, 이런 경우에는 thenThrow() 메서드를 사용하면 된다. 아래는 사용 예를 보여주고 있다.

when(mockedDao.insert(article)).thenThrow(new RuntimeException("invalid title"));
thenThrow() 메서드에서 발생시킬 예외 객체를 전달해주면, when()에서 지정한 조건의 메서드가 호출될 때 예외를 발생시킨다.

메서드 호출 회수 검사

메서드가 지정한 회수 만큼 호출되었는 지의 여부를 확인하려면 times() 메서드를 사용하면 된다. 예를 들어, Mock 객체의 특정 메서드가 3번 호출되었는 지 확인하려면 다음과 같이 verify() 메서드의 두 번째 인자에 times() 메서드를 (정확히는 times() 메서드의 리턴 값을) 전달해주면 된다.

verify(mockedAuthenticator, times(3)).authenticate(anyString(), anyString());
호출 회수를 따로 지정하지 않을 경우 times(1)이 기본 값이 된다. times() 외에 다음과 같은 메서드를 사용할 수 있다.
times(int) - 지정한 회수 만큼 호출되었는 지 검증
never() - 호출되지 않았는지 여부 검증
atLeastOnce() - 최소한 한번은 호출되었는 지 검증
atLeast(int) - 최소한 지정한 회수 만큼 호출되었는 지 검증
atMost(int) - 최대 지정한 회수 만큼 호출되었는 지 검증
다수의 Mock 객체들이 사용되지 않은 것을 검증하고 싶은 경우에는 verifyZeroInteractions(Object ... mocks) 메서드를 사용하면 된다. 아래는 사용 예이다.


verifyZeroInteractions(mockedOne, mockedTwo, mockedThree);

Answer를 이용한 메서드 구현

Mock 객체를 사용하다보면 직접 Mock의 동작 방식을 구현해 주고 싶을 때가 있다. (사실, 필자가 개인적으로 굳이 간단한 Mock 라이브러릴 만든 이유도 이것 때문이었다.) 이런 경우 thenAnswer() 메서드와 Answer 인터페이스를 사용하면 된다. 아래 코드는 사용 예이다.

when(mockedGenerator.getNextId()).thenAnswer(new Answer<Integer>() {
    private int nextId = 0;
    public Integer answer(InvocationOnMock invocation) throws Throwable {
        return new Integer(++nextId);
    }
});
위와 같이 Answer를 사용하면, mockedGenerator의 getNextId() 메서드를 호출할 때 마다 answer() 메서드가 호출된다. 위 코드의 경우 getnextId() 메서드가 호출될 때 마다 1씩 증가된 값을 리턴하는 Anwser 구현  클래스를 리턴하였다.

만약 파라미터로 전달되는 값을 사용하고 싶다면 answer() 메서드에 전달된 InvocationOnMock을 이용하면 된다. 아래 코드는 사용 예이다.

when(authenticator.authenticate(anyString(), anyString())).thenAnswer(new Answer<Object> (){
    public Object answer(InvocationOnMock invocation) throws Throwable {
        Object[] arguments = invocation.getArguments();
        String userId = (String) arguments[0];
        String password = (String) arguments[1];
        Object authObject = null;
        // ...
        return authObject;
    }
});

@Mock 어노테이션을 이용한 코드 단순화

Mockito.mock() 메서드를 이용해서 Mock 객체를 생성하는 코드가 다소 성가시게 느껴진다면, @Mock 어노테이션을 이용해서 Mock 객체를 생성할 수 있다. 예를 들어, 아래 코드와 같이 테스트 클래스의 멤버 필드에 @Mock 어노테이션을 적용하면 해당 타입에 대한 Mock 객체가 할당된다.

@Mock 어노테이션이 동작하려면 테스트가 실행되기 전에 @Mock 어노테이션이 적용된 필드에 Mock 객체를 할당하도록 해 주어야 한다. JUnit 4 버전의 경우 @RunWith 어노테이션에서 MockitoJUnit44Runner.class를 값으로 지정해주면 된다.

import static org.junit.Assert.*;
import static org.mockito.Mockito.*;

import org.junit.Test;
import org.junit.runner.RunWith;
import org.mockito.Mock;

@RunWith(MockitoJUnit44Runner.class)
public class WriteArticleServiceImplTest {

    @Mock Authenticator authenticator;
    @Mock ArticleDao mockedDao;
    @Mock IdGenerator mockedGenerator;
   
    @Test
    public void setup() {
        when(authenticator.authenticate(anyString(), eq("password"))).thenReturn(null);
        ...
    }

}
또는 테스트가 실행되기 전에 명시적으로 MockitoAnnotations.initMocks(this) 메서드를 호출해주면 된다.

public class WriteArticleServiceImplTest {

    @Mock Authenticator authenticator;
    @Mock ArticleDao mockedDao;
    @Mock IdGenerator mockedGenerator;
   
    @Before
    public void test() {
        MockitoAnnotations.initMocks(this);
    }
   
    @Test
    public void writeArticle() {
        when(authenticator.authenticate(anyString(), eq("password"))).thenReturn(null);







https://javacan.tistory.com/entry/MocktestUsingMockito


#### 단위 테스트는 단순한 디자인의 4가지 원칙을 지켜야 한다. 

1. 모든 테스트로 실행한다 : 단위 테스트는 소스코드와 함께 지속적으로 작성되며 통합된다.
2. 중복 포함을 최소화한다 : 코드에는 가능한 한 중복을 최소화한다.
3. 프로그래머의 의도를 표현한다 : 코드는 이해하기 쉬워야 한다. 테스트에서 중요한 값을 명확하게 강조해 쉽게 읽을 수 있게 하고, 테스트에 좋은 메소드 이름을 지정한다. Hamcrest Matchers, AssertJ와 같은 프레임워크를 사용해 훌룡한 assertion을 작성하자.
4. 클래스 및 메소드 수를 최소화 한다 : 관련된 각 요소는 가능한 한 최소로 한다. 테스트당 조건을 하나만 작성해 단위 테스트를 작게 유지할 수 있다. 또한 단위 테스트의 범위도 가능한 작게 유지한다. 


### 단위 테스트 모범 사례

1. 프로덕션 코드에 문제가 있는 경우에만 테스트가 실패한다. 
2. 테스트는 프로덕션 코드와 관련된 중요한 문제를 찾아야 한다.
3. 테스트는 빠르게 실행돼야 한다.
4. 테스트는 가능한 한 자주 실행된다. 
