---
title: HTTP 상태 코드 살펴보기
author: namu6747
date: 2022-09-07 09:01:00 +0900
categories: [공부, HTTP]
tags: [HTTP, 상태코드, http status code]
---

학원 최종 프로젝트였던 project-Betty 가 끝났다.   
시간에 쫒겨 공통 예외 처리를 구현하지 못했었는데   
결과적으로 상태 코드에 대한 학습이 우선이라 생각됐다.

<br/>

정리하려는 이유는 아래와 같다.

<br/>

**클라이언트 측의 예외 인지**
```
서버에서 Exception 이 발생하면 ajax 문 내부 error 에 정의된 콜백 함수가 실행되길 바랬다.

요청에 대한 정상적인 응답이 아님에도 200(OK) 코드로 전송되어
success 내부에서 약속에 의한 코드 또는 메시지로 분기하는 건, 
ajax 를 정의한 개발자에 대한 예의가 아니라 느꼈다.
(애초에 전 지구적 약속인 Http 상태 코드가 존재한다.)

확실하진 않으나 서버에서 발생한 Exception 에 상태코드를 따로 붙여주지 않는다면
톰캣이 멋대로 200 코드로 넘긴다고 알고 있다.

클라이언트 - 서버 구조에서 좀더 우아하게 Http 프로토콜을 다루려면
상태코드에 대한 학습이 필요하다고 생각한다.
```

<br/>

아래의 코드는 __"org.springframework.http"__ 패키지의 __HttpStatus__ 클래스에 정의된 목록 중 일부이다.

<br/>
<hr/>
<br/>

## &nbsp;&nbsp; 3xx 리다이렉트

<br/>

- MOVED_PERMANENTLY(301, "Moved Permanently")

- FOUND(302, "Found")

- TEMPORARY_REDIRECT(307, "Temporary Redirect")

- PERMANENT_REDIRECT(308, "Permanent Redirect")

<br/>

&nbsp;&nbsp; **정리**
```
요청 경로와 다른 URL로의 이동에 대해 몇 가지 생각나는 게 있다.
1. 요청한 페이지가 영구적으로 사라졌다.
2. 요청한 페이지가 단기간 다른 페이지로 전환됐다.
3. 권한 불충분으로 인해 접근이 거부되었다.
4. Rest 방식에서의 URL 은 대게 리소스의 고유 값이 들어가는 데, 이 때의 비정상 접근

1,2 번은 정상적인 리다이렉션이지만
3,4 번은 비정상 접근에 대한 리다이렉션이므로 4xx 에러에서 다루는 게 맞는 듯하다.

[301], [308] : 영구적인 페이지 이동은 301, 308 로 해결할 수 있을 것 같다.
하지만 내 눈엔 동일해보여 둘 중 누구를 선택할 지는 모르겠다.
물론 3xx 코드를 사용하는 경우, Exception 과 연관지을 생각은 없다.

[302] :  location.href 등 단순 링크이동, 흔한 response.sendRedirect 의 결과이다.
직접 사용할 일은 없어보인다.

[307] : 이벤트 또는 점검 중에 활용할 수 있을 것 같다.

전체적으로 
Http Header 를 Exception 관련 없이 다룰테니 ResponseEntity 를 사용하는 방법이 떠오른다.

```


<br/>
<br/>

## &nbsp;&nbsp; 4xx 클라이언트 에러

<br/>

- BAD_REQUEST(400, "Bad Request")

- UNAUTHORIZED(401, "Unauthorized")

- FORBIDDEN(403, "Forbidden")

- NOT_FOUND(404, "Not Found")

- METHOD_NOT_ALLOWED(405, "Method Not Allowed")

- NOT_ACCEPTABLE(406, "Not Acceptable")

- REQUEST_TIMEOUT(408, "Request Timeout")

- UNSUPPORTED_MEDIA_TYPE(415, "Unsupported Media Type")

- I_AM_A_TEAPOT(418, "I'm a teapot")

- UNAVAILABLE_FOR_LEGAL_REASONS(451, "Unavailable For Legal Reasons")

<br/>

&nbsp;&nbsp; **정리**
```
클라이언트 에러를 정리해보기 전에, 우선 서버 에러 코드(5xx)를 발생시키지 않는 게 
나의 명줄과 직결된 사항이라 생각한다. 
가능한 예외는 모두 클라이언트의 몫이어야 된다.
그리하여 4xx 에러는 상태 코드를 정리하려는 목적 중 핵심이다.

[400] : 잘못된 요청이라하면 어떤 게 있을까? 
400번이 4xx 의 부모격이고 나머지는 400을 상속한 코드들로 보인다.

[401] : 비인가. 권한 불충분일 때, 아마 잘 막아놨다면 이는 악의적인 접근일 확률이 높다.

[403] : 금지됐다하면.. 사실 비슷비슷해보이지만 계정 권환과 상관없이 URL을 조작하여
UI상 나타나지 않은 이벤트(?)를 발생시키려할 때 사용할 수 있을 것 같다.
하지만 서버에서 이를 인지하려면 현재 URL 정보를 인지하고 예외에 대한 핸들링을 해야하는데
구현하기엔 쉽지 않을 듯하다.

[404] : PAGE NOT FOUND 즉, 매핑된 핸들러가 없는 경로 
 또는 공통 리소스에 접근했으나 영속 계층에 데이터가 존재하지 않는 상황에 던질 수 있다.

[405] : 메소드가 허용되지 않은 상황이 어떤 게 있을까.
Validation 을 적용하였으나 바인딩 에러가 나타난 상황에 사용할 수 있을 것 같다.
다른 상황은 정말 모르겠다.
굳이 엮자면 IllegalArgumentsException 과 엮어, 
결괏값과 무관하게 서비스단 호출부터 비정상적으로 이루어지는 상황에 사용하자.

[406] : Betty 프로젝트를 진행하며 계정과 권한 관련하여 시큐리티 없이,, 되게 신경써서 작업했는데 권한 관련 접근 제어는 이 상태 코드를 이용하면 됐을 것 같다.
우린 Rest 방식의 URL 에 회원 id 자체를 넣게 되었는데, 이건 딱히 좋지 않은 것 같다.
데이터베이스에서 기본 키 자체를 id로 지정하여 달리 방법이 없었다.
아무튼 사용자는 다른 계정의 개인 페이지 접근에 대해 호기심을 품을 수 밖에 없을 테다.
세션과 uri 비교로 막긴 했으나, 좀더 깔끔하게 처리하려면 406 코드를 사용했으면 좋았겠다.

[408] : 요청 시간 초과는 서버에서 핸들링 할 수 있을까? 그것도 사용자 문제로 돌릴려면,
문자나 이메일 인증에 대한 시간 초과를 이 코드로 해결할 수 있겠다.
나의 경우는 두 인증에 대한 코드를 세션에 저장하고 세션 유지 시간을 3분으로 잡았다.
클라이언트가 인증 코드를 전송했으나 3분이 지난 경우, 
즉 세션이 사라졌을 때 408 코드를 던질 수 있겠다.

[415] : 그대로 사용하면 될 것 같다. 필요한 영역이 확실해보인다.

[418] : 이건 궁금해서 놔뒀다. 뭘까?

[451] : 나쁜 이유로 불허? 이건 사실 400이나 다른 코드로 처리하면 될 것 같다.
언젠가 쓸모가 있기에 있는 게 아닐까 싶다. 
```

<br/>
<br/>

## 5xx 서버 에러
<br/>

- INTERNAL_SERVER_ERROR(500, "Internal Server Error")

- BAD_GATEWAY(502, "Bad Gateway")

- SERVICE_UNAVAILABLE(503, "Service Unavailable")

- GATEWAY_TIMEOUT(504, "Gateway Timeout")

- INSUFFICIENT_STORAGE(507, "Insufficient Storage")

- NETWORK_AUTHENTICATION_REQUIRED(511, "Network Authentication Required")


<br/>
<br/>

&nbsp;&nbsp; **정리**
```
5xx 코드를 자랑스럽게 반환할 상황이 있을까?
이걸 정리하려는 것조차 자존심이 상한다.
코드를 잘 짜고 서버는 수평 확장이 가능해야되지 않을까?
```

<br/>
<hr/>
<br/>

## 총정리

<br/>

공통 예외 처리에 대한 고민으로 시작된 상태 코드 정리에 너무 몰입해버렸다.   
적는 도중에 스스로 느낀 건데, 상태 코드 = Exception 라는 생각이 어느새 박혀있었다.   
<br/>
지금의 나에게 상태 코드란,    
서버에서 어떤 일이 일어났는가,   
클라이언트의 요청을 서버는 어떻게 받아들였는가.   
<br/>
여기에 대한 메시지라고 여겨진다.  
