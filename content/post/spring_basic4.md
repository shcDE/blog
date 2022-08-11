---
title: "스프링의 기본에 대해 알아볼까요?(4)"
date: 2022-08-11T16:42:23+09:00
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
안녕하세요. shcDE입니다. 이번 포스팅에서는 스프링에서 기본적으로 쓰이는 의존관계 자동 주입과 빈 생명주기 콜백에 대해 알아보겠습니다. 시작 전에, 해당 포스팅은 김영한님의 ['스프링 핵심 원리 - 기본편'](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-%EA%B8%B0%EB%B3%B8%ED%8E%B8/dashboard) 섹션 7, 8를 공부하여 정리한 내용임을 밝히며, 직접 해당 강의를 구매하여 게시물을 작성함을 알립니다.
________________________________________________________________________________________________________________________________________________________________________
우선 생성자 주입을 먼저 해줍니다. 코드 예시는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
```java
@Component
public class OrderServiceImpl implements OrderService {
      private final MemberRepository memberRepository;
      private final DiscountPolicy discountPolicy;
      @Autowired
      public OrderServiceImpl(MemberRepository memberRepository, DiscountPolicydiscountPolicy) {
          this.memberRepository = memberRepository;
          this.discountPolicy = discountPolicy;
      }
}
```
________________________________________________________________________________________________________________________________________________________________________
스프링 빈에 경우, 생성자가 딱 1개만 있으면 @Autowired를 생략해도 자동 주입이 됩니다.
________________________________________________________________________________________________________________________________________________________________________
```java
@Component
public class OrderServiceImpl implements OrderService {
      private final MemberRepository memberRepository;
      private final DiscountPolicy discountPolicy;
      public OrderServiceImpl(MemberRepository memberRepository, DiscountPolicy discountPolicy) {
          this.memberRepository = memberRepository;
          this.discountPolicy = discountPolicy;
      }
}
```
________________________________________________________________________________________________________________________________________________________________________
수정자 주입(setter 주입)은 다음과 같이 진행합니다.
________________________________________________________________________________________________________________________________________________________________________
```java
@Component
public class OrderServiceImpl implements OrderService {
      private MemberRepository memberRepository;
      private DiscountPolicy discountPolicy;
      @Autowired
      public void setMemberRepository(MemberRepository memberRepository) {
          this.memberRepository = memberRepository;
      }
      @Autowired
      public void setDiscountPolicy(DiscountPolicy discountPolicy) {
          this.discountPolicy = discountPolicy;
      }
}
```
________________________________________________________________________________________________________________________________________________________________________
자바빈 프로퍼티 규약 예시는 다음과 같이 합니다.
________________________________________________________________________________________________________________________________________________________________________
```java
class Data {
      private int age;
      public void setAge(int age) {
        this.age = age;
      }
      public int getAge() {
        return age;
      } 
}
```
________________________________________________________________________________________________________________________________________________________________________
필드 주입과 일반 메서드 주입의 경우 일반적으로 사용하는 것을 비추천하기 때문에 넘어가도록 하겠습니다.
________________________________________________________________________________________________________________________________________________________________________
의존 관계는 제 포스팅에서는 여기까지 설명하도록 하겠습니다. 제가 설명하기 어려운 부분이 많기 때문에 빈 생명주기 콜백으로 바로 넘어가겠습니다.
________________________________________________________________________________________________________________________________________________________________________
빈 생명주기 콜백과 관련된 예제 코드를 살펴보겠습니다.
________________________________________________________________________________________________________________________________________________________________________
- 예제 코드, 테스트 하위에 생성
```java
package hello.core.lifecycle;
public class NetworkClient {
      private String url;
      public NetworkClient() { System.out.println("생성자 호출, url = " + url);
      connect();
      call("초기화 연결 메시지");
      }
      public void setUrl(String url) {
            this.url = url;
      }
      //서비스 시작시 호출
      public void connect() {
            System.out.println("connect: " + url);
      }
      public void call(String message) {
            System.out.println("call: " + url + " message = " + message);
      }
      //서비스 종료시 호출
      public void disconnect() {
            System.out.println("close: " + url);
      }
}
```
________________________________________________________________________________________________________________________________________________________________________
- 스프링 환경설정과 실행
```java
package hello.core.lifecycle;
import org.junit.jupiter.api.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
public class BeanLifeCycleTest {
      @Test
      public void lifeCycleTest() {
            ConfigurableApplicationContext ac = new AnnotationConfigApplicationContext(LifeCycleConfig.class);
            NetworkClient client = ac.getBean(NetworkClient.class);
            ac.close(); //스프링 컨테이너를 종료, ConfigurableApplicationContext 필요 
      }
      @Configuration
      static class LifeCycleConfig {
            @Bean
            public NetworkClient networkClient() {
                  NetworkClient networkClient = new NetworkClient();
                  networkClient.setUrl("http://hello-spring.dev");
                  return networkClient;
            } 
      }
}
```
________________________________________________________________________________________________________________________________________________________________________
하지만 실행 시 다음과 같은 이상한 결과가 나옵니다.
________________________________________________________________________________________________________________________________________________________________________
```
생성자 호출, url = null
connect: null
call: null message = 초기화 연결 메시지
```
________________________________________________________________________________________________________________________________________________________________________
인터페이스 InitializingBean, DisposableBean에 대해 알아보겠습니다. 예시 코드는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
```java
package hello.core.lifecycle;
import org.springframework.beans.factory.DisposableBean;
import org.springframework.beans.factory.InitializingBean;
public class NetworkClient implements InitializingBean, DisposableBean {
      private String url;
      public NetworkClient() {
            System.out.println("생성자 호출, url = " + url); }
      public void setUrl(String url) {
            this.url = url;
      }
      //서비스 시작시 호출
      public void connect() {
            System.out.println("connect: " + url);
      }
      public void call(String message) {
            System.out.println("call: " + url + " message = " + message);
      }
      //서비스 종료시 호출
      public void disConnect() {
            System.out.println("close + " + url);
      }
      @Override
      public void afterPropertiesSet() throws Exception {
            connect();
            call("초기화 연결 메시지");
      }
      @Override
      public void destroy() throws Exception {
            disConnect();
      }
}
```
________________________________________________________________________________________________________________________________________________________________________
이 때의 출력 결과는 적절하게 나온 것을 확인할 수 있습니다.
```
생성자 호출, url = null
NetworkClient.afterPropertiesSet
connect: http://hello-spring.dev
call: http://hello-spring.dev message = 초기화 연결 메시지
13:24:49.043 [main] DEBUG org.springframework.context.annotation.AnnotationConfigApplicationContext - Closing NetworkClient.destroy
close + http://hello-spring.dev
```
________________________________________________________________________________________________________________________________________________________________________
이제 빈 등록 초기화, 소멸 메서드 지정에 대해 알아보겠습니다.
________________________________________________________________________________________________________________________________________________________________________
설정 정보를 사용하도록 변경은 다음과 같이 진행합니다.
________________________________________________________________________________________________________________________________________________________________________
```java
package hello.core.lifecycle;
public class NetworkClient {
      private String url;
      public NetworkClient() {
            System.out.println("생성자 호출, url = " + url); }
      public void setUrl(String url) {
            this.url = url;
      }
      //서비스 시작시 호출
      public void connect() {
            System.out.println("connect: " + url);
      }
      public void call(String message) {
            System.out.println("call: " + url + " message = " + message);
      }
      //서비스 종료시 호출
      public void disConnect() {
            System.out.println("close + " + url);
      }
      public void init() { System.out.println("NetworkClient.init"); 
      connect();
      call("초기화 연결 메시지");
      }
      public void close() {
            System.out.println("NetworkClient.close");
            disConnect();
      }
}
```
________________________________________________________________________________________________________________________________________________________________________
- 설정 정보에 초기화 소멸 메서드 지정
```java
@Configuration
static class LifeCycleConfig {
      @Bean(initMethod = "init", destroyMethod = "close")
      public NetworkClient networkClient() {
            NetworkClient networkClient = new NetworkClient();
            networkClient.setUrl("http://hello-spring.dev");
            return networkClient;
      } 
}
```
________________________________________________________________________________________________________________________________________________________________________
결과는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
```
생성자 호출, url = null
NetworkClient.init
connect: http://hello-spring.dev
call: http://hello-spring.dev message = 초기화 연결 메시지
13:33:10.029 [main] DEBUG org.springframework.context.annotation.AnnotationConfigApplicationContext - Closing NetworkClient.close
close + http://hello-spring.dev
```
________________________________________________________________________________________________________________________________________________________________________
애노테이션 @PostConstruct, @PreDestroy과 관련된 코드는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
```java
package hello.core.lifecycle;
import javax.annotation.PostConstruct;
import javax.annotation.PreDestroy;
public class NetworkClient {
      private String url;
      public NetworkClient() {
            System.out.println("생성자 호출, url = " + url); }
      public void setUrl(String url) {
            this.url = url;
      }
      //서비스 시작시 호출
      public void connect() {
            System.out.println("connect: " + url);
      }
      public void call(String message) {
            System.out.println("call: " + url + " message = " + message);
      }
      //서비스 종료시 호출
      public void disConnect() {
            System.out.println("close + " + url);
      }
      @PostConstruct
      public void init() {
            System.out.println("NetworkClient.init"); 
            connect();
            call("초기화 연결 메시지");
      }
      @PreDestroy
      public void close() {
            System.out.println("NetworkClient.close");
            disConnect();
      }
}
```
________________________________________________________________________________________________________________________________________________________________________
```java
@Configuration
static class LifeCycleConfig {
      @Bean
      public NetworkClient networkClient() {
            NetworkClient networkClient = new NetworkClient();
            networkClient.setUrl("http://hello-spring.dev");
            return networkClient;
      } 
}
```
________________________________________________________________________________________________________________________________________________________________________
실행 결과는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
```
생성자 호출, url = null
NetworkClient.init
connect: http://hello-spring.dev
call: http://hello-spring.dev message = 초기화 연결 메시지
19:40:50.269 [main] DEBUG org.springframework.context.annotation.AnnotationConfigApplicationContext - Closing NetworkClient.close
close + http://hello-spring.dev
```
________________________________________________________________________________________________________________________________________________________________________
이상으로 의존관계 자동 주입과 빈 생명주기 콜백과 관련된 내용을 제가 작성할 수 있는 범위 내에서 설명하였습니다. 다음 포스팅에서는 해당 강의의 마지막 섹션에 해당하는 빈 스코프와 ['모든 개발자를 위한 HTTP 웹 기본 지식' 강의](https://www.inflearn.com/course/http-%EC%9B%B9-%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC/dashboard)의 섹션 1에 해당하는 인터넷 네트워크에 대한 포스팅을 하겠습니다.
________________________________________________________________________________________________________________________________________________________________________
포스팅을 하면서 말씀드리자면, 지금까지의 제 포스팅은 공부한 내용을 정리하는 것에 가깝기 때문에 보시는 분들은 큰 흐름만 잡고 가시는 것을 권장드립니다. 제대로 공부를 하시려면, 제가 직접 구매한 책과 강의를 보시는 것을 추천드리고, 다른 방법이 맞으시면 자신만의 방법대로 공부하시는 것을 추천드립니다. 사실 다음 포스팅에 스프링 기본편을 마치고 후기를 남기려고 했는데, HTTP와 관련된 강의의 첫 장의 내용도 같이 기록할 예정이기 때문에 이번 포스팅에 후기를 남깁니다. 확실히 스프링 공부를 하면서 직접 코드를 따라 치는 것이 도움이 많이 된다고 느꼈습니다. 사실 저도 공부를 하면서 이해가 안 가는 내용이 많습니다. 그렇기에 이렇게 블로그에 정리를 하면서 늘 복습을 합니다. 저와 같은 강의를 보시는 분들에게 도움이 되는 말씀을 드리자면, 강의를 들은 이후에 직접 노션이나 개인 블로그에 내용을 적으시면서 복습하시는 것을 추천드립니다. 어떠한 공부든 복습이 개인적으로 중요한 것 같습니다.
________________________________________________________________________________________________________________________________________________________________________
그럼 오늘도 긴 글 읽어주시느라 고생하셨고, 내일도 비가 안 오길 바라겠습니다.
________________________________________________________________________________________________________________________________________________________________________
#### 감사합니다.
________________________________________________________________________________________________________________________________________________________________________
[레퍼런스]
1. 스프링 핵심 원리 - 기본편 : https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-%EA%B8%B0%EB%B3%B8%ED%8E%B8/dashboard
2. 모든 개발자를 위한 HTTP 웹 기본 지식 : https://www.inflearn.com/course/http-%EC%9B%B9-%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC/dashboard