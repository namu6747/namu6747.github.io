---
title: 14. DTO, Form, VO, Entity ? - Betty
author: namu6747
date: 2022-09-06 09:14:00 +0900
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

&nbsp;**_도대체 뭐가 뭔지? 어떻게 사용할까?_**

<br/>
<hr/>

## VO, ENTITY - 뭐가 다르지?

처음엔 얘네들을 사용하자 말을 꺼내기 전에 고민이 많았다.  

우선, **_그냥 VO 면 되지 않을까?_** 였다.  
Entity 라는 말은 JPA 강의에서 처음 들었는데   
이게 VO 와 다른 게 무엇인가 하는 의문이 들었다.  

검색을 토대로, 테이블을 모델로 한 객체라고 생각 했는데  
학원 수업에서 다루는 VO 의 역할과 다름이 없었기 때문이다.  

JPA 를 사용할 땐, 이 VO 에 테이블 관련 어노테이션을 선언하고  
이를 Entity 라고 부르는 것을 봤었다.  
이러면 제법 Entity 같긴 하다.  

그래서 난 Betty 에서 VO 와 Entity 를 구분하지 않았고,  
뭐 굳이 따지면 Entity 쪽에 손을 들었다.  
class 명을 단순히 'Member' 와 같이 짓고   
@Getter, @Builder 만을 열어놓았다.  

```
Entity 얘기가 나와서 하는 말인데  
처음엔 도메인 주도 개발을 적용해보고 싶었다.  
대여 관련한 로직에서 Entity 에 메소드를 선언하여 
신나게 Service 단을 감량시키려 했는데,  
대부분 데이터베이스 내부 로직으로 처리된 게 아쉽다.  
```

<br/>
<hr/>

## DTO - 왜 사용해야 될까?

DTO 는 Model 에 담고 JSP 에 렌더링하는 형태의  
서버 사이드 렌더링이라면, 사실 필요한 때가 별로 없어 보인다.  
나름의 이유라면, 굳이 객체를 하나 더 생성하는 것 아닌가?  
어짜피 문서를 다 그리고 난 뒤 보낼 텐데 별반 차이 없을 것 같다.  

하지만 ajax 를 사용할 떄, 데이터만 받아  
클라이언트 측에서 렌더링 한다면 이야기가 달라졌다.  

애초에 주고 받는 객체도 사실 바이트 코드 아닌가?  
객체(데이터)를 주고 받는 비용이 든다.  
여기서 매번 VO에 모든 데이터를 담아 보내고,  
클라이언트 측에서 이를 수신한다면.. 좀 찝찝하다.  

개발자라면 상시 사용자 편의를 고려해야된다고 생각한다.  
이걸 네트워크 비용이라고 하면 될까?  
데이터를 주고 받는 비용을 줄이는 게 좋지 않을까?  

이러한 이유로 난 DTO 도입에 대한 합리성을 얻었다.  

<br/>
<hr/>

## Form - DTO 랑 무슨 차이인가?

이제 검증을 위한 Form 객체도 정의하게 되었는데,  
Form 객체를 굳이 구분한 이유는 아래와 같다.  
(내 머릿속에서 Form 은 DTO 의 자식 느낌이다.)

1. DTO 는 응답 위주로 사용하고 싶다.
2. DTO 에 Validation 을 적용하고 싶지 않다.
3. html form 태그에서 넘어오는 객체를 명확히 구분 짓고 싶다.

그래서 나온 결과는 아래와 같다.

![Desktop View](/assets/img/betty/object/form.png)

Validation 을 거친 뒤 encode, convert 메소드를 호출한다.  
convert 내부에서 암호화를 수행해도 되지만,  
정말 혹시나 해서 굳이 분리해두었다.  

_"DTO 에 아무 메소드도 정의되지 않아야 한다."_  
_"DTO 는 행위를 갖지 않는다."_  
라고 누군가 말하겠지만, 세상에 정해진 일이 어디있는가?  

<br/>

&nbsp;**결과적으로 Betty 에서는 이렇게 요약된다.**

```
form : form 양식을 전달 받는 객체
dto : 변경이 가능한 데이터 응답용 객체
entity : 불변성을 갖는 영속 계층(db와 직결된)의 객체
```


<br/>
<hr/>

## DTO 는 언제 생성되야 할까?

**_Entity -> DTO 변환은 어디서 이루어져야 하는가?_**  
이건 내 생각엔 단연 **Service** 계층이다.  
정확히는 데이터베이스에서 read 되어 반환된 순간이다.  

DTO 는 렌더링에 필요한 데이터 관련 필드만 정의되어 있을 것이다.  

이 시점에서 이미 끝난 이야기인 게,  
이 DTO 의 명칭 또한 제시되어 있을 것이고,  
데이터 가공 시 클래스 이름을 통해 용도까지 파악할 수 있다.  

가능한, 빠른 타이밍에 변환하는 게 무조건 이롭다고 여겨진다.  

<br/>
<hr/>

## 애초에 DTO 를 꺼내면 안될까?

이런 생각도 든다.  

**_데이터베이스에서 꺼낼 때 애초에 DTO 로 꺼내면 안되나?_**  

ORM 에 대해선 아직 자세히 모르고, Mapper 는 마이바티스 덕에 좀 안다.  

위 질문에 대해선, 우선 난 **그냥 싫다.**  

원하는 컬럼만을 꺼내는 메소드를 모두 만든다면  
Repository 클래스의 코드가 매우 길어진다.  

**많아지고 볼품 없다.**  

게다가, 그 각각의 행위를 모두 알고 있어야 될까?  

난 좀 귀찮더라도 대용량 컬럼이 존재하는 게 아닌 이상,  
레코드 하나를 꺼내서 가공하는 편이 차라리 속 편하다.  

Repository 에 적히는 SQL 이야 사실 자바 코드긴 하다만,  
난 어쨌든 데이터베이스에 의존하는 듯한 그림이 싫다.  
