---
title: 5. 예외 다루기 - Betty
author: namu6747
date: 2022-09-06 09:05:00 +0900
categories: [프로젝트, Betty]
tags: [스프링, 자바, 포트폴리오]
---

## &nbsp;&nbsp;&nbsp; index
1. [서론](/posts/project-betty-0/)
2. [기획 및 명세](/posts/project-betty-1-concept/)
3. [패키지 트리](/posts/project-betty-2-package-tree/)
4. [프로젝트 환경](/posts/project-betty-3-config/)
5. [메시지와 국제화](/posts/project-betty-4-message/)
6. [예외 다루기](/posts/project-betty-5-exception/)
7. [검증](/posts/project-betty-6-validation/)
8. [계정 관련](/posts/project-betty-7-sign/)
9. [권한 인터셉터](/posts/project-betty-8-interceptor/)
10. [도서 관련](/posts/project-betty-9-book/)
11. [대여 관련](/posts/project-betty-10-rental/)
12. [오프라인 관련](/posts/project-betty-11-offline/)
13. [about ajax](/posts/project-betty-12-ajax/)
14. [팀원 교육 - ajax](/posts/project-betty-13-edu-ajax/)
15. [DTO, Form, VO, Entity ?](/posts/project-betty-14-object/)
16. [후기](/posts/project-betty-15-review/)

<hr/>
<br/>

내가 꿈꿔왔던 **공통 예외 처리부**이다.  
하지만 !.. 예외를 아름답게 처리해두기엔 시간이 부족했다.  
프로젝트 기간 동안 ajax 를 뼈저리게 익히는 시간조차 부족했기 때문이다.  
아무튼 적용했다 치고 적어볼 것이다.  

<br/>
<hr/>

## package

<!-- package -->
![Desktop View](/assets/img/betty/exception/package.png)

공통 영역인 global 패키지 하위에 있는 error 패키지다.  
RequestMappingHandler 상위로 예외가 전파되는 타이밍에 ControllerAdvice 가 낚아채어 작업한다.  

일반 컨트롤러에선 ModelAndView 객체를 반환하면 되고,  
Rest 컨트롤러에선 응답용 객체나 ResponseEntity 등을 반환하면 된다.  
**물론 예외에 대한 Handling 이므로 신중해야 한다.**  

이 패키지엔 직접 정의한 비즈니스 예외, 컨트롤러 어드바이스, 응답용 객체 모두 존재한다.  

컨트롤러 어드바이스는 특정 컨트롤러만 담당시킬 수 있으므로  
글로벌하게 적용되는 게 아니라면 해당 도메인에 위치하는 게 맞을 것 같다.  
그러므로 비즈니스 예외 또한 이곳에 몽땅 있으면 안 된다.  

하지만 이렇게 놔두고 리뷰하자.  


<hr/>

## GlobalControllerAdvice

<!-- GlobalControllerAdvice -->
![Desktop View](/assets/img/betty/exception/gca.png)

가장 간단하게 직접 적용해봤던 컨트롤러 어드바이스다.  

엉뚱한 경로를 요청했을 때 아래 페이지를 반환하도록 했다.  

![Desktop View](/assets/img/betty/exception/404.png)

<br/>
<hr/>

## ErrorResult 

<!-- error result object-->
![Desktop View](/assets/img/betty/exception/er.png)

api 예외에서 에러를 응답할 객체이다.  
웬만하면 ResponseEntity 에 반환하는 편이 나은 것 같다.  
`new ResponseEntity<ErrorResult>(errorResult, HttpStatus.XXX)`
ResponseEntity 는 Http status code 를 간편히 함께 전달할 수 있기 때문이다.  

상태 코드를 적는 이유는  
내가 알기로 _"서버에서 예외가 발생하더라도 클라이언트에서는 알 수 없다"_ 라는 이유다.  
(절대 정확하지 않다.)  
(아마 DefaultHandlerExcpetionResolver 가 어느정도 처리해줄 것 같다.)

서버의 예외를 인지하고 프론트 단에서 처리해야되는 거야 많겠지만,  
당장 나는 ajax 를 위해 이를 공부했다.  
ajax 에서 error 로 인식시키려면 서버에서 예외 처리 후 상태 코드를 함께 던져야 했다.  

이 객체는 코드와 메시지를 담는 게 주 역할이다.  
코드는 "B001" 과 같이 약속된 코드이고,  
메시지는 사용자에게 출력하게 될 내용이다.  


<br/>
<hr/>

## 커스텀 예외에 대한 생각
<br/>

**_비즈니스 관련 예외를 직접 정의해야될까_**에 대한 고민을 많이 했다.  
(물론 적용은 못했지만, 추후 사이드 프로젝트에는 반드시 적용할 것이다.)  

그리고 직접 정의하는 편이 낫다고 생각했다.  
왜 그렇게 생각했냐면,  

1. 사전에 제공되는 Exception 종류가 상당하다. 이걸 언제 다 보고 있을까
2. 사전에 제공되는 Exception 을 비즈니스 예외로 어떻게 적절히 끼워넣나
3. 일관성 있는 예외 처리를 바랬으나 커스텀하지 않으면 다른 의도의 예외까지 처리하지 않을까

이 정도다.
나는 비즈니스 예외를 최대한 직접 정의하는 게 나은 것 같다.  


<br/>
<hr/>

## ExceptionHandler 에 대한 생각
<br/>

처음 ExceptionHandler 를 사용했을 때  
HandlerExceptionResolver 를 상속하여 만드는 예외 핸들러와 굉장히 헷갈렸다.  

잘 생각해보면 ExceptionHandler, ResponseStatusHandler,  
DefaultExceptionHandler 는 이미 존재하는 객체들이다.  
얘네가 HandlerExceptionResolver 이므로 내가 직접 커스텀할 필요가 없었다.  

당연해보이지만 HandlerExceptionResolver 를 상속하여 
Servlet에 추가하여 사용한다 생각했을때,  
커스텀과 기본 제공 Handler의 범주가 같다는 걸 이해해야   
내게 필요없다는 걸 이해할 수 있다.

뇌에 힘 빼고 @ExceptionHandler 와 @ControllerAdvice 만  
적절히 잘 사용하면 된다.  