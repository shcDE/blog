---
title: "스프링의 기본에 대해 알아볼까요?(2)"
date: 2022-08-09T22:38:21+09:00
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
안녕하세요. shcDE입니다. 이번 포스팅에서는 이전 포스팅에 이어 스프링의 핵심 원리부터 스프링 빈까지 알아보도록 하겠습니다. 시작 전에, 해당 포스팅은 김영한님의 ['스프링 핵심 원리 - 기본편'](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-%EA%B8%B0%EB%B3%B8%ED%8E%B8/dashboard) 섹션 3, 4를 공부하여 정리한 내용임을 밝히며, 직접 해당 강의를 구매하여 게시물을 작성함을 알립니다.
________________________________________________________________________________________________________________________________________________________________________
우선 주어진 상황은 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
#### 새로운 할인 정책 개발
- 새로운 할인 정책을 확장해보자.
- 악덕 기획자: 서비스 오픈 직전에 할인 정책을 지금처럼 고정 금액 할인이 아니라 좀 더 합리적인 주문 금액당 할인하는 정률% 할인으로 변경하고 싶어요. 예를 들어서 기존 정책은 VIP가 10000원을 주문하든 20000원을 주문하든 항상 1000원을 할인했는데, 이번에 새로 나온 정책은 10%로 지정해두면 고객이 10000원 주문시 1000원을 할인해주고, 20000원 주문시에 2000원을 할인해주는 거에요!
- 순진 개발자: 제가 처음부터 고정 금액 할인은 아니라고 했잖아요.
- 악덕 기획자: 애자일 소프트웨어 개발 선언 몰라요? “계획을 따르기보다 변화에 대응하기를”
- 순진 개발자: ... (하지만 난 유연한 설계가 가능하도록 객체지향 설계 원칙을 준수했지 후후)
________________________________________________________________________________________________________________________________________________________________________
이 상황을 해결하기 위해서 우선 RateDiscountPolicy 코드를 추가해줍니다.
________________________________________________________________________________________________________________________________________________________________________
```java
package hello.core.discount;
  import hello.core.member.Grade;
  import hello.core.member.Member;
public class RateDiscountPolicy implements DiscountPolicy { private int discountPercent = 10; //10% 할인
      @Override
      public int discount(Member member, int price) {
          if (member.getGrade() == Grade.VIP) {
              return price * discountPercent /  100;
          } else {
              return 0;
          } 
      }
}
```
________________________________________________________________________________________________________________________________________________________________________
그 다음에, 테스트 코드를 작성해줍니다.
________________________________________________________________________________________________________________________________________________________________________
```java
package hello.core.discount;
import hello.core.member.Grade;
import hello.core.member.Member;
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;
import static org.assertj.core.api.Assertions.*;
import static org.junit.jupiter.api.Assertions.*;
class RateDiscountPolicyTest {
    RateDiscountPolicy discountPolicy = new RateDiscountPolicy();
    @Test
    @DisplayName("VIP는 10% 할인이 적용되어야 한다.")
    void vip_o() {
        //given
        Member member = new Member(1L, "memberVIP", Grade.VIP);
        //when
        int discount = discountPolicy.discount(member, 10000);
        //then
        assertThat(discount).isEqualTo(1000);
    }
    @Test
    @DisplayName("VIP가 아니면 할인이 적용되지 않아야 한다.")
    void vip_x() {
        //given
        Member member = new Member(2L, "memberBASIC", Grade.BASIC);
        //when
        int discount = discountPolicy.discount(member, 10000);
        //then
        assertThat(discount).isEqualTo(0);
    }
}
```
________________________________________________________________________________________________________________________________________________________________________
위의 두 코드를 통해 할인정책을 추가하고 테스트까지 완료했습니다.
________________________________________________________________________________________________________________________________________________________________________
이제 새로운 할인 정책 적용과 문제점 파악을 해보겠습니다. 우선 방금 추가한 할인 정책을 적용해보겠습니다.
________________________________________________________________________________________________________________________________________________________________________
할인 정책을 변경하려면 클라이언트인 'OrderServiceImpl' 코드를 고쳐야 합니다.
________________________________________________________________________________________________________________________________________________________________________
```java
public class OrderServiceImpl implements OrderService {
  //    private final DiscountPolicy discountPolicy = new FixDiscountPolicy();
      private final DiscountPolicy discountPolicy = new RateDiscountPolicy();
}
```
________________________________________________________________________________________________________________________________________________________________________
여기에서 발견한 문제점은 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
- 우리는 역할과 구현을 충실하게 분리했다. OK
- 다형성도 활용하고, 인터페이스와 구현 객체를 분리했다. 
- OK OCP, DIP 같은 객체지향 설계 원칙을 충실히 준수했다
  - 그렇게 보이지만 사실은 아니다.
- DIP: 주문서비스 클라이언트( OrderServiceImpl )는 DiscountPolicy 인터페이스에 의존하면서 DIP를 지킨 것 같은데?
  - 클래스 의존관계를 분석해 보자. 추상(인터페이스) 뿐만 아니라 구체(구현) 클래스에도 의존하고 있다.
    - 추상(인터페이스) 의존: DiscountPolicy
    - 구체(구현) 클래스: FixDiscountPolicy , RateDiscountPolicy 
- OCP: 변경하지 않고 확장할 수 있다고 했는데!
  - 지금 코드는 기능을 확장해서 변경하면, 클라이언트 코드에 영향을 준다! 따라서 OCP를 위반한다.
________________________________________________________________________________________________________________________________________________________________________
이제 이러한 문제점을 어떻게 해결할 수 있을지 확인해보겠습니다.
________________________________________________________________________________________________________________________________________________________________________
- 클라이언트 코드인 OrderServiceImpl 은 DiscountPolicy 의 인터페이스 뿐만 아니라 구체 클래스도 함께 의존한다.
- 그래서 구체 클래스를 변경할 때 클라이언트 코드도 함께 변경해야 한다.
- DIP 위반 추상에만 의존하도록 변경(인터페이스에만 의존)
- DIP를 위반하지 않도록 인터페이스에만 의존하도록 의존관계를 변경하면 된다.
________________________________________________________________________________________________________________________________________________________________________
- 인터페이스에만 의존하도록 코드 변경
```java
public class OrderServiceImpl implements OrderService {
  //private final DiscountPolicy discountPolicy = new RateDiscountPolicy();
  private DiscountPolicy discountPolicy;
}
```
________________________________________________________________________________________________________________________________________________________________________
위와 같이 인터페이스에만 의존하도록 설계와 코드를 변경했습니다. 그런데 구현체가 없는데 어떻게 코드를 실행할 수 있을까요? 실제 실행을 해보면 NPE(null pointer exception)가 발생합니다. 이 문제를 해결하려면 누군가가 클라이언트인 OrderServiceImpl 에 DiscountPolicy 의 구현 객체를 대신 생성하고 주입해주어야 합니다.
________________________________________________________________________________________________________________________________________________________________________
이제 관심사를 분리하기 위한 상황은 다음과 같이 주어집니다.
________________________________________________________________________________________________________________________________________________________________________
- 애플리케이션을 하나의 공연이라 생각해보자. 각각의 인터페이스를 배역(배우 역할)이라 생각하자. 그런데! 실제 배역 맞는 배우를 선택하는 것은 누가 하는가?
- 로미오와 줄리엣 공연을 하면 로미오 역할을 누가 할지 줄리엣 역할을 누가 할지는 배우들이 정하는게 아니다. 이전 코드는 마치 로미오 역할(인터페이스)을 하는 레오나르도 디카프리오(구현체, 배우)가 줄리엣 역할(인터페이스)을 하는 여자 주인공(구현체, 배우)을 직접 초빙하는 것과 같다. 디카프리오는 공연도 해야하고 동시에 여자 주인공도 공연에 직접 초빙해야 하는 다양한 책임을 가지고 있다.
________________________________________________________________________________________________________________________________________________________________________
이제 관심사를 분리하겠습니다.
________________________________________________________________________________________________________________________________________________________________________
- 배우는 본인의 역할인 배역을 수행하는 것에만 집중해야 한다.
- 디카프리오는 어떤 여자 주인공이 선택되더라도 똑같이 공연을 할 수 있어야 한다.
- 공연을 구성하고, 담당 배우를 섭외하고, 역할에 맞는 배우를 지정하는 책임을 담당하는 별도의 공연 기획자가 나올시점이다.
- 공연 기획자를 만들고, 배우와 공연 기획자의 책임을 확실히 분리하자.
________________________________________________________________________________________________________________________________________________________________________
이제 AppConfig를 통해 애플리케이션의 전체 동작 방식을 구성(config)하기 위해, 구현 객체를 생성하고, 연결하는 책임을 가지는 별도의 설정 클래스를 만들겠습니다.
________________________________________________________________________________________________________________________________________________________________________
```java
package hello.core;
import hello.core.discount.FixDiscountPolicy;
import hello.core.member.MemberService;
import hello.core.member.MemberServiceImpl;
import hello.core.member.MemoryMemberRepository;
import hello.core.order.OrderService;
import hello.core.order.OrderServiceImpl;
public class AppConfig {
    public MemberService memberService() {
        return new MemberServiceImpl(new MemoryMemberRepository());
    }
    public OrderService orderService() {
        return new OrderServiceImpl(
                new MemoryMemberRepository(),
                new FixDiscountPolicy());
    } 
}
```
________________________________________________________________________________________________________________________________________________________________________
MemberServiceImpl을 통한 생성자 주입은 다음과 같이 합니다.
________________________________________________________________________________________________________________________________________________________________________
```java
package hello.core.member;
public class MemberServiceImpl implements MemberService {
    private final MemberRepository memberRepository;
    public MemberServiceImpl(MemberRepository memberRepository) {
        this.memberRepository = memberRepository;
    }
    public void join(Member member) {
        memberRepository.save(member);
    }
    public Member findMember(Long memberId) {
        return memberRepository.findById(memberId);
    } 
}
```
________________________________________________________________________________________________________________________________________________________________________
OrderServiceImpl을 통한 생성자 주입은 다음과 같이 합니다.
________________________________________________________________________________________________________________________________________________________________________
```java
package hello.core.order;
import hello.core.discount.DiscountPolicy;
import hello.core.member.Member;
import hello.core.member.MemberRepository;
public class OrderServiceImpl implements OrderService {
    private final MemberRepository memberRepository;
    private final DiscountPolicy discountPolicy;
    public OrderServiceImpl(MemberRepository memberRepository, DiscountPolicy discountPolicy) {
        this.memberRepository = memberRepository;
        this.discountPolicy = discountPolicy;
    }
    @Override
    public Order createOrder(Long memberId, String itemName, int itemPrice) {
        Member member = memberRepository.findById(memberId);
        int discountPrice = discountPolicy.discount(member, itemPrice);
        return new Order(memberId, itemName, itemPrice, discountPrice);
    }
}
```
________________________________________________________________________________________________________________________________________________________________________
이제 AppConfig를 실행하겠습니다. 사용 클래스는 MemberApp입니다.
________________________________________________________________________________________________________________________________________________________________________
```java
package hello.core;
import hello.core.member.Grade;
import hello.core.member.Member;
import hello.core.member.MemberService;
public class MemberApp {
    public static void main(String[] args) {
        AppConfig appConfig = new AppConfig();
        MemberService memberService = appConfig.memberService();
        Member member = new Member(1L, "memberA", Grade.VIP);
        memberService.join(member);
        Member findMember = memberService.findMember(1L);
        System.out.println("new member = " + member.getName());
        System.out.println("find Member = " + findMember.getName());
    } 
}
```
________________________________________________________________________________________________________________________________________________________________________
OrderApp은 다음과 같이 구현합니다.
________________________________________________________________________________________________________________________________________________________________________
```java
package hello.core;
import hello.core.member.Grade;
import hello.core.member.Member;
import hello.core.member.MemberService;
import hello.core.order.Order;
import hello.core.order.OrderService;
public class OrderApp {
public static void main(String[] args) {
    AppConfig appConfig = new AppConfig();
    MemberService memberService = appConfig.memberService();
    OrderService orderService = appConfig.orderService();
    long memberId = 1L;
    Member member = new Member(memberId, "memberA", Grade.VIP);
    memberService.join(member);
    Order order = orderService.createOrder(memberId, "itemA", 10000);
    System.out.println("order = " + order);
    }
}
```
________________________________________________________________________________________________________________________________________________________________________
테스트 코드 오류 수정은 다음과 같이 하면 됩니다.
________________________________________________________________________________________________________________________________________________________________________
```java
class MemberServiceTest {
      MemberService memberService;
      @BeforeEach
      public void beforeEach() {
          AppConfig appConfig = new AppConfig();
          memberService = appConfig.memberService();
      }
}
```
```java
class OrderServiceTest {
      MemberService memberService;
      OrderService orderService;
      @BeforeEach
      public void beforeEach() {
          AppConfig appConfig = new AppConfig();
          memberService = appConfig.memberService();
          orderService = appConfig.orderService();
      } 
}
```
________________________________________________________________________________________________________________________________________________________________________
위의 진행 상황을 정리하면 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
- AppConfig를 통해서 관심사를 확실하게 분리했다.
- 배역, 배우를 생각해보자.
- AppConfig는 공연 기획자다.
- AppConfig는 구체 클래스를 선택한다. 배역에 맞는 담당 배우를 선택한다. 애플리케이션이 어떻게 동작해야 할지 전체 구성을 책임진다.
- 이제 각 배우들은 담당 기능을 실행하는 책임만 지면 된다. 
- OrderServiceImpl 은 기능을 실행하는 책임만 지면 된다.
________________________________________________________________________________________________________________________________________________________________________
이제 AppConfig 리팩터링에 관련된 코드를 살펴보겠습니다.
________________________________________________________________________________________________________________________________________________________________________
리팩터링 전은 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
```java
package hello.core;
import hello.core.discount.FixDiscountPolicy;
import hello.core.member.MemberService;
import hello.core.member.MemberServiceImpl;
import hello.core.member.MemoryMemberRepository;
import hello.core.order.OrderService;
import hello.core.order.OrderServiceImpl;
public class AppConfig {
    public MemberService memberService() {
        return new MemberServiceImpl(new MemoryMemberRepository());
    }
    public OrderService orderService() {
        return new OrderServiceImpl(
                new MemoryMemberRepository(),
                new FixDiscountPolicy());
    }
}
```
________________________________________________________________________________________________________________________________________________________________________
리팩터링 후는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
```java
package hello.core;
import hello.core.discount.DiscountPolicy;
import hello.core.discount.FixDiscountPolicy;
import hello.core.member.MemberRepository;
import hello.core.member.MemberService;
import hello.core.member.MemberServiceImpl;
import hello.core.member.MemoryMemberRepository;
import hello.core.order.OrderService;
import hello.core.order.OrderServiceImpl;
public class AppConfig {
    public MemberService memberService() {
        return new MemberServiceImpl(memberRepository());
    }
    public OrderService orderService() {
        return new OrderServiceImpl(
                memberRepository(),
                discountPolicy());
    }
    public MemberRepository memberRepository() {
        return new MemoryMemberRepository();
    }
    public DiscountPolicy discountPolicy() {
        return new FixDiscountPolicy();
    }
}
```
________________________________________________________________________________________________________________________________________________________________________
이제 새로운 구조를 만들고 할인 정책을 적용시키겠습니다.
________________________________________________________________________________________________________________________________________________________________________
할인 정책 변경 구성 코드는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
```java
package hello.core;
import hello.core.discount.DiscountPolicy;
import hello.core.discount.FixDiscountPolicy;
import hello.core.discount.RateDiscountPolicy;
import hello.core.member.MemberRepository;
import hello.core.member.MemberService;
import hello.core.member.MemberServiceImpl;
import hello.core.member.MemoryMemberRepository;
import hello.core.order.OrderService;
import hello.core.order.OrderServiceImpl;
public class AppConfig {
  public MemberService memberService() {
        return new MemberServiceImpl(memberRepository());
  }
  public OrderService orderService() {
        return new OrderServiceImpl(
                memberRepository(),
                discountPolicy());
  }
  public MemberRepository memberRepository() {
        return new MemoryMemberRepository();
  }
  public DiscountPolicy discountPolicy() {
  //        return new FixDiscountPolicy();
        return new RateDiscountPolicy();
  }
}
```
________________________________________________________________________________________________________________________________________________________________________
전체 흐름을 정리하면 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________ - 새로운 할인 정책 개발
- 새로운 할인 정책 적용과 문제점 
- 관심사의 분리
- AppConfig 리팩터링
- 새로운 구조와 할인 정책 적용
________________________________________________________________________________________________________________________________________________________________________
이제 앞에서 작성한 자바 코드를 스프링으로 전환시키겠습니다.
________________________________________________________________________________________________________________________________________________________________________
- AppConfig 스프링 기반으로 변경
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
          return new MemberServiceImpl(memberRepository());
      }
      @Bean
      public OrderService orderService() {
          return new OrderServiceImpl(
                  memberRepository(),
                  discountPolicy());
      }
      @Bean
      public MemberRepository memberRepository() {
          return new MemoryMemberRepository();
      }
      @Bean
      public DiscountPolicy discountPolicy() {
          return new RateDiscountPolicy();
      }
}
```
________________________________________________________________________________________________________________________________________________________________________
- MemberApp에 스프링 컨테이너 적용
```java
package hello.core;
import hello.core.member.Grade;
import hello.core.member.Member;
import hello.core.member.MemberService;
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
public class MemberApp {
    public static void main(String[] args) {
//        AppConfig appConfig = new AppConfig();
//        MemberService memberService = appConfig.memberService();
        ApplicationContext applicationContext = new AnnotationConfigApplicationContext(AppConfig.class);
        MemberService memberService = applicationContext.getBean("memberService", MemberService.class);
        Member member = new Member(1L, "memberA", Grade.VIP);
        memberService.join(member);
        Member findMember = memberService.findMember(1L);
        System.out.println("new member = " + member.getName());
        System.out.println("find Member = " + findMember.getName());
    } 
}
```
________________________________________________________________________________________________________________________________________________________________________
- OrderApp에 스프링 컨테이너 적용
```java
package hello.core;
import hello.core.member.Grade;
import hello.core.member.Member;
import hello.core.member.MemberService;
import hello.core.order.Order;
import hello.core.order.OrderService;
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
public class OrderApp {

  public static void main(String[] args) {
 //     AppConfig appConfig = new AppConfig();
 //     MemberService memberService = appConfig.memberService();
 //     OrderService orderService = appConfig.orderService();
          ApplicationContext applicationContext = new AnnotationConfigApplicationContext(AppConfig.class);
          MemberService memberService = applicationContext.getBean("memberService", MemberService.class);
          OrderService orderService = applicationContext.getBean("orderService", OrderService.class);
          long memberId = 1L;
          Member member = new Member(memberId, "memberA", Grade.VIP);
          memberService.join(member);
          Order order = orderService.createOrder(memberId, "itemA", 10000);
          System.out.println("order = " + order);
          }
}
```
________________________________________________________________________________________________________________________________________________________________________
이제 스프링 컨테이너와 스프링 빈에 대해 알아보겠습니다. 우선 스프링 컨테이너가 생성되는 과정에 대해 알아보겠습니다. 코드 작성 방식은 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
```java
//스프링 컨테이너 생성
ApplicationContext applicationContext =
                            new
    AnnotationConfigApplicationContext(AppConfig.class);
```
________________________________________________________________________________________________________________________________________________________________________
다음으로 스프링 컨테이너에 실제 스프링 빈들이 잘 등록 되었는지 확인해보겠습니다.
________________________________________________________________________________________________________________________________________________________________________
```java
package hello.core.beanfind;
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.config.BeanDefinition;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
import static org.assertj.core.api.Assertions.assertThat;
class ApplicationContextInfoTest {
    AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(AppConfig.class);
    @Test
    @DisplayName("모든 빈 출력하기")
    void findAllBean() {
        String[] beanDefinitionNames = ac.getBeanDefinitionNames();
        for (String beanDefinitionName : beanDefinitionNames) {
            Object bean = ac.getBean(beanDefinitionName);
            System.out.println("name=" + beanDefinitionName + " object=" + bean);
        }
    } 
    @Test
    @DisplayName("애플리케이션 빈 출력하기") void findApplicationBean() {
      String[] beanDefinitionNames = ac.getBeanDefinitionNames();
      for (String beanDefinitionName : beanDefinitionNames) {
          BeanDefinition beanDefinition = ac.getBeanDefinition(beanDefinitionName);
          //Role ROLE_APPLICATION: 직접 등록한 애플리케이션 빈
          //Role ROLE_INFRASTRUCTURE: 스프링이 내부에서 사용하는 빈
          if (beanDefinition.getRole() == BeanDefinition.ROLE_APPLICATION) {
            Object bean = ac.getBean(beanDefinitionName);
            System.out.println("name=" + beanDefinitionName + " object=" + bean);
          }
      } 
    }
}
```
________________________________________________________________________________________________________________________________________________________________________
스프링 빈 조회 - 기본과 관련된 코드는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
```java
package hello.core.beanfind;
import hello.core.AppConfig;
import hello.core.member.MemberService;
import hello.core.member.MemberServiceImpl;
import org.junit.jupiter.api.Assertions;
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.NoSuchBeanDefinitionException;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
import static org.assertj.core.api.Assertions.*;
class ApplicationContextBasicFindTest {
    AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(AppConfig.class);
    @Test
    @DisplayName("빈 이름으로 조회")
      void findBeanByName() {
          MemberService memberService = ac.getBean("memberService", MemberService.class);
          assertThat(memberService).isInstanceOf(MemberServiceImpl.class);
    } 
    @Test
    @DisplayName("이름 없이 타입만으로 조회") void findBeanByType() {
          MemberService memberService = ac.getBean(MemberService.class);
          assertThat(memberService).isInstanceOf(MemberServiceImpl.class);
    }
    @Test
    @DisplayName("구체 타입으로 조회")
      void findBeanByName2() {
          MemberServiceImpl memberService = ac.getBean("memberService", MemberServiceImpl.class);
          assertThat(memberService).isInstanceOf(MemberServiceImpl.class);
    } 
    @Test
    @DisplayName("빈 이름으로 조회X") void findBeanByNameX() {
            //ac.getBean("xxxxx", MemberService.class);
            Assertions.assertThrows(NoSuchBeanDefinitionException.class, () -> ac.getBean("xxxxx", MemberService.class));
    } 
}
```
________________________________________________________________________________________________________________________________________________________________________
스프링 빈 조회 - 동일한 타입이 둘 이상과 관련된 코드는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
```java
package hello.core.beanfind;
import hello.core.member.MemberRepository;
import hello.core.member.MemoryMemberRepository;
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.NoUniqueBeanDefinitionException;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import java.util.Map;
import static org.assertj.core.api.Assertions.assertThat;
import static org.junit.jupiter.api.Assertions.assertThrows;
class ApplicationContextSameBeanFindTest {
    AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(SameBeanConfig.class);
    @Test
    @DisplayName("타입으로 조회시 같은 타입이 둘 이상 있으면, 중복 오류가 발생한다") void findBeanByTypeDuplicate() {
        //DiscountPolicy bean = ac.getBean(MemberRepository.class);
        assertThrows(NoUniqueBeanDefinitionException.class, () -> ac.getBean(MemberRepository.class));
    } 
    @Test
    @DisplayName("타입으로 조회시 같은 타입이 둘 이상 있으면, 빈 이름을 지정하면 된다") void findBeanByName() {
        MemberRepository memberRepository = ac.getBean("memberRepository1", MemberRepository.class);
        assertThat(memberRepository).isInstanceOf(MemberRepository.class);
    }
    @Test
    @DisplayName("특정 타입을 모두 조회하기")
    void findAllBeanByType() {
        Map<String, MemberRepository> beansOfType = ac.getBeansOfType(MemberRepository.class);
        for (String key : beansOfType.keySet()) {
            System.out.println("key = " + key + " value = " + beansOfType.get(key));
        }
        System.out.println("beansOfType = " + beansOfType);
        assertThat(beansOfType.size()).isEqualTo(2);
    }
    @Configuration
    static class SameBeanConfig {
        @Bean
        public MemberRepository memberRepository1() {
            return new MemoryMemberRepository();
        }
        @Bean
        public MemberRepository memberRepository2() {
            return new MemoryMemberRepository();
        }
    } 
}
```
________________________________________________________________________________________________________________________________________________________________________
스프링 빈 조회 - 상속 관계와 관련된 코드는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
```java
package hello.core.beanfind;
import hello.core.discount.DiscountPolicy;
import hello.core.discount.FixDiscountPolicy;
import hello.core.discount.RateDiscountPolicy;
import hello.core.member.MemberService;
import hello.core.member.MemberServiceImpl;
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.NoUniqueBeanDefinitionException;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import java.util.Map;
import static org.assertj.core.api.Assertions.assertThat;
import static org.junit.jupiter.api.Assertions.assertThrows;
class ApplicationContextExtendsFindTest {
      AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(TestConfig.class);
      @Test
      @DisplayName("부모 타입으로 조회시, 자식이 둘 이상 있으면, 중복 오류가 발생한다") void findBeanByParentTypeDuplicate() {
          //DiscountPolicy bean = ac.getBean(DiscountPolicy.class);
          assertThrows(NoUniqueBeanDefinitionException.class, () -> ac.getBean(DiscountPolicy.class));
      }
      @Test
      @DisplayName("부모 타입으로 조회시, 자식이 둘 이상 있으면, 빈 이름을 지정하면 된다")
      void findBeanByParentTypeBeanName() {
        DiscountPolicy rateDiscountPolicy = ac.getBean("rateDiscountPolicy", DiscountPolicy.class);
        assertThat(rateDiscountPolicy).isInstanceOf(RateDiscountPolicy.class);
      } 
      @Test
      @DisplayName("특정 하위 타입으로 조회") void findBeanBySubType() {
        RateDiscountPolicy bean = ac.getBean(RateDiscountPolicy.class);
        assertThat(bean).isInstanceOf(RateDiscountPolicy.class);
      }
      @Test
      @DisplayName("부모 타입으로 모두 조회하기")
      void findAllBeanByParentType() {
        Map<String, DiscountPolicy> beansOfType = ac.getBeansOfType(DiscountPolicy.class);
        assertThat(beansOfType.size()).isEqualTo(2);
        for (String key : beansOfType.keySet()) {
            System.out.println("key = " + key + " value=" + beansOfType.get(key));
        } 
      }
      @Test
      @DisplayName("부모 타입으로 모두 조회하기 - Object")
      void findAllBeanByObjectType() {
        Map<String, Object> beansOfType = ac.getBeansOfType(Object.class);
        for (String key : beansOfType.keySet()) {
            System.out.println("key = " + key + " value=" + beansOfType.get(key));
        } 
      }
      @Configuration
      static class TestConfig {
           @Bean
          public DiscountPolicy rateDiscountPolicy() {
              return new RateDiscountPolicy();
          }
          @Bean
          public DiscountPolicy fixDiscountPolicy() {
              return new FixDiscountPolicy();
          }
      } 
}
```
________________________________________________________________________________________________________________________________________________________________________
XML과 관련된 코드는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
- XmlAppConfig 사용 자바 코드
```java
package hello.core.xml;

import hello.core.member.MemberService;
import org.junit.jupiter.api.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.GenericXmlApplicationContext;
import static org.assertj.core.api.Assertions.*;
public class XmlAppContext {
      @Test
      void xmlAppContext() {
          ApplicationContext ac = new GenericXmlApplicationContext("appConfig.xml");
          MemberService memberService = ac.getBean("memberService", MemberService.class);
          assertThat(memberService).isInstanceOf(MemberService.class);
      }
}
```
________________________________________________________________________________________________________________________________________________________________________
- xml 기반의 스프링 빈 설정 정보(src/main/resources/appConfig.xml)
```java
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://www.springframework.org/schema/beans http://
  www.springframework.org/schema/beans/spring-beans.xsd">
      <bean id="memberService" class="hello.core.member.MemberServiceImpl">
          <constructor-arg name="memberRepository" ref="memberRepository" />
      </bean>
      <bean id="memberRepository"
  class="hello.core.member.MemoryMemberRepository" />
       <bean id="orderService" class="hello.core.order.OrderServiceImpl">
          <constructor-arg name="memberRepository" ref="memberRepository" />
          <constructor-arg name="discountPolicy" ref="discountPolicy" />
      </bean>
      <bean id="discountPolicy" class="hello.core.discount.RateDiscountPolicy" />
</beans>
```
________________________________________________________________________________________________________________________________________________________________________
스프링 빈 설정 메타 정보 - BeanDefinition과 관련된 코드는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
```java
package hello.core.beandefinition;
import hello.core.AppConfig;
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;
import org.springframework.beans.MutablePropertyValues;
import org.springframework.beans.factory.config.BeanDefinition;
import org.springframework.beans.factory.config.ConstructorArgumentValues;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
import org.springframework.context.support.GenericXmlApplicationContext;
public class BeanDefinitionTest {
    AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(AppConfig.class);
//    GenericXmlApplicationContext ac = new
    GenericXmlApplicationContext("appConfig.xml");
    @Test
    @DisplayName("빈 설정 메타정보 확인")
    void findApplicationBean() {
        String[] beanDefinitionNames = ac.getBeanDefinitionNames();
        for (String beanDefinitionName : beanDefinitionNames) {
            BeanDefinition beanDefinition = ac.getBeanDefinition(beanDefinitionName);
            if (beanDefinition.getRole() == BeanDefinition.ROLE_APPLICATION) {
                System.out.println("beanDefinitionName" + beanDefinitionName + " beanDefinition = " + beanDefinition);
            }
        } 
    }
}
```
________________________________________________________________________________________________________________________________________________________________________
스프링 컨테이너 및 빈에 대한 정리는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
- BeanDefinition을 직접 생성해서 스프링 컨테이너에 등록할 수 도 있다. 하지만 실무에서 BeanDefinition을 직접 정의하거나 사용할 일은 거의 없다. 어려우면 그냥 넘어가면 된다.
- BeanDefinition에 대해서는 너무 깊이있게 이해하기 보다는, 스프링이 다양한 형태의 설정 정보를 BeanDefinition으로 추상화해서 사용하는 것 정도만 이해하면 된다.
- 가끔 스프링 코드나 스프링 관련 오픈 소스의 코드를 볼 때, BeanDefinition 이라는 것이 보일 때가 있다. 이때 이러한 메커니즘을 떠올리면 된다.
________________________________________________________________________________________________________________________________________________________________________
이상으로 스프링의 핵심 원리부터 스프링 빈까지 확인했습니다. 모두 이틀 연속 폭우로 인하여 고생하십니다. 하지만 태풍이 멈출 때 까지 조금만 버티시길 바랍니다. 오늘도 긴 글 읽어주시느라 고생하셨습니다.
________________________________________________________________________________________________________________________________________________________________________
#### 감사합니다.
________________________________________________________________________________________________________________________________________________________________________
[레퍼런스]
1. 스프링 핵심 원리 - 기본편 : https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-%EA%B8%B0%EB%B3%B8%ED%8E%B8/dashboard