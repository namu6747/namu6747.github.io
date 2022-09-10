---
title: 3. 프로젝트 환경 - Betty
author: namu6747
date: 2022-09-06 09:03:00 +0900
categories: [프로젝트, Betty]
tags: [스프링, 자바, 포트폴리오]
---

<hr/>
<br/>

이번 프로젝트에서 내 핵심 역할은   
프로젝트 환경을 구성  
계정 관련 로직 처리  
잡다한 프론트, 비즈니스 로직 등이 있는데  

가장 소개하고 싶은 게 바로  
xml 을 모두 제거하고 java config 로 돌린 것과  
스프링이 제공하는 여러 기능을 활용한 것이다.  

아래는 스프링 공식 문서에 나타난 내용이다.   

**_"스프링 환경 설정에서 어노테이션 기반이 XML 기반 설정보다 나은가?"_**

![Desktop View](/assets/img/betty/config/configbase.png)

```
어노테이션 기반의 설정 소개는, 'XML 보다 더 나은 가' 에 대한 의문을 일으켰다.
간단한 답변으로는, 그냥 따르라 ? ?
긴 답변으로는, 찬반이 있는데, 대개 개발자의 전략에 달려 있다.
어노테이션은 짧고 간결한 설정을 따르는 걸로 많은 context(정보)를 제공한다.
XML은 소스 코드를 건드리지 않고 리컴파일링이 가능하다.
몇몇 개발자는 ~~ 하지만 몇몇 개발자는 어노테이션 클래스는
더이상 POJO가 아니고 분산되어 제어 하기가 힘들다고 한다.
```

**_(죄송합니다. 영어를 잘 못해요..)_**

내가 해석하기엔 이런 느낌이었다.   
_"그냥 익숙한 거 써라"_  

난 **POJO** 의미 자체를 좋아하긴 한다만,  
어노테이션 기반 설정이 XML 을 무너뜨릴 수 있다면  
어노테이션 쪽 손을 들어주고 싶다.  
XML 을 쓰는 건 일단 구식인 것 같아서 싫었다.

인프런 강의에선 모두 스프링 부트를 사용 했었고,  
서블릿을 다루긴 했지만 레거시 프로젝트에서 진행되진 않았다.  
결국 난 내 손으로 A to Z 를 다 해냈어야 했는데,  
아무튼 성공했다.  

**굉장히 힘들게.**  

<br/>
<hr/>
<br/>

<!-- Web Config -->
# WebConfig
<br/>

![Desktop View](/assets/img/betty/config/web.png)

처음엔 Web.xml 을 대체할 때, 이상한 클래스를 상속 받길래 읽기도 싫었다.  
가만 보면, 어노테이션 설정 디스패쳐 서블릿 이니셜라이저   
완전 어노테이션 기반 설정을 위한 톰캣으로 느껴진다.  

서블릿은 굳이 여러 개 사용해야될까 싶어 하나만 사용했다.  
(서블릿을 여러 개 사용하는 것과 MSA 는 관련 없겠지?)

여기서 볼만한 것은 필터와 그 아래의 것인데.  
<br/>

## - Filter  

UTF-8 변환을 위핸 인코딩 필터는 무조건 필요했고,  

FormContentFilter 는 읽어본 결과
```
서블릿 스펙에선 html form data 를 POST 형태로만 받는 게 기본인데,
PUT, PATCH, DELETE Method 로도 받게 해준다.
```
이정도로 해석했다. 
사실 Betty 에서는 form 태그를 전부 POST 형태로 정의 했기에 쓸모 없는 것 같다.  

HiddenHttpMethodFilter 는 약간의 꼼수로 서버 내에서 POST 를   
Put 과 같은 Method 로 변형하여 Mapping 시키는 걸로 안다.   
이또한 사실 필요 없었다.  

근데 사실 FormContent 와 HiddenHttpMethod 는 함께 사용되어야 하는 게 아닐까 싶다.  
<br/>

## - customizeRegistration

throwExceptionIfNoHandlerFound 를 true 로 설정했다.  
단순히 핸들러를 찾지 못했을 때 Exception 을 발생시키겠다는 것.  

좀더 파고들면, 요청 경로에 대한 Mapping 메소드가 존재하지 않는다면,  
예외를 발생시켜, 직접 제작한 404 Page 를 띄워주기 위한 설정이다.

그냥 xml 을 사용했다면, 간단히 `<error-code>` 를 사용하면 됐을 일이긴 하다.  

<br/>
<hr/>
<br/>

<!-- DB Config -->
# DBConfig
<br/>

![Desktop View](/assets/img/betty/config/db.png)

## - HikariCP

데이터소스와 마이바티스 관련 설정도 고비가 많았다.  
HiKariCP 를 사용했는데, 단순히 url, name, pw 등 익숙한 것만 하려니 안 됐었다.  
아마 내가 못한 거겠지만, Hikari github 에 가서 찾아보니  
MySQL 추천 설정이 나타나 있어 그걸 그대로 썼다.  

일단 모든 설정을 properties 로 바꾸려니 익숙치 않고 어려웠는데,  
key 가 지칭하는 게 field 의 식별자인 것을 알고 나선 그리 어렵지 않았다.  
<br/>

## - SqlSessionFactory

다음은 마이바티스를 위한 세션 팩토리이다.  
이건 딱히 설정이랄 건 없다.  
공식 사이트에서 보니 기본 설정이 굉장히 깔끔하게 되어있었고,  
under_score 를 underScore 로 바꿔 매핑하도록 하는 설정만 지정해줬다.  

이 설정은 DB 에서 흔히 사용하는 전통 언더스코어 명칭 규약을  
자바의 변수 명에 사용되는 카멜 케이스와 매핑시키기 위해 존재한다.  
<br/>

## - DatabaseConfiguration
<br/>

![Desktop View](/assets/img/betty/config/dbconfig.png)

클래스 어노테이션으로 @DatabaseConfiguration 이 있는데,  
세션 팩토리 내부에 마이바티스 Configuration 객체를 생성할 때  
Configuration 이 겹치기에 어노테이션을 차라리 새로 만들기로 하였다.  
구성은 단순하다. 상속 느낌으로 이해하면 된다.  
<br/>

## - TransactionManager
<br/>

![Desktop View](/assets/img/betty/config/dbtrmanager.png)

트랜잭션 매니저로는 공식 문서에 DataSourceTransactionManager 를 권장했다.  
그래서 사용했다. 깊은 뜻이 있지 않을까?  
<br/>

## - MapperScan

Servlet 의 ComponentScan 도 그렇고 스캔 경로는 최대한 간결하길 바랬다.  
처음엔 root 경로부터 돌렸으나, 불필요하게 많은 것들을 스캔하기에 좀더 상세하게 바꿨다.  

<br/>
<hr/>
<br/>

<!-- Root Config -->
# RootConfig
<br/>

![Desktop View](/assets/img/betty/config/root.png)

## - Import

열심히 설정한 데이터베이스 설정은 RootConfig 에 포함시켰다.  
이상하게 import 는 한 클래스 밖에 안됐다.  
하나라도 되는 게 어디인가 싶다.  
<br/>

## - EnableTransactionManagement

@Transactional 을 인식하고 트랜잭션 매니저를 사용하여  
커넥션을 관리하도록 만드는 걸 허용하는 어노테이션으로 알고 있다.  
<br/>

## - PropertySource

properties 파일을 해당 클래스에서 인식시킬 수 있다.  
@Value 와 EL 을 사용하여 값을 가져올 수 있다.  
<br/>

## - MessageSource

ReloadableResourceBundleMessageSource 에 대해선 정확히 뭔진 모르겠다.  
공식 문서에서도 이 객체를 사용하기에 나도 썼다.  
메시지, 국제화에 사용하는 properties 를 이 객체가 가져온다.  
그리고 사용한다.  
<br/>

## - CommonsMultipartResolver

파일업로드에 사용하는 객체이다.  
좀더 나아가면 form encoding type 이 multipart 일 때  
이 객체가 번역에 힘쓰는 걸로 알고 있다.  

그것보단 Servlet 3.0 부터 지원하는 Standard~~resolver 도 있는데  
검색해보니 인코딩 관련 문제를 마주했었고,   
수업 때 Commons 를 사용하기도 해서 Commons 를 적용했다.
<br/>

## - PropertySourcesPlaceholderConfigurer

PropertySource 가 아마 테스트 환경에선 동작하지 않았을 거다.  
이 객체를 static 으로 등록하고 나서부턴 테스트 환경에서도 properties 를  
정상적으로 읽어왔다.   
<br/>

## - DefaultMessageService 

문자 인증 관련 객체   

<br/>
<hr/>
<br/>

<!-- App Config 1 -->
# AppConfig - 1  
<br/>

![Desktop View](/assets/img/betty/config/app1.png)

AppConfig 는 단 하나 뿐인 소중한 Servlet 이다.  
수동 Bean 과 각종 커스텀 기능이 추가되어 있어 두 사진으로 나눴다.  
<br/>

## - Enable

@EnableScheduling : Batch 기능 이용   
@EnableAspectJAutoProxy : AOP 기능 이용  
@EnableWebMvc : 아마 스프링 프레임워크의 Web Servlet api 를   
이용할 수 있게 해주는 어노테이션   
<br/>

## - ComponentScan

베이스 패키지를 root 부터 잡는데,  
@Configuration 은 다시 읽지 않도록 설정   
<br/>

## - addArgumentResolver
<br/>

![Desktop View](/assets/img/betty/config/argsr.png)

어디서 설명할까 했는데 간단하게 여기서 설명해야겠다.  
어노테이션을 정의하고 해당 어노테이션이 선언된 인자의 데이터를 처리할 때,   
Member 클래스가 맞는지 확인한 뒤 세션에 있는 Member 객체를 주입 시키는 객체다.  
<br/>

![Desktop View](/assets/img/betty/config/args.png)

내가 정의한 어노테이션은 @User 이고,  
위와 같이 매개변수 앞에 @User 어노테이션을 선언하면,  
현재 세션에 담긴 Member 객체를 꺼내온다.  

참고로 Betty 에선 로그인 회원을 "user" 라는 key 값으로 세션에 담아둔다.  
<br/>

## - interceptor

인터셉터는 3가지가 있다.  

LocaleChangeInterceptor : Locale 쿠키 기반으로 클라이언트 Locale 조작   
CookieInterceptor : 쿠키를 통해 로그인 정보 저장  
AuthInterceptor : 인증 - 인가 관련하여 페이지 접근 제어  

쿠키 인터셉터가 좀 특이한데, 생성자에 memberService 를 받는다.  
인터셉터 등록은 bean 등록이 아니기 때문에,  
내부에서 memberService 를 @AutoWired 로 주입받을 수 없어   
하는 수 없이 생성자로 받게 되었다.  

<br/>
<hr/>
<br/>

<!-- App Config 2 -->
# AppConfig - 2
<br/>

![Desktop View](/assets/img/betty/config/app2.png)

## - addResourceHandlers

정적 자원에 대한 경로와 uri 설정이다.  
핵심은 cacheControl 이다.  
<br/>

## - CacheControl
**_잘 모르지만 적어보겠습니다. 정말 잘 모릅니다.._**  
Betty 프로젝트는 도서 사진이 굉장히 많다.  
이 사진을 매 요청시마다 전달하는 건 굉장히 큰 부담이라 생각했고  
방법을 찾아야 했었는데..   
(근데 아마 브라우저 자체적으로 알아서 캐싱을 하는 걸로 알고 있긴 하다.)    
바로 클라이언트의 캐시 저장소를 이용하는 것이다.  

공식 문서를 보다가 주워온 거라 우선 365일로 지정하긴 했는데  
실제 서비스라면 상당히 무책임한 행동이라 생각되긴 하다..  

내가 알기로 원리는 이렇다.  

1. 최초 페이지 요청 시 날아오는 Resource 를 브라우저 캐시 저장소에 저장한다.  
2. 이후 동일한 Resource 를 요청할 상황이 나타났을 떄,  
캐시 저장소에 Resource 가 있다면   
HTTP 프로토콜에 이미지 이름과 수정 날짜 등의 정보를 이용하여   
새로 받을 필요가 없다면 안 줘도 된다는 메시지를 Http Header 에 함께 보내는 것.  

하드웨어가 발달하고 더불어 메모리도 발달한 현대에는  
클라이언트에게 부담을 떠넘기는 게 마냥 나쁘지도 않은 것 같긴 하다.  
<br/>

## - configureViewResolvers

ViewResolver 객체야 일반 Controller Handler 반환 페이지를 결정할 때 필요하다만  
JstlView 의 역할이 너무 궁금하다.  
우선 정보가 별로 없고, 주석을 읽어보면 messageSource 와 관련됐기도 하던데   
너무 궁금하지만 다음에 다시 사용할 때에 찾아보려 한다.  
<br/>

## - LocaleResolver, LocaleChangeInterceptor

전자는 **"lang"** 쿠키를 통해 쿠키 기반으로 클라이언트 국가를 결정하는 객체이고,  
후자는 **"lang"** key 값으로 파라미터를 받아 **"lang"** 쿠키를 응답하는 객체로 알고 있다.  
둘다 국제화 관련된 객체이다.  

<br/>
<hr/>
<br/>

<!-- Email COnfig -->
# EmailConfig
<br/>

![Desktop View](/assets/img/betty/config/email.png)

properties 를 적극 활용한 이메일 설정이다.  

<hr/>



