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
4. *H*ypermedia As The Engine of Application State
