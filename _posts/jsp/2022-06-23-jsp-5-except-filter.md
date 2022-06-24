---
title: 쉽게 배우는 JSP 웹 프로그래밍 - 5 - 예외 처리 - 필터
author: namu6747
date: 2022-06-23 09:11:00 +0900
categories: [공부, JSP]
tags: [자바, 웹]
---

- - - - 예외 처리 - - - -
"프로그램이 처리되는 동안 특정한 문제가 발생했을 때
 처리를 중단하고 다른 처리를 하는 것으로 오류 처리라고도 한다."

1. page 디렉티브 태그를 이용한 예외 처리
= errorPage, isErrorPage
2. web.xml 파일을 이용한 예외 처리
= <error-code> 또는 <exception-type> 요소를 이용
3. try/catch/finally를 이용한 예외 처리

- - 예외 처리 방법의 우선순위 - - 
1. JSP try-catch
2. page 디렉티브 태그의 errorPage 출력
3. JSP 예외 유형이 web.xml과 동일할 경우 설정한 오류 페이지 출력
4. JSP 오류 코드가 web.xml과 동일할 경우 설정한 오류 페이지 출력
5. 이 항목에 해당되지 않는 경우 웹서버 제공 기본 오류 페이지 출력
- - - - - - - -

- - web.xml 파일을 이용한 예외 처리 - - 
1. error-code : 오류 코드를 설정
2. exception-type : 자바 예외 유형이 정규화된 클래스 이름을 설정
3. location : 오류 페이지의 URL 설정

- 주요 오류 코드의 종류 -
200 : 요청 정상 처리
307 : 임시로 페이지 리다이렉트
400 : 클라이언트의 요청이 잘못된 구문으로 구성
401 : 접근이 허용되지 않음
404 : 지정된 URL을 처리하기 위한 자원이 존재하지 않음(페이지x)
405 : 요청된 메소드가 허용되지 않음
500 : 서버 내부의 에러 (JSP에서 예외 발생)
503 : 서버가 일시적으로 서비스 불능 (서버 과부하, 보수중)

- - try/catch - -
RequestDispatcher + forward 이용

- 서블릿에서의 jsp 호출 -
forward()
= JSP 페이지를 호출하는 순간 서블릿 프로그램이 실행을 멈추며
 JSP 페이지로 넘어가 그곳에서 실행하고 프로그램이 끝남
> forward 이후의 문장을 무시하는가?

include()
= JSP 페이지가 실행된 후 나머지 서블릿 프로그램이 실행됨
> include 이후의 문장을 모두 끝마치는가?
 - - - -

★★★★★★★★★★★★★★★★★★★★★★★★★★★★★★

- - - - 필터 - - - - 
클라이언트와 서버 사이에서 request, response 객체를 
먼저 받아 사전/사후 작업 등 공통적으로 필요한 부분을 처리

javax.servlet.Filter 의 구현 클래스를 생성해야함

- Request filter -
인증 (사용자 인증)
요청 정보를 로그 파일로 작성
암호화 인코딩 작업

-Response filter -
응답 결과 데이터 압축
응답 결과에 내용 추가/수정
총 서비스 시간 측정

- Filter 인터페이스 메소드의 종류 -
init : 필터 인스턴스의 초기화 메소드
= JSP 컨테이너가 필터를 초기화할 때 호출
doFilter : 필터 기능을 작성하는 메소드
destroy : 필터 인스턴스의 종료 전에 호출되는 메소드

- - init - -
init 의 매개변수 중 FilterConfig 타입이 존재한다.
* F.C = FilterConfig

F.C 객체는 JSP 컨테이너가 초기화 중
필터에 정보를 전달하는 데 사용하는 필터 구성 객체

- F.C 인터페이스의 메소드 - 
getFilterName : web.xml 파일에 설정된 필터 이름
getInitParameter : web.xml 파일에 설정된 매개변수 
getInitParameterNames : web.xml 파일에 설정된 매개변수 집합
getServletContext : ServletContext 객체

- - - - - - - -

- - doFilter - > JSP 컨테이너가 필터를 리소스에 적용할 때마다 호출
doFilter의 매개변수 중 FilterChain 타입이 존재한다.
FilterChain은 다음 필터를 호출하는 데 사용됨
 마지막 필터라면 체인의 끝에서 리소스를 호출
FilterChain은 동기화를 위해서도 존재하지만
 필터의 수행 과정을 연속적으로 하는 방법으로도 사용된다.
FilterChain은 doFilter 메소드 내부에서 
 다시 filterChain.doFilter(request, response)와 같이 작성

ServletRequest 객체는 체인을 따라 전달하는 요청
response는 위와 반대
- - - - - - - -

- - - web.xml 파일의 필터 구성 - - - 
필터를 사용하려면 어떤 필터가 어떤 리소스에
적용되는지 JSP 컨테이너에 알려주어야 한다.
web.xml 파일에 필터를 설정할 때
<filter>와 <filter-mapping> 요소를 사용하며
여러 개의 필터가 설정되어 있으면 선언된 순서대로 실행된다.

<init-param> 요소에 설정된 매개변수와 값에 접근하려면
getServletConfig().getInitParameter("매개변수");

<filter-mapping> 요소는 특정 리소스에 대해
어떤 필터를 사용할지 설정한다.

<url-pattern> 요소에는 리소스가 기입된다. (ex. filter.jsp)

- 요청 URL 패턴의 유형 -
*.do = .do로 끝나는 모든 URL과 일치하는 파일 확장 패턴
/* = 모든 URL과 일치하는 경로 패턴
/filter.jsp = 리소스와 일치하는 특정 패턴

> 필터를 이용해 로그를 기록할 수 있음
