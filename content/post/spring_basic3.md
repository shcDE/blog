---
title: "스프링의 기본에 대해 알아볼까요?(3)"
date: 2022-08-10T17:41:50+09:00
categories:
- development
tags:
- development
keywords:
- backend, java, development, spring
image: spring-gfd1d6e339_1920.jpg
---
![spring2](https://github.com/shcDE/pictures/blob/main/images_for_blog/spring-gfd1d6e339_1920.jpg?raw=true)
________________________________________________________________________________________________________________________________________________________________________
안녕하세요. shcDE입니다. 이번 포스팅에서는 스프링에서 기본적으로 쓰이는 싱글톤 컨테이너와 컴포넌트 스캔에 대해 알아보겠습니다. 시작 전에, 해당 포스팅은 김영한님의 ['스프링 핵심 원리 - 기본편'](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-%EA%B8%B0%EB%B3%B8%ED%8E%B8/dashboard) 섹션 5, 6를 공부하여 정리한 내용임을 밝히며, 직접 해당 강의를 구매하여 게시물을 작성함을 알립니다.
________________________________________________________________________________________________________________________________________________________________________
우선 스프링이 없는 순수한 DI 컨테이너 테스트는 다음과 같이 진행합니다.
________________________________________________________________________________________________________________________________________________________________________
```java
package hello.core.singleton;
import hello.core.AppConfig;
import hello.core.member.MemberService;
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;
import static org.assertj.core.api.Assertions.*;
public class SingletonTest {
      @Test
      @DisplayName("스프링 없는 순수한 DI 컨테이너")
      void pureContainer() {
          AppConfig appConfig = new AppConfig();
          //1. 조회: 호출할 때 마다 객체를 생성
          MemberService memberService1 = appConfig.memberService();
          //2. 조회: 호출할 때 마다 객체를 생성
          MemberService memberService2 = appConfig.memberService();
          //참조값이 다른 것을 확인
          System.out.println("memberService1 = " + memberService1); System.out.println("memberService2 = " + memberService2);
          //memberService1 != memberService2
          assertThat(memberService1).isNotSameAs(memberService2);
      }
}
```
________________________________________________________________________________________________________________________________________________________________________
싱글톤 패턴을 적용한 예제 코드를 보겠습니다. 이 때 예제 코드는 main이 아닌 test 위치에 생성해야 합니다.
________________________________________________________________________________________________________________________________________________________________________
```java
package hello.core.singleton;
public class SingletonService {
        //1. static 영역에 객체를 딱 1개만 생성해둔다.
        private static final SingletonService instance = new SingletonService();
        //2. public으로 열어서 객체 인스터스가 필요하면 이 static 메서드를 통해서만 조회하도록 허용한다.
        public static SingletonService getInstance() {
                return instance;
        }
        //3. 생성자를 private으로 선언해서 외부에서 new 키워드를 사용한 객체 생성을 못하게 막는다. 
        private SingletonService() {
        }
        public void logic() { System.out.println("싱글톤 객체 로직 호출");
        } 
}
```
________________________________________________________________________________________________________________________________________________________________________
이제 싱글톤 패턴을 사용하는 테스트 코드를 보겠습니다.
________________________________________________________________________________________________________________________________________________________________________
```java
@Test
@DisplayName("싱글톤 패턴을 적용한 객체 사용") public void singletonServiceTest() {
        //private으로 생성자를 막아두었다. 컴파일 오류가 발생한다. //new SingletonService();
        //1. 조회: 호출할 때 마다 같은 객체를 반환
        SingletonService singletonService1 = SingletonService.getInstance();
        //2. 조회: 호출할 때 마다 같은 객체를 반환
        SingletonService singletonService2 = SingletonService.getInstance();
        //참조값이 같은 것을 확인
        System.out.println("singletonService1 = " + singletonService1); 
        System.out.println("singletonService2 = " + singletonService2);
        // singletonService1 == singletonService2
        assertThat(singletonService1).isSameAs(singletonService2);
        singletonService1.logic();
}
```
________________________________________________________________________________________________________________________________________________________________________
이제부터 스프링 컨테이너를 사용해보겠습니다. 테스트 코드는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
```java
@Test
@DisplayName("스프링 컨테이너와 싱글톤")
void springContainer() {
      ApplicationContext ac = new AnnotationConfigApplicationContext(AppConfig.class);
      //1. 조회: 호출할 때 마다 같은 객체를 반환
      MemberService memberService1 = ac.getBean("memberService", MemberService.class);
      //2. 조회: 호출할 때 마다 같은 객체를 반환
      MemberService memberService2 = ac.getBean("memberService", MemberService.class);
      //참조값이 같은 것을 확인
      System.out.println("memberService1 = " + memberService1); 
      System.out.println("memberService2 = " + memberService2);
      //memberService1 == memberService2
      assertThat(memberService1).isSameAs(memberService2);
}
```
________________________________________________________________________________________________________________________________________________________________________
이제 싱글톤 방식의 주의점에 대해 살펴보겠습니다. 상태를 유지할 경우 발생하는 문제점 예시 코드는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
```java
package hello.core.singleton;
public class StatefulService {
        private int price; //상태를 유지하는 필드
        public void order(String name, int price) { 
                System.out.println("name = " + name + " price = " + price); 
                this.price = price; //여기가 문제!
        }
        public int getPrice() {
                return price;
        } 
}
```
________________________________________________________________________________________________________________________________________________________________________
상태를 유지할 경우 발생하는 문제점 예시 코드는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
```java
package hello.core.singleton;
import org.assertj.core.api.Assertions;
import org.junit.jupiter.api.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
import org.springframework.context.annotation.Bean;
public class StatefulServiceTest {
    @Test
    void statefulServiceSingleton() {
        ApplicationContext ac = new AnnotationConfigApplicationContext(TestConfig.class);
        StatefulService statefulService1 = ac.getBean("statefulService", StatefulService.class);
        StatefulService statefulService2 = ac.getBean("statefulService", StatefulService.class);
        //ThreadA: A사용자 10000원 주문 
        statefulService1.order("userA", 10000);
        //ThreadB: B사용자 20000원 주문 
        statefulService2.order("userB", 20000);
        //ThreadA: 사용자A 주문 금액 조회
        int price = statefulService1.getPrice();
        //ThreadA: 사용자A는 10000원을 기대했지만, 기대와 다르게 20000원 출력 
        System.out.println("price = " + price);
        Assertions.assertThat(statefulService1.getPrice()).isEqualTo(20000);
    }
    static class TestConfig {
        @Bean
        public StatefulService statefulService() {
            return new StatefulService();
        }
    } 
}
```
________________________________________________________________________________________________________________________________________________________________________
다음으로 AppConfig 코드를 확인해야 합니다.
________________________________________________________________________________________________________________________________________________________________________
```java
@Configuration
public class AppConfig {
      @Bean
      public MemberService memberService() {
          return new MemberServiceImpl(memberRepository());
      }
      @Bean
      public OrderService orderService() {
          return new OrderServiceImpl(
                  memberRepository(),
      } 
      @Bean
      public MemberRepository memberRepository() {
          return new MemoryMemberRepository();
      }
      ... 
}
```
________________________________________________________________________________________________________________________________________________________________________
문제를 해결하기 위한 검증 용도의 코드를 확인해보겠습니다.
________________________________________________________________________________________________________________________________________________________________________
```java
public class MemberServiceImpl implements MemberService {
      private final MemberRepository memberRepository;
      //테스트 용도
      public MemberRepository getMemberRepository() {
          return memberRepository;
      }
}
public class OrderServiceImpl implements OrderService {
      private final MemberRepository memberRepository;
      //테스트 용도
      public MemberRepository getMemberRepository() {
          return memberRepository;
      }
}
```
________________________________________________________________________________________________________________________________________________________________________
테스트 코드는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
```java
package hello.core.singleton;
import hello.core.AppConfig;
import hello.core.member.MemberRepository;
import hello.core.member.MemberServiceImpl;
import hello.core.order.OrderServiceImpl;
import org.junit.jupiter.api.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
import static org.assertj.core.api.Assertions.*;
public class ConfigurationSingletonTest {
      @Test
      void configurationTest() {
          ApplicationContext ac = new AnnotationConfigApplicationContext(AppConfig.class);
          MemberServiceImpl memberService = ac.getBean("memberService", MemberServiceImpl.class);
          OrderServiceImpl orderService = ac.getBean("orderService", OrderServiceImpl.class);
          MemberRepository memberRepository = ac.getBean("memberRepository", MemberRepository.class);
          //모두 같은 인스턴스를 참고하고 있다.
          System.out.println("memberService -> memberRepository = " + memberService.getMemberRepository());
          System.out.println("orderService -> memberRepository  = " + orderService.getMemberRepository());
          System.out.println("memberRepository = " + memberRepository);
          //모두 같은 인스턴스를 참고하고 있다.
          assertThat(memberService.getMemberRepository()).isSameAs(memberRepository);
          assertThat(orderService.getMemberRepository()).isSameAs(memberRepository);
      }
}
```
________________________________________________________________________________________________________________________________________________________________________
이제 AppConfig에 호출 로그를 남기겠습니다.
________________________________________________________________________________________________________________________________________________________________________
```java
package hello.core;
import hello.core.discount.DiscountPolicy;
import hello.core.discount.RateDiscountPolicy;
import hello.core.member.MemberRepository;
import hello.core.member.MemberService;
import hello.core.member.MemberServiceImpl;
import hello.core.member.MemoryMemberRepository;
import hello.core.order.OrderService;
import hello.core.order.OrderServiceImpl;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
@Configuration
public class AppConfig {
      @Bean
      public MemberService memberService() {
          //1번
          System.out.println("call AppConfig.memberService");
          return new MemberServiceImpl(memberRepository());
      }
      @Bean
      public OrderService orderService() {
          //1번
          System.out.println("call AppConfig.orderService"); 
          return new OrderServiceImpl(memberRepository(), discountPolicy());
      }
      @Bean
      public MemberRepository memberRepository() {
          //2번? 3번?
          System.out.println("call AppConfig.memberRepository"); 
          return new MemoryMemberRepository();
      }
      @Bean
      public DiscountPolicy discountPolicy() {
          return new RateDiscountPolicy();
      }
}
```
________________________________________________________________________________________________________________________________________________________________________
이제 @Configuration과 바이트코드 조작에 관련한 코드를 보겠습니다.
________________________________________________________________________________________________________________________________________________________________________
```java
@Test
  void configurationDeep() {
      ApplicationContext ac = new AnnotationConfigApplicationContext(AppConfig.class);
      //AppConfig도 스프링 빈으로 등록된다.
      AppConfig bean = ac.getBean(AppConfig.class);
      System.out.println("bean = " + bean.getClass());
      //출력: bean = class hello.core.AppConfig$$EnhancerBySpringCGLIB$$bd479d70
}
```
________________________________________________________________________________________________________________________________________________________________________
AppConfig@CGLIB 예상 코드는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
```java
@Bean
public MemberRepository memberRepository() {
        if (memoryMemberRepository가 이미 스프링 컨테이너에 등록되어 있으면?) { 
                return 스프링 컨테이너에서 찾아서 반환;
        } else { //스프링 컨테이너에 없으면 
        기존 로직을 호출해서 MemoryMemberRepository를 생성하고 스프링 컨테이너에 등록 
        return 반환
        } 
}
```
________________________________________________________________________________________________________________________________________________________________________
이제 컴포넌트 스캔과 관련한 코드를 살펴보겠습니다. 우선 컴포넌트 스캔과 의존관계 자동 주입을 시작하겠습니다.
________________________________________________________________________________________________________________________________________________________________________
- AutoAppConfig.java
```java
package hello.core;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.FilterType;
import static org.springframework.context.annotation.ComponentScan.*;
@Configuration
@ComponentScan(excludeFilters = @Filter(type = FilterType.ANNOTATION, classes =Configuration.class))
public class AutoAppConfig {
}
```
________________________________________________________________________________________________________________________________________________________________________
- MemoryMemberRepository @Component 추가
```java
@Component
public class MemoryMemberRepository implements MemberRepository {}
```
________________________________________________________________________________________________________________________________________________________________________
- RateDiscountPolicy @Component 추가
```java
@Component
public class RateDiscountPolicy implements DiscountPolicy {}
```
________________________________________________________________________________________________________________________________________________________________________
- MemberServiceImpl @Component, @Autowired 추가
```java
@Component
public class MemberServiceImpl implements MemberService {
      private final MemberRepository memberRepository;
      @Autowired
      public MemberServiceImpl(MemberRepository memberRepository) {
          this.memberRepository = memberRepository;
      }
}
```
________________________________________________________________________________________________________________________________________________________________________
- OrderServiceImpl @Component, @Autowired 추가
```java
@Component
public class OrderServiceImpl implements OrderService {
      private final MemberRepository memberRepository;
      private final DiscountPolicy discountPolicy;
      @Autowired
      public OrderServiceImpl(MemberRepository memberRepository, DiscountPolicy discountPolicy) {
          this.memberRepository = memberRepository;
          this.discountPolicy = discountPolicy;
      }
}
```
________________________________________________________________________________________________________________________________________________________________________
- AutoAppConfigTest.java
```java
package hello.core.scan;
import hello.core.AutoAppConfig;
import hello.core.member.MemberService;
import org.junit.jupiter.api.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
import static org.assertj.core.api.Assertions.*;
public class AutoAppConfigTest {
      @Test
      void basicScan() {
          ApplicationContext ac = new AnnotationConfigApplicationContext(AutoAppConfig.class);
          MemberService memberService = ac.getBean(MemberService.class);
          assertThat(memberService).isInstanceOf(MemberService.class);
      }
}
```
________________________________________________________________________________________________________________________________________________________________________
이제 다음으로 필터와 관련된 코드를 보겠습니다.
________________________________________________________________________________________________________________________________________________________________________
- 컴포넌트 스캔 대상에 추가할 애노테이션
```java
package hello.core.scan.filter;
import java.lang.annotation.*;
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface MyIncludeComponent {
}
```
________________________________________________________________________________________________________________________________________________________________________
- 컴포넌트 스캔 대상에서 제외할 애노테이션
```java
package hello.core.scan.filter;
import java.lang.annotation.*;
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface MyExcludeComponent {
}
```
________________________________________________________________________________________________________________________________________________________________________
- 컴포넌트 스캔 대상에 추가할 클래스
```java
package hello.core.scan.filter;
@MyIncludeComponent
public class BeanA {
}
```
________________________________________________________________________________________________________________________________________________________________________
- 컴포넌트 스캔 대상에서 제외할 클래스
```java
package hello.core.scan.filter;
@MyExcludeComponent
public class BeanB {
}
```
________________________________________________________________________________________________________________________________________________________________________
- 설정 정보와 전체 테스트 코드
```java
package hello.core.scan.filter;
import org.junit.jupiter.api.Assertions;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.NoSuchBeanDefinitionException;
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.FilterType;
import static org.assertj.core.api.Assertions.assertThat;
import static org.springframework.context.annotation.ComponentScan.Filter;
public class ComponentFilterAppConfigTest {
    @Test
    void filterScan() {
        ApplicationContext ac = new AnnotationConfigApplicationContext(ComponentFilterAppConfig.class);
        BeanA beanA = ac.getBean("beanA", BeanA.class);
        assertThat(beanA).isNotNull();
        Assertions.assertThrows(
                NoSuchBeanDefinitionException.class,
                () -> ac.getBean("beanB", BeanB.class));
    }
    @Configuration
    @ComponentScan(
            includeFilters = @Filter(type = FilterType.ANNOTATION, classes = MyIncludeComponent.class),
            excludeFilters = @Filter(type = FilterType.ANNOTATION, classes = MyExcludeComponent.class)
    )
    static class ComponentFilterAppConfig {
    }
}
```
```java
@ComponentScan(
    includeFilters = @Filter(type = FilterType.ANNOTATION, classes = MyIncludeComponent.class),
    excludeFilters = @Filter(type = FilterType.ANNOTATION, classes = MyExcludeComponent.class)
)
```
________________________________________________________________________________________________________________________________________________________________________
FilterType 옵션을 활용하여 BeanA를 빼는 코드 예시는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
```java
@ComponentScan(
    includeFilters = {
              @Filter(type = FilterType.ANNOTATION, classes = MyIncludeComponent.class),
    },
    excludeFilters = {
             @Filter(type = FilterType.ANNOTATION, classes = MyExcludeComponent.class),
             @Filter(type = FilterType.ASSIGNABLE_TYPE, classes = BeanA.class)
    }
)
```
________________________________________________________________________________________________________________________________________________________________________
수동 빈 등록과 자동 빈 등록이 충돌하게 되면 수동 빈 등록이 우선 실행됩니다. 위의 코드가 수동 빈 등록, 아래의 코드가 자동 빈 등록입니다.
________________________________________________________________________________________________________________________________________________________________________
```java
@Component
public class MemoryMemberRepository implements MemberRepository {}
```
```java
@Configuration
@ComponentScan(
          excludeFilters = @Filter(type = FilterType.ANNOTATION, classes = Configuration.class)
)
public class AutoAppConfig {
      @Bean(name = "memoryMemberRepository")
      public MemberRepository memberRepository() {
          return new MemoryMemberRepository();
      }
}
```
________________________________________________________________________________________________________________________________________________________________________
이상으로 싱글톤 컨테이너와 컴포넌트 스캔에 대해 알아보았습니다. 오늘은 드디어 날씨가 풀려서 폭우 걱정을 안 해도 되는 것 같습니다. 모두 오늘 하루도 무사히 귀가하시길 바랍니다. 그럼 다음 포스팅도 이어서 스프링 기본편에 대해 다루겠습니다. 오늘도 긴 글 읽어주시느라 고생하셨습니다.
________________________________________________________________________________________________________________________________________________________________________
#### 감사합니다.
________________________________________________________________________________________________________________________________________________________________________
[레퍼런스]
1. 스프링 핵심 원리 - 기본편 : https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-%EA%B8%B0%EB%B3%B8%ED%8E%B8/dashboard