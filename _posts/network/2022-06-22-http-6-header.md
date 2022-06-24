---
title: HTTP - 6 - header
author: namu6747
date: 2022-06-23 09:11:00 +0900
categories: [공부, 네트워크]
tags: [HTTP, 자바, 웹]
---

**## 인프런 김영한님의 강의 내용 기록 ##**

- - - -HTTP 헤더 1 (일반 헤더) - - - -

과거의 Entity라는 단어는 사라지고
Representaion이 등장
여기서 R이 바로 Restful의 R이다.

HTTP
message body - RFC7230 기준

메시지 본문(message body)을 통해 표현(Representation) 데이터 전달
message body = payload (실제 데이터부)
표현은 요청이나 응답에서 전달할 실제 데이터
표현 헤더는 표현 데이터를 해석할 수 있는 정보 제공
 데이터 유형(html, json), 데이터 길이, 압축 정보 등등
* 참고 : 표현 헤더는 표현 메타데이터와, 페이로드 메시지를 
	구분해야하지만 힘듬

- - 표현 (Representation) - -

"Resource를 html이란 표현으로 전달할 것인가
 json이란 데이터 형태로 전달할 것인가 . . ."

Content-Type : 표현 데이터의 형식
= application/json 기본 문자 인코딩 방식이 UTF-8 {"data":"hello"}
Content-Encoding : 표현 데이터의 압축 방식
= 데이터를 전달하는 곳에서 압축 후 인코딩 헤더 추가
= gzip, identity(압축안한 기본데이터) 등
Content-Language : 표현 데이터의 자연 언어
= ko, en 등
Content-Length : 표현 데이터의 길이 (byte)
= 표현이랑 딱히 상관은 없다. payload 헤더라고 말하는 게 더 어울린다.
= Transfer-Encoding 을 사용하면 Content-Length를 사용하지 않는다.

표현 헤더는 전송, 응답 둘다 사용

- - - - 

- - 협상 (Content-negotiation) - - > 클라이언트가 선호하는 표현 요청

Quality Values(q) 우선 순위 
1. q = 0 ~ 1
2. 구체적인 것이 우선한다.
3. 구체적인 것을 기준으로 미디어 타입을 맞춘다.

Accept : 클라이언트가 선호하는 미디어 타입 전달
= */*, text/*, text/plain, text/pain;format=flowed
= text/*;q=0.3, text/html;q=0.7 . . . 
Accept-Charset : 클라이언트가 선호하는 문자 인코딩
Accept-Encoding : 클라이언트가 선호하는 압축 인코딩
Accept-Language : 클라이언트가 선호하는 자연 언어
= ko-KR,ko;q=0.9,en-US;q=0.8

> 협상 헤더는 요청시에만 사용
> OS와 Local 위치, 브라우저에 의해 어느 정도? 정해지는 것 같다

- - - -

- - 전송 방식 - -
단순 전송 : Content-Length
= 기본 데이터 그대로 
압축 전송 : Content-Encoding
= 실제로 절반 이상 줄어듦
분할 전송 : Transfer-Encoding : chunked
= chuck마다 Length 정보가 있고 전체 데이터 길이를 예측할 수 없기에
	Content-Length를 헤더에 넣지 않는다.
범위 전송 : Range, Content-Range
= 원하는 범위의 데이터만 받을 수 있음

- - - -

- - 일반 정보 - -
From : 유저 에이전트의 이메일 정보
= 잘 사용되진 않음. 검색 엔진 같은 곳에서 주로 사용, 요청에서 사용
Referer : 이전 웹 페이지 주소 : Referrer의 오타
= A -> B 로 이동하는 경우 B를 요청할 때 Referer : A를 포함해서 요청
= 주로 유입경로 분석할 때 사용, 요청에서 사용
User-Agent : 유저 에이전트 애플리케이션 정보
= 보통 웹브라우저 정보로 알면 됨
= 특정 브라우저의 문제를 인지할 수 있음
= 통계 정보, 요청에서 사용
Server : 요청을 처리하는 origin 서버의 소프트웨어 정보
* HTTP 요청을 보내면 노드를 거쳐 진짜 마지막인 origin 서버에 도달
= Apache, nginx 등의 정보 응답에서 사용
Date : 메시지가 생성된 날짜
= 응답에서 사용

- - - - 

- - 특별한 정보 - -

Host : 요청한 호스트 정보(Domain)
= 요청에서 "필수"로 사용
= 하나의 서버가 여러 도메인을 처리해야 할 때
= 하나의 IP 주소에 여러 도메인이 적용되어 있을 때
> domain을 기재한다는 건 현시대에서 자명한 이야기다

Location : page redirection
= 웹 브라우저는 3xx 응답의 결과에 Location 헤더가 있으면
	Location 위치로 자동 이동
= 201 Location 값은 요청에 의해 생성된 리소스 URI
= 3xx Location 값은 자동으로 리디렉션하기 위한 대상 리소스를 가리킴

Allow : 허용 가능한 HTTP 메서드
= 405 에서 응답에 포함해야함
= Allow : GET, HEAD, PUT
> 아 이런 게 있구나

Retry-After : user agent가 다음 요청을 하기까지 기다려야 하는 시간
= 503 : 서비스가 언제까지 불능인지 알려줄 수 있음
> 아 이런 게 있구나

- - - -

- - 인증 - - 
Authorization : 클라이언트 인증 정보를 서버에 전달
> ? 인증과 관련된 값을 넣어주면 되겠죠?
WWW-Authenticate : 리소스 접근시 필요한 인증 방법 정의
= 401 응답과 함께 사용

