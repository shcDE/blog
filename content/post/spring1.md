---
title: "스프링을 입문해볼까요?(1)"
date: 2022-08-02T18:37:08+09:00
categories:
- development
tags:
- development
keywords:
- backend, java, development, spring
image: bird-g128b5223c_1920.jpg
---
![spring](https://github.com/shcDE/pictures/blob/main/images_for_blog/bird-g128b5223c_1920.jpg?raw=true)
________________________________________________________________________________________________________________________________________________________________________
안녕하세요. shcDE입니다. 이번 포스팅부터 스프링 공부 기록을 시작해보겠습니다. 이번 포스팅에서는 스프링 웹 개발 기초, 회원 관리 예제 - 백엔드 개발, 스프링 빈과 의존관계와 관련된 내용에 대해 다룰 예정입니다. 해당 포스팅에서는 운영체제에 따라 설치 방법이 다를 수 있으므로, 설치 및 환경설정에 대해서는 다루지 않을 예정입니다.
________________________________________________________________________________________________________________________________________________________________________
시작 전에, 해당 포스팅은 김영한님의 ['스프링 입문 - 코드로 배우는 스프링 부트, 웹 MVC, DB 접근 기술'](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%EC%9E%85%EB%AC%B8-%EC%8A%A4%ED%94%84%EB%A7%81%EB%B6%80%ED%8A%B8/dashboard) 섹션2~4를 공부하여 정리한 내용임을 밝히며, 직접 해당 강의를 구매하여 게시물을 작성함을 알립니다.
________________________________________________________________________________________________________________________________________________________________________
우선 스프링 웹 개발 기초의 내용은 다음 카테고리로 구성되어 있습니다.
________________________________________________________________________________________________________________________________________________________________________
1. 정적 컨텐츠
2. MVC와 템플릿 엔진 
3. API
________________________________________________________________________________________________________________________________________________________________________
먼저, 정적 컨텐츠에 관련된 내용부터 정리하겠습니다. 실행시킬 파일 경로 및 소스코드는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
- resources/static/hello-static.html
```html
<!DOCTYPE HTML>
  <html>
  <head>
      <title>static content</title>
      <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
  </head>
<body>
정적 컨텐츠 입니다.
  </body>
  </html>
```
________________________________________________________________________________________________________________________________________________________________________
해당 소스코드 작성 후 프레임워크를 작동시키면 'http://localhost:8080/hello-static.html'로 접속하면 정상적으로 웹 페이지가 노출되는 것을 확인할 수 있습니다.
________________________________________________________________________________________________________________________________________________________________________
MVC와 템플릿 엔진과 관련된 경로 및 소스코드는 다음과 같습니다. 여기에서 MVC란 'Model, View, Controller'의 약자입니다.
________________________________________________________________________________________________________________________________________________________________________
- Controller
```java
 @Controller
public class HelloController {
    @GetMapping("hello-mvc")
    public String helloMvc(@RequestParam("name") String name, Model model) {
        model.addAttribute("name", name);
        return "hello-template";
    }
}
```
________________________________________________________________________________________________________________________________________________________________________
- resources/template/hello-template.html
```html
  <html xmlns:th="http://www.thymeleaf.org">
  <body>
  <p th:text="'hello ' + ${name}">hello! empty</p>
  </body>
  </html>
```
________________________________________________________________________________________________________________________________________________________________________
해당 소스코드 작성 및 실행 후 'http://localhost:8080/hello-mvc?name=spring'로 접속하면 정상적으로 웹 페이지가 수정된 것을 확인할 수 있습니다.
________________________________________________________________________________________________________________________________________________________________________
API와 관련된 소스코드는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
- @ResponseBody 문자 반환
```java
@Controller
public class HelloController {
    @GetMapping("hello-string")
    @ResponseBody
    public String helloString(@RequestParam("name") String name) {
        return "hello " + name;
    }
}
```
@ResponseBody를 사용하면 뷰 리졸버(viewResolver)를 사용하지 않으며, HTTP의 BODY에 문자 내용을 직접 반환합니다. 이는 HTML BODY TAG를 의미하는 것이 아닙니다.
________________________________________________________________________________________________________________________________________________________________________
해당 소스코드 작성 및 실행 후 'http://localhost:8080/hello-string?name=spring'로 접속하면 정상적으로 웹 페이지가 수정된 것을 확인할 수 있습니다.
________________________________________________________________________________________________________________________________________________________________________
다른 API 사용 사례는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
- @ResponseBody 객체 반환
```java
@Controller
public class HelloController {
    @GetMapping("hello-api")
    @ResponseBody
    public Hello helloApi(@RequestParam("name") String name) {
        Hello hello = new Hello();
        hello.setName(name);
        return hello;
    }
    static class Hello {
        private String name;
        public String getName() {
            return name;
}
        public void setName(String name) {
            this.name = name;
} }
}
```
해당 코드 실행 시, @ResponseBody를 사용하고, 객체를 반환하면 객체가 JSON으로 변환됩니다.
________________________________________________________________________________________________________________________________________________________________________
해당 소스코드 작성 및 실행 후 'http://localhost:8080/hello-api?name=spring'로 접속하면 정상적으로 JSON 형태로 수정된 것을 확인할 수 있습니다.
________________________________________________________________________________________________________________________________________________________________________
다음으로 회원 관리 예제 - 백엔드 개발의 내용은 다음 카테고리로 구성되어 있습니다.
________________________________________________________________________________________________________________________________________________________________________
1. 비즈니스 요구사항 정리
2. 회원 도메인과 리포지토리 만들기 
3. 회원 리포지토리 테스트 케이스 작성 
4. 회원 서비스 개발
5. 회원 서비스 테스트
________________________________________________________________________________________________________________________________________________________________________
비즈니스 요구사항 정리는 다음과 같이 진행합니다.
________________________________________________________________________________________________________________________________________________________________________
데이터: 회원ID, 이름
기능: 회원 등록, 조회
아직 데이터 저장소가 선정되지 않음(가상의 시나리오)
________________________________________________________________________________________________________________________________________________________________________
회원 도메인과 리포지토리 만들기는 다음과 같이 진행합니다.
________________________________________________________________________________________________________________________________________________________________________
- 회원객체
```java
package hello.hellospring.domain;
public class Member {
    private Long id;
    private String name;
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
}
```
________________________________________________________________________________________________________________________________________________________________________
- 회원 리포지토리 인터페이스
```java
package hello.hellospring.repository;
import hello.hellospring.domain.Member;
import java.util.List;
import java.util.Optional;
public interface MemberRepository {
    Member save(Member member);
    Optional<Member> findById(Long id);
    Optional<Member> findByName(String name);
    List<Member> findAll();
}
```
________________________________________________________________________________________________________________________________________________________________________
- 회원 리포지토리 메모리 구현체
```java
package hello.hellospring.repository;
import hello.hellospring.domain.Member;
import java.util.*;
/**
* 동시성 문제가 고려되어 있지 않음, 실무에서는 ConcurrentHashMap, AtomicLong 사용 고려
*/
public class MemoryMemberRepository implements MemberRepository {
    private static Map<Long, Member> store = new HashMap<>();
    private static long sequence = 0L;
    @Override
    public Member save(Member member) {
        member.setId(++sequence);
        store.put(member.getId(), member);
        return member;
}
    @Override
    public Optional<Member> findById(Long id) {
        return Optional.ofNullable(store.get(id));
      }
    @Override
    public List<Member> findAll() {
        return new ArrayList<>(store.values());
      }
      @Override
      public Optional<Member> findByName(String name) {
          return store.values().stream()
                  .filter(member -> member.getName().equals(name))
                  .findAny();
      }
      public void clearStore() {
          store.clear();
      } 
}
```
________________________________________________________________________________________________________________________________________________________________________
이제 회원 리포지토리 테스트 케이스 작성에 대해 설명해드리겠습니다. 회원 리포지토리 메모리 구현체 테스트와 관련된 경로 및 소스코드는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
- src/test/java
```java
package hello.hellospring.repository;
import hello.hellospring.domain.Member;
import org.junit.jupiter.api.AfterEach;
import org.junit.jupiter.api.Test;
import java.util.List;
import java.util.Optional;
import static org.assertj.core.api.Assertions.*;
class MemoryMemberRepositoryTest {
    MemoryMemberRepository repository = new MemoryMemberRepository();
    @AfterEach
    public void afterEach() {
        repository.clearStore();
    }
    @Test
    public void save() {
//given
        Member member = new Member();
        member.setName("spring");
//when
        repository.save(member);
//then
        Member result = repository.findById(member.getId()).get();
        assertThat(result).isEqualTo(member);
    }
    @Test
    public void findByName() {
//given
        Member member1 = new Member();
        member1.setName("spring1");
        repository.save(member1);
        Member member2 = new Member();
        member2.setName("spring2");
        repository.save(member2);
//when
        Member result = repository.findByName("spring1").get();
        //then
        assertThat(result).isEqualTo(member1);
      }
    @Test
    public void findAll() {
//given
        Member member1 = new Member();
        member1.setName("spring1");
        repository.save(member1);
        Member member2 = new Member();
        member2.setName("spring2");
        repository.save(member2);
//when
        List<Member> result = repository.findAll();
//then
        assertThat(result.size()).isEqualTo(2);
    }
}
```
________________________________________________________________________________________________________________________________________________________________________
회원 서비스 개발은 다음과 같이 진행합니다.
________________________________________________________________________________________________________________________________________________________________________
```java
package hello.hellospring.service;
import hello.hellospring.domain.Member;
import hello.hellospring.repository.MemberRepository;
import java.util.List;
import java.util.Optional;
public class MemberService {
    private final MemberRepository memberRepository = new
MemoryMemberRepository();
/**
* 회원가입
*/
    public Long join(Member member) {
validateDuplicateMember(member); //중복 회원 검증 
memberRepository.save(member);
return member.getId();
}
    private void validateDuplicateMember(Member member) {
        memberRepository.findByName(member.getName())
}
.ifPresent(m -> {
throw new IllegalStateException("이미 존재하는 회원입니다.");
});
/**
*전체 회원 조회
*/
    public List<Member> findMembers() {
        return memberRepository.findAll();
}
    public Optional<Member> findOne(Long memberId) {
        return memberRepository.findById(memberId);
} }
```
________________________________________________________________________________________________________________________________________________________________________
회원 서비스 테스트는 다음과 같이 진행합니다.
________________________________________________________________________________________________________________________________________________________________________
기존에는 회원 서비스가 메모리 회원 리포지토리를 직접 생성하게 했습니다.
```java
public class MemberService {
      private final MemberRepository memberRepository =
                                            new MemoryMemberRepository();
}
```
________________________________________________________________________________________________________________________________________________________________________
하지만 이제는 회원 리포지토리의 코드가 회원 서비스 코드를 DI 가능하게 변경할 수 있습니다.
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
회원 서비스 테스트에 대한 전체 코드는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
```java
package hello.hellospring.service;
import hello.hellospring.domain.Member;
import hello.hellospring.repository.MemoryMemberRepository;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import static org.assertj.core.api.Assertions.*;
import static org.junit.jupiter.api.Assertions.*;
class MemberServiceTest {
  MemberService memberService;
  MemoryMemberRepository memberRepository;
  @BeforeEach
  public void beforeEach() {
      memberRepository = new MemoryMemberRepository();
      memberService = new MemberService(memberRepository);
  }
  @AfterEach
  public void afterEach() {
      memberRepository.clearStore();
  }
  @Test
  public void 회원가입() throws Exception {
  //Given
      Member member = new Member();
      member.setName("hello");
  //When
      Long saveId = memberService.join(member);
  //Then
      Member findMember = memberRepository.findById(saveId).get();
      assertEquals(member.getName(), findMember.getName());
  }
  @Test
  public void 중복_회원_예외() throws Exception {
  //Given
      Member member1 = new Member();
      member1.setName("spring");
      Member member2 = new Member();
      member2.setName("spring");
  //When
      memberService.join(member1);
      IllegalStateException e = assertThrows(IllegalStateException.class, () -> memberService.join(member2));//예외가 발생해야 한다. 
      assertThat(e.getMessage()).isEqualTo("이미 존재하는 회원입니다.");} 
}
```
________________________________________________________________________________________________________________________________________________________________________
이제 마지막으로 스프링 빈과 의존관계에 대해 알아보겠습니다. 해당 섹션은 '컴포넌트 스캔과 자동 의존관계 설정'과 '자바 코드로 직접 스프링 빈 등록하기'로 구성되어 있습니다.
________________________________________________________________________________________________________________________________________________________________________
컴포넌트 스캔과 자동 의존관계 설정과 관련된 코드는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
- 회원 서비스 스프링 빈 등록
```java
@Service
public class MemberService {
    private final MemberRepository memberRepository;
    @Autowired
    public MemberService(MemberRepository memberRepository) {
        this.memberRepository = memberRepository;
    }
}
```
- 회원 리포지토리 스프링 빈 등록
```java
@Repository
public class MemoryMemberRepository implements MemberRepository {}
```
________________________________________________________________________________________________________________________________________________________________________
자바 코드로 직접 스프링 빈 등록하기와 관련된 코드는 다음과 같습니다.
________________________________________________________________________________________________________________________________________________________________________
```java
package hello.hellospring;
import hello.hellospring.repository.MemberRepository;
import hello.hellospring.repository.MemoryMemberRepository;
import hello.hellospring.service.MemberService;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
@Configuration
public class SpringConfig {
         @Bean
        public MemberService memberService() {
            return new MemberService(memberRepository());
        }
        @Bean
        public MemberRepository memberRepository() {
          return new MemoryMemberRepository();
        }
}
```
________________________________________________________________________________________________________________________________________________________________________
여기까지 오늘 제가 공부한 내용을 복습하였습니다. 사실 내용이 그렇게 많지는 않지만, 상대적으로 직접 실습을 하면서 시간 소요가 꽤 컸던 것 같습니다. 다음 포스팅은 '스프링 입문 - 코드로 배우는 스프링 부트, 웹 MVC, DB 접근 기술' 마무리 단계까지 정리한 내용을 올릴 예정입니다. 오늘도 긴 글 읽어주시느라 모두 고생하셨습니다.
________________________________________________________________________________________________________________________________________________________________________
#### 감사합니다.
________________________________________________________________________________________________________________________________________________________________________
[레퍼런스]
1. 스프링 입문 - 코드로 배우는 스프링 부트, 웹 MVC, DB 접근 기술 : https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%EC%9E%85%EB%AC%B8-%EC%8A%A4%ED%94%84%EB%A7%81%EB%B6%80%ED%8A%B8/dashboard