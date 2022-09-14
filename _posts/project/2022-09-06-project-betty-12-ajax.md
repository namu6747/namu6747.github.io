---
title: 12. about ajax - Betty
author: namu6747
date: 2022-09-06 09:12:00 +0900
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

# ajax 에 대한 견해

## 약속
ajax 를 통한 데이터 송수신은 엄격한 약속으로 이루어졌다.  
예외 처리의 경우 위 특징이 극명하게 드러났다.   
아직 제대로 구현해보진 않았지만 "C001" 같은 코드를 통해  
분기문을 수행하는 걸 떠올려보면 약간은 두렵다.   

## 비동기
비동기 방식으로 데이터만을 주고 받는다 생각하고 사용했지만  
꼭 여기에 매몰될 필요도 없었다.   
내 경우는 변수를 미리 선언해두고 반환 값을 이용하려 했던 적이 있는데  
비동기 방식으론 정상적인 수행을 기대할 순 없었다.  
이 때는 ajax 를 그대로 사용하되, 동기화 시켰다.   

## 리소스
페이지 전환 없이 데이터만을 주고 받는다는 이점이 있지만,   
또 불필요한 데이터까지 받는 다면 뭔가 이상하다.   
물론 이것만으로도 자원 낭비를 줄일 수 있을 것이다.  
하지만 dto 를 적극 활용하여 이러한 비용 절약을   
극대화 할 때 ajax 의 진가가 나타날 것이라 생각한다.   

## 리다이렉트
전형적인 POST method 를 활용한 통신에서,   
ajax 를 사용했을 땐 javascript 문법으로 location 을 조작하여  
중복 호출이 일어나지 않게 막았다.   

## 사용자 편의
도서 상세 검색을 포함한 다양한 조건 검색에선,   
사용자 편의랍시고 ajax 를 꼭 써야되는 것도 아니였다.   
url 상 쿼리 파라미터로 나타나는 검색 조건에 대해   
숨길 필요가 없었다.   

## 웹앱
모바일앱으로 넘어간다면 이야기는 달라지겠지만   
아직까지 웹앱 밖에 다뤄본 적이 없는 지라   
ajax 는 꼭 필요한 건 아니지만,   
분명 장점이 상당히 많은 기능인 것은 알 수 있었다.  

렌더링을 자바스크립트를 통한 요소 또는 노드 조작으로 진행하여,     
프론트 전반적으로 기본은 할 줄 알아야겠다 싶었고,   
이번 프로젝트에서 나름 제대로 복습하는 시간을 가졌다고 생각된다.   

