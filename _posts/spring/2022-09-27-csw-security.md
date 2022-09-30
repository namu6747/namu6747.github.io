---
title: TEMP 스프링 시큐리티 - 코.스.웹
author: namu6747
date: 2021-09-27 09:01:00 +0900
categories: [공부, 스프링]
tags: [자바, 스프링, 웹]
---

## 코드로 배우는 스프링 웹 프로젝트 - 시큐리티편

<br/>

**"권한이나 등급에 기반을 두는 요청"**  

Spring Web Security 를 이용하여  
인터셉터 등을 대신해서 위 요청을 처리한다.  

ex)  
- 로그인 처리와 CSRF 토큰 처리
- 암호화 처리
- 자동 로그인
- JSP 에서의 로그인 처리

스프링 시큐리티의 기본 동작 방식은  
서블릿의 여러 종류의 필터와 인터셉터를 이용해서 처리된다.  

하나의 스프링 MVC 프로젝트에 스프링 시큐리티가 적용된다면,  
다음과 같은 구조가 생성된다.  

```
ServletContext{
    filter
    
    SpringContext{
        Interceptor
        WebApplicationContext
        RootContext
        SecurityContext{
            AuthenticationManager
            AccessDecisionManager
        }
    }
}
```

필터의 경우 스프링 컨텍스트와 실질적으로 무관하고,  
인터셉터의 경우 스프링의 내부에서 컨트롤러를 호출할 때 관여하기 때문에,  
스프링 컨텐스트 내에 있는 모든 자원을 활용할 수 있다.  

