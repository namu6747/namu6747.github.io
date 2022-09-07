---
title: HTTP - 1 - network
author: namu6747
date: 2022-06-23 09:11:00 +0900
categories: [공부, HTTP]
tags: [HTTP, 자바, 웹]
---

**## 인프런 김영한님의 강의 내용 기록 ##**

# IP 
Internet Protocol   

지정한 IP주소에 데이터 전달
패킷이라는 통신 단위로 데이터 전달
패킷에 각 endpoint 정보와 데이터를 함께 전달함

### IP의 방식의 단점
**비연결성**
<br/>
= 패킷을 받을 대상이 없거나 서비스 불능 상태여도 패킷 전송
<br/>

**비신뢰성**
<br/>
= 중간에 패킷이 사라진다면, 패킷이 순서대로 안오면?
	중간 노드에 문제가 생긴다면
<br/>

**프로그램 구분**
<br/>
= 같은 IP를 사용하는 서버에서 통신하는 app이 둘 이상이면?
<br/>

### 인터넷 프로토콜 스택의 4계층
<br/>

**애플리케이션 계층** - HTTP, FTP (Socket Library)
<br/>

**전송 계층** - TCP, UDP
<br/>

**인터넷 계층** - IP
<br/>

**네트워크 인터페이스 계층** (Ethernet frame)
<br/>


# TCP 
Transmission Control Protocol   

전송 제어, 순서, 검증 정보와 endpoint의 PORT 정보가 추가됨   

**연결 지향** - TCP 3 way handshake (가상 연결)   
synchronize와 acknowledge(접속 요청과 수락)를 거쳐 데이터 통신   
완전한 1대1 직결은 아님. 논리적으로 연결되었다고 인지만 하자.


**데이터 전달 보증**   
데이터 전달이 완료됨을 송신자에게 알려줌   

**순서 보장**   
IP 단독 방식의 단점을 보완했다

신뢰할 수 있는 프로토콜   
현재 대부분 TCP 사용   
TCP를 IP로 둘러싼 형태를 줄여서 TCP/IP 패킷이라 부름   


# UDP
User Datagram Protocol   

IP와 흡사한 방식에 PORT와 체크섬 정도만 추가됨   
TCP가 지배하는 상황에서 최적화가 너무 하고 싶다면   
UDP를 이용하되 어플리케이션 계층에서 추가 작업을 해준다   
HTTP3에선 UDP기반의 통신을 사용한다


### Port
IP는 서버를 찾고 Port는 서버 안의 app을 찾는다고 보면 된다   
좀더 정확히, 같은 IP 내에서 프로세스를 구분   
클라이언트는 자신의 IP, Port 까지 패킷에 넣어 보내기에   
서버도 응답하기 편해진다.   
0 ~ 65535 할당 가능   
0 ~ 1023 잘 알려진 포트, 사용하지 않는 것이 좋음   
* FTP - 20, 21   
* TELNET - 23   
* HTTP - 80   
* HTTPS - 443   


# DNS 
Domain Name System   

전화번호부 같은 서버를 제공   
도메인 명을 등록하고 IP 주소로 바꿀 수 있음   


# URI 
Uniform Resource Identifier   

**U** : 리소스 식별하는 통일된 방식   
**R** : 자원, URI로 식별할 수 있는 모든 것(제한 없음)   
**I** : 다른 항목과 구분하는데 필요한 정보   
**L** : 리소스가 있는 위치를 지정   
**N** : 리소스에 이름을 부여   

*URI는 로케이터(L), 이름(N) 또는 둘 다 추가로 분류될 수 있다*

**URI ⊃ { URL , URN }**   

?URN은 이름으로만 지정되긴 하지만 매핑이 어렵다   
URN 이름만으로 실제 리소스를 찾을 수 있는 방법이 보편화 되지 않음   
**URL 위주로 사용한다고 보면 된다**

<br/>

## URL 분석
``` 
scheme :// [userinfo@] host [:port] [/path] [?query] [#fragment]
https :// www.google.com :443 /search ?q=hello^hI=ko
```

*scheme* : 주로 프로토콜 사용 (http, https, ftp . . . )   
어떤 방식으로 자원에 접근할 것인가 하는 약속 규칙   
http는 80, https는 443 포트를 주로 사용, 포트는 생략 가능   
*userinfo* :  거의 안 씀. URL에 사용자 정보를 포함   
*host* : 도메인 주소 또는 아이피 주소   
*port* : 접속 포트   
*path* : resource 경로, 계층적 구조   
*query* : json 형태, ?로 시작, &로 추가 ( ≒ query [ parameter, string ] )   
*fragment* : 거의 안 씀. 서버 전송x, html 내부 북마크 등에 사용   
