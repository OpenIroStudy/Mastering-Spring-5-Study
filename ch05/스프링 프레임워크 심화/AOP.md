# 크로스 컷팅
스프링 애플리케이션은 레이어 구조로 만들어진다.  
일반적으론 프리젠테이션, 비즈니스, 데이터 레이어로 나뉘어진다.  
이것을 3 Tier Architecture 라고 한다.  

* 프리젠테이션 레이어
  * 애플리케이션 소비자에 중점을 두고, REST API 또는 사용자 인터페이스를 제공
* 비즈니스 레이어
  * 비즈니스 로직에 중점을 둔다.
* 데이터 레이어
  * 데이터베이스 및 외부 애플리케이션과의 통합에 중점을 둔다.

애플리케이션에는 여러 레이어를 가로질러 잘라내는 기능도 있다.  
* 로깅
* 시큐리티
* 성능 추적
* ...

여러 레이어를 가로지르는 이러한 문제를 크로스 컷팅이라고 한다.  
크로스 컷팅을 각각의 레이어에서 구현한다면 유지 관리는 어려워지기 때문에, 한 곳에서 구현하고 여러 레이어에 적용한다.  
이를 가능케 하는 프로그래밍 패러다임을 AOP(aspect-oriented programming, 관점 지향 프로그래밍)라 한다.  

AOP는 핵심 기능와 공통 기능를 분리하여 코드의 재사용성을 높여주는 프로그래밍 기법이다.  
AOP의 기본 개념은 핵심 기능에 공통 기능을 삽입하는 것.  
즉, 핵심 기능에만 집중할 수 있도록 중복해서 작성해야 하는 코드들을 따로 빼놓는 프로그래밍 방법이다.
![image](https://user-images.githubusercontent.com/67637716/155657805-709d9a68-12a8-4e69-960a-e6610c48faf6.png)

<br>

## 중요한 AOP 용어
![image](https://user-images.githubusercontent.com/67637716/155653099-182ac2e9-40d8-4954-b414-c2ad042a72b3.png)  
* 핵심관심사항
  * 핵심 비지니스 로직을 다루는 관심 사항이다.
* 공통관심사항
  * 보안, 트랜잭션 등등 여러 객체에 공통으로 적용되는 관심사항이다.
  * 횡단 관심사를 의미한다.
* Target
  * 핵심사항이 구현된 객체를 말한다. 핵심 비지니스 로직를 가진 객체이다.
* JoinPoint
  * 공통관심사항이 적용될 수 있는 지점을 말한다.
  * 즉 Advice를 적용 가능한 지점을 의미한다.
  * 메서드 호출, 예외발생, 필드값 수정 또는 객체 생성이 JoinPoint가 될 수 있다.
* Advice
  * 공통관심사항의 구현 코드로 적용 시점을 정의 한다.
  * 핵심 비지니스 로직을 중심으로 언제 공통관심사항이 실행될 지를 정의한다.
  * around, Before, After, after-returning, after-throwing 와 같은 선언방식으로 사용한다.
* Aspect
  * JoinPoint와 Advice를 아우르는 표현이다.
  * 트랜잭션이나 보안등이 Aspect의 좋은 예이다.
* Weaving
  * JoinPoint에 Advice를 삽입하는 과정으로 핵심 비지니스 로직에 전혀 영향을 주지 않은 상태에서 공통관심기능을 추가하는 AOP의 핵심적인 처리과정이다.
  * 컴파일시, 클래스로딩시, 런타임시에 위빙할 수 있다.

<br>
Spring AOP는 런타임 시 Proxy를 이용하여 횡단 로직을 수행 할 수 있도록 한다.  
Spring AOP에서 사용되는 Proxy는 2가지가 있다.  

* JDK Dynamic Proxy - java library에서 제공  
* CGLIB Proxy - CGLIB library 추가해야함  

# AOP 동작 원리
### Proxy란?
![image](https://user-images.githubusercontent.com/67637716/155658414-bd181dea-7863-4f1f-a7d5-8d3614a03718.png)
> Proxy란 마치 자신이 클라이언트가 사용하려고 하는 실제 대상인 것처럼 위장해서 클라이언트의 요청을 받아주는 것을 대리자, 대리인과 같은 역할을 하는 객체의 의미한다.
> 그리고 프록시를 통해 최종적으로 요청을 위임받아 처리하는 실제 오브젝트를 타깃(Target) 또는 실체(Real Object) 라고 부른다.

Spring에서는 Proxy를 이용해 객체지향의 5대원칙 중 하나인 OCP(Open-Close Principal : 개방폐쇄의 원칙)을 적용하고 있다.
> OCP (Open-Close Principal : 개방 폐쇄의 원칙)
> 개방-폐쇄 원칙(OCP, Open-Closed Principle)은 '소프트웨어 개체(클래스, 모듈, 함수 등등)는 확장에 대해 열려 있어야 하고, 수정에 대해서는 닫혀 있어야 한다.'는 프로그래밍 원칙이다.  
실제 Target에 대한 수정을 하지 않으면서, Proxy를 통해 추가적인 코드를 작성 하여 기능을 확장 시킬 수 있음을 의미한다.

### JDK Dynamic Proxy
JDK dynamic proxy는 내부적으로 Reflection을 이용.  
Proxy Factory에 의해 런타임 시 동적으로 만들어지는 오브젝트이다.  
JDK Dynamic Proxy는 <b>반드시 Interface가 정의 되어야 Proxy 생성한다.</b>  
따라서 Interface 선언에 대한 강제성이 있다는 단점이 있다.  
이전에는 직접 수동으로 Proxy객체를 만들어서 사용하였지만 요즘은 자동 프록시 생성기라는 모듈을 이용하여 Dynamic Proxy를 생성한다.  

내부적으로 Dynamic Proxy는 <b>InvocationHandler라는 Interface를 구현</b>하여 만들어 지는데 InvocationHandler의 invoke함수를 Override하여 Proxy의 위임 기능을 수행한다.  
이 과정에서는 Object에 대해 Reflection 기능을 사용해서 기능을 구현하기 때문에 성능 하락의 원인이 되기도 한다.  

MethodMatcher 구현
``` java
import core.aop.pointcut.MethodMatcher;

import java.lang.reflect.Method;

public class StartMethodMatcher implements MethodMatcher {
    private static final String TALK_PREFIX = "start";

    @Override
    public boolean matches(Method method) {
        final String methodName = method.getName();

        return methodName.startsWith(TALK_PREFIX);
    }
}
```

Invocation Handler 
``` java
import core.aop.pointcut.MethodMatcher;
import lombok.RequiredArgsConstructor;

import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;

@RequiredArgsConstructor
public class UpperCaseHandler implements InvocationHandler {

    private final Car car; // 타깃 오브젝트 주입
    private final MethodMatcher methodMatcher; // Method를 선택적으로 Proxy화 하기 

    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        final String methodName = (String) method.invoke(car, args); // 타깃 메서드 호출에 모두 
        if (methodMatcher.matches(method)) {
            return methodName.toUpperCase();
        }
        return methodName;
    }
}
```

### CGLIB Proxy
Enhancer를 바탕으로 Proxy를 구현하는 방식.  
JDK Dynamic Proxy와는 다르게 Reflection을 사용하지 않고, Extends 방식을 이용해서 Proxy화 할 메서드를 오버라이딩 하는 방식  
실제 Proxy로 핸들링할 Handler가 필요한데, CGlib 에서는 이를 <b>MethodInterceptor 라는 인터페이스로 정의</b>.

MethodInterceptor 구현
``` java
@RequiredArgsConstructor
public class UpperCaseInterceptor implements MethodInterceptor {

    private final MethodMatcher methodMatcher;

    @Override
    public Object intercept(Object obj, Method method, Object[] args, MethodProxy proxy) throws Throwable {
        final String methodName = (String) proxy.invokeSuper(obj, args);
        if (methodMatcher.matches(method)) {
            return methodName.toUpperCase();
        }
        return methodName;
    }
}
```

Enhancer클래스 바탕으로 Target Class를 상속받아 생성
``` java
   /* given */
        Enhancer enhancer = new Enhancer();
        enhancer.setSuperclass(CarTarget.class);
        enhancer.setCallback(new UpperCaseInterceptor(new StartMethodMatcher()));

        /* when */
        final Car proxiedCar = (Car) enhancer.create();

        /* then */
        assertThat(proxiedCar.start("huisam")).isEqualTo("CAR HUISAM STARTED!");
        assertThat(proxiedCar.stop("huisam")).isEqualTo("Car huisam stopped!");
```

> CGLIB Proxy는 Enhancer라는 클래스를 바탕으로 Proxy를 생성 -> SuperClass 클래스 반드시 지정.  
> CGLIB은 Byte코드를 조작해서 바이너리가 만들어지기 때문에 JDK Dynamic Proxy보다 성능적으로 우세.  
> 다만, final 객체 또는 private 접근자로 된 메서드는 상속이 지원되지 않기 때문에 제약적인 Proxy구현.  

> InvocationHandler, MethodInterceptor 는 Spring AOP에서 JoinPoint  
> MethodMatcher는 Spring AOP에서 PointCut  
> invoke, Intercept 메서드는 Spring AOP에서 Advice개념과 일치.  

### 정리
* 내부적으로는 이렇게 돌아가지만 실제 현업에서는 어노테이션으로 편하게 AOP 지원.  
* Spring AOP는 Proxy를 기반으로 한 Runtime Weaving 방식이다.
* Spring AOP에서는 JDK Dynamic Proxy 와 CGlib 을 통해 Proxy화 한다.
* JDK Dynamic Proxy는 Reflection을 기반으로 이루어지고 CGlib 은 상속을 기반으로 이루어진다.

<br>

# AOP 사용하기
AspectJ는 자바에서 가장 널리 사용되는 AOP 프레임워크다. AspectJ는 컴파일 타임 위빙을 제공한다.  
스프링 AOP는 AspectJ와 몇 가지 기본 AOP기능과의 통합을 제공하며 , 런타임 위빙을 수행한다.  
간단하게 사용할 수 있지만, 스프링 빈에서 메소드 호출만 인터셉트 할 수 있다.  

스프링 빈으로 작업 중이고 스프링 빈에서 메소드 호출을 인터셉트하려는 경우, 스프링 AOP면 충분하다.  
스프링 컨테이너에 의해 관리되지 않는 객체의 메소드 호출을 가로채려면 완전한 AOP 프레임워크인 AspectJ를 사용해야 한다.  

스프링 부트로 AOP를 사용하기 위해 사용할 스타터는 spring-boot-starter-aop다.
spring-boot-starter-aop는 스프링aop와 aspectJ의 의존 관계를 추가한다.  

의존성 주입 후 Aspect를 사용한다고 활성화 해야함.  
@Configuration에 @EnableAspectAutoProxy를 추가 
=====> @SpringBootApplication안에 @EnableAutoConfiguration에 포함되어있음.  
@EnableAutoConfiguration으로 사용하는 AOP방식이 CGLIB으로 되어있음.  
JDK Dynamic proxy를 사용하고자 할때 @EnableAspectJAutoProxy를 추가하여 커스텀 해주면 됨.  

1. Aspect 선언
Aspect로 이용하기 위해서는 bean으로 등록해야한다.  
하지만 @Aspect만으로는 bean등록이 되지않아서 component-scan으로 bean등록이 될 수 있도록 @Component를 함께 선언한다.  
``` java
@Aspect
@Component
public class TimeCheckAspect {}
```

2. PointCut 선언
Aspect를 선언했으니 어떤 joinPoint에서 advice를 실행 시킬 것인지 PointCut을 선언해야함.  
``` java
@Pointcut("execution(public * personal.ykh.sample.target.*.*(..))")
private void timeCheckPointCut() {}
```

3. Advice 구성하기
target method를 실행하는 부분은 joinPoint.proceed()이다.  
``` java
// pointcut 메서드와 advice 메서드를 합쳐 한번에 표현할 수 있다.
// @Around("personal.ykh.sample.TimeCheckAspect.timeCheckPointCut()")
@Around(pointcut = "execution(public * personal.ykh.sample.target.*.*(..))")
spublic Object timeCheck(ProceedingJoinPoint joinPoint) throws Throwable {
    Stopwatch stopwatch = Stopwatch.createStarted();
    Object proceed = joinPoint.proceed();
    stopwatch.stop();
    System.out.println("time : " + stopwatch.elapsed(TimeUnit.MILLISECONDS) + "ms");
    return proceed;
}
```

## Annotations
### @Pointcut
포인트 컷이란 메소드의 Joinpoint에 대한 Advice가 언제 실행될 지를 지정하는데 사용한다.  
여러 메소드의 Joinpoint시점 여러개를 묶어 하나의 pointcut으로 만들 수 있다.  
* pointcut에 대한 표현식을 값으로 가짐
* @Pointcut이 적용된 메소드는 무조건 리턴타입이 void

### 포인트 컷 표현식
![image](https://user-images.githubusercontent.com/67637716/155667739-1c0ec19a-a90f-40b1-9dfd-e4c3fa3ec9ef.png)

1. 포인트 컷의 지정자   
포인트 컷의 표현식은 항상 특정 지정자를 선언하여 시작한다.  
* @Pointcut(execution(...))
* @Pointcut(@annotation(...))
우리가 사용하고 있는 execution이나 @annotation 지정자를 PointCut Designator이라하며 "PCD"라 불리고 있다.  
PCD는 타깃의 여러 조인 포인트 중에 어드바이스를 어디에 적용을 시킬지 AOP에게 알려주는 키워드이다.  

1-1. execution -기본적인 PCD
``` java
@Pointcut(
          "execution("    // PCD execution 지정
        + "[접근제한자 패턴] "  // public
        + "리턴타입 패턴"       // long
        + "[패키지명, 클래스 경로 패턴]"          // com.moong.ahea.UserService
        + "메소드명 패턴(파라미터 타입 패턴|..)"  // .findUserId(String)
        + "[throws 예외 타입 패턴]"             // throws RuntimeException
        +")"   
          )
```
[]은 생략이 가능한 옵션을 의미하고 |는 OR조건을 의미.  
``` java
// 모든 패키지에 포함된 클래스 중에, 클래스 이름이 Service로 끝나는 클래스
@Pointcut("execution( * *..*Service.findUserId(..) )")

// 최소한의 필수 규칙들 → 리턴타입 메소드명(파라미터)
@Pointcut("execution( * findUserId(..) )")

// find로 시작하는 메소드
@Pointcut("execution( * find*(..) )")
```  

1-2. within -패키지, 클래스를 제한하자
execution과 비슷하지만, within은 메소드가 아닌 특정 타입에 속한 메소드를 포인트 컷으로 설정할 때 사용.  
``` java
// UserService안에 있는 모든 메소드에 어드바이스 주입
@Pointcut("within(com.moong.ahea.UserService)")
```

1-3. this와 target -생성된 Proxy에 대한 PCD
* this -> CGLIB
* target -> JDK Dynamic Proxy
this는 Proxy에 대한 조인 포인트이고, target은 타깃에 대한 조인 포인트를 사용할 수 있다.  
this는 CGLIB방식으로 생성된 Proxy일때 사용되고, target은 JDK Dynamic Proxy로 Proxy가 생성될 때 사용.  

ex) 
``` java
class UserService implements DefaultService{
  ...
}

// UserService는 인터페이스를 상속받기 때문에 JDK DynamicProxy를 사용하여 Proxy bean 생성, target PCD 사용
@Pointcut("target(com.moong.ahea.UserService)")
```

1-4. args -파라미터 값이 궁금하다면?
타깃의 메소드 호출에서 사용되는 전달받은 파라미터 값이 궁금하다면 args PCD를 사용하면 됨.  

1-5. @annotation -해당 어노테이션 지정
``` java
@Around("@annotation(kr.co.openlabs.mtf.client.aop.GlobalTransaction)")
```

<br>

### aspect 클래스 정의하기
AspectJ는 메서드 호출을 위한 여러 가지 인터셉션 포인트를 제공한다.  
* @Before : 메서드를 실행하기 전
* @After : 메서드 실행 후. 메서드가 예외를 발생시키더라도 실행
* @AfterReturning : 메서드가 성공적으로 실행된 후, 메소드 반환값 획득 가능
* @AfterThrowing : 메서드 호출 후 예외가 발생했을 시
* @Around : 메소드 호출 자체를 가로채서 비즈니스 메소드 실행 전&후 모두에 처리할 로직을 삽입할 수 있음.

``` java
 @Aspect 
    @Component 
    public class LogAspect { 
        // Aspect : 부가 기능 구현체들을 포함하고 있는 모듈 
        private final Logger logger = LoggerFactory.getLogger(this.getClass()); 
       
        // PointCut : 적용할 지점 또는 범위 선택 
        @Pointcut("execution(public * com.example.demo.service..*(..))") 
        private void publicTarget() { } 
        
        // Advice : 실제 부가기능 구현부 
        @Around("publicTarget()")
        public Object calcPerformanceAdvice(ProceedingJoinPoint pjp) throws Throwable { 
            logger.info("성능 측정을 시작합니다."); 
            StopWatch sw = new StopWatch(); 
            sw.start(); 
            // 비즈니스 로직 (메인 로직) 
            Object result = pjp.proceed(); 
            sw.stop(); 
            logger.info("성능 측정이 끝났습니다."); 
            logger.info("걸린시간: {} ms", sw.getLastTaskTimeMillis()); return result; } 
        }
        }
```

<br>

### JoinPoint에서 제공하는 메서드들
Signature getSignature()  
 - 클라이언트가 호출한 메서드의 시그니처 (리턴타입, 이름, 매개변수) 정보가 저장된 Signature 객체를 리턴  
 - getSignature() 메서드가 리턴하는 Signature 객체를 사용하면 호출되는 메서드에 대한 다양한 정보를 얻을 수 있는데 Signature 객체가 제공하는 메서드들은 다음과 같음
![image](https://user-images.githubusercontent.com/67637716/155682181-30203e77-631e-4d4e-8a43-5001d9ecafe4.png)
 
Object getTarget()  
 - 클라이언트가 호출한 비즈니스 메서드를 포함하는 비즈니스 객체를 리턴(해당 클래스 객체를 리턴)

Object[] getArgs()  
 - 클라이언트가 메서드를 호출할 때 넘겨준 인자 목록을 Object 배열로 리턴

@Annotation의 속성값 가져오기  
![image](https://user-images.githubusercontent.com/67637716/155682465-8b929d67-7511-47d4-b482-1689c4152315.png)

