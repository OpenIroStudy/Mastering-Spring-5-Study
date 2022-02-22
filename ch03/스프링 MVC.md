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

