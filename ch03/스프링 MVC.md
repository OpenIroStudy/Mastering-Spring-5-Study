# 동진 참고하시게

![image](https://user-images.githubusercontent.com/67637716/154502123-bb8ddb98-2f22-41f4-af61-587c0e7a98d2.png)
![image](https://user-images.githubusercontent.com/67637716/154502169-e567181b-a9c2-4cb9-8779-f66cd4e52fc0.png)
따라서 요청이 들어오면 Filter → Interceptor → AOP → Interceptor → Filter 순으로 거치게 된다.

출처: https://goddaehee.tistory.com/154 [갓대희의 작은공간]


1. 서버를 실행시켜 서블릿이 올라오는 동안에 init이 실행되고, 그 후 doFilter가 실행된다. 

2. 컨트롤러에 들어가기 전 preHandler가 실행된다

3. 컨트롤러에서 나와 postHandler, after Completion, doFilter 순으로 진행이 된다.

4. 서블릿 종료 시 destroy가 실행된다.





Filter, Interceptor, AOP의 개념

1.  Filter(필터)

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

필터의 URL-PATTERN을 /*로 정의하면 servlet, jsp뿐만 아니라 이미지와 같은 모든 자원의 요청에도 호출 된다.



[ 필터의 실행메서드 ]

ㆍinit() - 필터 인스턴스 초기화

ㆍdoFilter() - 전/후 처리

ㆍdestroy() - 필터 인스턴스 종료





2. Interceptor(인터셉터)



요청에 대한 작업 전/후로 가로챈다고 보면 된다.



필터는 스프링 컨텍스트 외부에 존재하여 스프링과 무관한 자원에 대해 동작한다. 

하지만 인터셉터는 스프링의 DistpatcherServlet이 컨트롤러를 호출하기 전, 후로 끼어들기 때문에 스프링 컨텍스트(Context, 영역) 내부에서 Controller(Handler)에 관한 요청과 응답에 대해 처리한다.



스프링의 모든 빈 객체에 접근할 수 있다.



인터셉터는 여러 개를 사용할 수 있고 로그인 체크, 권한체크, 프로그램 실행시간 계산작업 로그확인 등의 업무처리



인터셉터의 실행메서드

preHandler() - 컨트롤러 메서드가 실행되기 전

postHanler() - 컨트롤러 메서드 실행직 후 view페이지 렌더링 되기 전

afterCompletion() - view페이지가 렌더링 되고 난 후









# interceptor 사용법
![image](https://user-images.githubusercontent.com/67637716/154502373-64178ad5-e26e-450e-ad23-19fdd882cd2a.png)
HandlerInterceptor 인터페이스 구현  
책에서 나오는 HandlerInterceptorAdator는 deprecated  
![image](https://user-images.githubusercontent.com/67637716/154502538-ccb8961d-30f9-41a0-9644-6b1cbfefeb12.png)
WebMvcConfigurer 구현  
addInterceptors메서드 구현  


