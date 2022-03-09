## 스프링 시큐리티를 이용한 시큐리티 REST API
REST API를 빌드할 때 고려해야 할 가장 중요한 기능은 인증 및 권한 부여이다.

스프링 시큐리티는 자바 세계에서 웹 애플리케이션과 REST API를 보호하기 위해 선택한 프레임워크다.

## REST API 시큐리티
* 인증 : 사용자 자격 증명을 확인하고 API에 유효한 사용자인지 확인하는 과정이 인증이다.
* 권한 부여 : 사용자에게 작업을 수행할 수 있는 권한이 있는지 확인하는 프로세스를 권하 부여라고 한다.

## REST API 시큐리티 구현
<img width="1050" alt="스크린샷 2022-03-09 오후 9 14 11" src="https://user-images.githubusercontent.com/82895809/157440067-dd3a8114-d481-4ae2-a516-6bd6732a2ecf.png">

일반적으로 API가 실행되기 전에 필터를 실행할 수 있다. 위의 그림은 일반적인 구현 방식이다.

1. REST API 요청은 URL, 요청 데이터와 사용자 자겨 증명 정보를 제공받아 실행된다.
2. 필터가 인증을 확인하고 권한을 검사한다.
3. REST API요청이 실행된다.

## 스프링 시큐리티 스타터
```
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-security</artifactId>
</dependency>
```

* 스프링 부트 스타터 시큐리티 자동 설정
<img width="866" alt="스크린샷 2022-03-09 오후 9 28 58" src="https://user-images.githubusercontent.com/82895809/157442016-aacaf217-927c-4957-8229-c0c6ee1202be.png">

자동 구성되는 기본 인증 방법을 기본 인증이라고 한다.

<img width="1359" alt="스크린샷 2022-03-09 오후 9 29 40" src="https://user-images.githubusercontent.com/82895809/157442103-d59ba0c7-1608-42e2-b054-1dbe2a385b27.png">

기본 자동 구성 사용자 ID는 user이며 기본 시크릿번호는 서버 시작 시 로그에 프린트된다.

* 시큐리티르 비활성호하기 위해 단위 테스트 업데이트하기
```

@WebMvcTest(value = XXX.class, secure = false)

```

secure = false 매개변수로 단위 테스트의 스프링 시큐리티르 비활성화한다.

#### @WebMvcTest
------------------

Application Context 완전하게 Start 시키지 않고 web layer를 테스트 하고 싶을 때 @WebMvcTest를 사용하는 것을 고려한다. Present Layer 관련 컴포넌트만 스캔을 한다.

Service, Repository dependency가 필요한 경우에는 @MockBean으로 주입받아 테스트를 진행 한다. 

@SpringBootTest의 경우 모든 빈을 로드하기 때문에 테스트 구동 시간이 오래 걸리고, 테스트 단위가 크기 때문에 디버깅이 어려울 수 있다. Controller 레이어만 슬라이스 테스트 하고 싶을 때에는 @WebMvcTest를 쓰는게 유용하다.

------------------


