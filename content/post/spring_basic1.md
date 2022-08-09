---
title: "스프링의 기본에 대해 알아볼까요?(1)"
date: 2022-08-08T18:20:47+09:00
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
안녕하세요. shcDE입니다. 이번 포스팅부터는 스프링 입문 단계를 벗어나서, 기본 단계로 진입하겠습니다. 사실 이번에 들은 분량에서는 대부분 이론 및 설계에 대한 설명이 많았습니다. 그래서 실제 작성한 코드의 분량은 많지 않습니다. 이번에는 스프링의 구현 원리를 이해하고, 자바에 대한 기본기를 복습하기 위해 순수 자바 코드로 작성하였음을 알려드립니다.
________________________________________________________________________________________________________________________________________________________________________
시작 전에, 해당 포스팅은 김영한님의 ['스프링 핵심 원리 - 기본편'](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-%EA%B8%B0%EB%B3%B8%ED%8E%B8/dashboard) 섹션 1, 2를 공부하여 정리한 내용임을 밝히며, 직접 해당 강의를 구매하여 게시물을 작성함을 알립니다. 여기에서는 '회원 도메인 개발', '회원 도메인 실행과 테스트', '주문과 할인 도메인 개발', '주문과 할인 도메인 실행과 테스트'에 대한 내용을 중점으로 다루겠습니다.
________________________________________________________________________________________________________________________________________________________________________
회원 도메인 개발과 관련된 자바 소스코드는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
우선 회원 엔티티를 먼저 개발합니다.
________________________________________________________________________________________________________________________________________________________________________
- 회원 등급
```java
package hello.core.member;
public enum Grade {
    BASIC,
    VIP 
}
```
________________________________________________________________________________________________________________________________________________________________________
- 회원 엔티티
```java
package hello.core.member;
public class Member {
    private Long id;
    private String name;
    private Grade grade;
    public Member(Long id, String name, Grade grade) {
        this.id = id;
        this.name = name;
        this.grade = grade;
    }
    public Long getId() {
        return id;
    }
    public void setId(Long id) {
        this.id = id;
    }
    public String getName() {
        return name;
    }
    public void setName(String name) {
        this.name = name;
    }
    public Grade getGrade() {
        return grade;
    }
    public void setGrade(Grade grade) {
        this.grade = grade;
    } 
}
```
________________________________________________________________________________________________________________________________________________________________________
회원 저장소는 다음과 같은 방식으로 개발합니다.
________________________________________________________________________________________________________________________________________________________________________
- 회원 저장소 인터페이스
```java
package hello.core.member;
public interface MemberRepository {
      void save(Member member);
      Member findById(Long memberId);
}
```
________________________________________________________________________________________________________________________________________________________________________
- 메모리 회원 저장소 구현체
```java
package hello.core.member;
  import java.util.HashMap;
  import java.util.Map;
  public class MemoryMemberRepository implements MemberRepository {
      private static Map<Long, Member> store = new HashMap<>();
      @Override
      public void save(Member member) {
          store.put(member.getId(), member);
      }
      @Override
      public Member findById(Long memberId) {
          return store.get(memberId);
      }
}
```
________________________________________________________________________________________________________________________________________________________________________
회원 서비스는 다음과 같은 방식으로 개발합니다.
________________________________________________________________________________________________________________________________________________________________________
- 회원 서비스 인터페이스
```java
package hello.core.member;
    public interface MemberService {
        void join(Member member);
        Member findMember(Long memberId);
}
```
________________________________________________________________________________________________________________________________________________________________________
- 회원 서비스 구현체
```java
package hello.core.member;
public class MemberServiceImpl implements MemberService {
    private final MemberRepository memberRepository = new MemoryMemberRepository();
    public void join(Member member) {
        memberRepository.save(member);
    }
    public Member findMember(Long memberId) {
        return memberRepository.findById(memberId);
    }
}
```
________________________________________________________________________________________________________________________________________________________________________
이제 개발은 완료되었고, 해당 시스템을 실행하고 테스트를 진행해보겠습니다.
________________________________________________________________________________________________________________________________________________________________________
- 회원 도메인 - 회원 가입 main
```java
package hello.core;
import hello.core.member.Grade;
import hello.core.member.Member;
import hello.core.member.MemberService;
import hello.core.member.MemberServiceImpl;
public class MemberApp {
    public static void main(String[] args) {
        MemberService memberService = new MemberServiceImpl();
        Member member = new Member(1L, "memberA", Grade.VIP);
        memberService.join(member);
        Member findMember = memberService.findMember(1L);
        System.out.println("new member = " + member.getName());
        System.out.println("find Member = " + findMember.getName());
    }
}
```
________________________________________________________________________________________________________________________________________________________________________
- 회원 도메인 - 회원 가입 테스트
```java
package hello.core.member;
import org.assertj.core.api.Assertions;
import org.junit.jupiter.api.Test;
import static org.junit.jupiter.api.Assertions.*;
class MemberServiceTest {
    MemberService memberService = new MemberServiceImpl();
    @Test
    void join() {
      //given
        Member member = new Member(1L, "memberA", Grade.VIP);
      //when
        memberService.join(member);
        Member findMember = memberService.findMember(1L);
      //then
        Assertions.assertThat(member).isEqualTo(findMember);
      }
}
```
________________________________________________________________________________________________________________________________________________________________________
여기까지가 '회원 도메인 개발', '회원 도메인 실행과 테스트'과 관련된 예시였습니다. 다음으로 '주문과 할인 도메인 개발', '주문과 할인 도메인 실행과 테스트'에 대한 코드를 살펴보겠습니다.
________________________________________________________________________________________________________________________________________________________________________
주문과 할인 도메인 개발과 관련된 코드는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
- 할인 정책 인터페이스
```java
package hello.core.discount;
import hello.core.member.Member;

public interface DiscountPolicy {
      /**
        * @return 할인 대상 금액
        */
      int discount(Member member, int price);
}
```
________________________________________________________________________________________________________________________________________________________________________
- 정액 할인 정책 구현체
```java
package hello.core.discount;
import hello.core.member.Grade;
import hello.core.member.Member;
public class FixDiscountPolicy implements DiscountPolicy {
      private int discountFixAmount = 1000; //1000원 할인
      @Override
      public int discount(Member member, int price) {
          if (member.getGrade() == Grade.VIP) {
              return discountFixAmount;
          } else {
              return 0;
          } 
      }
}
```
________________________________________________________________________________________________________________________________________________________________________
- 주문 엔티티
```java
package hello.core.order;
public class Order {
    private Long memberId;
    private String itemName;
    private int itemPrice;
    private int discountPrice;
    public Order(Long memberId, String itemName, int itemPrice, int
discountPrice) {
        this.memberId = memberId;
        this.itemName = itemName;
        this.itemPrice = itemPrice;
        this.discountPrice = discountPrice;
    }
    public int calculatePrice() {
        return itemPrice - discountPrice;
    }
    public Long getMemberId() {
        return memberId;
    }
    public String getItemName() {
        return itemName;
    }
    public int getItemPrice() {
        return itemPrice;
    }
    public int getDiscountPrice() {
        return discountPrice;
    }
    @Override
    public String toString() {
        return "Order{" +
        "memberId=" + memberId +
        ", itemName='" + itemName + '\'' +
        ", itemPrice=" + itemPrice +
        ", discountPrice=" + discountPrice +
        '}';
    }
}
```
________________________________________________________________________________________________________________________________________________________________________
- 주문 서비스 인터페이스
```java
package hello.core.order;
public interface OrderService {
      Order createOrder(Long memberId, String itemName, int itemPrice);
}
```
________________________________________________________________________________________________________________________________________________________________________
- 주문 서비스 구현체
```java
package hello.core.order;
import hello.core.discount.DiscountPolicy;
import hello.core.discount.FixDiscountPolicy;
import hello.core.member.Member;
import hello.core.member.MemberRepository;
import hello.core.member.MemoryMemberRepository;
public class OrderServiceImpl implements OrderService {
    private final MemberRepository memberRepository = new MemoryMemberRepository();
    private final DiscountPolicy discountPolicy = new FixDiscountPolicy();
    @Override
    public Order createOrder(Long memberId, String itemName, int itemPrice) {
        Member member = memberRepository.findById(memberId);
        int discountPrice = discountPolicy.discount(member, itemPrice);
        return new Order(memberId, itemName, itemPrice, discountPrice);
    }
}
```
________________________________________________________________________________________________________________________________________________________________________
이제 마지막으로 '주문과 할인 도메인 실행과 테스트' 파트에 해당하는 코드를 보겠습니다.
________________________________________________________________________________________________________________________________________________________________________
- 주문과 할인 정책 실행
```java
package hello.core;
import hello.core.member.Grade;
import hello.core.member.Member;
import hello.core.member.MemberService;
import hello.core.member.MemberServiceImpl;
import hello.core.order.Order;
import hello.core.order.OrderService;
import hello.core.order.OrderServiceImpl;
public class OrderApp {
    public static void main(String[] args) {
        MemberService memberService = new MemberServiceImpl();
        OrderService orderService = new OrderServiceImpl();
        long memberId = 1L;
        Member member = new Member(memberId, "memberA", Grade.VIP);
        memberService.join(member);
        Order order = orderService.createOrder(memberId, "itemA", 10000);
        System.out.println("order = " + order);
    }
}
```
```
order = Order{memberId=1, itemName='itemA', itemPrice=10000,
discountPrice=1000}
```
________________________________________________________________________________________________________________________________________________________________________
- 주문과 할인 정책 테스트
```java
package hello.core.order;
import hello.core.member.Grade;
import hello.core.member.Member;
import hello.core.member.MemberService;
import hello.core.member.MemberServiceImpl;
import org.assertj.core.api.Assertions;
import org.junit.jupiter.api.Test;
import static org.junit.jupiter.api.Assertions.*;
class OrderServiceTest {
    MemberService memberService = new MemberServiceImpl();
    OrderService orderService = new OrderServiceImpl();
    @Test
    void createOrder() {
        long memberId = 1L;
        Member member = new Member(memberId, "memberA", Grade.VIP);
        memberService.join(member);
        Order order = orderService.createOrder(memberId, "itemA", 10000);
        Assertions.assertThat(order.getDiscountPrice()).isEqualTo(1000);
    }
}
```
________________________________________________________________________________________________________________________________________________________________________
이상으로 자바 코드를 활용하여 서비스를 기초적으로 구현하였습니다. 다음 포스팅에서는 객체 지향 원리를 적용하고, 스프링 컨테이너와 스프링 빈을 다루는 방법에 대한 포스팅을 하겠습니다. 지금 밖에 비가 너무 많이 내려와서 땅이 잠긴 곳이 많습니다. 그만큼 오늘 퇴근길과 내일 출근길이 힘들 것으로 예상됩니다. 부디 비가 무사히 멈추길 바라며, 다치는 사람도 없길 바랍니다. 특히, 강남구 및 서초구에 거주하시는 분들께서는 더더욱 안전히 지내시길 바랍니다. 그럼 오늘도 긴 글 읽어주시느라 고생하셨고 새로운 한 주 화이팅입니다.
________________________________________________________________________________________________________________________________________________________________________
#### 감사합니다.
________________________________________________________________________________________________________________________________________________________________________
[레퍼런스]
1. 스프링 핵심 원리 - 기본편 : https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-%EA%B8%B0%EB%B3%B8%ED%8E%B8/dashboard