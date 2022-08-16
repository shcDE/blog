---
title: "스프링 부트와 JPA에 대해 알아볼까요?(1)"
date: 2022-08-15T23:40:29+09:00
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
안녕하세요. shcDE입니다. 드디어 이번 포스팅부터는 스프링 부트와 JPA에 대해 다루게 되었습니다. 스프링 부트란 기존 스프링 프레임워크에 톰캣 서버를 내장하고, 여러 기능을 추가하여 꾸준한 인기를 얻고 있는 프레임워크이며, JPA란 자바 진영에서 ORM(Object-Relational Mapping) 기술 표준으로 사용하는 인터페이스 모음이며, 자바 애플리케이션에서 관계형 데이터베이스를 사용하는 방식을 정의한 인터페이스입니다.
________________________________________________________________________________________________________________________________________________________________________
이번 포스팅에서는 이러한 스프링 부트와 JPA를 활용하여 웹 사이트를 개발하는 과정에 대해 알아보겠으며, 해당 포스팅은 김영한님의 ['실전! 스프링 부트와 JPA 활용1 - 웹 애플리케이션 개발'](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81%EB%B6%80%ED%8A%B8-JPA-%ED%99%9C%EC%9A%A9-1/dashboard) 전체 내용을 공부하여 정리한 내용임을 밝히며, 직접 해당 강의를 구매하여 게시물을 작성함을 알립니다. 참고로, 환경설정은 개인의 컴퓨터 환경에 따라 다르므로 따로 다루지 않겠습니다.
________________________________________________________________________________________________________________________________________________________________________
우선 엔티티 클래스를 개발하는 과정에 대해 알아보겠습니다. 회원 엔티티 코드는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
```java
package jpabook.jpashop.domain;
import javax.persistence.*;
import java.util.ArrayList;
import java.util.List;
@Entity
@Getter @Setter
public class Member {
    @Id @GeneratedValue
    @Column(name = "member_id")
    private Long id;
    private String name;
    @Embedded
    private Address address;
    @OneToMany(mappedBy = "member")
    private List<Order> orders = new ArrayList<>();
}
```
________________________________________________________________________________________________________________________________________________________________________
주문 엔티티 개발 코드는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
```java
package jpabook.jpashop.domain;
import lombok.Getter;
import lombok.Setter;
import javax.persistence.*;
import java.time.LocalDateTime;
import java.util.ArrayList;
import java.util.List;
@Entity
@Table(name = "orders")
@Getter @Setter
public class Order {
    @Id @GeneratedValue
    @Column(name = "order_id")
    private Long id;
    @ManyToOne(fetch = FetchType.LAZY) @JoinColumn(name = "member_id") private Member member; //주문 회원
    @OneToMany(mappedBy = "order", cascade = CascadeType.ALL)
    private List<OrderItem> orderItems = new ArrayList<>();
    @OneToOne(cascade = CascadeType.ALL, fetch = FetchType.LAZY) @JoinColumn(name = "delivery_id")
    private Delivery delivery; //배송정보
    private LocalDateTime orderDate; //주문시간 @Enumerated(EnumType.STRING)
    private OrderStatus status; //주문상태 [ORDER, CANCEL]
    //==연관관계 메서드==//
    public void setMember(Member member) {
        this.member = member;
        member.getOrders().add(this);
    }
    public void addOrderItem(OrderItem orderItem) {
        orderItems.add(orderItem);
        orderItem.setOrder(this);
    }
    public void setDelivery(Delivery delivery) {
        this.delivery = delivery;
        delivery.setOrder(this);
    } 
}
```
________________________________________________________________________________________________________________________________________________________________________
주문상태 코드는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
```java
package jpabook.jpashop.domain;
public enum OrderStatus {
    ORDER, CANCEL
}
```
________________________________________________________________________________________________________________________________________________________________________
주문상품 엔티티 코드는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
```java
package jpabook.jpashop.domain;
import lombok.Getter;
import lombok.Setter;
import jpabook.jpashop.domain.item.Item;
import javax.persistence.*;
@Entity
@Table(name = "order_item")
@Getter @Setter
public class OrderItem {
    @Id @GeneratedValue
    @Column(name = "order_item_id")
    private Long id;
    @ManyToOne(fetch = FetchType.LAZY) @JoinColumn(name = "item_id") private Item item; //주문 상품
    @ManyToOne(fetch = FetchType.LAZY) @JoinColumn(name = "order_id") private Order order; //주문
    private int orderPrice; //주문 가격 private int count; //주문 수량
}
```
________________________________________________________________________________________________________________________________________________________________________
상품 엔티티 코드는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
```java
package jpabook.jpashop.domain.item;
import lombok.Getter;
import lombok.Setter;
import jpabook.jpashop.domain.Category;
import javax.persistence.*;
import java.util.ArrayList;
import java.util.List;
@Entity
@Inheritance(strategy = InheritanceType.SINGLE_TABLE)
@DiscriminatorColumn(name = "dtype")
@Getter @Setter
public abstract class Item {
    @Id @GeneratedValue
    @Column(name = "item_id")
    private Long id;
    private String name;
    private int price;
    private int stockQuantity;
    @ManyToMany(mappedBy = "items")
    private List<Category> categories = new ArrayList<Category>();
}
```
________________________________________________________________________________________________________________________________________________________________________
상품 - 도서 엔티티 코드는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
```java
package jpabook.jpashop.domain.item;
import lombok.Getter;
import lombok.Setter;
import javax.persistence.DiscriminatorValue;
import javax.persistence.Entity;
@Entity
@DiscriminatorValue("B")
@Getter @Setter
public class Book extends Item {
    private String author;
    private String isbn;
}
```
________________________________________________________________________________________________________________________________________________________________________
상품 - 음반 엔티티 코드는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
```java
package jpabook.jpashop.domain.item;
import lombok.Getter;
import lombok.Setter;
import javax.persistence.DiscriminatorValue;
import javax.persistence.Entity;
@Entity
@DiscriminatorValue("A")
@Getter @Setter
public class Album extends Item {
    private String artist;
    private String etc;
}
```
________________________________________________________________________________________________________________________________________________________________________
상품 - 영화 엔티티 코드는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
```java
package jpabook.jpashop.domain.item;
import lombok.Getter;
import lombok.Setter;
import javax.persistence.DiscriminatorValue;
import javax.persistence.Entity;
@Entity
@DiscriminatorValue("M")
@Getter @Setter
public class Movie extends Item {
    private String director;
    private String actor;
}
```
________________________________________________________________________________________________________________________________________________________________________
배송 엔티티 코드는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
```java
package jpabook.jpashop.domain;
import lombok.Getter;
import lombok.Setter;
import javax.persistence.*;
@Entity
@Getter @Setter
public class Delivery {
    @Id @GeneratedValue
    @Column(name = "delivery_id")
    private Long id;
    @OneToOne(mappedBy = "delivery", fetch = FetchType.LAZY)
    private Order order;
    @Embedded
    private Address address;
    @Enumerated(EnumType.STRING)
    private DeliveryStatus status; //ENUM [READY(준비), COMP(배송)]
}
```
________________________________________________________________________________________________________________________________________________________________________
배송 상태 코드는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
```java
package jpabook.jpashop.domain;
public enum DeliveryStatus {
    READY, COMP
}
```
________________________________________________________________________________________________________________________________________________________________________
카테고리 엔티티 코드는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
```java
package jpabook.jpashop.domain;
import lombok.Getter;
import lombok.Setter;
import jpabook.jpashop.domain.item.Item;
import javax.persistence.*;
import java.util.ArrayList;
import java.util.List;
@Entity
@Getter @Setter
public class Category {
    @Id @GeneratedValue
    @Column(name = "category_id")
    private Long id;
    private String name;
    @ManyToMany
    @JoinTable(name = "category_item",
            joinColumns = @JoinColumn(name = "category_id"),
            inverseJoinColumns = @JoinColumn(name = "item_id"))
    private List<Item> items = new ArrayList<>();
    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "parent_id")
    private Category parent;
    @OneToMany(mappedBy = "parent")
    private List<Category> child = new ArrayList<>();
    //==연관관계 메서드==//
    public void addChildCategory(Category child) {
        this.child.add(child);
        child.setParent(this);
    }
}
```
________________________________________________________________________________________________________________________________________________________________________
주소 값 타입 코드는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
```java
package jpabook.jpashop.domain;
import lombok.Getter;
import lombok.Setter;
import javax.persistence.Embeddable;
@Embeddable
@Getter
public class Address {
    private String city;
    private String street;
    private String zipcode;
    protected Address() {
    }
    public Address(String city, String street, String zipcode) {
        this.city = city;
        this.street = street;
        this.zipcode = zipcode;
    } 
}
```
________________________________________________________________________________________________________________________________________________________________________
이제 회원 도메인 개발을 진행하겠습니다. 그 중에서, 먼저  회원 리포지토리 개발을 진행하겠습니다. 회원 리포지토리 코드는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
```java
package jpabook.jpashop.repository;
import jpabook.jpashop.domain.Member;
import org.springframework.stereotype.Repository;
import javax.persistence.EntityManager;
import javax.persistence.PersistenceContext;
import java.util.List;
@Repository
public class MemberRepository {
    @PersistenceContext
    private EntityManager em;
    public void save(Member member) {
        em.persist(member);
    }
    public Member findOne(Long id) {
        return em.find(Member.class, id);
    }
    public List<Member> findAll() {
        return em.createQuery("select m from Member m", Member.class)
                .getResultList();
    }
    public List<Member> findByName(String name) {
        return em.createQuery("select m from Member m where m.name = :name", Member.class)
    } 
}
```
________________________________________________________________________________________________________________________________________________________________________
다음으로, 회원 서비스 개발을 진행하겠습니다. 회원 서비스 코드는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
```java
package jpabook.jpashop.service;
import jpabook.jpashop.domain.Member;
import jpabook.jpashop.repository.MemberRepository;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;
import java.util.List;
@Service
@Transactional(readOnly = true)
public class MemberService {
    @Autowired
    MemberRepository memberRepository;
    /**
    * 회원가입
    */
    @Transactional //변경
    public Long join(Member member) { validateDuplicateMember(member); //중복 회원 검증
        memberRepository.save(member);
        return member.getId();
    }
    private void validateDuplicateMember(Member member) {
        List<Member> findMembers = memberRepository.findByName(member.getName());
        if (!findMembers.isEmpty()) {
          throw new IllegalStateException("이미 존재하는 회원입니다."); 
        }
    }
    /**
    *전체 회원 조회
    */
    public List<Member> findMembers() {
        return memberRepository.findAll();
    }
    public Member findOne(Long memberId) {
        return memberRepository.findOne(memberId);
    } 
}
```
________________________________________________________________________________________________________________________________________________________________________
필드 주입은 다음과 같이 진행합니다.
________________________________________________________________________________________________________________________________________________________________________
```java
public class MemberService {
    @Autowired
    MemberRepository memberRepository;
    ...
}
```
________________________________________________________________________________________________________________________________________________________________________
생성자 주입은 다음과 같이 진행합니다.
________________________________________________________________________________________________________________________________________________________________________
```java
public class MemberService {
    private final MemberRepository memberRepository;
    public MemberService(MemberRepository memberRepository) {
        this.memberRepository = memberRepository;
    }
... 
}
```
________________________________________________________________________________________________________________________________________________________________________
lombok은 다음과 같이 활용합니다.
________________________________________________________________________________________________________________________________________________________________________
```java
@RequiredArgsConstructor
public class MemberService {
    private final MemberRepository memberRepository;
... 
}
```
________________________________________________________________________________________________________________________________________________________________________
참고로 스프링 데이터 JPA를 사용하면 EntityManager도 주입이 가능합니다.
________________________________________________________________________________________________________________________________________________________________________
```java
@Repository
@RequiredArgsConstructor
public class MemberRepository {
    private final EntityManager em;
... 
}
```
________________________________________________________________________________________________________________________________________________________________________
MemberService 최종 코드는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
```java
@Service
@Transactional(readOnly = true)
@RequiredArgsConstructor
public class MemberService {
    private final MemberRepository memberRepository;
    /**
    * 회원가입 
    */
    @Transactional //변경
    public Long join(Member member) {
    validateDuplicateMember(member); //중복 회원 검증 memberRepository.save(member);
    return member.getId();
    }
    private void validateDuplicateMember(Member member) {
        List<Member> findMembers = memberRepository.findByName(member.getName());
        if (!findMembers.isEmpty()) {throw new IllegalStateException("이미 존재하는 회원입니다."); }
    }
    /**
    *전체 회원 조회
    */
    public List<Member> findMembers() {
        return memberRepository.findAll();
    }
    public Member findOne(Long memberId) {
        return memberRepository.findOne(memberId);
    } 
}
```
________________________________________________________________________________________________________________________________________________________________________
회원가입 기능 테스트 코드는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
```java
package jpabook.jpashop.service;
import jpabook.jpashop.domain.Member;
import jpabook.jpashop.repository.MemberRepository;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.junit4.SpringRunner;
import org.springframework.transaction.annotation.Transactional;
import static org.junit.Assert.assertEquals;
import static org.junit.Assert.fail;
@RunWith(SpringRunner.class)
@SpringBootTest
@Transactional
public class MemberServiceTest {
    @Autowired MemberService memberService;
    @Autowired MemberRepository memberRepository;
    @Test
    public void 회원가입() throws Exception {
    //Given
        Member member = new Member();
        member.setName("kim");
    //When
        Long saveId = memberService.join(member);
    //Then
        assertEquals(member, memberRepository.findOne(saveId));
    }
    @Test(expected = IllegalStateException.class) public void 중복_회원_예외() throws Exception {
    //Given
        Member member1 = new Member();
        member1.setName("kim");
        Member member2 = new Member();
        member2.setName("kim");
    //When
    memberService.join(member1); memberService.join(member2); //예외가 발생해야 한다.
    //Then
    fail("예외가 발생해야 한다."); 
    }
}
```
________________________________________________________________________________________________________________________________________________________________________
이제 상품 도메인 개발(비즈니스 로직 추가)을 진행하겠습니다. 상품 엔티티 코드는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
```java
package jpabook.jpashop.domain.item;
import jpabook.jpashop.exception.NotEnoughStockException;
import lombok.Getter;
import lombok.Setter;
import jpabook.jpashop.domain.Category;
import javax.persistence.*;
import java.util.ArrayList;
import java.util.List;
@Entity
@Inheritance(strategy = InheritanceType.SINGLE_TABLE)
@DiscriminatorColumn(name = "dtype")
@Getter @Setter
public abstract class Item {
    @Id @GeneratedValue
    @Column(name = "item_id")
    private Long id;
    private String name;
    private int price;
    private int stockQuantity;
    @ManyToMany(mappedBy = "items")
    private List<Category> categories = new ArrayList<Category>();
    //==비즈니스 로직==//
    public void addStock(int quantity) {
        this.stockQuantity += quantity;
    }
    public void removeStock(int quantity) {
        int restStock = this.stockQuantity - quantity;
        if (restStock < 0) {
            throw new NotEnoughStockException("need more stock");
        }
        this.stockQuantity = restStock;
    }
}
```
________________________________________________________________________________________________________________________________________________________________________
예외 추가 코드는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
```java
package jpabook.jpashop.exception;
public class NotEnoughStockException extends RuntimeException {
    public NotEnoughStockException() {
    }
    public NotEnoughStockException(String message) {
        super(message);
    }
    public NotEnoughStockException(String message, Throwable cause) {
        super(message, cause);
    }
    public NotEnoughStockException(Throwable cause) {
        super(cause);
    } 
}
```
________________________________________________________________________________________________________________________________________________________________________
이제 상품 리포지토리 개발을 진행하겠습니다. 상품 리포지토리 코드는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
```java
package jpabook.jpashop.repository;
import jpabook.jpashop.domain.item.Item;
import lombok.RequiredArgsConstructor;
import org.springframework.stereotype.Repository;
import javax.persistence.EntityManager;
import java.util.List;
@Repository
@RequiredArgsConstructor
public class ItemRepository {
    private final EntityManager em;
    public void save(Item item) {
        if (item.getId() == null) {
            em.persist(item);
        } else {
            em.merge(item);
        }
    }
    public Item findOne(Long id) {
        return em.find(Item.class, id);
    }
    public List<Item> findAll() {
        return em.createQuery("select i from Item i",Item.class).getResultList();
    }
}
```
________________________________________________________________________________________________________________________________________________________________________
이제 상품 서비스 개발을 진행하겠습니다. 상품 서비스 코드는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
```java
package jpabook.jpashop.service;
import jpabook.jpashop.domain.item.Item;
import jpabook.jpashop.repository.ItemRepository;
import lombok.RequiredArgsConstructor;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;
import java.util.List;
@Service
@Transactional(readOnly = true)
@RequiredArgsConstructor
public class ItemService {
    private final ItemRepository itemRepository;
    @Transactional
    public void saveItem(Item item) {
        itemRepository.save(item);
    }
    public List<Item> findItems() {
        return itemRepository.findAll();
    }
    public Item findOne(Long itemId) {
        return itemRepository.findOne(itemId);
    } 
}
```
________________________________________________________________________________________________________________________________________________________________________
이제 주문 도메인 개발을 진행하겠습니다. 먼저 주문 엔티티 개발부터 진행하겠습니다. 개발 코드는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
```java
package jpabook.jpashop.domain;
import lombok.Getter;
import lombok.Setter;
import javax.persistence.*;
import java.time.LocalDateTime;
import java.util.ArrayList;
import java.util.List;
@Entity
@Table(name = "orders")
@Getter @Setter
public class Order {
    @Id @GeneratedValue
    @Column(name = "order_id")
    private Long id;
    @ManyToOne(fetch = FetchType.LAZY) @JoinColumn(name = "member_id") private Member member; //주문 회원
    @OneToMany(mappedBy = "order", cascade = CascadeType.ALL)
    private List<OrderItem> orderItems = new ArrayList<>();
    @OneToOne(cascade = CascadeType.ALL, fetch = FetchType.LAZY) @JoinColumn(name = "delivery_id")
    private Delivery delivery; //배송정보
    private LocalDateTime orderDate; //주문시간 @Enumerated(EnumType.STRING)
    private OrderStatus status; //주문상태 [ORDER, CANCEL]
    //==연관관계 메서드==//
    public void setMember(Member member) {
        this.member = member;
        member.getOrders().add(this);
    }
    public void addOrderItem(OrderItem orderItem) {
        orderItems.add(orderItem);
        orderItem.setOrder(this);
    }
    public void setDelivery(Delivery delivery) {
        this.delivery = delivery;
        delivery.setOrder(this);
    }
    //==생성 메서드==//
    public static Order createOrder(Member member, Delivery delivery, OrderItem... orderItems) {
        Order order = new Order();
        order.setMember(member);
        order.setDelivery(delivery);
        for (OrderItem orderItem : orderItems) {
            order.addOrderItem(orderItem);
        }
        order.setStatus(OrderStatus.ORDER);
        order.setOrderDate(LocalDateTime.now());
        return order;
    }
    //==비즈니스 로직==//
    /** 주문 취소 */
    public void cancel() {
    if (delivery.getStatus() == DeliveryStatus.COMP) {
        throw new IllegalStateException("이미 배송완료된 상품은 취소가 불가능합니다.");
    }
    this.setStatus(OrderStatus.CANCEL);
    for (OrderItem orderItem : orderItems) {
        orderItem.cancel();
    }
    }
    //==조회 로직==//
    /**전체 주문 가격 조회*/ 
    public int getTotalPrice() {
        int totalPrice = 0;
        for (OrderItem orderItem : orderItems) {
            totalPrice += orderItem.getTotalPrice();
        }
        return totalPrice;
    }
}
```
________________________________________________________________________________________________________________________________________________________________________
다음으로 주문상품 엔티티 개발을 진행하겠습니다. 코드는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
```java
package jpabook.jpashop.domain;
import lombok.Getter;
import lombok.Setter;
import jpabook.jpashop.domain.item.Item;
import javax.persistence.*;
@Entity
@Table(name = "order_item")
@Getter @Setter
public class OrderItem {
    @Id @GeneratedValue
    @Column(name = "order_item_id")
    private Long id;
    @ManyToOne(fetch = FetchType.LAZY) @JoinColumn(name = "item_id") private Item item; //주문 상품
    @ManyToOne(fetch = FetchType.LAZY) @JoinColumn(name = "order_id") private Order order; //주문
    private int orderPrice; //주문 가격
    private int count; //주문 수량
    //==생성 메서드==//
    public static OrderItem createOrderItem(Item item, int orderPrice, int count) {
        OrderItem orderItem = new OrderItem();
        orderItem.setItem(item);
        orderItem.setOrderPrice(orderPrice);
        orderItem.setCount(count);
        item.removeStock(count);
        return orderItem;
    }
    //==비즈니스 로직==//
    /** 주문 취소 */
    public void cancel() {
        getItem().addStock(count);
    }
    //==조회 로직==//
    /** 주문상품 전체 가격 조회 */ 
    public int getTotalPrice() {
        return getOrderPrice() * getCount();
    }
}
```
________________________________________________________________________________________________________________________________________________________________________
다음으로 주문 리포지토리 개발 코드는 아래 예시와 같습니다.
________________________________________________________________________________________________________________________________________________________________________
```java
package jpabook.jpashop.repository;
import jpabook.jpashop.domain.Order;
import lombok.RequiredArgsConstructor;
import org.springframework.stereotype.Repository;
import javax.persistence.EntityManager;
@Repository
@RequiredArgsConstructor
public class OrderRepository {
    private final EntityManager em;
    public void save(Order order) {
        em.persist(order);
    }
    public Order findOne(Long id) {
        return em.find(Order.class, id);
    }
}
```
________________________________________________________________________________________________________________________________________________________________________
이제 주문 서비스 개발을 진행하겠습니다. 코드는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
```java
package jpabook.jpashop.service;
import jpabook.jpashop.domain.Delivery;
import jpabook.jpashop.domain.Member;
import jpabook.jpashop.domain.Order;
import jpabook.jpashop.domain.OrderItem;
import jpabook.jpashop.domain.item.Item;
import jpabook.jpashop.repository.ItemRepository;
import jpabook.jpashop.repository.MemberRepository;
import jpabook.jpashop.repository.OrderRepository;
import lombok.RequiredArgsConstructor;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;
@Service
@Transactional(readOnly = true)
@RequiredArgsConstructor
public class OrderService {
    private final MemberRepository memberRepository;
    private final OrderRepository orderRepository;
    private final ItemRepository itemRepository;
    /** 주문 */
    @Transactional
    public Long order(Long memberId, Long itemId, int count) {
        //엔티티 조회
        Member member = memberRepository.findOne(memberId); Item item = itemRepository.findOne(itemId);
        //배송정보 생성
        Delivery delivery = new Delivery(); delivery.setAddress(member.getAddress()); delivery.setStatus(DeliveryStatus.READY);
        //주문상품 생성
        OrderItem orderItem = OrderItem.createOrderItem(item, item.getPrice(), count);
        //주문 생성
        Order order = Order.createOrder(member, delivery, orderItem);
        //주문 저장 orderRepository.save(order); return order.getId();
    }
    /** 주문 취소 */
    @Transactional
    public void cancelOrder(Long orderId) {
        //주문 엔티티 조회
        Order order = orderRepository.findOne(orderId);
        //주문 취소
        order.cancel();
    }
    /** 주문 검색 */ 
/*
    public List<Order> findOrders(OrderSearch orderSearch) {
        return orderRepository.findAll(orderSearch);
    } 
*/
}
```
________________________________________________________________________________________________________________________________________________________________________
이제 주문 기능 테스트를 진행하겠습니다. 상품 주문 테스트 코드는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
```java
package jpabook.jpashop.service;
import jpabook.jpashop.domain.Address;
import jpabook.jpashop.domain.Member;
import jpabook.jpashop.domain.Order;
import jpabook.jpashop.domain.OrderStatus;
import jpabook.jpashop.domain.item.Book;
import jpabook.jpashop.domain.item.Item;
import jpabook.jpashop.exception.NotEnoughStockException;
import jpabook.jpashop.repository.OrderRepository;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.junit4.SpringRunner;
import org.springframework.transaction.annotation.Transactional;
import javax.persistence.EntityManager;
import javax.persistence.PersistenceContext;
import static org.junit.Assert.assertEquals;
import static org.junit.Assert.fail;
@RunWith(SpringRunner.class)
@SpringBootTest
@Transactional
public class OrderServiceTest {
    @PersistenceContext
    EntityManager em;
    @Autowired OrderService orderService;
    @Autowired OrderRepository orderRepository;
    @Test
    public void 상품주문() throws Exception {
        //Given
        Member member = createMember();
        Item item = createBook("시골 JPA", 10000, 10); //이름, 가격, 재고
        int orderCount = 2;
        //When
        Long orderId = orderService.order(member.getId(), item.getId(), orderCount);
        //Then
        Order getOrder = orderRepository.findOne(orderId); assertEquals("상품 주문시 상태는 ORDER",OrderStatus.ORDER, getOrder.getStatus());
        assertEquals("주문한 상품 종류 수가 정확해야 한다.",1,
            getOrder.getOrderItems().size());
        assertEquals("주문 가격은 가격 * 수량이다.", 10000 * 2,
            getOrder.getTotalPrice());
        assertEquals("주문 수량만큼 재고가 줄어야 한다.",8, item.getStockQuantity());
    }
    @Test(expected = NotEnoughStockException.class)
    public void 상품주문_재고수량초과() throws Exception { //...
    }
    @Test
    public void 주문취소() {
    //...
    }
    private Member createMember() {
        Member member = new Member();
        member.setName("회원1");
        member.setAddress(new Address("서울", "강가", "123-123")); em.persist(member);
        return member;
    }
    private Book createBook(String name, int price, int stockQuantity) {
        Book book = new Book();
        book.setName(name);
        book.setStockQuantity(stockQuantity);
        book.setPrice(price);
        em.persist(book);
        return book;
    } 
}
```
________________________________________________________________________________________________________________________________________________________________________
재고 수량 초과 테스트 코드는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
```java
@Test(expected = NotEnoughStockException.class) public void 상품주문_재고수량초과() throws Exception {
    //Given
    Member member = createMember();
    Item item = createBook("시골 JPA", 10000, 10); //이름, 가격, 재고
    int orderCount = 11; //재고보다 많은 수량 
    //When
    orderService.order(member.getId(), item.getId(), orderCount);
    //Then
    fail("재고 수량 부족 예외가 발생해야 한다."); 
}
```
________________________________________________________________________________________________________________________________________________________________________
주문 취소 테스트 코드는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
```java
@Test
public void 주문취소() {
    //Given
    Member member = createMember();
    Item item = createBook("시골 JPA", 10000, 10); //이름, 가격, 재고
    int orderCount = 2;
    Long orderId = orderService.order(member.getId(), item.getId(), orderCount);
    //When
    orderService.cancelOrder(orderId);
    //Then
    Order getOrder = orderRepository.findOne(orderId); assertEquals("주문 취소시 상태는 CANCEL 이다.",OrderStatus.CANCEL,
        getOrder.getStatus());
    assertEquals("주문이 취소된 상품은 그만큼 재고가 증가해야 한다.", 10,
        item.getStockQuantity());
}
```
________________________________________________________________________________________________________________________________________________________________________
이제 주문 검색 기능 개발을 진행하겠습니다. 우선 OrderSearch는 다음과 같이 작성합니다.
________________________________________________________________________________________________________________________________________________________________________
```java
package jpabook.jpashop.domain;
public class OrderSearch {
    private String memberName; //회원 이름
    private OrderStatus orderStatus;//주문 상태[ORDER, CANCEL] //Getter, Setter
}
```
________________________________________________________________________________________________________________________________________________________________________
검색을 추가한 주문 리포지토리 코드는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
```java
package jpabook.jpashop.repository;
@Repository
public class OrderRepository {
    @PersistenceContext
    EntityManager em;
    public void save(Order order) {
        em.persist(order);
    }
    public Order findOne(Long id) {
        return em.find(Order.class, id);
    }
    public List<Order> findAll(OrderSearch orderSearch) { //... 검색 로직
    } 
}
```
________________________________________________________________________________________________________________________________________________________________________
JPQL로 처리는 다음과 같이 진행합니다.
________________________________________________________________________________________________________________________________________________________________________
```java
public List<Order> findAllByString(OrderSearch orderSearch) {
    //language=JPAQL
    String jpql = "select o From Order o join o.member m";
    boolean isFirstCondition = true;
    //주문 상태 검색
    if (orderSearch.getOrderStatus() != null) {
        if (isFirstCondition) {
            jpql += " where";
            isFirstCondition = false;
        } else {
            jpql += " and";
        }
        jpql += " o.status = :status";
    }
    //회원 이름 검색
    if (StringUtils.hasText(orderSearch.getMemberName())) {
        if (isFirstCondition) {
            jpql += " where";
            isFirstCondition = false;
        } else {
            jpql += " and";
        }
        jpql += " m.name like :name";
    }
    TypedQuery<Order> query = em.createQuery(jpql, Order.class) .setMaxResults(1000); //최대 1000건
    if (orderSearch.getOrderStatus() != null) {
        query = query.setParameter("status", orderSearch.getOrderStatus());
    }
    if (StringUtils.hasText(orderSearch.getMemberName())) {
        query = query.setParameter("name", orderSearch.getMemberName());
    }
    return query.getResultList();
}
```
________________________________________________________________________________________________________________________________________________________________________
JPA Criteria로 처리는 다음과 같이 진행합니다.
________________________________________________________________________________________________________________________________________________________________________
```java
public List<Order> findAllByCriteria(OrderSearch orderSearch) {
    CriteriaBuilder cb = em.getCriteriaBuilder();
    CriteriaQuery<Order> cq = cb.createQuery(Order.class);
    Root<Order> o = cq.from(Order.class);
    Join<Order, Member> m = o.join("member", JoinType.INNER); //회원과 조인
    List<Predicate> criteria = new ArrayList<>();
    //주문 상태 검색
    if (orderSearch.getOrderStatus() != null) {
        Predicate status = cb.equal(o.get("status"),
        orderSearch.getOrderStatus());
        criteria.add(status);
    }
    //회원 이름 검색
    if (StringUtils.hasText(orderSearch.getMemberName())) {
        Predicate name =
            cb.like(m.<String>get("name"), "%" +
            orderSearch.getMemberName() + "%");
        criteria.add(name);
    }
    cq.where(cb.and(criteria.toArray(new Predicate[criteria.size()])));
    TypedQuery<Order> query = em.createQuery(cq).setMaxResults(1000); //최대 1000건
    return query.getResultList();
}
```
________________________________________________________________________________________________________________________________________________________________________
이제 웹 계층 개발에 대해 알아보겠습니다. 우선 홈 화면과 레이아웃을 개발하겠습니다. 홈 컨트롤러 등록은 다음과 같이 진행합니다.
________________________________________________________________________________________________________________________________________________________________________
```java
package jpabook.jpashop.web;
import lombok.extern.slf4j.Slf4j;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
@Controller
@Slf4j
public class HomeController {
    @RequestMapping("/")
    public String home() {
        log.info("home controller");
        return "home";
    }
}
```
________________________________________________________________________________________________________________________________________________________________________
타임리프 템플릿 등록은 다음과 같이 진행합니다.
________________________________________________________________________________________________________________________________________________________________________
- home.html
```html
<!DOCTYPE HTML>
<html xmlns:th="http://www.thymeleaf.org">
<head th:replace="fragments/header :: header">
    <title>Hello</title>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
</head>

<body>

<div class="container">
      
    <div th:replace="fragments/bodyHeader :: bodyHeader" />

    <div class="jumbotron"> 
        <h1>HELLO SHOP</h1>
        <p class="lead">회원 기능</p> 
        <p>
            <a class="btn btn-lg btn-secondary" href="/members/new">회원 가입</a>
            <a class="btn btn-lg btn-secondary" href="/members">회원 목록</a> 
        </p>
        <p class="lead">상품 기능</p> 
        <p>
            <a class="btn btn-lg btn-dark" href="/items/new">상품 등록</a>
            <a class="btn btn-lg btn-dark" href="/items">상품 목록</a> 
        </p>
        <p class="lead">주문 기능</p> 
        <p>
            <a class="btn btn-lg btn-info" href="/order">상품 주문</a>
            <a class="btn btn-lg btn-info" href="/orders">주문 내역</a> 
        </p>
    </div>
    
    <div th:replace="fragments/footer :: footer" />

</div> <!-- /container -->
</body>
</html>
```
________________________________________________________________________________________________________________________________________________________________________
- fragments/header
```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head th:fragment="header">
    <!-- Required meta tags -->
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-
to-fit=no">
    <!-- Bootstrap CSS -->
    <link rel="stylesheet" href="/css/bootstrap.min.css" integrity="sha384-
ggOyR0iXCbMQv3Xipma34MD+dH/1fQ784/j6cY/iJTQUOhcWr7x9JvoRxT2MZw1T"
crossorigin="anonymous">
    <!-- Custom styles for this template -->
    <link href="/css/jumbotron-narrow.css" rel="stylesheet">
    
    <title>Hello, world!</title>
</head>
```
________________________________________________________________________________________________________________________________________________________________________
- fragments/bodyHeader
```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<div class="header" th:fragment="bodyHeader">
    <ul class="nav nav-pills pull-right">
        <li><a href="/">Home</a></li>
    </ul>
    <a href="/"><h3 class="text-muted">HELLO SHOP</h3></a>
</div>
```
________________________________________________________________________________________________________________________________________________________________________
- fragments/footer
```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<div class="footer" th:fragment="footer">
    <p>&copy; Hello Shop V2</p>
</div>
```
________________________________________________________________________________________________________________________________________________________________________
이렇게 html을 모두 등록했으면 CSS 파일을 통해 꾸며주시면 메인 화면이 노출됩니다. CSS 파일은 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
```css
/* Space out content a bit */
body {
    padding-top: 20px;
    padding-bottom: 20px;
}
/* Everything but the jumbotron gets side spacing for mobile first views */
.header,
.marketing,
.footer {
    padding-left: 15px;
    padding-right: 15px;
}
/* Custom page header */
.header {
    border-bottom: 1px solid #e5e5e5;
}
/* Make the masthead heading the same height as the navigation */
.header h3 {
    margin-top: 0;
    margin-bottom: 0;
    line-height: 40px;
    padding-bottom: 19px;
}
/* Custom page footer */
.footer {
    padding-top: 19px;
    color: #777;
    border-top: 1px solid #e5e5e5;
}
/* Customize container */
@media (min-width: 768px) {
    .container {
        max-width: 730px;
    }
}
.container-narrow > hr {
    margin: 30px 0;
}
/* Main marketing message and sign up button */
.jumbotron {
    text-align: center;
    border-bottom: 1px solid #e5e5e5;
}
.jumbotron .btn {
    font-size: 21px;
    padding: 14px 24px;
}
/* Supporting marketing content */
.marketing {
    margin: 40px 0;
}
.marketing p + h4 {
    margin-top: 28px;
}
/* Responsive: Portrait tablets and up */
@media screen and (min-width: 768px) {
    /* Remove the padding we set earlier */
    .header,
    .marketing,
    .footer {
        padding-left: 0;
        padding-right: 0;
    }
    /* Space out the masthead */
    .header {
        margin-bottom: 30px;
    }
    /* Remove the bottom border on the jumbotron for visual effect */
    .jumbotron {
        border-bottom: 0;
    }
}
```
________________________________________________________________________________________________________________________________________________________________________
이제 회원 등록을 진행해보겠습니다. 회원 등록 폼 객체는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
```java
package jpabook.jpashop.web;
import lombok.Getter;
import lombok.Setter;
import javax.validation.constraints.NotEmpty;
@Getter @Setter
public class MemberForm {
@NotEmpty(message = "회원 이름은 필수 입니다") private String name;
    private String city;
    private String street;
    private String zipcode;
}
```
________________________________________________________________________________________________________________________________________________________________________
회원 등록 컨트롤러 코드는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
```java
package jpabook.jpashop.web;
import jpabook.jpashop.domain.Address;
import jpabook.jpashop.domain.Member;
import jpabook.jpashop.service.MemberService;
import lombok.RequiredArgsConstructor;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.validation.BindingResult;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import javax.validation.Valid;
import java.util.List;
@Controller
@RequiredArgsConstructor
public class MemberController {
    private final MemberService memberService;
    @GetMapping(value = "/members/new")
    public String createForm(Model model) {
        model.addAttribute("memberForm", new MemberForm());
        return "members/createMemberForm";
    }
    @PostMapping(value = "/members/new")
    public String create(@Valid MemberForm form, BindingResult result) {
        if (result.hasErrors()) {
            return "members/createMemberForm";
        }
        Address address = new Address(form.getCity(), form.getStreet(), form.getZipcode());
        Member member = new Member();
        member.setName(form.getName());
        member.setAddress(address);
        memberService.join(member);
        return "redirect:/";
    }
}
```
________________________________________________________________________________________________________________________________________________________________________
회원 등록 폼 화면 코드(templates/members/createMemberForm.html)는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
```html
<!DOCTYPE HTML>
<html xmlns:th="http://www.thymeleaf.org">
<head th:replace="fragments/header :: header" />
<style>
    .fieldError {
        border-color: #bd2130;
    } 
</style>
<body>
  
<div class="container">
    <div th:replace="fragments/bodyHeader :: bodyHeader"/>
    <form role="form" action="/members/new" th:object="${memberForm}"
method="post">
        <div class="form-group">
            <label th:for="name">이름</label>
            <input type="text" th:field="*{name}" class="form-control" placeholder="이름을 입력하세요"
                th:class="${#fields.hasErrors('name')}? 'form-control
fieldError' : 'form-control'">
            <p th:if="${#fields.hasErrors('name')}"
th:errors="*{name}">Incorrect date</p>
        </div>
        <div class="form-group">
            <label th:for="city">도시</label>
            <input type="text" th:field="*{city}" class="form-control"
placeholder="도시를 입력하세요"> 
        </div>
        <div class="form-group">
            <label th:for="street">거리</label>
            <input type="text" th:field="*{street}" class="form-control" placeholder="거리를 입력하세요">
        </div>
        <div class="form-group">
            <label th:for="zipcode">우편번호</label>
            <input type="text" th:field="*{zipcode}" class="form-control"
placeholder="우편번호를 입력하세요"> 
        </div>
        <button type="submit" class="btn btn-primary">Submit</button>
    </form>
    <br/>
    <div th:replace="fragments/footer :: footer" />
</div> <!-- /container -->
</body>
</html>
```
________________________________________________________________________________________________________________________________________________________________________
이제 회원 목록 조회 기능을 개발하겠습니다. 회원 목록 컨트롤러 추가는 다음과 같이 진행합니다.
________________________________________________________________________________________________________________________________________________________________________
```java
package jpabook.jpashop.web;
@Controller
@RequiredArgsConstructor
public class MemberController {
    //추가
    @GetMapping(value = "/members") public String list(Model model) {
        List<Member> members = memberService.findMembers();
        model.addAttribute("members", members);
        return "members/memberList";
    }
}
```
________________________________________________________________________________________________________________________________________________________________________
회원 목록 뷰(templates/members/memberList.html) 기능은 다음과 같이 개발합니다.
________________________________________________________________________________________________________________________________________________________________________
```html
<!DOCTYPE HTML>
<html xmlns:th="http://www.thymeleaf.org">
<head th:replace="fragments/header :: header" />
<body>
  
<div class="container">
    <div th:replace="fragments/bodyHeader :: bodyHeader" />
    <div>
        <table class="table table-striped">
            <thead>
            <tr>
                <th>#</th>
                <th>이름</th> 
                <th>도시</th> 
                <th>주소</th> 
                <th>우편번호</th>
            </tr>
            </thead>
            <tbody>
            <tr th:each="member : ${members}">
                <td th:text="${member.id}"></td>
                <td th:text="${member.name}"></td>
                <td th:text="${member.address?.city}"></td>
                <td th:text="${member.address?.street}"></td>
                <td th:text="${member.address?.zipcode}"></td>
            </tr>
            </tbody>
        </table>
    </div>
        
    <div th:replace="fragments/footer :: footer" />
</div> <!-- /container -->
</body>
</html>
```
________________________________________________________________________________________________________________________________________________________________________
상품 등록 기능은 다음과 같이 개발합니다.
________________________________________________________________________________________________________________________________________________________________________
```java
package jpabook.jpashop.web;
import lombok.Getter;
import lombok.Setter;
@Getter @Setter
public class BookForm {
    private Long id;
    private String name;
    private int price;
    private int stockQuantity;
    private String author;
    private String isbn;
}
```
________________________________________________________________________________________________________________________________________________________________________
상품 등록 컨트롤러는 다음과 같이 개발합니다.
________________________________________________________________________________________________________________________________________________________________________
```java
package jpabook.jpashop.web;
import jpabook.jpashop.domain.item.Book;
import jpabook.jpashop.domain.item.Item;
import jpabook.jpashop.service.ItemService;
import lombok.RequiredArgsConstructor;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.*;
import java.util.List;
@Controller
@RequiredArgsConstructor
public class ItemController {
    private final ItemService itemService;
    @GetMapping(value = "/items/new")
    public String createForm(Model model) {
        model.addAttribute("form", new BookForm());
        return "items/createItemForm";
    }
    @PostMapping(value = "/items/new")
    public String create(BookForm form) {
        Book book = new Book();
        book.setName(form.getName());
        book.setPrice(form.getPrice());
        book.setStockQuantity(form.getStockQuantity());
        book.setAuthor(form.getAuthor());
        book.setIsbn(form.getIsbn());
        itemService.saveItem(book);
        return "redirect:/items";
    }
}
```
________________________________________________________________________________________________________________________________________________________________________
상품 등록 뷰(items/createItemForm.html) 코드는 다음과 같이 작성합니다.
________________________________________________________________________________________________________________________________________________________________________
```html
<!DOCTYPE HTML>
<html xmlns:th="http://www.thymeleaf.org">
<head th:replace="fragments/header :: header" />
<body>

<div class="container">
    <div th:replace="fragments/bodyHeader :: bodyHeader"/>
    
    <form th:action="@{/items/new}" th:object="${form}" method="post">
        <div class="form-group">
            <label th:for="name">상품명</label>
            <input type="text" th:field="*{name}" class="form-control"
placeholder="이름을 입력하세요"> 
        </div>
        <div class="form-group">
            <label th:for="price">가격</label>
            <input type="number" th:field="*{price}" class="form-control" placeholder="가격을 입력하세요">
        </div>
        <div class="form-group">
            <label th:for="stockQuantity">수량</label>
            <input type="number" th:field="*{stockQuantity}" class="form-
control" placeholder="수량을 입력하세요"> 
        </div>
        <div class="form-group">
            <label th:for="author">저자</label>
            <input type="text" th:field="*{author}" class="form-control"
placeholder="저자를 입력하세요"> 
        </div>
        <div class="form-group">
            <label th:for="isbn">ISBN</label>
            <input type="text" th:field="*{isbn}" class="form-control"
placeholder="ISBN을 입력하세요"> 
        </div>
        <button type="submit" class="btn btn-primary">Submit</button>
    </form>
    <br/>
    <div th:replace="fragments/footer :: footer" />
</div> <!-- /container -->
    
</body>
</html>
```
________________________________________________________________________________________________________________________________________________________________________
이제 상품 목록을 개발하겠습니다. 상품 목록 컨트롤러는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
```java
package jpabook.jpashop.web;
@Controller
@RequiredArgsConstructor
public class ItemController {
    private final ItemService itemService;
    /**
    * 상품 목록
    */
    @GetMapping(value = "/items")
    public String list(Model model) {
        List<Item> items = itemService.findItems();
        model.addAttribute("items", items);
        return "items/itemList";
    } 
}
```
________________________________________________________________________________________________________________________________________________________________________
상품 목록 뷰(items/itemList.html) 코드는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
```html
<!DOCTYPE HTML>
<html xmlns:th="http://www.thymeleaf.org">
<head th:replace="fragments/header :: header" />
<body>

<div class="container">
    <div th:replace="fragments/bodyHeader :: bodyHeader"/>
    
    <div>
        <table class="table table-striped">
            <thead> 
            <tr>
                <th>#</th> 
                <th>상품명</th> 
                <th>가격</th> 
                <th>재고수량</th> 
                <th></th>
            </tr>
            </thead>
            <tbody>
            <tr th:each="item : ${items}">
                <td th:text="${item.id}"></td>
                <td th:text="${item.name}"></td>
                <td th:text="${item.price}"></td>
                <td th:text="${item.stockQuantity}"></td>
                <td>
                    <a href="#" th:href="@{/items/{id}/edit (id=${item.id})}" class="btn btn-primary" role="button">수정</a>
                </td> 
            </tr>
            </tbody>
        </table>
    </div>
    
    <div th:replace="fragments/footer :: footer"/>
</div> <!-- /container -->
</body>
</html>
```
________________________________________________________________________________________________________________________________________________________________________
상품 수정과 관련된 컨트롤러 코드는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
```java
package jpabook.jpashop.web;
import jpabook.jpashop.domain.item.Book;
import jpabook.jpashop.domain.item.Item;
import jpabook.jpashop.service.ItemService;
import lombok.RequiredArgsConstructor;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.*;
import java.util.List;
@Controller
@RequiredArgsConstructor
public class ItemController {
    /**
    * 상품 수정 폼
    */
    @GetMapping(value = "/items/{itemId}/edit")
    public String updateItemForm(@PathVariable("itemId") Long itemId, Model model) {
        Book item = (Book) itemService.findOne(itemId);
        BookForm form = new BookForm();
        form.setId(item.getId());
        form.setName(item.getName());
        form.setPrice(item.getPrice());
        form.setStockQuantity(item.getStockQuantity());
        form.setAuthor(item.getAuthor());
        form.setIsbn(item.getIsbn());
        model.addAttribute("form", form);
        return "items/updateItemForm";
    }
    /**
    * 상품 수정
    */
    @PostMapping(value = "/items/{itemId}/edit")
    public String updateItem(@ModelAttribute("form") BookForm form) {
        Book book = new Book();
        book.setId(form.getId());
        book.setName(form.getName());
        book.setPrice(form.getPrice());
        book.setStockQuantity(form.getStockQuantity());
        book.setAuthor(form.getAuthor());
        book.setIsbn(form.getIsbn());
        itemService.saveItem(book);
        return "redirect:/items";
    }
}
```
________________________________________________________________________________________________________________________________________________________________________
상품 수정 폼 화면(items/updateItemForm)은 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
```html
<!DOCTYPE HTML>
<html xmlns:th="http://www.thymeleaf.org">
<head th:replace="fragments/header :: header" />
<body>

<div class="container">
    <div th:replace="fragments/bodyHeader :: bodyHeader"/>
    
    <form th:object="${form}" method="post">
        <!-- id -->
        <input type="hidden" th:field="*{id}" />
        <div class="form-group">
            <label th:for="name">상품명</label>
            <input type="text" th:field="*{name}" class="form-control"
placeholder="이름을 입력하세요" /> 
        </div>
        <div class="form-group">
            <label th:for="price">가격</label>
            <input type="number" th:field="*{price}" class="form-control" placeholder="가격을 입력하세요" />
        </div>
        <div class="form-group">
            <label th:for="stockQuantity">수량</label>
            <input type="number" th:field="*{stockQuantity}" class="form-
control" placeholder="수량을 입력하세요" /> 
        </div>
        <div class="form-group">
            <label th:for="author">저자</label>
            <input type="text" th:field="*{author}" class="form-control" placeholder="저자를 입력하세요" />
        </div>
        <div class="form-group">
            <label th:for="isbn">ISBN</label>
            <input type="text" th:field="*{isbn}" class="form-control" placeholder="ISBN을 입력하세요" />
        </div>
        <button type="submit" class="btn btn-primary">Submit</button>
    </form>
    
    <div th:replace="fragments/footer :: footer" />
</div> <!-- /container -->
     
</body>
</html>
```
________________________________________________________________________________________________________________________________________________________________________
이제 상품 주문 기능 개발을 진행하겠습니다. 상품 주문 컨트롤러 코드는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
```java
package jpabook.jpashop.web;
import jpabook.jpashop.domain.Member;
import jpabook.jpashop.domain.Order;
import jpabook.jpashop.domain.OrderSearch;
import jpabook.jpashop.domain.item.Item;
import jpabook.jpashop.service.ItemService;
import jpabook.jpashop.service.MemberService;
import jpabook.jpashop.service.OrderService;
import lombok.RequiredArgsConstructor;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.*;
import java.util.List;
@Controller
@RequiredArgsConstructor
public class OrderController {
    private final OrderService orderService;
    private final MemberService memberService;
    private final ItemService itemService;
    @GetMapping(value = "/order")
    public String createForm(Model model) {
        List<Member> members = memberService.findMembers();
        List<Item> items = itemService.findItems();
        model.addAttribute("members", members);
        model.addAttribute("items", items);
        return "order/orderForm";
    }
    @PostMapping(value = "/order")
    public String order(@RequestParam("memberId") Long memberId,
@RequestParam("itemId") Long itemId, @RequestParam("count") int count) {
        orderService.order(memberId, itemId, count);
        return "redirect:/orders";
    }
}
```
________________________________________________________________________________________________________________________________________________________________________
상품 주문 폼(order/orderForm) 코드는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
```html
<!DOCTYPE HTML>
<html xmlns:th="http://www.thymeleaf.org">
<head th:replace="fragments/header :: header" />
<body>

<div class="container">
    <div th:replace="fragments/bodyHeader :: bodyHeader"/>
    
    <form role="form" action="/order" method="post">
        
        <div class="form-group">
            <label for="member">주문회원</label>
            <select name="memberId" id="member" class="form-control">
                <option value="">회원선택</option> <option th:each="member : ${members}"
                          th:value="${member.id}"
                          th:text="${member.name}" />
            </select>
        </div>

        <div class="form-group">
            <label for="item">상품명</label>
            <select name="itemId" id="item" class="form-control"> <option value="">상품선택</option>
                <option th:each="item : ${items}"
                    th:value="${item.id}"
                    th:text="${item.name}" />
            </select>
        </div>
        
        <div class="form-group">
            <label for="count">주문수량</label>
            <input type="number" name="count" class="form-control" id="count"
placeholder="주문 수량을 입력하세요"> 
        </div>
        
        <button type="submit" class="btn btn-primary">Submit</button>
    </form>
    <br/>
    <div th:replace="fragments/footer :: footer" />

</div> <!-- /container -->

</body>
</html>
```
________________________________________________________________________________________________________________________________________________________________________
이제 주문 목록 검색, 취소 기능을 만들겠습니다. 먼저 주문 목록 검색 컨트롤러 코드는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
```java
package jpabook.jpashop.web;
@Controller
@RequiredArgsConstructor
public class OrderController {
    @GetMapping(value = "/orders")
    public String orderList(@ModelAttribute("orderSearch") OrderSearch
orderSearch, Model model) {
        List<Order> orders = orderService.findOrders(orderSearch);
        model.addAttribute("orders", orders);
        return "order/orderList";
    }
}
```
________________________________________________________________________________________________________________________________________________________________________
주문 목록 검색 화면(order/orderList) 코드는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
```html
<!DOCTYPE HTML>
<html xmlns:th="http://www.thymeleaf.org">
<head th:replace="fragments/header :: header"/>
<body>

<div class="container">
    
    <div th:replace="fragments/bodyHeader :: bodyHeader"/>
    
    <div> 
        <div>
            <form th:object="${orderSearch}" class="form-inline">
                <div class="form-group mb-2">
                    <input type="text" th:field="*{memberName}" class="form- control" placeholder="회원명"/>
                </div>
                <div class="form-group mx-sm-1 mb-2">
                    <select th:field="*{orderStatus}" class="form-control"> 
                        <option value="">주문상태</option>
                        <option th:each=
               "status : ${T(jpabook.jpashop.domain.OrderStatus).values()}"
                                th:value="${status}"
                                th:text="${status}">option
                        </option>
                    </select>
                </div>
                <button type="submit" class="btn btn-primary mb-2">검색</button> 
            </form>
        </div>
        
        <table class="table table-striped">
            <thead>
            <tr>
                <th>#</th>
                <th>회원명</th> 
                <th>대표상품 이름</th> 
                <th>대표상품 주문가격</th>
                <th>대표상품 주문수량</th> 
                <th>상태</th> 
                <th>일시</th> 
                <th></th>
            </tr>
            </thead>
            <tbody>
            <tr th:each="item : ${orders}">
                <td th:text="${item.id}"></td>
                <td th:text="${item.member.name}"></td>
                <td th:text="${item.orderItems[0].item.name}"></td>
                <td th:text="${item.orderItems[0].orderPrice}"></td>
                <td th:text="${item.orderItems[0].count}"></td>
                <td th:text="${item.status}"></td>
                <td th:text="${item.orderDate}"></td>
                <td>
                    <a th:if="${item.status.name() == 'ORDER'}" href="#"
th:href="'javascript:cancel('+${item.id}+')'"
                        class="btn btn-danger">CANCEL</a>
                </td>
            </tr>
            
            </tbody>
        </table>
    </div>
    
    <div th:replace="fragments/footer :: footer"/>

</div> <!-- /container -->

</body>
<script>
    function cancel(id) {
        var form = document.createElement("form");
        form.setAttribute("method", "post");
        form.setAttribute("action", "/orders/" + id + "/cancel");
        document.body.appendChild(form);
        form.submit();
    }
</script>
</html>
```
________________________________________________________________________________________________________________________________________________________________________
마지막으로 주문 취소 코드는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
```java
package jpabook.jpashop.web;
@Controller
@RequiredArgsConstructor
public class OrderController {
    @PostMapping(value = "/orders/{orderId}/cancel")
    public String cancelOrder(@PathVariable("orderId") Long orderId) {
        orderService.cancelOrder(orderId);
        return "redirect:/orders";
    } 
}
```
________________________________________________________________________________________________________________________________________________________________________
위의 코드가 모두 정상적으로 작동된다면, 하나의 웹 페이지가 완성됩니다. 이렇게 스프링 부트와 JPA로 웹 사이트를 개발하는 방법에 대해 알아보았습니다. 다음 포스팅은 스프링 부트와 JPA를 활용하여 API를 개발하는 방법에 대해 알아보겠습니다. 오늘도 긴 글 읽어주시느라 고생하셨습니다.
________________________________________________________________________________________________________________________________________________________________________
#### 감사합니다.
________________________________________________________________________________________________________________________________________________________________________
[레퍼런스]
1. 실전! 스프링 부트와 JPA 활용1 - 웹 애플리케이션 개발 : https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81%EB%B6%80%ED%8A%B8-JPA-%ED%99%9C%EC%9A%A9-1/dashboard