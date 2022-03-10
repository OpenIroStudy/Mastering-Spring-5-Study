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

secure = false 매개변수로 단위 테스트의 스프링 시큐리티를 비활성화한다.

#### @WebMvcTest
------------------

Application Context 완전하게 Start 시키지 않고 web layer를 테스트 하고 싶을 때 @WebMvcTest를 사용하는 것을 고려한다. Present Layer 관련 컴포넌트만 스캔을 한다.

Service, Repository dependency가 필요한 경우에는 @MockBean으로 주입받아 테스트를 진행 한다. 

@SpringBootTest의 경우 모든 빈을 로드하기 때문에 테스트 구동 시간이 오래 걸리고, 테스트 단위가 크기 때문에 디버깅이 어려울 수 있다. Controller 레이어만 슬라이스 테스트 하고 싶을 때에는 @WebMvcTest를 쓰는게 유용하다.

------------------

## 스프링 시큐리티 필터
모든 시큐리티 구현에서 요청이 실행되기 전에 인증하고 권한이 부여되도로 필터르 사용한다.

스프링 시큐리티는 요청이 실행될 권한이 부여되기 전에 인증 및 권한 부여를 확인하기 위해 필터 체인을 사용한다.

<img width="1077" alt="스크린샷 2022-03-10 오전 12 50 15" src="https://user-images.githubusercontent.com/82895809/157477801-1524505e-300f-4614-badc-d1538fbf5a93.png">

스프링 시큐리티 필터 체인은 모든 요청에서 인증 및 권한 부여를 체크한다. 요청에 적절한 자격 증명이나 권한이 없으며 요청이 거부되고 오류가 발생한다.

## 스프링 시큐리티의 인증
인증 필터는 일반적으로 인증 매니저에게 위임되는 것을 확인 가능하다.

AuthenticationManager(ProviderManager)는 여러 AuthenticationProvider와 통신한다.

AuthenticationManager는 interface다.

ProviderManager는 AuthenticationManager interface의 구현체 클래스이다.

## WebSecurity
인증 및 권한이 필요 없는 URL을 구성하기 위해 configure를 override해서 특정 요청에 대해서는 시큐리티 설정을 무시하도록 하는 등 전체에 관한 설정을 한다.

대부분의 웹 경로를 다 보안으로 제어를 하는데 정적 리소스에 대해서는 무시하라고 ignoring()에 매칭 표현식을 추가해준다.

## HttpSecurity
스프링 시큐리티의 각종 설정은 HttpSecurity로 대부분 하게 된다.

```
    /**
     * 스프링시큐리티 앞단 설정들을 할 수 있다.
     */
    @Override
    public void configure(WebSecurity web) throws Exception {
        // resources 모든 접근을 허용하는 설정을 해버리면
        // HttpSecurity 설정한 ADIM권한을 가진 사용자만 resources 접근가능한 설정을 무시해버린다.
        web.ignoring()
                .antMatchers("/resources/**");
    }

    /**
     * 스프링시큐리티의 설정을 할 수 있다.
     */
    @Override
    public void configure(HttpSecurity http) throws Exception {
        // WebSecurity에 접근 허용 설정을 해버리면 이 설정이 적용되지 않는다.
        http.authorizeRequests()
                .antMatchers("/resources/**").hasRole("ADIM") // no effect
                .anyRequest().authenticated();
    }
```

---------------------

스프링 시큐리티의 각종 설정은 HttpSecuriy로 한다. WebSecurity 스프링 시큐리티 앞단의 설정들을 하는 객체이므로 HttpSecurity 설정한 스프링 시큐리티 설정이 override 되는 설정이 있는 경우도 있다.

---------------------

## 인증(Authentication)과 인가(Authorization)
* 인증 : 보호된 리소스에 접근하는 것을 허용하기 이전에 등록된 유저의 신원을 입증(validating)하는 과정.
* 인가 : 요청된 리소스에 접근할 수 있는 권한이 있는 인증된(Authenticated) 유저인지 입증(validating)하는 과정.

1. 인증하기 (Request Header) - 매번 인증해야 한다.
2. 인증 유지하기 (Browser) - 로컬, 쿠키 등에 사용자의 정보를 저장.
3. 안전하게 인증하기 (Server) - Session 활용. 클라이언트에서 사용자의 raw한 데이터를 가지고 있지 않아 해킹에 대해 큰 위험은 없다. 만료기간 설정 가능.
서버가 많아질 경우(로드 밸런스가 있을 경우) 세션 DB를 만들어 세션을 따로 저장. 하지만 클라이언트가 많아질 경우 DB 과부하.
4. 효율적으로 인증하기 (Token) - 정보의 요청과 응답안에 사용자의 state를 담는 것이 Token을 활용한 인증 방법.
JWT (Json Web Token) : JWT는 해독하기 쉬워서 민감한 정보(비밀번호)를 담지 않는다. 시크릿 키를 사용해 JWT를 만들어 내고 시크릿 키를 사용해서 JWT의 인증과정을 거침.
Token이 클라이언트로 부터 서버로 요청이 감 -> 서버는 토큰의 유효성 검사를 본인이 가진 시크릿 키로 진행 -> 유효하지 않다면 버리고 유효하다면 사용자 정보 파악
토큰으로 상태관리를 하기에 따로 세션을 둘 필요가 없다.
5. 다른 채널을 통해 인증하기 (OAuth)

## OAuth
### OAuth란?
다른 웹사이트 상의 자신들의 정보에 대해 접근 권한을 부여할 수 있는 공통적인 수단이자 개방형 표준.
구글 로그인, 페이스북 로그인, 깃헙 로그인 등이 사용하는 인증절차를 OAuth라고 한다.

### OAuth 인증절차
<img width="700" alt="Oauth인증절차1" src="https://user-images.githubusercontent.com/82895809/157562677-0ce0f359-d27f-4f82-9ff6-99666557b30e.png">
<img width="700" alt="Oauth인증절차2" src="https://user-images.githubusercontent.com/82895809/157562740-916a33fa-2746-4db3-806b-9c2936820517.png">
