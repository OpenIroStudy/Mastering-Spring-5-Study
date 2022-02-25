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

> 정리 ::
> CGLIB Proxy는 Enhancer라는 클래스를 바탕으로 Proxy를 생성 -> SuperClass 클래스 반드시 지정.
> CGLIB은 Byte코드를 조작해서 바이너리가 만들어지기 때문에 JDK Dynamic Proxy보다 성능적으로 우세.
> 다만, final 객체 또는 private 접근자로 된 메서드는 상속이 지원되지 않기 때문에 제약적인 Proxy구현.

> InvocationHandler, MethodInterceptor 는 Spring AOP에서 JoinPoint
> MethodMatcher는 Spring AOP에서 PointCut
> invoke, Intercept 메서드는 Spring AOP에서 Advice개념과 일치.

내부적으로는 이렇게 돌아가지만 실제 현업에서는 어노테이션으로 편하게 AOP 지원.  

