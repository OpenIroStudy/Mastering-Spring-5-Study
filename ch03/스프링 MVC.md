## MVC(Model-View-Controller) 패턴의 개념
소프트웨어 공학에서 사용되는 아키텍처 패턴으로 MVC패턴의 주 목적은 **Business Logic과 Presentation Logic을 분리**하기 위함이다.

Srping MVC
===========
스프링 MVC는 자바 서블릿 기반 웹 애플리케이션을 개발할 때 가장 많이 사용하는 웹 프레임워크다.

스프링 MVC는 깔끔하고 느슨하게 결합된 아키텍처라는 장점이 있다
<hr>

## 모델1 아키텍처
모델1 아키텍처는 자바 기반 웹 애플리케이션을 개발할 때 사용되는 초기 아키텍처 스타일 중 하나다.

* JSP페이지는 브라우저의 요청을 직접 처리한다.
* JSP페이지는 간단한 자바 빈을 포함하는 모델을 사용한다. 
(JavaBeans의 Bean은 자바에서 객체를 의마하는 용어. 즉, JavaBeans는 DB연동에 사용되는 자바 객체들.)
* 아키텍처 스타일의 일부 애플리케이션에서는 JSP가 데이터베이스의 쿼리를 수행한다.
* JSP는 다음에 표시할 페이지인 플로우 로직을 처리한다.

<img src="https://user-images.githubusercontent.com/82895809/154843215-9d7bfb25-c8c3-479b-9e62-524a77709426.png" width="700px" height="500px"></img>

* 접근법에 단점이 많아서 다른 아키텍처로의 진화가 시작됐다.
    * 어려운 관점 분리 : JSP는 다른 데이터를 검색하고 데이터를 표시한다. 다음에 표시할 페이지(플로우)를 결정하고 때로는 비즈니스 로직까지 책임졌다.
    * 복잡한 JSP : JSP가 많은 로직을 처리했기 때문에 거대해지고 유지 관리하기가 어려웠다.

## 모델2 아키텍처
모델2 아키텍처는 여러 책임이 있은 복잡한 JSP와 관련된 복잡성을 해결하기 위해 도입됐다. 이것은 MVC 아키텍처 스타일의 기반을 형성한다.

<img src="https://user-images.githubusercontent.com/82895809/154870730-9cdc8c51-1083-4840-9442-7bbfd9357a81.png" width="300px" height="500px"></img>

모델2 아키텍처는 모델, 뷰와 컨트롤러 간의 역할을 명확하게 구분해 유지 관리가 좀 더 쉬운 애플리케이션이 만들어진다.
* 모델 : 뷰를 생성하는 데 사용할 데이터를 나타낸다.
* 뷰 : 모델을 사용해 화면을 렌더링한다.
* 컨트롤러 : 플로우를 제어한다. 브라우저에서 요청을 가져와 모델을 채우고 뷰로 전환한다.

## 모델2 프론트 컨트롤러
모델2 아키텍처의 기본 버전에서 브라우저의 요청은 다른 서블릿(또는 컨트롤러)에 의해 직접 처리된다.

여러 비즈니스 시나리오에서 요청을 처리하기 전에 서블릿에서 몇 가지 일반적인 작업을 수행하려고 한다.

모델2 프론트 컨트롤러 아키텍처에서는 모든 요청이 프론트 컨트롤러라는 단일 컨트롤러로 전달된다.

<img src="https://user-images.githubusercontent.com/82895809/154873339-fd9e65b7-38eb-4f38-be41-d8cb94b74b5b.png" width="300px" height="500px"></img>

* 어떤 컨트롤러가 요청을 실행할지 결정한다.
* 렌더링할 뷰를 결정한다.
* 좀 더 일반적인 기능을 추가하는 조항을 제공한다.
* 스프링 MVC는 프론트 컨트롤러에서 MVC패턴을 사용한다. 프론트 컨트롤러를 DispatcherServlet이라고 한다.

## 스프링 MVC 여섯 가지 기본 플로우
DispatcherServlet은 프론트 컨트롤러 패턴의 구현이다. 스프링 MVC의 모든 요청은 프론트 컨트롤러, 즉 DispatcherServlet이 처리할 것.
#### component-scan
* 특정 패키지 내의 클래스를 스캔하고 annotation(@Component @Controller @Service @Repository)을 확인한 후 Bean인스턴스로 생성한다. 
* 이를 이용하면 @Autowired와 @Qualifier annotation을 인식할 수 있다.
#### mvc:annotation-driven 
* spring MVC컴포넌트들을 디폴트 설정을 통해 활성화한다.
* spring MVC @Controller에 요청을 보내기 위해 필요한 HandlerMapping과 HandlerAdapter를 Bean으로 등록한다.
   * HandlerMapping : HTTP 요청정보를 이용해서 컨트롤러를 찾아주는 기능
   * HandlerAdapter : HandlerMapping을 통해 찾은 컨트롤러를 직접 실행하는 기능을 수행


### 플로우1 : 뷰가 없는 컨트롤러
* @Controller : 요청 매핑을 포함할 수 있는 스프링 MVC 컨트롤러를 정의한다.
* @RequesetMapping(value = "/xxx"): URL /xxx을 xxx메소드에 매핑하는 것을 정의. 브라우저가 /xxx에 요청을 보내면 스프링 MVC는 xxx메소드 실행.
* @ResponseBody: 특정 컨텍스트에서 xxx메소드가 리턴한 텍스트는 응답 콘텐츠로 브라우저에 전송된다.

### 플로우2 : 뷰(JSP)로 간단한 컨트롤러 플로우 만들기
이전 플로우에서는 브라우저에 표시할 텍스트가 컨트롤러에 하드 코딩됐는데 보통은 브라우저에 표시할 콘텐츠는 일반적으로 뷰에서 생성된다.
JSP를 뷰로 사용할 때 에는 InternalResourceViewResolver를 빈으로 등록해야한다.

``` java
@Controller
public class BasicViewController {
   @RequestMapping(value = "/welcome-view")
   public String welcome() {
      return "welcome";
   }
}
```
src/main/webapp/WEB-INF/views/welcome.jsp를 생성하면 스프링 MVC는 welcome메소드에서 리턴된 문자열을 /WEB-INF/views/welcome.jsp의 실제 JSP로 매핑한다.
뷰 리졸버의 구성을 보면 알 수 있다.

뷰 리졸버는 뷰 이름을 실제 JSP페이지로 해석한다.

``` java
<bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">

   <property name="prefix">
      <value>/WEB-INF/views/</value>
   </property>
   
   <property name="suffix">
      <value>.jsp</value>
   </property>

</bean>
```
* InternalResourceViewResolver : JSP를 지원하는 뷰 리졸버로 보통 JstlView가 사용된다.
* <property name=...</property> : 맵 뷰 리졸버에서 사용할 접두사와 접미사로 뷰 리졸버는 컨트롤러 메소드에서 문자열을 받아 뷰를(접두사 + 뷰 이름 + 접미사)로 변환한다. 따라서 뷰 이름인 welcome은 /WEB-INF/views/welcome.jsp로 해석된다.

### 플로우3 : 모델이 있는 뷰로 전환하는 컨트롤러
일반적으로 뷰를 생성하려면 일부 데이터를 뷰에 전달해야 한다. 스프링 MVC에서는 모델을 사용해 데이터를 뷰에 전달할 수 있다.

``` java
@Controller
public class BasicModleMapController {
   @RequestMapping(value = "/welcome-model-map")
   public String welcome(ModleMap model) {
      model.put("name", "XYZ");
      return "welcome-model-map";
   }
}
```
* public String welcome(ModelMap model) : 새로 추가된 매개변수는 ModelMap모델이다. 스프링 MVC는 모델을 인스턴스화해 메소드에서 사용할 수 있도록 한다. 모델에 통합된 속성은 뷰에서 사용할 수 있다.
* model.put("name", "XYZ") : 모델에 name이라는 속성 이름과 XYZ 값을 추가한다.

``` jsp
Welcome ${name}! This is coming from a model-map - a JSP
```
![modelmap](https://user-images.githubusercontent.com/82895809/155110365-831dd4ad-963d-49cd-9e5e-2028c852a5eb.png)

${name)은 EL(Expression Language)구문을 사용해 모델의 속성에 액세스한다.

### 플로우4 : ModelAndView를 사용해 뷰로 전환하는 컨트롤러
이전 플로우에서는 뷰 이름을 반환하고 뷰에서 사용할 속성으로 모델을 채웠다. 

스프링 MVC는 모델과 뷰 세부 정보가 모두 포함된 단일 객체를 반환하는 대체 접근법을 제공한다. 

> Model, ModelMap vs ModelAndView
>  > Model, ModelMap
>  > * addAttribute를 사용한다.
>  > * Model or ModelMap에 데이터만 저장 후 View에서 사용목적
>  > ModelAndView
>  > * addObject를 통해 데이터만 저장
>  > * setViewName을 통해 이동하고자 하는 View를 저장

```
@Controller
public class BasicModelViewController {
   @RequestMapping(value = "/welcome-model-view")
   public ModelAndView welcome(ModelMap model) {
      model.put("name", "XYZ");
      return new ModelAndView("welcome-model-view", model);
   }
}
```
* return new ModelAndView("welcome-model-view", model) : 적절한 뷰 이름과 모델을 가진 ModelAndView 객체를 만든다.
``` jsp
Welcome ${name}! This is coming from a model-view - a JSP
```
![modelandview](https://user-images.githubusercontent.com/82895809/155110404-6fd710f5-150b-4228-b86f-8b4ef607c97f.png)

### 플로우5 : 폼이 있는 뷰로 전환하는 컨트롤러
사용자의 입력을 캡처하기 위한 간단한 폼

**User클래스 생성**
```java
public class User {
   private ...
   ...
   //toString
}
```

**폼을 표시하는 컨트롤러 메소드**
```java
@RequestMapping(value = "/create-user", method = RequestMethod.GET)
public String showCreateUserPage(ModelMap model) {
   model.addAttribute("user", new User());
   return "user";
}
```
* model.addAttribute("user", new User()) : 빈 폼 백업 객체로 모델을 설정할 때 사용.

**폼으로 뷰 만들기**
JSP는 스프링 프레임워크에서 지원하는 뷰 기술 중 하나다. 스프링 프레임워크를 사용하면 태그 라이브러리를 제공해 JSP로 뷰를 쉽게 만들 수 있다.
```java
<%@ taglib prefix="접두사" uri="URI" %>
```
![taglib](https://user-images.githubusercontent.com/82895809/155114116-62acf594-c563-4ff6-897a-28b04323e93c.png)

```java
<form:from method="post" modelAttribute="user">
   ...
</form:form>
```
스프링 폼 태그를 사용하면 폼 백업 객체(modelAttribute="user")의 값이 폼에 자동으로 바인딩되고, 폼을 제출할 때 폼의 값이 자동으로 폼 백업 객체에 바인딩된다.

**컨트롤러가 폼 제출을 처리하는 메소드 가져오기**
사용자가 폼을 제출하면 브라우저는 POST요청을 보낸다. 요청을 처리하는 메소드
```java
@RequestMapping(value = "/create-user", method = RequestMethod.POST)
public String addTode(User user) {
   logger.info("user details " + user);
   return "redirect:list-users";
}
```
* public String addTodo(User user) : 폼 백업 객체를 매개변수로 사용한다. 스프링 MVC는 폼의 값을 폼 백업 객체에 자동으로 바인딩한다.

### 플로우6 : 이전 플로우에 유효성 검사 
이전 플로우에서는 폼을 추가했지만 폼의 값을 확인하지는 않았다. 폼 내용의 유효성을 검사하기 위해 자바스크립트를 작성할 수는 있지만, 

서버에서 유효성 검사를 실행해야 더 안전한다.

**하이버네이트 밸리데이터 의존 관계 추가**
```java
<dependency>
   <groupId>org.hibernate</groupId>
   <artifactId>hibernate-validator</artifactId>
   <version>5.0.2.Final</version>
</dependency>
```

빈 밸리데이션 API는 빈의 속성에 지정할 수 있는 여러 가지 유효성 검사를 지정한다.
```java
@Size(min = 6, message = "Enter at least 6 characters")
private  String name;
```

컨트롤러 메소드를 가져와 폼의 유효성 검사
```java
@RequestMapping(value = "/create-user-with-validation", method = RequestMethod.POST)
public String addTodo(@Valid User user, BindingResult result) {
   if(result.hasErrors()) {
      return "user";
   }
   
   logger.info("user details " + user);
   
   return "redirect:list-users";
}
```
* public String addTodo(@Valid User user, BindingResult result) : @Vaild 어노테이션이 사용되면 스프링 MVC는 빈의 유효성을 검증한다. 유효성 검사 결과는 BindingResult 인스턴스 결과에서 사용할 수 있다.
> @Valid vs @Validated
>  > @Validated는 @Valid의 기능을 포함한다. 쉽게 @Valid를 적용한 곳이라면 이는 @Validated로 변경 가능하다. 더하여 @Validated는 검토할 검토 옵션의 그룹을 지정할 수 있다.
>  > @Vaild를 적용시킬 때는 제약조건을 달아놓은 속성에 대해 전부 유효성 검사를 하게 되는데, 만약 제약조건은 그대로 선언해놓되 원하는 속성만 유효성 검사를 하고싶은 경우에 사용하는 것이 @Validated이다.

**@Validated 에시**
```java
public class UserAccount {
    
    @NotNull(groups = BasicInfo.class)
    @Size(min = 4, max = 15, groups = BasicInfo.class)
    private String password;
 
    @NotBlank(groups = BasicInfo.class)
    private String name;
 
    @Min(value = 18, message = "Age should not be less than 18", groups = AdvanceInfo.class)
    private int age;
 
    @NotBlank(groups = AdvanceInfo.class)
    private String phone;
    
    // standard constructors / setters / getters / toString   
}
```
```java
@RequestMapping(value = "/saveBasicInfoStep1", method = RequestMethod.POST)
public String saveBasicInfoStep1(
  @Validated(BasicInfo.class) 
  @ModelAttribute("useraccount") UserAccount useraccount, 
  BindingResult result, ModelMap model) {
    if (result.hasErrors()) {
        return "error";
    }
    return "success";
}
```

**사용자 지정 유효성 검사 구현**
@AssertTrue 어노테이션을 사용해 좀 더 복잡한 사용자 지정 유효성 검사를 구현할 수 있다.
```java
@AssertTure(message = "Password fields don`t match")
private boolean isValid() {
   return this.password.equals(this.password2);
}
```
이러한 방법으로 여러 필드를 가진 복잡한 유효성 검사 로직을 구현할 수 있다.

## 스프링 MVC
스프링 MVC 프레임워크의 중요한 기능
* 각 객체에 잘 정의된 독립적인 역할을 가진 느슨하게 결홥된 아키텍처다.
* 매우 유연한 컨트롤러 메소드 정의다. 컨트롤러 메소드는 다양한 범위의 매개변수와 리턴 값을 가질 수 있다. 프로그래머는 필요에 맞는 정의를 유연하게 선택할 수 있게 된다.
* 도메인 객체를 폼 백업 객체로 재사용할 수 있으며 별도의 폼 객체가 필요하지도 않다.
* 현지화를 지원하는 내장 태그 라이브러리(spring, spring-form)가 있다.
* 모델은 키-값 쌍이 있는 HashMap을 사용하며 여러 뷰 기술과 통합할 수 있다.
* 유연한 바인딩이다. 바인딩하는 동안 불일치되는 타입은 런타임 에러 대신 유효성 검사 에러로 처리할 수 있다.
* 단위 테스트 컨트롤러를 위한 MockMVC 프레임워크가 있다.

**스프링 MVC 아키텍처의 주요 컴포넌트**
![springmvc](https://user-images.githubusercontent.com/82895809/155123512-90940f88-91c2-4a51-ac5d-8530f451f7be.png)

## 스프링 MVC의 핵심 개념
### RequestMapping
RequestMapping은 URI를 컨트롤러 또는 컨트롤러 메소드에 매핑하는 데 사용된다. 클래스나 메소드 레벨에서 수행할 수 있다. 선택적 메소드 매개변수를 사용하면 메소드를 특정 요청 메소드(GET, POST 등)에 매핑할 수 있다.

### 뷰 리솔루션
스프링 MVC는 매우 유연한 뷰 리솔루션, 다중 뷰 옵션을 제공한다.
* JSP, 프리마커와의 통합
* 다중 뷰 리솔루션 전략
* 명시적으로 정의된 선호도 순서로 뷰 리졸버 체인을 지원한다.
* 콘텐츠 협상을 사용해 XML, JSON 및 Atom을 직접 생산한다.

#### 프리마커 구성
프리마커(FTL : Freemarker Template Language)는 널리 사용되는 자바 템플릿 엔징 중 하나다. 스프링 애플리케이션에서 프리마커를 사용해 뷰를 생성할 수 있다.
![freemarker](https://user-images.githubusercontent.com/82895809/155125155-b7549b45-b298-416b-8ab6-898e15fc9d5e.png)

### 공통 프로세스에 대한 고민

공통 업무에 관련된 코드를 모든 페이지마다 작성 해야한다면 중복된 코드가 많아지게 되고 

프로젝트 단위가 커질 수록 서버에 부하를 줄 수도 있으며, 소스관리도 되지 않는다.

공통 부분은 빼서 따로 관리하는게 좋은데, 이러한 공통업무를 프로그램 흐름의 앞, 중간, 뒤에 추가하여 자동으로 처리할 수 있는 방법이 있다.

1. Filter
2. Interceptor
3. AOP

출처: https://goddaehee.tistory.com/154 [갓대희의 작은공간]
![fia](https://user-images.githubusercontent.com/82895809/155128016-eb11b839-ab80-4cd4-a792-c5e75076c379.png)

요청이 들어오면 Filter → Interceptor → AOP → Interceptor → Filter 순으로 거치게 된다.

1. 서버를 실행시켜 서블릿이 올라오는 동안에 init이 실행되고, 그 후 doFilter가 실행된다. 

2. 컨트롤러에 들어가기 전 preHandler가 실행된다

3. 컨트롤러에서 나와 postHandler, after Completion, doFilter 순으로 진행이 된다.

4. 서블릿 종료 시 destroy가 실행된다.





Filter, Interceptor, AOP의 개념

#### Filter(필터)
말그대로 요청과 응답을 거른뒤 정제하는 역할을 한다.

서블릿 필터는 DispatcherServlet 이전에 실행이 되는데 필터가 동작하도록 지정된 자원의 앞단에서 요청내용을 변경하거나,  여러가지 체크를 수행할 수 있다.

또한 자원의 처리가 끝난 후 응답내용에 대해서도 변경하는 처리를 할 수가 있다.

보통 web.xml에 등록하고, 일반적으로 인코딩 변환 처리, XSS방어 등의 요청에 대한 처리로 사용된다.

EX)

<!-- 한글 처리를 위한 인코딩 필터 -->

<filter>

    <filter-name>encoding</filter-name>

    <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>

    <init-param>

        <param-name>encoding</param-name>

        <param-value>UTF-8</param-value>

    </init-param>

</filter>

<filter-mapping>

    <filter-name>encoding</filter-name>

    <url-pattern>/*</url-pattern>

</filter-mapping>

해당 필터의 이름은 encoding, 값은 UTF-8인 파라미터를 정의하고 있다. 
필터의 URL-PATTERN을 /* 로 정의하면 servlet, jsp뿐만 아니라 이미지와 같은 모든 자원의 요청에도 호출 된다.

[ 필터의 실행메서드 ]

* init() - 필터 인스턴스 초기화
* doFilter() - 전/후 처리
* destroy() - 필터 인스턴스 종료


#### Interceptor(인터셉터)
요청에 대한 작업 전/후로 가로챈다고 보면 된다.

필터는 스프링 컨텍스트 외부에 존재하여 스프링과 무관한 자원에 대해 동작한다. 

하지만 인터셉터는 스프링의 DistpatcherServlet이 컨트롤러를 호출하기 전, 후로 끼어들기 때문에 스프링 컨텍스트(Context, 영역) 내부에서 Controller(Handler)에 관한 요청과 응답에 대해 처리한다.

스프링의 모든 빈 객체에 접근할 수 있다.

인터셉터는 여러 개를 사용할 수 있고 로그인 체크, 권한체크, 프로그램 실행시간 계산작업 로그확인 등의 업무처리

자바에서는 **HandlerInterceptor**라는 인터페이스가 interceptor의 역할을 한다.

여기서 handler라는 단어가 있는데 여기서는 Controller의 역할로 사용한다.

통상적으로 handler는 Controller를 포함한 HttpRequestHandler, WebRequestHandler 등과 같이 Dispatcher Servlet과 함께 일하는 것들을 통칭한다.

Controller는 Dispatcher Servlet과 협업하여 web request를 실행하고 view를 반환하기 때문에 handler라고 불려진다.

**HandlerInterceptor정의**
* public boolean preHandle : 핸들러 메소드가 호출되기 전에 호출된다. (Controller 동작 이전)
* public void postHandle : 핸들러 메소드가 호출된 후 호출된다. (Controller 동작 이후, View 동작 이전)
* public void afterCompletion : 요청 처리가 완료된 후 호출된다. (View 동작 이후)

![deprecated](https://user-images.githubusercontent.com/82895809/155250962-49a9410f-e1f2-44fa-8541-50eb69c2c092.png)
**현재 HandlerInterceptorAdapter는 deprecated가 되었다.**

![interceptor](https://user-images.githubusercontent.com/82895809/155252108-4958b5e7-cf6b-4b17-8221-c374c3cb78a7.png)
* /test라는 요청이 들어오면 DispatcherServlet이 HandlerMapping을 통해 /test라는 요청에 해당하는 Controller를 선택하도록 시킨다.
* Interceptor의 preHandle 메소드가 /test 요청을 Controller가 동작하기 이전에 가로챈다.
* Controller가 실행
* Interceptor의 postHandle 메소드가 Controller가 실행된 이후 동작한다.
* postHandle 메소드가 실행되고 View가 실행되어 response를 클라이언트에게 전달한다.
* Interceptor의 afterCompletion 메소드가 View가 실행된 이후에 동작한다.

#### interceptor 사용법
![image](https://user-images.githubusercontent.com/67637716/154502373-64178ad5-e26e-450e-ad23-19fdd882cd2a.png)
↑ HandlerInterceptor 인터페이스 구현

![image](https://user-images.githubusercontent.com/67637716/154502538-ccb8961d-30f9-41a0-9644-6b1cbfefeb12.png)
↑ WebMvcConfigurer 구현, addInterceptors메서드 구현  

### 모델 속성
공통 사항은 일반적으로 @ModelAttribute 어노테이션으로 표시된 메소드를 사용해 모델에 채워진다.

@ModelAttribute 어노테이션으로 표시할 수 있는 메소드 수에 제한이 없다는 점이다.

컨트롤러 어드바이스를 사용하면 여러 컨트롤러에서 모델 속성을 공통으로 지정할 수 있다.

### 세션 속성 사용
스프링 MVC는 세션에 저장되는 속성을 지정하는 단순 타입 레벨(클래스 레벨) 어노테이션인 @SessionAttributes를 제공한다.

#### HttpSession
기본적으로 HttpSession을 이용해서 session에 값을 넣고, session에서 값을 가져올 수 있다.

* setAttribute()
```java
public class SampleController {
    @GetMapping(value="/hello")
    public String httpSession(HttpSession session){
        Long memberId =100L;
        session.setAttribute("test",memberId);
        return String.valueOf(memberId);
    }
}
```

* getAttribute()
```java
public class SampleController {
    @GetMapping(value="/hello_2")
    public String getHttpSession(HttpSession session){
        Long memberId = (Long)session.getAttribute("memberId");
        return String.valueOf(memberId);
    }
}
```

#### @SessionAttribute
@SessiongAttribute는 요청 session에서 명시한 key에 해당하는 값을 바인딩해준다.

HttpSession을 직접 사용할 경우, getAttribute는 Object 형식으로 반환하지만 @SessionAttribute를 사용하면, 형식에 맞게 알아서 바인딩을 해준다.
``` java
public class SampleController {
    @GetMapping(value="/hello_3")
    public String sessionAttribute(@SessionAttribute("memberId") Long id){
        return String.valueOf(id);
    }
}
```

위 예시처럼 @SessionAtrribute("key-name")으로 원하는 값을 지정할 수도 있지만, 바인딩할 변수와 key 이름이 같은 경우 key-name 지정이 생략 가능하다.
``` java
public class SampleController {
    @GetMapping(value="/hello_3")
    public String sessionAttribute(@SessionAttribute Long memberId){
        return String.valueOf(memberId);
    }
}
```
#### @SessionAttributes
@SessionAttributes로 선언된 session-key와 같은 이름의 modelAttribute-key가 model에 등록된다면, @SessionAttributes가 알아서 session으로 바인딩 해준다.
``` java
@RestController
@SessionAttributes("memberId")
public class SampleController {
    @GetMapping(value="/login")
    public String login(Model model, @RequestParam String memberId){
        model.addAttribute("memberId", memberId);
        return memberId;
    }
}
```
@SessionAttributes로 "memberId"를 선언했으므로, model에 "memberId" 이름의 키를 추가할 때, 자동적으로 세션에 추가된다.

#### @SessionAttributes 사용 이유
@SesssionAttributes는 컨트롤러 전역에 걸쳐, 핸들러끼리 session을 공유하기 위해 사용된다.

예를 들면, 회원 가입 시 여러 페이지를 거치는 경우처럼 '여러 핸들러에서 같은 자원을 반복적으로 사용할테니,
modelAttributes에 해당 자원이 추가되면, 알아서 세션에도 추가해줘라'라고 선언하는 것이다.

#### 세션에서 속성 제거
사용을 마쳤을 때는 SessionStatus로 세션을 무력화한다.
```java
@GetMapping(value="/complete")
public String complete(SessionStatus sessionStatus){
    sessionStatus.setComplete();
    return "hello";
}
```
### InitBinder의 필요성
파라미터의 수집은 다른 용어로 binding(바인딩)이라고 한다.

일반적인 웹 폼에는 날짜, 통화, 금액이 있다. 폼의 값은 폼 백업 오브젝트에 바인딩돼야 한다.

대부분 데이터타입은 자동으로 스프링이 변환해서 처리해주지만
경우에 따라서는 파라미터를 직접 변환해주어야 할 때가 있다.
```java
@InitBinder
public void initBinder(WebDataBinder binder) {
	SimpleDateFormat dateFormat = new SimpleDateFormat("yyyy-MM-dd");
	binder.registerCustomEditor(java.util.Date.class, new CustomDateEditor(dateFormat, false));
}
```
### @ContorllerAdvice 어노테이션을 사용해 공통 기능 구현하기
컨트롤러 레벨에서 정의한 일부 기능은 애플리케이션에서 공통적으로 나타날 수 있다.

예를 들어 애플리케이션에서 동일한 날짜 형식을 사용할 수 있어서 앞에서 정의한 @Initbinder를 애플리케이션 전체에 적용할 수 있다.

@ControllerAdvice는 기본적으로 모든 RequestMapping에서 공통적인 기능을 만들도록 도와준다.

컨트롤러 어드바이스는 공통 모델 속성(@ModelAttribute)와 일반적인 예외 처리를 정의하는 데 사용할 수도 있다.

## 스프링 MVC 고급 기능
* 웹 애플리케이션의 일반적인 예외 처리
* 메시지 국제화
* 스프링 시큐리티

### 예외 처리 구현하기
예외 처리 전략을 세우는 것은 상당히 중요하다. 잘 설계되고 잘 작성된 애플리케이션의 경우에도 예외 처리는 제대로 수행해야 한다.

스프링 프레임워크에서 대부분 예외는 확인되지 않은 예외로 렌더링됐다. 
특정 예외 처리가 필요 없으면 일반적으로 애플리케이션 전체에서 예외를 처리할 수 있다.

#### 예외 복구 범위
* 메소드 영역 : 메소드 영역은 종속된 복구 기능으로 단순히 try, catch 사용하면 된다.
* 클래스 영역 : 클래스 내 공통 예외 복구는 @ExceptionHandler 사용할 수 있다.
* 전역 영역 : 여러 클래스의 공통 예외 복구는 @ControllerAdvice 사용할 수 있다.

### 컨트롤러 내 예외처리 (@ExceptionHandler)
```java
@RestController
@RequestMapping("/ex")
public class PersonController {
    @GetMapping
    public String test(){
        return "test";
    }

    @GetMapping("/exception")
    public String exception1(){
        throw new NullPointerException();
    }

    @GetMapping("/exception2")
    public String exception2(){
        throw new ClassCastException();
    }

    @ExceptionHandler({NullPointerException.class, ClassCastException.class})
    public String handle(Exception ex){
        return "Exception Handle!!!";
    }
}
```
/ex/exception이라는 api를 호출하면 NullPointerException이 발생하지만 @ExceptionHandler를 사용하여
"Exception Handle!!!"라는 문자열을 리턴할 것이다. (/ex/exception2를 호출해도 결과는 똑같다.)

이렇듯 다중 Exception도 하나의 ExceptionHandler에서 처리할 수 있는 것을 확인할 수 있다.

### 컨트롤러 전반의 일반적인 예외 처리 (@ControllerAdvice)
```java
@RestControllerAdvice
public class ControllerSupport {

    @ExceptionHandler({NullPointerException.class, ClassCastException.class})
    public String handle(Exception ex) {
        return "Exception Handle!!!";
    }
}
```
↑ ControllerSupport클래스 생성

그리고 두 개의 Controller를 생성하여 Exception 발생
```java
@RestController
@RequestMapping("/school")
public class SchoolController {
    @GetMapping
    public String test(){
        return "test";
    }

    @GetMapping("/exception")
    public String exception1(){
        throw new NullPointerException();
    }

    @GetMapping("/exception2")
    public String exception2(){
        throw new ClassCastException();
    }
}

@RestController
@RequestMapping("/person")
public class PersonController {
    @GetMapping
    public String test(){
        return "test";
    }

    @GetMapping("/exception")
    public String exception1(){
        throw new NullPointerException();
    }

    @GetMapping("/exception2")
    public String exception2(){
        throw new ClassCastException();
    }
}
```
컨트롤러 내에서는 Exception만 발생시키고 Exception을 처리하는 로직은 존재하지 않는다. api를 호출하면
동일하게 Exception 처리에 대한 메시지를 전달해준다.

@ExceptionHandler뿐만 아니라 @InitBinder, @ModelAttibute 또한 사용할 수 있다.

#### @RestControllerAdvice와 @ControllerAdvice의 차이
@Controller와 @RestController와 동일하게 @ResponseBody의 유무 차이만 있다.

그러므로 결과 타입을 Json으로 내려줄지 아니면 View 페이지로 전달할지에 따라서 사용하면 된다.
출처 : https://incheol-jung.gitbook.io/docs/q-and-a/spring/controlleradvice-exceptionhandler
