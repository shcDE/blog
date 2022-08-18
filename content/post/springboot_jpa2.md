---
title: "스프링 부트와 JPA에 대해 알아볼까요?(2)"
date: 2022-08-17T23:01:00+09:00
categories:
- development
tags:
- development
keywords:
- backend, java, development, spring, spring boot, jpa
image: web-design-gc14b2ac25_1920.jpg
---
![웹페이지](https://github.com/shcDE/pictures/blob/main/images_for_blog/web-design-gc14b2ac25_1920.jpg?raw=true)
________________________________________________________________________________________________________________________________________________________________________
안녕하세요. shcDE입니다. 이번 포스팅에서는 스프링 부트와 JPA를 활용하여 API를 개발하는 방법에 대해 알아보고자 합니다. API란 정의 및 프로토콜 집합을 사용하여 두 소프트웨어 구성 요소가 서로 통신할 수 있게 하는 메커니즘을 의미합니다. 예를 들어, 기상청의 소프트웨어 시스템에는 일일 기상 데이터가 들어 있습니다. 휴대폰의 날씨 애플리케이션은 API를 통해 이 시스템과 대화하여 휴대폰에 매일 최신 날씨 정보를 표시합니다. 이러한 API를 개발하는 방법에 대해 지금부터 알아보겠습니다.
________________________________________________________________________________________________________________________________________________________________________
시작 전에, 해당 포스팅은 김영한님의 ['실전! 스프링 부트와 JPA 활용2 - API 개발과 성능 최적화'](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81%EB%B6%80%ED%8A%B8-JPA-API%EA%B0%9C%EB%B0%9C-%EC%84%B1%EB%8A%A5%EC%B5%9C%EC%A0%81%ED%99%94/dashboard) 전체 내용을 공부하여 정리한 내용임을 밝히며, 직접 해당 강의를 구매하여 게시물을 작성함을 알립니다. 참고로, 환경설정은 개인의 컴퓨터 환경에 따라 다르므로 따로 다루지 않겠습니다.
________________________________________________________________________________________________________________________________________________________________________
우선 Postman 설치를 완료했으면 회원 등록 API를 설계합니다. 해당 코드는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
```java
package jpabook.jpashop.api;
import jpabook.jpashop.domain.Member;
import jpabook.jpashop.service.MemberService;
import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.RequiredArgsConstructor;
import org.springframework.web.bind.annotation.*;
import javax.validation.Valid;
import java.util.List;
import java.util.stream.Collectors;
@RestController
@RequiredArgsConstructor
public class MemberApiController {
    private final MemberService memberService;
/**
* 등록 V1: 요청 값으로 Member 엔티티를 직접 받는다.
* 문제점
* - 엔티티에 프레젠테이션 계층을 위한 로직이 추가된다.
* - 엔티티에 API 검증을 위한 로직이 들어간다. (@NotEmpty 등등)
* - 실무에서는 회원 엔티티를 위한 API가 다양하게 만들어지는데, 한 엔티티에 각각의 API를
위한 모든 요청 요구사항을 담기는 어렵다.
* - 엔티티가 변경되면 API 스펙이 변한다.
* 결론
* - API 요청 스펙에 맞추어 별도의 DTO를 파라미터로 받는다. 
*/
    @PostMapping("/api/v1/members")
    public CreateMemberResponse saveMemberV1(@RequestBody @Valid Member member) {
        Long id = memberService.join(member);
        return new CreateMemberResponse(id);
    }
    @Data
    static class CreateMemberRequest {
        private String name;
    }
    @Data
    static class CreateMemberResponse {
        private Long id;
        public CreateMemberResponse(Long id) {
            this.id = id;
        } 
    }
}
```
________________________________________________________________________________________________________________________________________________________________________
V2 엔티티 대신에 DTO를 RequestBody에 매핑하는 코드는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
```java
/**
* 등록 V2: 요청 값으로 Member 엔티티 대신에 별도의 DTO를 받는다.
*/
@PostMapping("/api/v2/members")
public CreateMemberResponse saveMemberV2(@RequestBody @Valid CreateMemberRequest request) {
    Member member = new Member();
    member.setName(request.getName());
    Long id = memberService.join(member);
    return new CreateMemberResponse(id);
}
```
________________________________________________________________________________________________________________________________________________________________________
회원 수정 API 코드는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
```java
/**
* 수정 API
*/
@PutMapping("/api/v2/members/{id}")
public UpdateMemberResponse updateMemberV2(@PathVariable("id") Long id, @RequestBody @Valid UpdateMemberRequest request) {
    memberService.update(id, request.getName());
    Member findMember = memberService.findOne(id);
    return new UpdateMemberResponse(findMember.getId(), findMember.getName());
}
    
@Data
static class UpdateMemberRequest {
    private String name;
}
    
@Data
@AllArgsConstructor
static class UpdateMemberResponse {
    private Long id;
    private String name;
}
```
________________________________________________________________________________________________________________________________________________________________________
회원 수정 코드도 DTO를 요청 파라미터에 매핑할 수 있습니다.
________________________________________________________________________________________________________________________________________________________________________
```java
public class MemberService {
    private final MemberRepository memberRepository;
    /**
    * 회원 수정
    */
    @Transactional
    public void update(Long id, String name) {
        Member member = memberRepository.findOne(id);
        member.setName(name);
    }
}
```
________________________________________________________________________________________________________________________________________________________________________
이제 회원 조회 API를 설계하겠습니다. 먼저 회원조회 V1 API는 응답 값으로 엔티티를 직접 외부에 노출하는 방식으로 설계합니다.
________________________________________________________________________________________________________________________________________________________________________
```java
package jpabook.jpashop.api;
@RestController
@RequiredArgsConstructor
public class MemberApiController {
    private final MemberService memberService;
/**
* 조회 V1: 응답 값으로 엔티티를 직접 외부에 노출한다.
* 문제점
* - 엔티티에 프레젠테이션 계층을 위한 로직이 추가된다.
* - 기본적으로 엔티티의 모든 값이 노출된다.
* - 응답 스펙을 맞추기 위해 로직이 추가된다. (@JsonIgnore, 별도의 뷰 로직 등등)
* - 실무에서는 같은 엔티티에 대해 API가 용도에 따라 다양하게 만들어지는데, 한 엔티티에 각각의
API를 위한 프레젠테이션 응답 로직을 담기는 어렵다.
* - 엔티티가 변경되면 API 스펙이 변한다.
* - 추가로 컬렉션을 직접 반환하면 항후 API 스펙을 변경하기 어렵다.(별도의 Result 클래스생성으로 해결) 
* 결론
* - API 응답 스펙에 맞추어 별도의 DTO를 반환한다. */
//조회 V1: 안 좋은 버전, 모든 엔티티가 노출, @JsonIgnore -> 이건 정말 최악, api가 이거 하나인가! 화면에 종속적이지 마라!
    @GetMapping("/api/v1/members")
    public List<Member> membersV1() {
        return memberService.findMembers();
    }
}
```
________________________________________________________________________________________________________________________________________________________________________
회원조회 V2 API는 응답 값으로 엔티티가 아닌 별도의 DTO를 사용할 수 있도록 설계합니다.
________________________________________________________________________________________________________________________________________________________________________
```java
/**
              
 * 조회 V2: 응답 값으로 엔티티가 아닌 별도의 DTO를 반환한다.
*/
@GetMapping("/api/v2/members")
public Result membersV2() {
    List<Member> findMembers = memberService.findMembers(); //엔티티 -> DTO 변환
    List<MemberDto> collect = findMembers.stream()
        .map(m -> new MemberDto(m.getName()))
        .collect(Collectors.toList());
        return new Result(collect);
}
      
@Data
@AllArgsConstructor
static class Result<T> {
    private T data;
}
      
@Data
@AllArgsConstructor
static class MemberDto {
    private String name;
}
```
________________________________________________________________________________________________________________________________________________________________________
이제 본격적으로 더 상세한 API 개발을 진행하겠습니다. 우선 조회용 샘플 데이터를 입력합니다.
________________________________________________________________________________________________________________________________________________________________________
```java
package jpabook.jpashop;
import jpabook.jpashop.domain.*;
import jpabook.jpashop.domain.item.Book;
import lombok.RequiredArgsConstructor;
import org.springframework.stereotype.Component;
import org.springframework.transaction.annotation.Transactional;
import javax.annotation.PostConstruct;
import javax.persistence.EntityManager;
@Component
@RequiredArgsConstructor
public class InitDb {
    private final InitService initService;
    @PostConstruct
    public void init() {
        initService.dbInit1();
        initService.dbInit2();
    }
    @Component
    @Transactional
    @RequiredArgsConstructor
    static class InitService {
        private final EntityManager em;
        public void dbInit1() {
            Member member = createMember("userA", "서울", "1", "1111"); em.persist(member);
            Book book1 = createBook("JPA1 BOOK", 10000, 100);
            em.persist(book1);
            Book book2 = createBook("JPA2 BOOK", 20000, 100);
            em.persist(book2);
            OrderItem orderItem1 = OrderItem.createOrderItem(book1, 10000, 1);
            OrderItem orderItem2 = OrderItem.createOrderItem(book2, 20000, 2);
            Order order = Order.createOrder(member, createDelivery(member),
orderItem1, orderItem2);
            em.persist(order);
        }
        public void dbInit2() {
            Member member = createMember("userB", "진주", "2", "2222"); em.persist(member);
            Book book1 = createBook("SPRING1 BOOK", 20000, 200);
            em.persist(book1);
            Book book2 = createBook("SPRING2 BOOK", 40000, 300);
            em.persist(book2);
            Delivery delivery = createDelivery(member);
            OrderItem orderItem1 = OrderItem.createOrderItem(book1, 20000, 3);
            OrderItem orderItem2 = OrderItem.createOrderItem(book2, 40000, 4);
            Order order = Order.createOrder(member, delivery, orderItem1,
orderItem2);
            em.persist(order);
        }
        private Member createMember(String name, String city, String street, String zipcode) {
            Member member = new Member();
            member.setName(name);
            member.setAddress(new Address(city, street, zipcode));
            return member;
        }
        private Book createBook(String name, int price, int stockQuantity) {
            Book book = new Book();
            book.setName(name);
            book.setPrice(price);
            book.setStockQuantity(stockQuantity);
            return book;
        }
        private Delivery createDelivery(Member member) {
            Delivery delivery = new Delivery();
            delivery.setAddress(member.getAddress());
            return delivery;
        }
    } 
}
```
________________________________________________________________________________________________________________________________________________________________________
이제 지연 로딩과 조회 성능 최적화를 진행하겠습니다. 우선 V1에서 간단한 주문 조회 API를 설계합니다. 여기에서 엔티티를 직접 노출해보겠습니다.
________________________________________________________________________________________________________________________________________________________________________
- OrderSimpleApiController
```java
package jpabook.jpashop.api;
import jpabook.jpashop.domain.Address;
import jpabook.jpashop.domain.Order;
import jpabook.jpashop.domain.OrderStatus;
import jpabook.jpashop.repository.*;
import lombok.Data;
import lombok.RequiredArgsConstructor;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;
import java.time.LocalDateTime;
import java.util.List;
import static java.util.stream.Collectors.toList;
/** *
* xToOne(ManyToOne, OneToOne) 관계 최적화 * Order
* Order -> Member
* Order -> Delivery
*
*/
@RestController
@RequiredArgsConstructor
public class OrderSimpleApiController {
    private final OrderRepository orderRepository;
    /**
    * V1. 엔티티 직접 노출
    * - Hibernate5Module 모듈 등록, LAZY=null 처리 * - 양방향 관계 문제 발생 -> @JsonIgnore
    */
    @GetMapping("/api/v1/simple-orders")
    public List<Order> ordersV1() {
        List<Order> all = orderRepository.findAllByString(new OrderSearch());
        for (Order order : all) {
            order.getMember().getName(); //Lazy 강제 초기화
            order.getDelivery().getAddress(); //Lazy 강제 초기환 
        }
        return all; 
    }
}
```
________________________________________________________________________________________________________________________________________________________________________
- JpashopApplication 에 다음 코드 추가
```java
@Bean
Hibernate5Module hibernate5Module() {
    return new Hibernate5Module();
}
```
________________________________________________________________________________________________________________________________________________________________________
- 다음과 같이 설정하면 강제로 지연 로딩 가능
```java
@Bean
Hibernate5Module hibernate5Module() {
    Hibernate5Module hibernate5Module = new Hibernate5Module();
    //강제 지연 로딩 설정 
    hibernate5Module.configure(Hibernate5Module.Feature.FORCE_LAZY_LOADING,
    true);
    return hibernate5Module;
}
```
________________________________________________________________________________________________________________________________________________________________________
이제 간단한 주문 조회용 API V2를 개발하겠습니다. 여기에서는 엔티티를 DTO로 변환합니다.
________________________________________________________________________________________________________________________________________________________________________
- OrderSimpleApiController에 다음 추가
```java
/**
* V2. 엔티티를 조회해서 DTO로 변환(fetch join 사용X) * - 단점: 지연로딩으로 쿼리 N번 호출
*/
@GetMapping("/api/v2/simple-orders")
public List<SimpleOrderDto> ordersV2() {
    List<Order> orders = orderRepository.findAll();
    List<SimpleOrderDto> result = orders.stream()
          .map(o -> new SimpleOrderDto(o))
          .collect(toList());
    return result;
}
  
@Data
static class SimpleOrderDto {
    private Long orderId;
    private String name;
    private LocalDateTime orderDate; //주문시간 
    private OrderStatus orderStatus;
    private Address address;
    public SimpleOrderDto(Order order) {
        orderId = order.getId();
        name = order.getMember().getName();
        orderDate = order.getOrderDate();
        orderStatus = order.getStatus();
        address = order.getDelivery().getAddress();
    } 
}
```
________________________________________________________________________________________________________________________________________________________________________
이제 간단한 주문 조회용 V3 API를 개발하여 엔티티를 DTO로 변환하겠습니다. 페치 조인 최적화를 중점으로 개발하겠습니다.
________________________________________________________________________________________________________________________________________________________________________
- OrderSimpleApiController에 다음 코드 추가
```java
/**
* V3. 엔티티를 조회해서 DTO로 변환(fetch join 사용O)
* - fetch join으로 쿼리 1번 호출
* 참고: fetch join에 대한 자세한 내용은 JPA 기본편 참고(정말 중요함) 
*/
@GetMapping("/api/v3/simple-orders")
public List<SimpleOrderDto> ordersV3() {
    List<Order> orders = orderRepository.findAllWithMemberDelivery();
    List<SimpleOrderDto> result = orders.stream()
        .map(o -> new SimpleOrderDto(o))
        .collect(toList());
    return result;
}
```
________________________________________________________________________________________________________________________________________________________________________
- OrderRepository에 다음 코드 추가
```java
public List<Order> findAllWithMemberDelivery() {
    return em.createQuery(
        "select o from Order o" +
        " join fetch o.member m" +
        " join fetch o.delivery d", Order.class)
        .getResultList();
}
```
________________________________________________________________________________________________________________________________________________________________________
이제 간단한 주문 조회용 V4 API를 개발하여 JPA에서 DTO로 바로 조회해보겠습니다.
________________________________________________________________________________________________________________________________________________________________________
- OrderSimpleApiController에 다음 코드 추가
```java
private final OrderSimpleQueryRepository orderSimpleQueryRepository; //의존관계 주입
/**
* V4. JPA에서 DTO로 바로 조회
*-쿼리1번 호출
* - select 절에서 원하는 데이터만 선택해서 조회 */
@GetMapping("/api/v4/simple-orders")
public List<OrderSimpleQueryDto> ordersV4() {
    return orderSimpleQueryRepository.findOrderDtos();
}
```
________________________________________________________________________________________________________________________________________________________________________
- OrderSimpleQueryRepository 조회 전용 리포지토리 코드 작성
```java
package jpabook.jpashop.repository.order.simplequery;
import lombok.RequiredArgsConstructor;
import org.springframework.stereotype.Repository;
import javax.persistence.EntityManager;
import java.util.List;
@Repository
@RequiredArgsConstructor
public class OrderSimpleQueryRepository {
    private final EntityManager em;
    public List<OrderSimpleQueryDto> findOrderDtos() {
        return em.createQuery(
            "select new jpabook.jpashop.repository.order.simplequery.OrderSimpleQueryDto(o.id, m.name, o.orderDate, o.status, d.address)" +
            " from Order o" +
            " join o.member m" +
            " join o.delivery d", OrderSimpleQueryDto.class)
            .getResultList();
    }
}
```
________________________________________________________________________________________________________________________________________________________________________
- OrderSimpleQueryDto 리포지토리에서 DTO 직접 조회
```java
package jpabook.jpashop.repository.order.simplequery;
import jpabook.jpashop.domain.Address;
import jpabook.jpashop.domain.OrderStatus;
import lombok.Data;
import java.time.LocalDateTime;
@Data
public class OrderSimpleQueryDto {
private Long orderId;
private String name;
private LocalDateTime orderDate; //주문시간 private OrderStatus orderStatus;
private Address address;
    public OrderSimpleQueryDto(Long orderId, String name, LocalDateTime orderDate, OrderStatus orderStatus, Address address) {
        this.orderId = orderId;
        this.name = name;
        this.orderDate = orderDate;
        this.orderStatus = orderStatus;
        this.address = address;
    } 
}
```
________________________________________________________________________________________________________________________________________________________________________
이제 컬렉션 조회 최적화를 진행하겠습니다. 우선 주문 조회 V1 API에서 엔티티를 직접 노출하겠습니다.
________________________________________________________________________________________________________________________________________________________________________
```java
package jpabook.jpashop.api;
import jpabook.jpashop.domain.Address;
import jpabook.jpashop.domain.Order;
import jpabook.jpashop.domain.OrderItem;
import jpabook.jpashop.domain.OrderStatus;
import jpabook.jpashop.repository.*;
import lombok.Data;
import lombok.RequiredArgsConstructor;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;
import java.time.LocalDateTime;
import java.util.List;
import static java.util.stream.Collectors.*;
/**
* V1. 엔티티 직접 노출
* - 엔티티가 변하면 API 스펙이 변한다.
* - 트랜잭션 안에서 지연 로딩 필요
* - 양방향 연관관계 문제 *
* V2. 엔티티를 조회해서 DTO로 변환(fetch join 사용X)
* - 트랜잭션 안에서 지연 로딩 필요
* V3. 엔티티를 조회해서 DTO로 변환(fetch join 사용O)
* - 페이징 시에는 N 부분을 포기해야함(대신에 batch fetch size? 옵션 주면 N -> 1 쿼리로 변경
가능) *
*V4.JPA에서 DTO로 바로 조회, 컬렉션 N 조회 (1+NQuery)
* - 페이징 가능
*V5.JPA에서 DTO로 바로 조회, 컬렉션 1 조회 최적화 버전 (1+1Query)
* - 페이징 가능
* V6. JPA에서 DTO로 바로 조회, 플랫 데이터(1Query) (1 Query)
* - 페이징 불가능... *
*/
@RestController
@RequiredArgsConstructor
public class OrderApiController {
    private final OrderRepository orderRepository;
    /**
    * V1. 엔티티 직접 노출
    * - Hibernate5Module 모듈 등록, LAZY=null 처리 
    * - 양방향 관계 문제 발생 -> @JsonIgnore
    */
    @GetMapping("/api/v1/orders")
    public List<Order> ordersV1() {
        List<Order> all = orderRepository.findAll();
        for (Order order : all) {
            order.getMember().getName(); //Lazy 강제 초기화 
            order.getDelivery().getAddress(); //Lazy 강제 초기환
            List<OrderItem> orderItems = order.getOrderItems(); 
            orderItems.stream().forEach(o -> o.getItem().getName()); //Lazy 강제 초기화
        }
        return all; 
    }
}
```
________________________________________________________________________________________________________________________________________________________________________
주문 조회용 V2 API에서는 엔티티를 DTO로 변환합니다.
________________________________________________________________________________________________________________________________________________________________________
```java 
@GetMapping("/api/v2/orders")
public List<OrderDto> ordersV2() {
    List<Order> orders = orderRepository.findAll();
    List<OrderDto> result = orders.stream()
            .map(o -> new OrderDto(o))
            .collect(toList());
    return result;
}
```
________________________________________________________________________________________________________________________________________________________________________
OrderApiController에는 다음 코드를 추가합니다.
________________________________________________________________________________________________________________________________________________________________________
```java 
@Data
static class OrderDto {
    private Long orderId;
    private String name;
    private LocalDateTime orderDate; //주문시간 
    private OrderStatus orderStatus;
    private Address address;
    private List<OrderItemDto> orderItems;
    public OrderDto(Order order) {
        orderId = order.getId();
        name = order.getMember().getName();
        orderDate = order.getOrderDate();
        orderStatus = order.getStatus();
        address = order.getDelivery().getAddress();
        orderItems = order.getOrderItems().stream()
            .map(orderItem -> new OrderItemDto(orderItem))
            .collect(toList());
    } 
}
  
@Data
static class OrderItemDto {
    private String itemName;//상품 명 
    private int orderPrice; //주문 가격 
    private int count; //주문 수량
    public OrderItemDto(OrderItem orderItem) {
        itemName = orderItem.getItem().getName();
        orderPrice = orderItem.getOrderPrice();
        count = orderItem.getCount();
    } 
}
```
________________________________________________________________________________________________________________________________________________________________________
주문 조회용 V3 API에서 엔티티를 DTO로 변환하겠습니다. 페치 조인 최적화에 중점을 두겠습니다.
________________________________________________________________________________________________________________________________________________________________________
- OrderApiController에 다음 코드 추가
```java
@GetMapping("/api/v3/orders")
public List<OrderDto> ordersV3() {
    List<Order> orders = orderRepository.findAllWithItem();
    List<OrderDto> result = orders.stream()
        .map(o -> new OrderDto(o))
        .collect(toList());
    return result;
}
```
________________________________________________________________________________________________________________________________________________________________________
- OrderRepository에 다음 코드 추가
```java
public List<Order> findAllWithItem() {
    return em.createQuery(
        "select distinct o from Order o" +
        " join fetch o.member m" +
        " join fetch o.delivery d" +
        " join fetch o.orderItems oi" +
        " join fetch oi.item i", Order.class)
        .getResultList();
}
```
________________________________________________________________________________________________________________________________________________________________________
이제 주문 조회용 V3.1 API를 개발하여 엔티티를 DTO로 변환하겠습니다. 페이징과 한계 돌파를 중점으로 보여드리겠습니다.
________________________________________________________________________________________________________________________________________________________________________
- OrderRepository에 다음 코드 추가
```java
public List<Order> findAllWithMemberDelivery(int offset, int limit) {
    return em.createQuery(
        "select o from Order o" +
        " join fetch o.member m" +
        " join fetch o.delivery d", Order.class)
        .setFirstResult(offset)
        .setMaxResults(limit)
        .getResultList();
}
```
________________________________________________________________________________________________________________________________________________________________________
- OrderApiController에 다음 코드 추가
```java
/**
* V3.1 엔티티를 조회해서 DTO로 변환 페이징 고려
*-ToOne 관계만 우선 모두 페치 조인으로 최적화
* - 컬렉션 관계는 hibernate.default_batch_fetch_size, @BatchSize로 최적화 
*/
@GetMapping("/api/v3.1/orders")
public List<OrderDto> ordersV3_page(@RequestParam(value = "offset", defaultValue = "0") int offset,
                                    @RequestParam(value = "limit", defaultValue= "100") int limit) {
    List<Order> orders = orderRepository.findAllWithMemberDelivery(offset, limit);
    List<OrderDto> result = orders.stream()
        .map(o -> new OrderDto(o))
        .collect(toList());
    return result;
}
```
________________________________________________________________________________________________________________________________________________________________________
주문 조회용 V4 API에서는 JPA에서 DTO를 직접 조회하겠습니다.
________________________________________________________________________________________________________________________________________________________________________
- OrderApiController에 다음 코드 추가
```java
private final OrderQueryRepository orderQueryRepository;
@GetMapping("/api/v4/orders")
public List<OrderQueryDto> ordersV4() {
    return orderQueryRepository.findOrderQueryDtos();
}
```
________________________________________________________________________________________________________________________________________________________________________
- OrderQueryRepository 코드
```java
package jpabook.jpashop.repository.order.query;
import lombok.RequiredArgsConstructor;
import org.springframework.stereotype.Repository;
import javax.persistence.EntityManager;
import java.util.List;
import java.util.Map;
import java.util.stream.Collectors;
@Repository
@RequiredArgsConstructor
public class OrderQueryRepository {
    private final EntityManager em;
/**
* 컬렉션은 별도로 조회
* Query: 루트 1번, 컬렉션 N 번 * 단건 조회에서 많이 사용하는 방식 
*/
    public List<OrderQueryDto> findOrderQueryDtos() { //루트 조회(toOne 코드를 모두 한번에 조회)
        List<OrderQueryDto> result = findOrders(); //루프를 돌면서 컬렉션 추가(추가 쿼리 실행)
        result.forEach(o -> {
            List<OrderItemQueryDto> orderItems = findOrderItems(o.getOrderId());
            o.setOrderItems(orderItems);
        });
        return result;
    }
/**
* 1:N 관계(컬렉션)를 제외한 나머지를 한번에 조회
*/
    private List<OrderQueryDto> findOrders() {
        return em.createQuery(
            "select new
            jpabook.jpashop.repository.order.query.OrderQueryDto(o.id, m.name, o.orderDate,
            o.status, d.address)" +
            " from Order o" +
            " join o.member m" +
            " join o.delivery d", OrderQueryDto.class)
            .getResultList();
    }
/**
* 1:N 관계인 orderItems 조회
*/
    private List<OrderItemQueryDto> findOrderItems(Long orderId) {
        return em.createQuery(
                "select new
                jpabook.jpashop.repository.order.query.OrderItemQueryDto(oi.order.id, i.name,
                oi.orderPrice, oi.count)" + 
                " from OrderItem oi" +
                " join oi.item i" +
                " where oi.order.id = : orderId",
                OrderItemQueryDto.class)
                .setParameter("orderId", orderId)
                .getResultList();
    }
}
```
________________________________________________________________________________________________________________________________________________________________________
- OrderQueryDto
```java
package jpabook.jpashop.repository.order.query;
import jpabook.jpashop.domain.Address;
import jpabook.jpashop.domain.OrderStatus;
import lombok.Data;
import lombok.EqualsAndHashCode;
import java.time.LocalDateTime;
import java.util.List;
@Data
@EqualsAndHashCode(of = "orderId")
public class OrderQueryDto {
    private Long orderId;
    private String name;
    private LocalDateTime orderDate; //주문시간 
    private OrderStatus orderStatus;
    private Address address;
    private List<OrderItemQueryDto> orderItems;
    public OrderQueryDto(Long orderId, String name, LocalDateTime orderDate,
    OrderStatus orderStatus, Address address) {
        this.orderId = orderId;
        this.name = name;
        this.orderDate = orderDate;
        this.orderStatus = orderStatus;
        this.address = address;
    } 
}
```
________________________________________________________________________________________________________________________________________________________________________
- OrderItemQueryDto
```java
package jpabook.jpashop.repository.order.query;
import com.fasterxml.jackson.annotation.JsonIgnore;
import lombok.Data;
@Data
public class OrderItemQueryDto {
    @JsonIgnore
    private Long orderId; //주문번호 
    private String itemName;//상품 명 
    private int orderPrice; //주문 가격 
    private int count; //주문 수량
    public OrderItemQueryDto(Long orderId, String itemName, int orderPrice, int count) {
        this.orderId = orderId;
        this.itemName = itemName;
        this.orderPrice = orderPrice;
        this.count = count;
    } 
}
```
________________________________________________________________________________________________________________________________________________________________________
이제 주문 조회용 V5 API에서는 JPA에서 DTO를 직접 조회하겠습니다. 특히, 컬렉션 조회 최적화에 중점을 두어 개발을 하겠습니다.
________________________________________________________________________________________________________________________________________________________________________
- OrderApiController에 추가
```java
@GetMapping("/api/v5/orders")
public List<OrderQueryDto> ordersV5() {
    return orderQueryRepository.findAllByDto_optimization();
}
```
________________________________________________________________________________________________________________________________________________________________________
- OrderQueryRepository에 추가
```java
/**
* 최적화
* Query: 루트 1번, 컬렉션 1번
* 데이터를 한꺼번에 처리할 때 많이 사용하는 방식 *
*/
public List<OrderQueryDto> findAllByDto_optimization() { 
    //루트 조회(toOne 코드를 모두 한번에 조회)
    List<OrderQueryDto> result = findOrders();
    //orderItem 컬렉션을 MAP 한방에 조회
    Map<Long, List<OrderItemQueryDto>> orderItemMap = findOrderItemMap(toOrderIds(result));
    //루프를 돌면서 컬렉션 추가(추가 쿼리 실행X)
    result.forEach(o -> o.setOrderItems(orderItemMap.get(o.getOrderId())));
    return result;
}
private List<Long> toOrderIds(List<OrderQueryDto> result) {
    return result.stream()
        .map(o -> o.getOrderId())
        .collect(Collectors.toList());
}
private Map<Long, List<OrderItemQueryDto>> findOrderItemMap(List<Long> orderIds) {
      List<OrderItemQueryDto> orderItems = em.createQuery(
              "select new
              jpabook.jpashop.repository.order.query.OrderItemQueryDto(oi.order.id, i.name,
  oi.orderPrice, oi.count)" +
              " from OrderItem oi" +
              " join oi.item i" +
              " where oi.order.id in :orderIds", OrderItemQueryDto.class)
              .setParameter("orderIds", orderIds)
              .getResultList();
              return orderItems.stream()
              .collect(Collectors.groupingBy(OrderItemQueryDto::getOrderId));
}
```
________________________________________________________________________________________________________________________________________________________________________
이제 주문 조회용 V6 API를 개발하겠습니다. JPA에서 DTO로 직접 조회하고, 플랫 데이터를 최적화하는 작업을 중점으로 진행하겠습니다.
________________________________________________________________________________________________________________________________________________________________________
- OrderApiController에 추가
```java
@GetMapping("/api/v6/orders")
public List<OrderQueryDto> ordersV6() {
    List<OrderFlatDto> flats = orderQueryRepository.findAllByDto_flat();
    return flats.stream()
              .collect(groupingBy(o -> new OrderQueryDto(o.getOrderId(),
              o.getName(), o.getOrderDate(), o.getOrderStatus(), o.getAddress()),
              mapping(o -> new OrderItemQueryDto(o.getOrderId(),
              o.getItemName(), o.getOrderPrice(), o.getCount()), toList())
              )).entrySet().stream()
              .map(e -> new OrderQueryDto(e.getKey().getOrderId(),
              e.getKey().getName(), e.getKey().getOrderDate(), e.getKey().getOrderStatus(),
              e.getKey().getAddress(), e.getValue()))
              .collect(toList());
}
```
________________________________________________________________________________________________________________________________________________________________________
- OrderQueryDto에 생성자 추가
```java
public OrderQueryDto(Long orderId, String name, LocalDateTime orderDate,
OrderStatus orderStatus, Address address, List<OrderItemQueryDto> orderItems) {
    this.orderId = orderId;
    this.name = name;
    this.orderDate = orderDate;
    this.orderStatus = orderStatus;
    this.address = address;
    this.orderItems = orderItems;
}
```
________________________________________________________________________________________________________________________________________________________________________
- OrderQueryRepository에 추가
```java
public List<OrderFlatDto> findAllByDto_flat() {
    return em.createQuery(
        "select new
        jpabook.jpashop.repository.order.query.OrderFlatDto(o.id, m.name, o.orderDate,
        o.status, d.address, i.name, oi.orderPrice, oi.count)" +
        " from Order o" +
        " join o.member m" +
        " join o.delivery d" +
        " join o.orderItems oi" +
        " join oi.item i", OrderFlatDto.class)
        .getResultList();
}
```
________________________________________________________________________________________________________________________________________________________________________
- OrderFlatDto
```java
package jpabook.jpashop.repository.order.query;
import jpabook.jpashop.domain.Address;
import jpabook.jpashop.domain.OrderStatus;
import lombok.Data;
import java.time.LocalDateTime;
@Data
public class OrderFlatDto {
    private Long orderId;
    private String name;
    private LocalDateTime orderDate; //주문시간 
    private Address address;
    private OrderStatus orderStatus;
    private String itemName;//상품 명 
    private int orderPrice; //주문 가격 
    private int count; //주문 수량
    public OrderFlatDto(Long orderId, String name, LocalDateTime orderDate,
    OrderStatus orderStatus, Address address, String itemName, int orderPrice, int
    count) {
        this.orderId = orderId;
        this.name = name;
        this.orderDate = orderDate;
        this.orderStatus = orderStatus;
        this.address = address;
        this.itemName = itemName;
        this.orderPrice = orderPrice;
        this.count = count;
    } 
}
```
________________________________________________________________________________________________________________________________________________________________________
사실 다음 파트로 'OSIV와 성능 최적화'와 관련된 내용이 강의에서는 있었습니다. 하지만 주로 이론을 다루는 내용이기에 여기에서는 작성하지 않겠습니다.
여기까지 스프링 부트와 JPA로 API를 개발하는 과정에 대한 코드를 다루었습니다. 
________________________________________________________________________________________________________________________________________________________________________
여기까지 공부하면서 스프링과 스프링 부트, JPA에 대한 기초적인 내용은 모두 공부한 것 같습니다. 아직 공부해야 할 내용이 많지만, 앞으로 CS에 집중하기 위해 다음 포스팅부터는 CS 전공 면접 책을 공부하고 정리한 내용을 게시하려고 합니다. 앞으로 하반기 공채 시즌이 다가오는 만큼, 최대한 매일 해당 내용을 게시하고자 합니다.
________________________________________________________________________________________________________________________________________________________________________
오늘도 긴 글 읽어주시느라 고생하셨습니다. 이상으로 스프링에 대한 내용 포스팅을 모두 마치겠습니다.
________________________________________________________________________________________________________________________________________________________________________
#### 감사합니다.
________________________________________________________________________________________________________________________________________________________________________
[레퍼런스]
1. 실전! 스프링 부트와 JPA 활용2 - API 개발과 성능 최적화 : https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81%EB%B6%80%ED%8A%B8-JPA-API%EA%B0%9C%EB%B0%9C-%EC%84%B1%EB%8A%A5%EC%B5%9C%EC%A0%81%ED%99%94/dashboard