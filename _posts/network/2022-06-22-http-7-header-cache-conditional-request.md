---
title: HTTP - 7 - header - caching & conditional request
author: namu6747
date: 2022-06-23 09:11:00 +0900
categories: [공부, HTTP]
tags: [HTTP, 자바, 웹]
---

**## 인프런 김영한님의 강의 내용 기록 ##**

- - - - HTTP 헤더2 (캐시와 조건부 요청) - - - -

- 캐시가 없을 때 -
데이터가 변경되지 않아도 계속 네트워크를 통해서
 데이터를 다운로드 받아야 한다.
인터넷 네트워크는 매우 느리고 비싸다.
브라우저 로딩 속도가 느리다.
느린 사용자 경험.

- 캐시 적용 -
헤더에 cache-control : max-age = ~~sec
웹 브라우저에는 캐시 저장소가 있다.
다음 요청에선 브라우저 캐시를 먼저 탐색하고 가져온다.

- 캐시 시간 초과 -
유효 시간이 초과하면, 서버를 통해 데이터를 다시 조회하고,
 캐시를 갱신한다
이때 다시 네트워크 다운로드가 발생한다.

검증 헤더 : 캐시 데이터와 서버 데이터가 같은지 검증하는 데이터

- - 검증 헤더와 조건부 요청 1 - - 
응답 Last-Modified : ~~ UTC
웹 브라우저가 요청을 보낼때 아래의 헤더를 함께 보낸다.
요청 if-modified-since : ~~ UTC

수정이 안 됐을 때 304 Nod Modified 상태 코드를 보낸다.
핵심은 이때 message body가 없다.

> F12에서 status 부분 색깔이 회색인 게 캐시에서 불러온 거다. (불확실)

- 단점 - 
1초 미만 단위로 캐시 조정이 불가능
날짜 기반의 로직 사용
시간을 기준으로 삼기 때문에 데이터가 같아도 갱신되었다면 다시 다운로드
- - - - - - - -

- - 검증 헤더와 조건부 요청 2 - -
ETag (Entity Tag)
캐시용 데이터에 임의의 고유한 버전 이름을 달아둠
ETag 헤더 교류만으로 검증
If-None-Match false일 시 304 Not Modified 응답
캐시 제어 로직을 서버에서 완전히 관리한다.
클라이언트는 캐시 메커니즘을 전혀 몰라도 된다.
- - - - - - - -

- - 캐시 제어 헤더 - -
Cache-Control : 캐시 제어
 max-age : 캐시 유효시간
 no-cache : 데이터는 캐시해도 되지만, 항상 origin 서버에 검증하고 사용
 no-store : 데이터에 민감한 정보가 있으므로 저장하면 안됨
	메모리에서 사용하고 최대한 빨리 삭제
> 현재 Pragma와 Expires 없이 Cache-Control로 다 할 수 있다.
Pragma : 캐시 제어 (하위 호환)
Expires : 캐시 유효 기간 (하위 호환)

- - - 프록시 캐시 도입 (CDN) - - - 
origin 서버와 웹 브라우저 사이에 캐싱이 가능한 프록시 서버가 존재함

- - 캐시 지시어 (directives) - -
Cache-Control : public
= 응답이 public 캐시에 저장되어도 됨
Cache-Control : private (default)
= 응답이 해당 사용자만을 위한 것임, private 캐시에 저장해야 함
Cache-Control : s-maxage
= 프록시 캐시에만 적용되는 max-age
> 이런 게 있다
Age : 60 (HTTP 헤더)
= origin 서버에서 응답 후 프록시 캐시 내에 머문 시간
> 이런 게 있다

- - - 캐시 무효화 - - -
캐시 헤더를 추가 안하면 캐시가 안되니깐 필요 없다고 생각할 수 있지만
웹 브라우저가 GET 요청의 경우 임의로 cache를 해버릴 때도 있다.

Cache-Control : no-cache, no-store, must-revalidate < 한번에 넣기

must-revalidate 
origin 서버에 접근할 수 없는 경우, 항상 오류가 발생 해야함
504 Gatewaty Timeout
no-cache
origin 서버에 접근할 수 없는 경우 프록시 서버에서 대신 처리할 수도 있음.
200 등.

★★★★★★★★★★★★★★★★★★★★★★★★★★★★★★

★★★★★★★★★★★★★★★★★★★★★★★★★★★★★★

text/html vs application/json 의 차이

query는 정확히 뭘 하는 건가 정말 질의 그자체?

프록시서버란?

트래킹? 트래픽?

domain/path?query

packit = package + bucket

http 스펙 : <https://tools.ietf.org/html/rfc7230>





