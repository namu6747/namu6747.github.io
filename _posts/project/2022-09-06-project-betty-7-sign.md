---
title: 7. 계정 관련 - Betty
author: namu6747
date: 2022-09-06 09:07:00 +0900
categories: [프로젝트, Betty]
tags: [스프링, 자바, 포트폴리오]
---

## &nbsp;&nbsp;&nbsp; index
0. [서론](/posts/project-betty-0/)
1. [기획 및 명세](/posts/project-betty-1-concept/)
2. [패키지 트리](/posts/project-betty-2-package-tree/)
3. [프로젝트 환경](/posts/project-betty-3-config/)
4. [메시지와 국제화](/posts/project-betty-4-message/)
5. [예외 다루기](/posts/project-betty-5-exception/)
6. [검증](/posts/project-betty-6-validation/)
7. [계정 관련](/posts/project-betty-7-sign/)
8. [권한 인터셉터](/posts/project-betty-8-interceptor/)
9. [도서 관련](/posts/project-betty-9-book/)
10. [대여 관련](/posts/project-betty-10-rental/)
11. [오프라인 관련](/posts/project-betty-11-offline/)
12. [about ajax](/posts/project-betty-12-ajax/)
13. [팀원 교육 - ajax](/posts/project-betty-13-edu-ajax/)
14. [DTO, Form, VO, Entity ?](/posts/project-betty-14-object/)
15. [후기](/posts/project-betty-15-review/)

<br/>
<hr/>
<br/>

내 딴에 심혈을 기울인 로직이라고 하면 계정과 그 권한에 대한 내용이다.  
나름 최선을 다해 세심하게 만들었지만, 이것 밖에 안되나 싶을 수도 있다.  
아쉬운 점은 ajax 를 적용하지 못했다.  

<br/>
<hr/>

## 로그인 화면
<br/>

<!-- view  -->
![Desktop View](/assets/img/betty/sign/signinview.png)

Betty 로그인 화면이다.  
로그인 시 쿠키 저장 체크박스를 만들어놓았다.  

<hr/>

## form object
<br/>

<!-- sign in form -->
![Desktop View](/assets/img/betty/sign/signinform.png)

로그인에서도 검증용 폼 객체를 이용했다.  
아주 심플하다.  

컨트롤러에서 바로 패스워드 암호화를 위해 폼 객체에 encode 메소드를 정의했다.  

객체가 생성되자마자 encode 됐으면 해서 클래스 내부에  
인스턴스 블록 스코프를 놔두고 encode 를 바로 수행해봤는데  
전체 생성자를 통해 생성되지 않아 실패했다.  

결국 컨트롤러에서 바로 호출시키는 걸로 만족했다.  

dto 의 일종인 form 객체를 바로 Member Entity 로 변환 시키는 메소드도 있다.  
변환은 Service 계층에서 이루어진다.  

<br/>
<hr/>


## 컨트롤러
<br/>

<!-- sign in -->
![Desktop View](/assets/img/betty/sign/signin.png)

1. 검증 에러가 있을 시 로그인 화면으로 리다이렉트
2. form 객체 패스워드 인코딩
3. ID & PW 로 Select 후 Member 반환
4. 반한된 Member 객체가 null 일 때 로그인 화면으로 리다이렉트
5. null 이 아니라면 세션에 저장(상수 사용)
6. 폼 객체의 로그인 쿠키가 체크되었다면 쿠키에 저장(SignHelper)
7. 권한이 필요한 페이지에 접근했다가 거부되어 로그인 화면으로 왔을 시   
 해당 URL 로 리다이렉트
8. 아무 조건 없이 정상 수행 시 Home 화면으로 리다이렉트

쿠키에 아이디를 저장할 때도 인코딩을 적용했다.  
다른 방법도 있겠지만 당장 간편하게 할 방법이 떠오르지 않았다.  
쿠키에 아이디를 평문으로 줬을 땐 너무 쉽게 악용할 수 있어서 
차마 인코딩을 하지 않을 수 없었다.  

<br/>
<hr/>

## Sign Helper
<br/>

<!-- sign helper -->
![Desktop View](/assets/img/betty/sign/signhelper.png)

난 static method 를 자주 쓴다.  
이것은 그저 나만 편한 방법이긴 하다.  
공통으로 사용될만한 로직이나 거슬리는 로직을 빼버린다.  
나름의 기준이 있는데, 불필요하게 많은 인자를 받게 될 것 같으면 안 한다.  
그거 적는 게 더 귀찮다.  

<br/>
<hr/>

## Base64Util
<br/>

<!-- base64 -->
![Desktop View](/assets/img/betty/sign/base64.png)

Base64 암호화 클래스다.  
이상하게 String 을 넣고 String 을 반환하는 게 없었다.  
매번 바이트 코드를 만들고 빼고 다시 넣고 하기 귀찮았다.  

~~바이트코드로 하면 될 것 같기도 한데~~,  
인코딩은 파면 팔수록 복잡한 영역이다.  
사실 패스워드를 반환할 일은 대개 없으니,  
String 을 DB 에 넣는 건 잘못된 선택이었을까?  

이 부분은 나중에 다시 생각해볼 필요가 있겠다.  

<br/>
<hr/>

## Cookie Interceptor
<br/>

<!-- cookie interceptor -->
![Desktop View](/assets/img/betty/sign/ci.png)

로그인 쿠키가 존재할 때 세션에 Member 객체를 넣는 인터셉터다.  

로그인 시 쿠키 저장에 체크한 회원이 성공적으로 로그인 했을 때,  
아이디 정보가 담긴 쿠키를 Response Header 를 통해 전달 받았을 텐데  
클라이언트가 브라우저를 종료한 이후, 이 쿠키를 통해 다시 로그인 시키는 과정이다.

클라이언트는 암호화된 자신의 아이디가 저장된 쿠키를 받는다.  
당연히 서버에선 해당 쿠키의 값을 디코딩하여 DB 에서 꺼내온다.  

<hr/>
