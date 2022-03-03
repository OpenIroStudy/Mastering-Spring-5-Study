# 스프링 부트로 REST API 구축하기
애플리케이션은 서로 소통을 하는데 애플리케이션은 다양한 언어(자바, C#, ...)로 구축할 수 있다.

소통 방법은 웹 서비스다. SOAP(Simple Object Access Protocol)를 사용하는 XML-기반 웹 서비스는 초기부터 지금까지 사용됐다.

--------
###### SOAP는 일반적으로 널리 알려진 HTTP, HTTPS, SMTP 등을 통해 XML 기반의 메시지를 컴퓨터 네트워크 상에서 교환하는 프로토콜이다.
###### API(Application Programming Interface)는 프로그래밍 내부에 대해서 몰라도 API 접점만 알고 있다면 우리는 해당 프로그램의 기능을 이용할 수 있다.
--------

현재는 REST 스타일로 구축된 웹 서비스가 주로 사용된다.

## REST란?
REST(Representational State Transfer)는 효율적, 안정적이며 확장가능한 분산시스템을 가져올 수 있는소프트웨어 아키텍처 디자인 제약의 모음을 나타냅니다. 그리고 그 제약들을 준수했을 때 그 시스템은 RESTful하다고 일컬어집니다.

## REST API의 제약 조건
* Client-Server
  * Client는 Server에서 어떤 일을 수행하더라도 내부 작업을 알지 않아도 된다.
  * 이는 플랫폼의 이식성을 향상시킨다.
  * 클라이언트와 서버가 서로 독립적이라 별도의 진화가 가능하다.

* Stateless
  * 클라이언트에서 서버로 각 요청에는 그 요청에 필요한 모든 정보가 포함되어야 한다.(메시지는 스스로 설명 가능해야 한다.)

* Cache
  * 요청에 대한 응답 내의 데이터에 해당 요청은 캐시가 가능한지 불가능한지 명시해야 한다.
  * 보통 HTTP Header에 Cache-Control헤더를 이용한다.

* Layer System
  * Client는 Server와 직접 연결돼서는 안 된다. 요청을 캐시할 수 있으므로 Client는 중간 레이어에서 캐시된 응답을 가져올 수 있다.

* Uniform Interface

* HATEOAS


### Uniform Interface의 4가지 제약 조건
1. Resource-Based
2. Manipluation Of Resources Through Representations
3. Self-Descriptive Message
4. **H**ypermedia **A**s **T**he **E**ngine **o**f **A**pplication **S**tate

1번과 2번을 합쳐서 한 문장으로 하면 **URI로 지정한 리소스를 Http Method를 통해서 표한하고 구분한다.** 이다.

```
https://my-server.com/page?user=guest/menu=login
```
예전에 HTML을 불러오는 방식

page의 파라미터로 guest인지 user인지 admin인지 구분하고 menu 파라미터를 통해서 어떤 메뉴인지 불러오는 방식이다.

하지만 REST API를 적용한다면
```
HTTP Method : get
https://my-server.com/user/login
```
를 통해서 간결하게 표현할 수 있다. 짧은 URI를 통해서 여러 형태의 표현이 가능하다.

```
HTTP Method : POST
https://my-server.com/user/login
```
위의 URL은 login이라는 URI로 POST요청을 보낸다는 뜻이 되는데, 
이것을 미루어 보았을 때 사용자 정보가 POST요청이 되는 것이니 로그인을 할 것이라고 짐작이 가능하다.

#### Self-Descriptive Message
메시지 스스록 자신에 대한 설명을 할 수 있어야 한다.

즉, API 문서가 REST API응답 본문에 존재해야 한다는 것이다.

API 문서 전체를 응답에 넣는 것은 불가능 하지만 적어도 API 문서가 어디 있는지는 알려줘야 한다.

이렇게 되면 서버가 변해서 Response Data가 변경되었다면 클라이언트에서는 해당 API 문서를 통해서 어떤 데이터가 바뀌었는지 알 수 있게 된다.

#### HATEOAS(Hypermedia As The Engine of Application State)
HATEOAS를 한 줄로 표현하면 Hypermedia(링크)를 통해서 애플리케이션의 상태 전이가 가능해야 한다.
또한 Hypermedia(링크)에 자기 자신에 대한 정보가 담겨야 한다.
<img width="574" alt="hateoas" src="https://user-images.githubusercontent.com/82895809/156522492-189dda57-c6d9-409f-9b5e-7621de16328d.png">

위는 웹 서비스들이 RESTful한 구현 결과를 향해 나아가는 그림이다.

이처럼 RESTful API를 사용하는 클라이언트가 전적으로 서버와 동적인 상호작용이 가능하도록 하는 것을 HATEOAS라고 부른다.

이러한 방법은 클라이언트가 서버로부터 어떠한 요처을 할 때, 요청에 필요한(의존되는) URI를 응답에 포함시켜 반환하는 것으로 가능하게 할 수 있다.

```
GET https://my-server.com/article
```
게시글을 조회하는 URI가 있다고 가정하면 여기서 해당 글을 조회한 사용자는 다음 행동으로 어떤 행동을 할까?

* 다음 게시물 조회
* 게시물을 내 피드에 저장
* 댓글 달기

이런 행동들이 바로 상태 전이가 가능한 것들인데, 이것들을 응답 본문에 넣어줘야 한다는 소리이다.

넣는 방법은 Hypermedia를 통해서 넣는다.

```
{
  "data": {
    "id": 1000,
    "name": "게시글 1",
    "content": "HAL JSON을 이용한 예시 JSON",
    "self": "http://localhost:8080/api/article/1000", // 현재 api 주소
    "profile": "http://localhost:8080/docs#query-article", // 해당 api의 문서
    "next": "http://localhost:8080/api/article/1001", // 다음 article을 조회하는 URI
    "comment": "http://localhost:8080/api/article/comment", // article의 댓글 달기
    "save": "http://localhost:8080/api/feed/article/1000", // article을 내 피드로 저장
  },
}
```
이렇게 되면
1. API 버전을 명세하지 않아도 된다.
2. 링크 정보를 동적으로 바꿀 수 있다.
3. 링크를 통해서 상태 전이가 쉽게 가능하다.

위와 같은 방식으로 데이터를 담아 클라이언트에게 보낸다면 클라이언트는 해당 링크를 참조하는 방식으로, 
JPA에서 객체 그래프 탐색을 하는 것처럼 API 그래프 탐색이 가능해진다.

그럼 링크에 대한 정보가 바뀌더라도 클라이언트에선 일일이 대응하지 않아도 된다.

## REST API 설계
<img width="505" alt="RESTmethod" src="https://user-images.githubusercontent.com/82895809/156339970-f980bbe7-e2e8-45e6-8afc-3e9f71caeec4.png">

* GET : 읽기 - 리소스의 세부 정보 검색
* POST : 생성 - 새로운 아이템 또는 리소스 생성
* PUT : 업데이트/교체
* PATCH : 리소스 일부 업데이트/수정
* DELETE : 삭제

## REST API의 예외 처리
REST 서비스에서 중점을 두어야 할 중요한 사항 중 하나는 오류 응답의 응답 상태다.

* 400 BAD REQUEST : 요청 본문이 API 스펙을 충족하지 않는다. 세부사항이 충분하지 않거나 유효성 검사 오류가 있다.
* 401 UNAUTHORIZED : 인증 또는 권한 부여에 실패했다.
* 403 FORBIDDEN : 사용자는 제한을 초과하는 등 다양한 요인으로 인해 작업을 수행할 수 없다.
* 404 NOT FOUND : 리소스가 없다.
* 405 METHOD NOT ALLOWED : 지원되지 않는 작업(예: GET만 허용되는 리소스에서 POST를 시도하는 경우)
* 500 INTERNAL SERVER ERROR : 서버에서 오류가 발생했다. 이상적으로는 이런 일이 발생하지 않아야 한다. 소비자는 문제를 해결할 수 없다.

## 스프링 부트 스타터 HATEOAS
```
<dependency>
 <groupId>org.springframework.boot</groupId>
 <artifactId>spring-boot-starter-hateoas</artifactId>
</dependency>

<dependency>
 <groupId>org.springframework.hateoas</groupId>
 <artifactId>spring-hateoas</artifactId>
</dependency>
```

## REST API의 유효성 검사 구현하기
### 컨트롤러 메소드에서 유효성 검사
@Valid 어노테이션은 유효성 검사를 위한 매개변수를 표시하는 데 사용된다. 

메소드가 실행되기 전에 빈에 정의된 모든 유효성 검사가 실행된다.

## OpenAPI 스펙을 사용한 REST 서비스 문서화
Springfox 스웨거를 사용하면 RESTful 서비스 코드에서 스웨거 문서를 생성할 수 있다. 

또한 스웨거 UI라는 도구는 애플리케이션에 통합돼 사람이 읽을 수 있는 문서를 제공한다.

```
<dependency>
 <groupId>io.springfox</groupId>
 <artifactId>springfox-swagger2</artifactId>
 <version>2.4.0</version>
</dependency>

<dependency>
 <groupId>io.springfox</groupId>
 <artifactId>springfox-swagger-ui</artifactId>
 <version>2.4.0</version>
</dependency>
```

```
@Configuration
@EnableSwagger2
public class SwaggerConfig {
    @Bean
    public Docket api() {
        return new Docket(DocumentationType.SWAGGER_2).select()
            .apis(RequestHandlerSelectors.any())
            .paths(PathSelectors.any())
            .build();
    }
}
```

## REST API 캐싱
서비스의 데이터 캐싱은 애플리케이션의 성능과 확장성을 향상시키는 데 중요한 역할을 한다.

* 캐시란?
  * 웹 캐시는 자주 쓰이는 문서의 사본을 자동으로 보관하는 HTTP장치이다. 웹요청이 캐시에 도착했을 때, 태시된 로컬 사본이 존재한다면, 그 문서는 원 서버가 아니라 그 캐시로 부터 제공된다.

* 캐시의 이점
  * 불필요한 데이터 전송을 줄여서, 네트워크 요금으로 인한 비용을 줄여 준다.
  * 네트워크 병목을 줄여준다. 대역폭을 늘리지 않고도 페이지를 빨리 불어로 수 있게 된다.
  * 원 서버에 대한 요청을 줄여준다. 서버는 부하를 줄일 수 있으며 더 빨리 응답할 수 있게 된다.
  * 페이지를 먼 곳에서 불러올수록 시간이 많이 걸리는데, 캐시는 거리로 인한 지연을 줄여준다.

* E-Tag : 메세지에 담겨있는 엔티티를 위한 엔티티 태그를 제공. 이를 활용하여 리소스를 식별할 수 있다.
* Last-Modified : 엔티티가 마지막으로 변경된 시각에 대한 정보를 제공(파일인 경우 파일 시스템이 제공해 준 최근 변경 시각, 동적으로 생성된 리소스라면 응답이 만들어진 시간)

## 웹 관련 용어
* @RequestParam
  * 1개의 HTTP 요청 파라미터를 받기 위해서 사용한다. 반드시 필요한 변수가 아니라면 required의 값을 false로 설정해둘 수 있다.

* @PathVariable
  * REST API에서 값을 호출할 때 주로 많이 사용한다.

----------------------
Type 1 = http://127.0.0.1?index=1&page=2

Type 2 = http://127.0.0.1/index/1

Type 1의 URL을 처리할 때 @RequestParam을 사용한다.
```
@GetMapping("read")
public ModelAndView getFactoryRead(@RequestParam("no") int factroyId, SearchCriteria criteria) 
{
  //...    
}
```

Type 2의 URL을 처리할 때는 @PathVariable을 사용한다.
```
@PostMapping("delete/{idx}")
@ResponseBody
public JsonResultVo postDeleteFactory(@PathVariable("idx") int factoryIdx) {
	return factoryService.deleteFacotryData(factoryIdx);
}
```

복합적으로 사용도 가능하다.
```
@GetMapping("/user/{userIdx}/invoices")
public List<Invoice> listUsersInvoices(
	 @PathVariable("userIdx") int user,
	 @RequestParam(value = "date", required = false) Date dateOrNull
)	
{//...}
```
--------------------

* @RequestBody
  * 클라이언트가 전송하는 Json형태의 HTTP Body내용을 Java Object로 변환시켜주는 역할을 한다. 그렇기 때문에 Body가 존재하지 않는 HTTP Get메소드에 활용하려고 한다면 에러가 발생.

* @ResponseBody
  * 자바 객체를 HTTP 요청의 Body내용으로 매핑하여 클라이언트로 전송한다. 즉, HTTP 요청 Body를 자바 객체로 전달받을 수 있다.

* @ModelAttribute
  * 클라이언트가 전송하는 multipart/form-data형태의 HTTP Body내용과 HTTP 파라미터의 값들을 생성자나 Setter를 통해 주입하기 위해 사용. 매핑시키는 파라미터의 타입이 객체의 타입과 일치하는지를 포함한 다양한 검증(Validiation)작업이 추가적으로 진행. int형 변수에 String형을 넣으면 BindException이 발생 

* ResponseEntity?
  * ResContorller는 별도의 View를 제공하지 않는 형태로 서비스를 실행한다. 때문에 결과데이터가 예외적인 상황에서 문제가 발생할 수 있다.
  * ResponseEntity는 개발자가 직접 결과 데이터와 HTTP 상태 코드를 직접 제어할 수 있는 클래스로 개발자는 404나 500 ERROR와 같은 HTTP 상태 코드를 전송하고 싶은 데이터와 함께 전송할 수 있기 때문에 좀 더 세밀한 제어가 필요한 경우 사용한다.
```
@Data
@Entity
@Table(name = "student_TBL")
@AllArgsConstructor
@NoArgsConstructor
public class Student {
    @Id
    @GeneratedValue
    private Long id;
    private String studentName;
    private int score;
}

//////////////////////////////////////////////////////////

// ResponseEntity : 데이터 + HTTP status code

@RestController
@RequestMapping("/api/student")
public class StudentRestController {
    @Autowired
    StudentService studentService;
    
    @PostMapping("test")
    ResponseEntity<Student> postStudent(@RequestBody Student student, UriComponentsBuilder uriBuilder){
        Student created = studentService.createOrUpdate(student);
        URI location = uriBuilder.path("api/student/test/{id}").buildAndExpand(created.getId()).toUri();
        HttpHeaders headers = new HttpHeaders();
        headers.setLocation(location);
        
        // return 할 때 data와 HTTP 상태메시지를 함께 전송
        return new ResponseEntity<>(created, headers, HttpStatus.CREATED);
    }
   
```
