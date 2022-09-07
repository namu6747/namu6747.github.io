---
title: 2. 패키지 트리 - Betty
author: namu6747
date: 2022-09-06 09:02:00 +0900
categories: [프로젝트, Betty]
tags: [스프링, 자바, 포트폴리오]
---

<hr/>
<br/>

패키지 트리를 어떻게 분류할 지는 다양한 방법이 있을 것이다.

```
1. 
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

각 도메인 영역에서 Controller, Service 등 컴포넌트를 분리
공통 영역을 common 패키지에 몰아넣기.
```

```
2.
+ root
    + controller
        - MemberController
        - BookController
    + service
        - MemberService . . .
    + repository
        - MemberRepo . . .

기능 별 컴포넌트를 분리 후 도메인 구분 없이 사용하기.
규모가 매우 작을 때 사용할법한 방법이다.
```

<br/>
<br/>

**내가 채택한 방법**

```
3.
+ root
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

나는 도메인, 글로벌, 인프라로 분류한 뒤 컴포넌트를 분리하는 방법을 선택했다.   
1번에 가까운 형상에 인프라 영역을 추가하고 설정, 인터셉터, 공통 예외 처리등은    
간략히 글로벌에 몰아넣었다.
```

<hr/>


<!-- 전체 패키지 트리 -->

![Desktop View](/assets/img/betty/package/package-all.png)

<hr/>



<!-- 멤버 패키지 -->

![Desktop View](/assets/img/betty/package/package-member.png)

<hr/>



<!-- 상수 패키지 -->

![Desktop View](/assets/img/betty/package/package-model.png)

<hr/>



<!-- 글로벌 패키지 -->

![Desktop View](/assets/img/betty/package/package-global.png)

<hr/>



<!-- 인프라 패키지 -->

![Desktop View](/assets/img/betty/package/package-infra.png)

<hr/>



<!-- 클래스패스 -->

![Desktop View](/assets/img/betty/package/package-classpath.png)

<hr/>
