---
title: "스프링의 기본에 대해 알아볼까요?(5)"
date: 2022-08-13T17:01:54+09:00
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
안녕하세요. shcDE입니다. 드디어 이번 포스팅을 마지막으로 스프링 기본 단계 포스팅을 마치게 되었습니다. 이번 포스팅에서는 빈 스코프에 대해 알아보겠으며, 해당 포스팅은 김영한님의 ['스프링 핵심 원리 - 기본편'](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-%EA%B8%B0%EB%B3%B8%ED%8E%B8/dashboard) 섹션 9를 공부하여 정리한 내용임을 밝히며, 직접 해당 강의를 구매하여 게시물을 작성함을 알립니다.
________________________________________________________________________________________________________________________________________________________________________
우선 빈 스코프에 대한 정의를 말씀드리겠습니다. 지금까지 배운 내용에서는 스프링 빈이 스프링 컨테이너의 시작과 함께 생성되어서 스프링 컨테이너가 종료될 때 까지 유지된다고 학습했습니다. 이것은 스프링 빈이 기본적으로 싱글톤 스코프로 생성되기 때문이라고 합니다. 여기에서 스코프는 번역 그대로 빈이 존재할 수 있는 범위를 뜻한다고 볼 수 있습니다. 이러한 빈 스코프는 다음과 같이 지정할 수 있습니다.
________________________________________________________________________________________________________________________________________________________________________
- 컴포넌트 스캔 자동 등록
```java
@Scope("prototype")
@Component
public class HelloBean {}
```
________________________________________________________________________________________________________________________________________________________________________
- 수동 등록
```java
@Scope("prototype")
@Bean
PrototypeBean HelloBean() {
    return new HelloBean();
}
```
________________________________________________________________________________________________________________________________________________________________________
지금까지의 과정에서는 싱글톤 스코프를 계속 사용했습니다. 이제 프로토타입 스코프부터 확인해보겠습니다.
________________________________________________________________________________________________________________________________________________________________________
먼저 프로토타입 스코프에 대해 확인해보겠습니다. 싱글톤 스코프의 빈을 조회하면 스프링 컨테이너는 항상 같은 인스턴스의 스프링 빈을 반환합니다. 반면에 프로토타입 스코프를 스프링 컨테이너에 조회하면 스프링 컨테이너는 항상 새로운 인스턴스를 생성해서 반환합니다. 이러한 프로토타입 스코프와 관련된 코드를 확인해보겠습니다.
________________________________________________________________________________________________________________________________________________________________________
- 싱글톤 스코프 빈 테스트
```java
package hello.core.scope;
import org.junit.jupiter.api.Test;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
import org.springframework.context.annotation.Scope;
import javax.annotation.PostConstruct;
import javax.annotation.PreDestroy;
import static org.assertj.core.api.Assertions.assertThat;
public class SingletonTest {
      @Test
      public void singletonBeanFind() {
          AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(SingletonBean.class);
          SingletonBean singletonBean1 = ac.getBean(SingletonBean.class);
          SingletonBean singletonBean2 = ac.getBean(SingletonBean.class);
          System.out.println("singletonBean1 = " + singletonBean1);
          System.out.println("singletonBean2 = " + singletonBean2);
          assertThat(singletonBean1).isSameAs(singletonBean2);
          ac.close(); //종료 
      }
      @Scope("singleton")
      static class SingletonBean {
          @PostConstruct
          public void init() {
              System.out.println("SingletonBean.init");
          }
          @PreDestroy
          public void destroy() {
              System.out.println("SingletonBean.destroy");
          }
      } 
}
```
________________________________________________________________________________________________________________________________________________________________________
실행 결과는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
```
SingletonBean.init
singletonBean1 = hello.core.scope.PrototypeTest$SingletonBean@54504ecd
singletonBean2 = hello.core.scope.PrototypeTest$SingletonBean@54504ecd
org.springframework.context.annotation.AnnotationConfigApplicationContext -
Closing SingletonBean.destroy
```
________________________________________________________________________________________________________________________________________________________________________
이제 프로토타입 스코프 빈 테스트를 진행해보겠습니다.
________________________________________________________________________________________________________________________________________________________________________
```java
package hello.core.scope;
import org.junit.jupiter.api.Test;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Scope;
import javax.annotation.PostConstruct;
import javax.annotation.PreDestroy;
import static org.assertj.core.api.Assertions.*;
public class PrototypeTest {
    @Test
    public void prototypeBeanFind() {
        AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(PrototypeBean.class);
        System.out.println("find prototypeBean1");
        PrototypeBean prototypeBean1 = ac.getBean(PrototypeBean.class); System.out.println("find prototypeBean2");
        PrototypeBean prototypeBean2 = ac.getBean(PrototypeBean.class); System.out.println("prototypeBean1 = " + prototypeBean1); System.out.println("prototypeBean2 = " + prototypeBean2); assertThat(prototypeBean1).isNotSameAs(prototypeBean2); ac.close(); //종료
    }
    @Scope("prototype")
    static class PrototypeBean {
        @PostConstruct
        public void init() {
            System.out.println("PrototypeBean.init");
        }
        @PreDestroy
        public void destroy() {
            System.out.println("PrototypeBean.destroy");
        }
    } 
}
```
________________________________________________________________________________________________________________________________________________________________________
실행 결과는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
```
find prototypeBean1
PrototypeBean.init
find prototypeBean2
PrototypeBean.init
prototypeBean1 = hello.core.scope.PrototypeTest$PrototypeBean@13d4992d
prototypeBean2 = hello.core.scope.PrototypeTest$PrototypeBean@302f7971
org.springframework.context.annotation.AnnotationConfigApplicationContext - Closing
```
________________________________________________________________________________________________________________________________________________________________________
이제 프로토타입 스코프를 싱글톤 빈과 함께 사용시 문제점에 대해 말씀드리겠습니다. 스프링 컨테이너에 프로토타입 스코프의 빈을 요청하면 항상 새로운 객체 인스턴스를 생성해서 반환합니다. 하지만 싱글톤 빈과 함께 사용할 때는 의도한 대로 잘 동작하지 않으므로 주의해야 합니다. 먼저 스프링 컨테이너에 프로토타입 빈을 직접 요청하는 예제를 확인해보겠습니다.
________________________________________________________________________________________________________________________________________________________________________
```java
public class SingletonWithPrototypeTest1 {
      @Test
      void prototypeFind() {
          AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(PrototypeBean.class);
          PrototypeBean prototypeBean1 = ac.getBean(PrototypeBean.class);
          prototypeBean1.addCount();
          assertThat(prototypeBean1.getCount()).isEqualTo(1);
          PrototypeBean prototypeBean2 = ac.getBean(PrototypeBean.class);
          prototypeBean2.addCount();
          assertThat(prototypeBean2.getCount()).isEqualTo(1);
      }
      @Scope("prototype")
      static class PrototypeBean {
          private int count = 0;
          public void addCount() {
              count++;
          }
          public int getCount() {
              return count;
          }
          @PostConstruct
          public void init() {
              System.out.println("PrototypeBean.init " + this);
          }
          @PreDestroy
          public void destroy() {
              System.out.println("PrototypeBean.destroy");
          }
      } 
}
```
________________________________________________________________________________________________________________________________________________________________________
싱글톤에서 프로토타입 빈 사용과 관련된 예제는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
```java
package hello.core.scope;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
import org.springframework.context.annotation.Scope;
import javax.annotation.PostConstruct;
import javax.annotation.PreDestroy;
import static org.assertj.core.api.Assertions.*;
public class SingletonWithPrototypeTest1 {
    @Test
    void singletonClientUsePrototype() {
        AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(ClientBean.class, PrototypeBean.class);
        ClientBean clientBean1 = ac.getBean(ClientBean.class);
        int count1 = clientBean1.logic();
        assertThat(count1).isEqualTo(1);
        ClientBean clientBean2 = ac.getBean(ClientBean.class);
        int count2 = clientBean2.logic();
        assertThat(count2).isEqualTo(2);
    }
    static class ClientBean {
        private final PrototypeBean prototypeBean;
        @Autowired
        public ClientBean(PrototypeBean prototypeBean) {
            this.prototypeBean = prototypeBean;
        }
        public int logic() {
            prototypeBean.addCount();
            int count = prototypeBean.getCount();
            return count;
        } 
    }
    @Scope("prototype")
    static class PrototypeBean {
        private int count = 0;
        public void addCount() {
            count++; }
            public int getCount() {
                return count;
            }
            @PostConstruct
            public void init() {
                System.out.println("PrototypeBean.init " + this);
            }
            @PreDestroy
            public void destroy() {
                System.out.println("PrototypeBean.destroy");
            }
    } 
}
```
________________________________________________________________________________________________________________________________________________________________________
프로토타입 스코프를 싱글톤 빈과 함께 사용 시, Provider로 문제 해결할 수 있습니다. 이러한 방법 중, 스프링 컨테이너에 요청에 대한 코드를 확인하겠습니다.
________________________________________________________________________________________________________________________________________________________________________
```java
public class PrototypeProviderTest {
      @Test
      void providerTest() {
          AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(ClientBean.class, PrototypeBean.class);
          ClientBean clientBean1 = ac.getBean(ClientBean.class);
          int count1 = clientBean1.logic();
          assertThat(count1).isEqualTo(1);
          ClientBean clientBean2 = ac.getBean(ClientBean.class);
          int count2 = clientBean2.logic();
          assertThat(count2).isEqualTo(1);
      }
      static class ClientBean {
          @Autowired
          private ApplicationContext ac;
          public int logic() {
              PrototypeBean prototypeBean = ac.getBean(PrototypeBean.class);
              prototypeBean.addCount();
              int count = prototypeBean.getCount();
              return count;
          } 
      }
      @Scope("prototype")
      static class PrototypeBean {
          private int count = 0;
          public void addCount() {
              count++;
          }
          public int getCount() {
              return count;
          }
          @PostConstruct
          public void init() {
              System.out.println("PrototypeBean.init " + this);
          }
          @PreDestroy
          public void destroy() {
              System.out.println("PrototypeBean.destroy");
          }
      } 
}
```
________________________________________________________________________________________________________________________________________________________________________
여기에서 핵심 코드는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
```java
@Autowired
private ApplicationContext ac;
public int logic() {
    PrototypeBean prototypeBean = ac.getBean(PrototypeBean.class);
    prototypeBean.addCount();
    int count = prototypeBean.getCount();
    return count;
}
```
________________________________________________________________________________________________________________________________________________________________________
ObjectFactory, ObjectProvider와 관련된 코드는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
```java
@Autowired
private ObjectProvider<PrototypeBean> prototypeBeanProvider;
public int logic() {
      PrototypeBean prototypeBean = prototypeBeanProvider.getObject();
      prototypeBean.addCount();
      int count = prototypeBean.getCount();
      return count;
}
```
________________________________________________________________________________________________________________________________________________________________________
JSR-330 Provider 자바 표준 방법을 사용할 수도 있습니다. 사용 예시는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
- javax.inject.Provider 참고용 코드
```java
package javax.inject;
public interface Provider<T> {
    T get(); 
}
```
________________________________________________________________________________________________________________________________________________________________________
```java
//implementation 'javax.inject:javax.inject:1' gradle 추가 필수 @Autowired
private Provider<PrototypeBean> provider;
public int logic() {
    PrototypeBean prototypeBean = provider.get();
    prototypeBean.addCount();
    int count = prototypeBean.getCount();
    return count;
}
```
________________________________________________________________________________________________________________________________________________________________________
이제 웹 스코프에 대해 알아보겠습니다. 웹 스코프의 특징은 웹 환경에서만 동작하며, 프로토타입과 다르게 스프링이 해당 스코프의 종료시점까지 관리합니다. 따라서 종료 메서드가 호출될 수 있습니다. 우선 request 스코프 예제를 확인해보겠습니다.
________________________________________________________________________________________________________________________________________________________________________
우선 build.gradle에 다음과 같은 라이브러리를 추가합니다.
________________________________________________________________________________________________________________________________________________________________________
```java
//web 라이브러리 추가
implementation 'org.springframework.boot:spring-boot-starter-web'
```
________________________________________________________________________________________________________________________________________________________________________
추가 후, hello.core.CoreApplication의 main 메서드를 실행하면 웹 애플리케이션이 실행되는 것을 확인할 수 있습니다.
________________________________________________________________________________________________________________________________________________________________________
```
Tomcat started on port(s): 8080 (http) with context path ''
Started CoreApplication in 0.914 seconds (JVM running for 1.528)
```
________________________________________________________________________________________________________________________________________________________________________
이제 request 스코프를 활용하여 추가 기능을 개발하겠습니다.
________________________________________________________________________________________________________________________________________________________________________
- MyLogger
```java
package hello.core.common;
import org.springframework.context.annotation.Scope;
import org.springframework.stereotype.Component;
import javax.annotation.PostConstruct;
import javax.annotation.PreDestroy;
import java.util.UUID;
@Component
@Scope(value = "request")
public class MyLogger {
    private String uuid;
    private String requestURL;
    public void setRequestURL(String requestURL) {
        this.requestURL = requestURL;
    }
    public void log(String message) {
        System.out.println("[" + uuid + "]" + "[" + requestURL + "] " + message); 
    }

    @PostConstruct
    public void init() {
        uuid = UUID.randomUUID().toString();
        System.out.println("[" + uuid + "] request scope bean create:" + this);
    }
    @PreDestroy
    public void close() {
        System.out.println("[" + uuid + "] request scope bean close:" + this);
    }
}
```
________________________________________________________________________________________________________________________________________________________________________
- LogDemoController
```java
package hello.core.web;
import hello.core.common.MyLogger;
import hello.core.logdemo.LogDemoService;
import lombok.RequiredArgsConstructor;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseBody;
import javax.servlet.http.HttpServletRequest;
@Controller
@RequiredArgsConstructor
public class LogDemoController {
    private final LogDemoService logDemoService;
    private final MyLogger myLogger;
    @RequestMapping("log-demo")
    @ResponseBody
    public String logDemo(HttpServletRequest request) {
        String requestURL = request.getRequestURL().toString();
        myLogger.setRequestURL(requestURL);
        myLogger.log("controller test");
        logDemoService.logic("testId");
        return "OK";
    } 
}
```
________________________________________________________________________________________________________________________________________________________________________
- LogDemoService 추가
```java
package hello.core.logdemo;
import hello.core.common.MyLogger;
import lombok.RequiredArgsConstructor;
import org.springframework.stereotype.Service;
@Service
@RequiredArgsConstructor
public class LogDemoService {
    private final MyLogger myLogger;
    public void logic(String id) {
        myLogger.log("service id = " + id);
    } 
}
```
________________________________________________________________________________________________________________________________________________________________________
여기서 실행을 하면 오류가 발생합니다.
________________________________________________________________________________________________________________________________________________________________________
```
Error creating bean with name 'myLogger': Scope 'request' is not active for the
current thread; consider defining a scoped proxy for this bean if you intend to
refer to it from a singleton;
```
________________________________________________________________________________________________________________________________________________________________________
오류가 발생하지 않게 Provider를 사용해보겠습니다.
________________________________________________________________________________________________________________________________________________________________________
```java
package hello.core.web;
import hello.core.common.MyLogger;
import hello.core.logdemo.LogDemoService;
import lombok.RequiredArgsConstructor;
import org.springframework.beans.factory.ObjectProvider;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseBody;
import javax.servlet.http.HttpServletRequest;
@Controller
@RequiredArgsConstructor
public class LogDemoController {
    private final LogDemoService logDemoService;
    private final ObjectProvider<MyLogger> myLoggerProvider;
    @RequestMapping("log-demo")
    @ResponseBody
    public String logDemo(HttpServletRequest request) {
        String requestURL = request.getRequestURL().toString();
        MyLogger myLogger = myLoggerProvider.getObject();
        myLogger.setRequestURL(requestURL);
        myLogger.log("controller test");
        logDemoService.logic("testId");
        return "OK";
    } 
}
```
________________________________________________________________________________________________________________________________________________________________________
```java
package hello.core.logdemo;
import hello.core.common.MyLogger;
import lombok.RequiredArgsConstructor;
import org.springframework.beans.factory.ObjectProvider;
import org.springframework.stereotype.Service;
@Service
@RequiredArgsConstructor
public class LogDemoService {
    private final ObjectProvider<MyLogger> myLoggerProvider;
    public void logic(String id) {
        MyLogger myLogger = myLoggerProvider.getObject();
        myLogger.log("service id = " + id);
    } 
}
```
________________________________________________________________________________________________________________________________________________________________________
이제 다시 실행하면 정상적으로 동작합니다.
________________________________________________________________________________________________________________________________________________________________________
```
[d06b992f...] request scope bean create
[d06b992f...][http://localhost:8080/log-demo] controller test
[d06b992f...][http://localhost:8080/log-demo] service id = testId
[d06b992f...] request scope bean close
```
________________________________________________________________________________________________________________________________________________________________________
더 간결하게 하는 방법으로는 프록시가 있습니다.
________________________________________________________________________________________________________________________________________________________________________
```java
@Component
@Scope(value = "request", proxyMode = ScopedProxyMode.TARGET_CLASS)
public class MyLogger {

}
```
________________________________________________________________________________________________________________________________________________________________________
프록시 추가 후 기존 코드를 Provider 적용 전으로 되돌리겠습니다.
________________________________________________________________________________________________________________________________________________________________________
```java
package hello.core.web;
import hello.core.common.MyLogger;
import hello.core.logdemo.LogDemoService;
import lombok.RequiredArgsConstructor;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseBody;
import javax.servlet.http.HttpServletRequest;
@Controller
@RequiredArgsConstructor
public class LogDemoController {
    private final LogDemoService logDemoService;
    private final MyLogger myLogger;
    @RequestMapping("log-demo")
    @ResponseBody
    public String logDemo(HttpServletRequest request) {
        String requestURL = request.getRequestURL().toString();
        myLogger.setRequestURL(requestURL);
        myLogger.log("controller test");
        logDemoService.logic("testId");
        return "OK";
    } 
}
```
________________________________________________________________________________________________________________________________________________________________________
```java
package hello.core.logdemo;
import hello.core.common.MyLogger;
import lombok.RequiredArgsConstructor;
import org.springframework.stereotype.Service;
@Service
@RequiredArgsConstructor
public class LogDemoService {
    private final MyLogger myLogger;
    public void logic(String id) {
        myLogger.log("service id = " + id);
    } 
}
```
________________________________________________________________________________________________________________________________________________________________________
마찬가지로, 코드 실행 시 마찬가지로 정상 작동됩니다. 이상으로 스프링 기본 단계 내용의 예제를 모두 확인해보았습니다.
________________________________________________________________________________________________________________________________________________________________________
사실 이번 포스팅은 HTTP 기초 단계에 대한 내용도 추가로 포스팅을 하려고 했습니다. 하지만 공부 계획을 바꾸게 되어 JPA 활용에 관련된 내용을 게시할 것 같습니다. 그 이유는 HTTP를 배우려 했던 이유인 스프링 MVC에 대한 내용을 학습하기 전에, 실무에서 많이 쓰이는 JPA의 기초부터 학습하고, 심화 단계에서 스프링 MVC와 스프링 DB에 대해 공부하는 것이 좋다고 강사님이 말씀해주셨기 때문입니다. 그래서 바로 다음 포스팅부터는 JPA 활용에 대한 내용을 다룰 예정이며, 해당 파트의 공부가 마무리된 이후에는 개인 프로젝트 제작을 바로 시작할 예정입니다. 이에 따라 스프링에 관련된 심화 내용은 프로젝트를 하면서 해당 내용에서 다루는 기능이 필요할 때 틈틈히 공부할 예정입니다. 이에 따라, JPA에 대한 포스팅을 다음부터 진행할 예정이며, 해당 포스팅 마무리 이후에는 CS 전공지식에 대한 내용을 기록할 계획입니다.
________________________________________________________________________________________________________________________________________________________________________
이상으로 스프링 기본 단계 공부 기록을 마치겠습니다. 모두 좋은 주말 보내시길 바라며, 오늘도 긴 글 읽어주시느라 고생하셨습니다.
________________________________________________________________________________________________________________________________________________________________________
#### 감사합니다.
________________________________________________________________________________________________________________________________________________________________________
[레퍼런스]
1. 스프링 핵심 원리 - 기본편 : https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-%EA%B8%B0%EB%B3%B8%ED%8E%B8/dashboard