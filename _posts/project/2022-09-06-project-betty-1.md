---
title: 1. 서론
author: namu6747
date: 2022-09-06 09:00:00 +0900
categories: [프로젝트, Betty]
tags: [스프링, 자바, 포트폴리오]
---

## &nbsp;&nbsp;&nbsp; index
1. [서론](/posts/project-betty-1/)
2. [기획 및 명세](/posts/project-betty-2-concept/)
3. [패키지 트리](/posts/project-betty-3-package-tree/)
4. [프로젝트 환경](/posts/project-betty-4-config/)
5. [메시지와 국제화](/posts/project-betty-5-message/)
6. [예외 다루기](/posts/project-betty-6-exception/)
7. [검증](/posts/project-betty-7-validation/)
8. [계정 관련](/posts/project-betty-8-sign/)
9. [권한 인터셉터](/posts/project-betty-9-interceptor/)
10. [도서 관련](/posts/project-betty-10-book/)
11. [대여 관련](/posts/project-betty-11-rental/)
12. [오프라인 관련](/posts/project-betty-12-offline/)
13. [about ajax](/posts/project-betty-13-ajax/)
14. [팀원 교육 - ajax](/posts/project-betty-14-edu-ajax/)
15. [DTO, Form, VO, Entity ?](/posts/project-betty-15-object/)
16. [후기](/posts/project-betty-16-review/)

<br/>
<hr/>

**기능 시연 연상**

[Youtube 에서 보기](https://youtu.be/AFNmRdEg1ck)

<iframe width="640" height="360" src="https://www.youtube.com/embed/AFNmRdEg1ck" title="한국기술교육직업전문학교 최종 프로젝트 Betty 시연 영상" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

<br/>
<hr/>
<br/>

기분이 좋다.     
저번 프로젝트는 프로젝트가 끝나고 수업에 또 치여서    
후기를 적을 시간이 없어 한참을 지난 뒤 적었는데,   
이번 프로젝트는 머릿속에 포트폴리오에 관한 모든 내용이 살아 있는 채로 작성할 수 있다.

_평범한 국비생은 스프링 프로젝트를 어떻게 진행했는가에 대해 흥미롭게 볼 수 있을 것이다._

기능만 보면 굉장히 단순하다.      
회원 관리, 대여, 체크인 시스템, 다양한 상세 검색 등   
정~말 별 거 없지만 프론트부터 백엔드까지 전 영역을 돌아다니고   
팀원 교육, 업무 분배 등 생각보다 많이 힘들었다.

이번 프로젝트의 핵심은 단순하다.
<br/>

__도서관과 독서실이 결합한 유료 시스템__

이것이 우리의 핵심이자 Betty 그 자체이다.

<br/>
<hr/>

## 간단히 maven 의존성부터 살펴보자
<br/>

![dependency0](/assets/img/betty/intro/top.png)
<br/>

![dependency1](/assets/img/betty/intro/middle.png)
<br/>

![dependency2](/assets/img/betty/intro/bottom.png)
<br/>

변경 사항에 대하여 최대한 알아보기 쉽게 작성했다.  

**AOP** 는 아직 정확히 뭘 해야 어노테이션식 적용을 할 수 있는 지 모르겠다.  
@EnableAspectJAutoProxy 내부에 들어가보면, 아래처럼 적혀있긴 하다.
<br/>

![eajap](/assets/img/betty/intro/eajap.png)

**Servlet** 쪽은 프로젝트 생성시 나타나는 servlet-api, jsp-api 를 지우고 아래 것들을 추가했다.  
이건 그냥 수업 때 하던 걸 그냥 따라했다.  

**데이터베이스** 의존성은 필요한 것만 했다.  
연동 모듈이 별도로 존재한다는 건 흥미롭다. mybatis-spring 이런 것.  

**테스트**는 assertj-core 를 추가했다.  
Assertions 클래스를 사용했을 때 테스트시 좀더 확실한 느낌이 있어서 좋았었다.  
junit 버전을 낮추는 건 수업 때 들었는데, 레거시 프로젝트와 연관됐던 것 같다.  

**validation** 에선 버전 설정에서 애먹었었는데  
이 또한 레거시 프로젝트의 영향이었던 것 같다.  
hibernate, jakarta 둘 다 버전을 낮춰서 다행히 해결됐었다.  

**mail && sms** 에는 javax.mail 이 있는데 이건 안 썼던 것 같다.  
아마 내가 착각하고 추가했던 것 같다.  

**Other** 에는 그 유명한 롬복과 jackson-databind 가 있다.  
jakarta-el 은 테스트 환경에서 아마 @PropertySource, @Value 에서   
  el 과 관련된 에러 떄문에 추가했던 걸로 기억한다.  
selenium 은 도서 자료 크롤링을 위한 의존성이다.  


<br/>
<hr/>