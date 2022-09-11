---
title: 4. 메시지와 국제화 - Betty
author: namu6747
date: 2022-09-06 09:04:00 +0900
categories: [프로젝트, Betty]
tags: [스프링, 자바, 포트폴리오]
---

<hr/>
<br/>

국제화는 이번 프로젝트에서 꽤 귀여운 기능 아닐까?  

메시지와 국제화에 대하여 알아보자.

틀린 내용이 있을 수 있지만, 내 머릿속에선 거의 맞다고 생각되는 내용들이다.  

<br/>
<hr/>

# 메시지, 국제화를 위한 준비

## properties
<br/>

<!-- message -->
![Desktop View](/assets/img/betty/internal/message.png)

내가 원하는 code 에 대한 keyword 를 key = value 형식으로 나열한다.  
**"en"**, **"ko"** 와 같은 국가 코드를 적지 않는다면,  
Default message 로 인식 된다.  

예를 들어, Locale 이 **"en"** 으로 되어 있다면 **"en"** 의 keyword 를 사용하지만  
**"en"** 이 아니라면, 아무 국가 코드도 없는 **messages.properties** 가 적용 된다.  

즉, 원하는 국가의 keyword 를 손수 작성해줘야 된다.  
적어도 스프링 레거시 프로젝트에서는 이렇게 사용했다.  

<br/>
<hr/>

## messageSource
<br/>

<!-- messageSource-->
![Desktop View](/assets/img/betty/internal/messagesource.png)

메시지와 국제화 properties 파일 작성이 완료되었다면,  
이를 인식시켜주기 위해 MessageSource 객체로 읽어줘야 된다.  
사진처럼 간단히 Basename 을 파일 이름까지 적어주면 된다.  

![Desktop View](/assets/img/betty/internal/reload.png)

ReloadableResourceBundleMessageSource 에 대한 설명이다.  
딱히 모르겠다.  
"WEB-INF" 같이,  classpath 이외의 경로에 있는 파일도 읽어주는 것 같다.    
스프링을 사용한다면, 그냥 권장하는 걸 쓰면 된다고 생각한다.  

<br/>
<hr/>

## message tag
<br/>

<!-- message tag spec -->
![Desktop View](/assets/img/betty/internal/messagetag.png)

레거시 프로젝트에서 국제화를 적용하려면,  
message 태그라는 것을 사용해야 한다.  

**_이게 무슨 말이냐면~_**

```jsp
<%@ taglib prefix="s" uri="http://www.springframework.org/tags" %>
```

jsp 파일 상단에 이렇게 태그 라이브러리를 추가하고,  

<!-- message tag -->
![Desktop View](/assets/img/betty/internal/springmessage.png)

아까 정리했던 properties 파일의 코드들을   
위와 같이 적재적소에 기입해주는 것이다.  

아마 눈치 빠르신 분들은 알겠지만   
화면 상에 보이는 모든 텍스트들은 이미지 파일에 합쳐진 게 아니여야 된다.  

하나라도 다른 언어가 있다면, **안하니만 못하는 화면을 보게 된다.**  

<br/>
<hr/>

## LocaleChangeInterceptor

<!-- localeChangeInterceptor -->
![Desktop View](/assets/img/betty/internal/lci.png)

이 인터셉터는 빈을 등록할 때 설정한 paramName 으로,  
요청 시 "lang" 파라미터를 확인하고 LocaleResolver 를 통해  
클라이언트 Locale 을 지정한다.  

<hr/>

## CookieLocaleResolver

<!-- CookieLocaleResolver -->
![Desktop View](/assets/img/betty/internal/clr.png)

내부에 선언된 메소드 중 몇 가지이다.  
쿠키를 통한 클라이언트 Locale 지정을 담당한다.  

<hr/>




# 버튼 하나로 국제화 하기

<!-- ENG Click -->
![Desktop View](/assets/img/betty/internal/btn.png)

![Desktop View](/assets/img/betty/internal/after.png)

ENG 버튼을 누르는 상황을 연출했다.  


**간단 요약**
1. ENG 버튼을 클릭
2. ajax 를 사용하여 root 경로로 쿼리파라미터 "lang=en" 을 함께 요청
3. 인터셉터가 인지 후 "en" 쿠키 반환
4. 페이지를 reload 하면 새 요청시 CookielocaleResolver 를 통해 en 으로 인식
5. message 태그의 value 를 "en" 에 해당하는 값으로 렌더링

<br/>
<hr/>

## 자바스크립트

<!-- javascript -->
![Desktop View](/assets/img/betty/internal/js.png)

KOR 또는 ENG 버튼을 눌렀을 때  
ajax 로 root 경로에 쿼리 파라미터를 함께 요청한다.  
바로 현재 location 으로 reload 하여  
국제화가 적용된 화면을 렌더링 시킨다.  

<br/>
<hr/>

## Response Header
<br/>

<!-- http -->
![Desktop View](/assets/img/betty/internal/http.png)

ENG 를 눌렀을 때의 Response Header  
`new Cookie("lang","en")` 가 응답된다.  
위 ajax 요청에 대한 응답이다.  

<br/>
<hr/>




