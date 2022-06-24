---
title: 쉽게 배우는 JSP 웹 프로그래밍 - 6 - 세션 - 쿠키
author: namu6747
date: 2022-06-23 09:11:00 +0900
categories: [공부, JSP]
tags: [자바, 웹]
---

- - - - 세션 - - - - > stateful

세션은 웹 서버에 존재하는 객체다.
서버에서만 접근이 가능하므로 보안 유지에 유리함.
브라우저마다 하나씩 존재하므로 사용자를 구분하는 단위가 됨

get-set-Attribute 
= 세션 속성 이름이 name인 속성 값을 Object 형으로 반환한다
 해당되는 속성 이름이 없을 때는 null을 반환한다
 반환 값이 Object이므로 반드시 형 변환해야 한다.
getAttributeNames
= 세션 속성 이름을 Enumeration 타입으로 반환
getCreationTime
= 세션이 생성된 시간 반환 long타입
getId
= 세션에 할당된 고유 아이디를 String으로
getLastAccessedTime
= 클라이언트가 마지막으로 request를 보낸 시간
get-set-MaxInactiveInterval
= 해당 세션을 유지하기위해 세션 유지 시간. default = 1800
isNew
= 처음 생성된 세션인지
removeAttribute 단일 삭제
Invalidate 다중 삭제
= 현재 세션에 저장된 모든 세션 속성을 제거한다.

Enumeration enum = session.getAttributeNames()
while (enum.hasMoreElements()){
	String name = enum.nextElement().toString();
}
Enumeraion은 위와 같이 가져오면 된다.

세션 유효 시간이 없는 상태에서 세션을 삭제하지 않으면
메모리에 계속 잔류한다.

★★★★★★★★★★★★★★★★★★★★★★★★★★★★★★

- - - - 쿠키 - - - -
세션과 마찬가지로 클라이언트와 웹 서버 간의
상태를 지속적으로 유지하는 방법

하지만 쿠키는 세션과 달리 상태 정보를 웹 서버가 아닌
클라이언트에 저장한다

웹 브라우저가 접속했던 웹 사이트에 관한 정보와
개인정보가 기록되기 때문에 보안상 문제가 있다.

- 쿠키의 동작 과정 -

1. 쿠키는 주로 웹 서버 측에서 생성한다
 생성된 쿠키는 응답 데이터에 함께 저장되어 웹 브라우저에 전송

2. 웹 브라우저는 응답 데이터에 포함된 쿠키를
 쿠키 저장소에 보관한다. 쿠키는 종류에 따라
 메모리나 파일로 저장된다.

3. 웹 브라우저는 한 번 저장된 쿠키를 요청이 있을 때마다
웹 서버에 전송한다. 

- - - - - - - -

get-set-Comment
get-set-Domain
get-set-MaxAge
getName
get-set-Path
get-set-Secure
get-set-Value
get-set-Version

쿠키는 여러 개를 주고 받아도 된다.
비단 아이디 뿐만 아니라.

쿠키를 삭제하는 기능은 별도로 제공하지 않고
유효 기간을 만료시켜 삭제시키면 된다.