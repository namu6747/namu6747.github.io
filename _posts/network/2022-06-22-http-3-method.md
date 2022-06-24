---
title: HTTP - 3 - method
author: namu6747
date: 2022-06-23 09:11:00 +0900
categories: [공부, 네트워크]
tags: [HTTP, 자바, 웹]
---

**## 인프런 김영한님의 강의 내용 기록 ##**


# HTTP 메서드   
*최근 Resource -> Representation 으로 스펙상 용어가 변경됨*

**API URI 설계**   
리소스 식별, URI 계층 구조 활용   
계층 구조상 상위를 컬렉션으로 보고 복수단어 사용 권장   
URI는 리소스만 식별, 리소스와 행위는 엄격히 분리   

*행위를 어떻게 구분하는가?*    
HTTP 메서드가 대신 해줌   
완벽히 분리하긴 어렵다.    
어쩔 수 없는 부분은 컨트롤 URI라는 것으로 설계해야한다.   

<br/>
<br/>

**HTTP 메서드 종류**   
- GET : 리소스 조회   
단순히 query 로 보냄   
서버 쪽에선 GET으로 오면 캐싱을 하겠다   

- POST : 요청 데이터 처리, 주로 등록에 사용   
메시지 바디를 통해 서버로 요청 데이터 전달   
사실 POST만으로 다 할 수 있긴 하다.   

- PUT : 리소스를 완전히 대체, 해당 리소스가 없으면 생성   
포스트와 달리 클라이언트가 정확한 주소를 알고 있다   

- PATCH : 리소스 부분 변경   
PUT과 달리 부분 변경됨   

- DELETE : 리소스 삭제   

- 기타 메서드
특히 CONNECT, TRACE 아예 안쓰는 수준   
HEAD : GET과 동일하지만 메시지 부분을 제외   
OPTIONS : 대상 리소스에 대한 통신 기능 옵션(메서드)을 설명   
CONNECT : 대상 자원으로 식별되는 서버에 대한 터널을 설정   
TRACE : 대상 리소스에 대한 경로를 따라 메시지 루프백 테스트

<br/>
<br/>

**HTTP 메서드의 속성**   
- 안전 (Safe Methods)   
호출해도 리소스를 변경하지 않는다.   

- 멱등 (Idempotent Methods)   
f(f(x)) = f(x)   
호출 횟수와 상관없이 결과가 같다.   
자동 복구 메커니즘   
서버가 TImeout 등으로 정상 응답을 못주었을 때   
클라이언트가 같은 요청을 다시 해도 되는가? 판단 근거   

- 캐시가능 (Cacheable Methods)   
실제로는 GET, HEAD 정도만 캐시로 사용   
 POST, PATCH는 본문 내용까지 캐시 키로 고려해야 해서 쉽지 않다.

<br/><br/>

- - - - - - - -

<br/><br/>



**HTTP 메서드 활용**

<br/>
클라이언트에서 서버로 데이터 전송     

<br/>

```
1. query parameters를 통한 데이터 전송   
= GET   
= 주로 정렬 필터 (검색어)   

2. message body를 통한 데이터 전송   
= POST, PUT, PATCH   
= 회원 가입, 상품 주문, 리소스 등록, 리소스 변경   
```

4가지 상황     
1. 정적 데이터 조회   
= 이미지, 정적 텍스트 문서   
일반적으로 query-P 없이 resource path로 단순 조회 가능   

2. 동적 데이터 조회   
= 주로 검색, 게시판 목록에서 정렬 필터 (검색어)   
조회는 GET 사용 및 query-P를 사용   


```
3. HTML Form을 통한 데이터 전송   
= 회원 가입, 상품 주문, 데이터 변경 

- POST -    
웹 브라우저가 summit 버튼을 누르면 form의 data를 읽어서   
 Content-Type이 application/x-www-form-urlencoded인   
 key-value 형식의 HTTP message를 생성해준다.   
 이때 body message 내용은 query-P와 매우 흡사하다.   

- GET -
query-P에 key-value를 담는다. 단 조회에만 사용한다.   
Resource 변경이 발생하는 곳에 사용하면 안됨

- multipart/form-data -   
각 data에 대한 boundary가 나눠져 전송됨.   
주로 binary data(or file) 전송에 사용됨   
```

4. HTTP API를 통한 데이터 전송   
= 회원 가입, 상품 주문, 데이터 변경   
GET, POST, PUT, PATCH 모두 활용   
content-Type : application/json을 주로 사용 (사실상 표준)   
TEXT, XML, JSON 등   
예전엔 XML을 주로 썼지만 이젠 JSON이 대세    

- 서버 to 서버 -   
백엔드 시스템 통신    

- 앱 클라이언트 -   
아이폰, 안드로이드   

- 웹 클라이언트(Ajax) -    
HTML에서 Form 전송 대신 자바 스크립트를 이용한 통신에 사용   
예) React, Vue.js 같은 웹 클라이언트와 API 통신   

<br/><br/>

- - - - - - - -

<br/><br/>

- - - - HTTP API 설계 예시 - - - -

- -회원 관리 시스템 - -

회원 목록 /members -> GET
회원 등록 /members -> POST
회원 조회 /members/{id} -> GET
회원 수정 /members/{id} -> PATCH, PUT, POST (추천순)
회원 삭제 /members/{id} -> DELETE

- - 파일 관리 시스템 - -
파일 목록 /files -> GET
파일 조회 /files/{filename} -> GET
파일 등록 /files/{filename} -> PUT
파일 삭제 /files/{filename} -> DELETE
파일 대량 등록 /files -> POST

POST - 신규 자원 등록 특징 
= 클라이언트는 등록될 리소스의 URI를 모른다
= 서버가 새로 등록된 리소스 URI를 생성해준다.
= 컬렉션 : 서버가 관리하는 리소스 디렉토리 /members 

PUT - 신규 자원 등록 특징
= 클라이언트가 리소스 URI를 알고 있어야 한다.
= 클라이언트가 직접 리소스의 URI를 지정한다
= 스토어 : 클라이언트가 관리하는 리소스 디렉토리 /files
> 사실 대부분 POST 사용함

HTML Form 사용
회원 목록 /members -> GET
회원 등록 폼 /members/new -> GET
회원 등록 /members/new, /members -> POST
회원 조회 /members/{id} -> GET
회원 수정 폼 /members/{id}/edit -> GET
회원 수정 /members/{id}/edit, /members/{id} -> POST
회원 삭제 /members/{id}/delete -> POST

POST의 new, edit, delete 같은 게 컨트롤 URI (동사로 작성)
문서, 컬렉션, 스토어로 해결하기 어려운 추가 프로세스 실행
GET, POST만 지원하므로 제약이 있음

참고 : https://restfulapi.net/resource-naming

