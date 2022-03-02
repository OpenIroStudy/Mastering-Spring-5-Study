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
