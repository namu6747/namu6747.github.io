---
title: 2. 패키지 트리 - Betty
author: namu6747
date: 2022-09-06 09:02:00 +0900
categories: [프로젝트, Betty]
tags: [스프링, 자바, 포트폴리오]
---

<hr/>

### 패키지 트리를 어떻게 분류할 지는 다양한 방법이 있을 것이다.

```java
1. 도메인을 기준으로 분리

+ root
    + member
        + controller
            - MemberController
            - SignController
        + service
            - MemberService
            - SignService
        + repository
            - MemberRepo . . .
    + common
        + controller
            - HomeController
        + interceptor
            - LocaleInterceptor . . .
        + util
            - FileUpload . . .

```

```java
2. 컴포넌트를 기준으로 분리

+ root
    + controller
        - MemberController
        - BookController
    + service
        - MemberService . . .
    + repository
        - MemberRepo . . .

```

<br/>
<br/>

**내가 채택한 방법**

```java
3. 도메인, 글로벌, 인프라 분리 후 도메인 기준으로 분리

+ root
    + domain
        + member
            + controller
            + service
            . . .
    + global
        + config
        + error
        + interceptor
        . . .
    + infra
        + email
        + sms

```

<hr/>


<!-- 전체 패키지 트리 -->

# 전체 패키지 트리

![Desktop View](/assets/img/betty/package/package-all.png)

[ domain, global, infra ] 세 영역으로 분리했다.  

**domain**  
아직 도메인을 어떤 기준으로 분리해야될 지는 감이 없다.  
다른 건 객체가 명확히 존재한다고 인정할 수 있지만  
rental 의 경우 일단 분리는 했으니 로직을 어느 영역에서 처리할 지 애매했다.   
rental table 의 경우 명확히 존재하므로  
entity, repository 의 정의는 필요하다 싶었고  
service 의 경우 다른 영역에서 참조하여 사용하였다.

**global**  
global 패키지엔 @Configuration 클래스와  
인터셉터, 홈 컨트롤러, 공통 예외인 error 패키지가 존재한다.   
각 도메인에만 해당하는 클래스들도 있지만   
비즈니스 예외를 정의하다보니 분류가 애매해져 한 데 모아버렸다.  

**infra**  
email 과 sms 를 뒀다.  
정확히는 송신 객체이다.  
Composite 형태로, 내부에서 객체를 주입받아 사용한다.  
(JavaMailSender, DefaultMessageService)   
정말 __"api"__ 느낌이 진할 때 infra 영역에 넣으면 될 것 같다.

<hr/>



<!-- 멤버 패키지 -->

# domain

![Desktop View](/assets/img/betty/package/package-member.png)

비즈니스 로직에 관여할 상황이 그리 많지 않았다.  
DB 를 담당했던 팀원이 대부분의 로직을 DB 에서 처리해줬다.  
아마 트리거로는 어디가서 무시받지 않을 것 같다.  

난 그나마 좀 중요하다 싶은 계정 관련 작업을 나서서 했다.   
물론 이외에도 내 손길이 간 부분은 많다.   

domain 영역은 내 애정이 담긴 member 패키지로 살펴보자.  

각 컴포넌트 또는 기능 별로 분리했다.  
controller, dao(repository), dto, provider, service, util, vo   

보면 member 내부에 Sign 도 있고 다른 권한인 staff, admin 도 존재한다.   
규모가 작아 이 구조에 큰 문제는 없었는데  
정답은 없겠지만 더 나은 방향이 무엇일까에 대한 의견을 얻고 싶다.   

사실 단순해서 달리 설명할 부분이 없는 것 같다.  

<hr/>



<!-- 상수 패키지 -->

# model

![Desktop View](/assets/img/betty/package/package-model.png)

이게 우리가 원하는 model 이 맞는 진 정말 모른다.  
domain 영역에서만 공통적으로 사용하는 '무언가' 를 놔뒀다.  

테이블 명, 세션 이름, 쿠키 이름 등등 다양한 상수의 집합이다.  
협업할 때 일관성, 통일성에 집착했는데, 상수의 존재는 내게 몹시 중요했다.

예를 들어, 우린 마이바티스를 사용했고, 이왕 할 거라면   
모든 쿼리문을 provider 로 사용하자고 제시했다.  
이 부분에서 상수의 존재는 큰힘을 발휘한다.  

![Desktop View](/assets/img/betty/package/package-model-provider.png)

이런식으로 정적 import 와 활용한다면,  
적어도 테이블 명이 틀려서 나타나는 trace 는 절대 마주칠 수 없을 것이다.

뿐만 아니라 세션과 쿠키의 key 역시 마찬가지다.   

<hr/>



<!-- 글로벌 패키지 -->

# global

![Desktop View](/assets/img/betty/package/package-global.png)

앞서 얘기했듯, 설정 정보와 다양한 공통 영역이다.  
이 또한 작은 규모라서 가능했을 것이고,  
어서 영역에 맞게 잘 분리된 프로젝트를 보거나, 직접 참여해보고 싶다.   

<hr/>