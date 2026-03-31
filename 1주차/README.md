# 1주차 Study
## Spring

### 1. Spring과 SpringBoot의 차이
#### Spring의 문제점
-  XML 설정 파일을 직접 작성해야함
-  여러 라이브러리 끼리의 의존성을 수동으로 관리해야함
-  별도의 WAS(톰캣)를 설치하고 WAR 파일로 실행했어야 함
-  위와 같은 이유로 매우 오래 걸리는 초기 세팅
#### SpringBoot의 등장
-  `spring-boot-starter-web` 하나만으로 웹 개발에 필요한 의존성을 한번에 불러올 수 있음
 - `@SpringBootApplication` 어노테이션으로 클래스 패스에 있는 라이브러리를 스캔해 필요한 설정을 알아서 해줌
 - Spring Boot는 톰캣을 내장하고 있어 별도의 서버 설치 없이 실행 가능

요약하면 Spring Boot는 복잡한 Spring의 기본 설정을 미리 설정해놓은 프레임워크

### 2. IOC/DI, AOP, PSA, POJO의 각각의 역할은?
#### IOC/DI
  - IoC (제어의 역전, Inversion of Control):   
  원래는 개발자가 new 키워드로 직접 객체를 생성하고 관리했음. 
  하지만 스프링에서는 개발자가 `@Component` 나 `@Beam` 어노테이션만 붙이면 프레임워크(컨테이너)가 객체(빈)를 대신 만들고 관리함.
  - DI (의존성 주입 Dependency Injection): 객체를 생성할 때, 스프링 프레임워크의 컨테이너가 해당 객체를 생성할 때 필요한 객체(이미 만들어 놓은 빈)를 전달하여 생성함.
  > DI 에는 생성자 주입과 수정자 주입, 필드 주입이 있음  
  > 각각 생성자, setXXX, `@Autowired` 를 사용
  > - 생성자 주입을 사용하는 이유  
  > 1. 불변성 - 한 번 주입된 의존성은 애플리케이션 종료 때까지 변경불가
  > 2. 누락방지 - 의존성 없는 상태에선 컴파일 자체가 안됨
  > 3. 순환참조 방지 - 두 객체가 서로를 참조하는 순환 참조 문제 있다면 애플리케이션 실행 시 에러 발생
  > 4. 테스트 용이 - 스프링 없이 순수 자바 코드를 짤 때 의존성 주입 용이
  - 장점 : 결합도 낮춤, 코드 재사용성 및 유지보수성 향상, 테스트 용이
#### AOP
AOP(관점 지향 프로그래밍, Aspect Oriented Programming)
- 비즈니스 로직과 공통 로직(로직, 트랜잭션 처리 등)을 분리하여 중복코드 제거
##### AOP 용어
- Aspect(관점): 공통 기능을 모듈화 한 것(로그 기능 등)
- Target(대상): 실제 Aspect 적용받는 클래스나 메서드
- Advice(언제/무엇을): 실질적으로 수행할 코드(실질적인 구현체)
- Join Point(합류 지점): Advice가 적용될 수 있는 모든 가능한 지점
- Pointcut(어디에): 여러 Join Point 중 실제로 Advice를 적용할 지점을 선별하는 규칙
##### AOP 동작 방식 
스프링은 프록시(가짜 객체)를 사용하여 AOP를 구현함.   
Target을 직접 호출하면 Target 내부의 구현을 바꾸지 않고서야 공통 로직을 삽입할 수 없기 때문에, 프록시를 사용.
> **프록시 동작 순서**
> 1. 사용자가 sayHello() 메서드 호출
> 2. 호출이 Target에게 바로 전달되지 않고, 프록시에 전달
> 3. Before Advice(메서드 실행 전 할 일) 실행
> 4. 실제 Target 메서드 호출
> 5. After Advice(메서드 실행 후 할 일) 실행
> 6. 결과 반환
- 주의할 점
외부에서 호출할 때는 프록시를 거치지만, 내부에서 메서드를 거쳐 호출할 때는 프록시를 사용하지 않음  
>예시: 같은 클래스의 A메서드가 B메서드 호출 시 B메서드에 AOP 걸려 있더라도 작동하지 않음

#### PSA(Portable Service Abstraction)
- Spring의 핵심 요소(IoC/DI, AOP, PSA) 중 하나로 특정 기술에 종속되지 않도록 서비스 기능을 추상화하여 일관된 방식으로 사용할 수 있게 하는 것 
##### PSA 핵심 개념
- 추상화 - 특정 기술의 내부 구현을 숨기고 개발자는 인터페이스만 바라봄
- 이식성 - 구현 기술이 바뀌어도 개발자는 동일한 인터페이스를 보기 때문에 기술 변경이 자유로움
##### 예시
- `@Transactional` - 트랜잭션을 처리할 때 하부 기술이 JDBC인지, JPA인지 관련 없이 동일한 방식으로 처리 가능
- Repository - DB 구현체가 MySQL이든, MongoDB든 관계 없이 추상화된 JpaRepository 인터페이스 사용
#### POJO(Plain Old Java Object)
##### POJO 이전(EJB)
- 반드시 특정 인터페이스 구현해야함
- 컨테이너 안에서만 동작
- 설정 복잡
- 테스트 어려움
- 비즈니스 로직이 아닌 프레임워크 코드가 클래스에 포함됨
```java
public class UserServiceBean implements SessionBean {
    public void ejbActivate() {}
    public void ejbPassivate() {}
    public void ejbRemove() {}
}
```
##### POJO의 등장
순수한 자바 객체를 통해 EJB의 문제를 해결하기 위해 등장   
객체는 순수한 자바 객체로 두고, 필요한 기능은 외부에서 주입
- 테스트 쉬움
- 재사용성 높음
- 결합도 낮음

> Q: 그럼 스프링 프레임워크는 POJO인가?  
> A: 여전히 POJO로 취급  
> `@Service`, `@Entity` 등 어노테이션이 붙어있어 완벽한 POJO는 아니지만, 클래스 구조 자체를 바꾸지 않고, 상속을 강제로 하는 경우도 없으므로 POJO라고 봄

### 3. @Transactional의 동작 원리에 대해 설명하세요.
`@Transactional`은 AOP 기반으로 프록시 객체가 메서드 호출을 가로채서 트랜잭션을 관리함  
프록시 내부에선 트랜잭션 매니저를 호출하고, DB 커넥션을 가져온 후 autoCommit = false(기본) 설정을 한 이후에야 트랜잭션 시작  
메서드 정상 종료 시 커밋, 예외 발생 시 롤백(기본 설정 상 RuntimeException -> 롤백, CheckedException -> 커밋, 설정 변경 가능)   
`@Transactional`을 통해 여러 작업을 하나로 묶어, 모두 성공 혹은 모두 롤백 하도록 할 수 있음

#### `@Transactional`의 전파 옵션
- REQUIRED(기본) - 기본 트랜잭션이 있으면 합류, 없으면 트랜잭션 생성 -> 하나의 트랜잭션으로 묶임
- REQUIRES_NEW - 항상 새로운 트랜잭션 생성, 기존 트랜잭션은 잠시 중단 -> 완전히 독립된 트랜잭션   
inner 롤백 되어도 outer 정상 진행 가능하고, inner 커밋 이후에 outer가 롤백 되더라도 inner는 독립적으로 커밋
- 이외 MANDATORY, NESTED, SUPPORTS, NOT_SUPPORTED 등 있음  
> Q: autoCommit 설정이 뭐지?  
> A: autoCommit = true 설정을 하면 SQL 하나마다 자동으로 commit 해버림 -> 한 줄 한 줄이 독립적인 트랜잭션  
> 사실상 트랜잭션을 묶어서 롤백 처리하는 게 불가

> Q: `@Transactional(readOnly = true) 설정이 실제로 하는 일은?  
> A: 성능 최적화를 위해서 사용
> - Dirty Checking 최소화 
> - flush 최소화 
> - DB에 따라 다르지만 쓰기를 아예 막아주지는 않음

### 4. Filter와 Interceptor에 대해 설명해주세요.
Filter와 Interceptor는 모두 요청을 가로채는 역할을 하지만 동작위치와 목적이 다르다. 
#### Filter
서블릿 스펙에서 제공, Http요청이 들어오면 Dispatcher Servlet 이전에 필터가 가로챔  
스프링과 직접적인 연관 없고 요청/응답을 직접 변경 가능  
> 주요 용도
> - 인증(JWT)
> - 인코딩 처리
> - CORS 처리

#### Interceptor
Spring MVC에서 제공, preHandle과 postHandle로 컨트롤러 전/후를 가로챔  
Dispatcher Servlet 이후에 있어, 스프링 빈 사용 가능, 핸들러 단위로 동작  
> 주요 용도
> - 로그인 체크(세션 기반)
> - 권한 검사
> - API 실행 시간 측정
> - 컨트롤러 전/후 공통 처리

> Q: 언제 무엇을 쓰는 게 좋지?  
> A: JWT, 보안, 요청 자체를 차단해야할 때는 컨트롤러로 접근 전에 거르는 필터를 사용  
세션 기반 로그인 여부 체크나 권한 검사, 로깅에는 인터셉터를 사용

#### 예외 처리
- Filter - Spring Context 밖에서 동작하므로 Controller Advice를 통해 전역적으로 예외 처리가 힘듦  
- Interceptor - Spring Context 안에서 동작하므로 전역 예외 처리 가능

