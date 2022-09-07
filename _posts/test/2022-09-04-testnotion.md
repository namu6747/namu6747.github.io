---
title: 투데이
author: namu6747
date: 2022-09-04 09:08:00 +0900
categories: [공부, 자바]
tags: [자바, 기타]
---

# today 8/29

---

**공지사항**

1. 이제 DB 건드릴 거 거의 없으니깐 전부  아래 “DB 0829 수정” 에서 최신화 해주세요.

![Untitled](today%208%2029%20cba43918dd9843e89af196aa4a4dfd64/Untitled.png)

1. 게시글에서 이미지 업로드 제외했습니다. editor 통해서 업로드하려니 엄두가 안나네요.
2. 계정 더미 필요하신 분은 test package 의 
    
    package com.koreate.betty.domain.member; 에 가셔서 테스트 한번 돌리시면 돼여
    
    test/resources 에 [database.properties](http://database.properties) 가 있으신 분들은 schema 이름 주의해주세요
    

![Untitled](today%208%2029%20cba43918dd9843e89af196aa4a4dfd64/Untitled%201.png)

1. Member field 의 rights 는 0(일반회원), 1(가입미승인직원), 2(가입승인직원), 3(관리자)
2. session에 등록되는 로그인 회원은 jsp 내에서 ${user} 로 사용하시면 됩니다.
    
    Member 객체가 사용되므로 field 접근 시 참고하세요. 
    
    model 에 key값 “user” 절대 넣지 말고, 넣어야되는 상황 생기면 말해주세여
    
3. Board 작업하시는 분들 pull 하고 FreeBoard 쪽 jsp 참고하세여, 에디터에서 값 가져오기, 값 넣기, 
    
    에디터에서 가져온 값 출력하기 대강 적어놨어여
    
4. 이제 기능 구현에 집중해주세여. div class 이런거 신경쓰지 마세요. 그거 나중에도 할 수 있어요

 

PathVariable 예시

```jsx
<button onclick="location.href='${path}/members/${user.id}/edit'">
	수정
</button>

각 테이블의 기본 키는 PathVarible 역할을 할 수 있음
```

자율적으로 작성 (수업 들을 분은 수업 들어도 됨)

---

박재민 :  회원가입, 로그인 검증 로직 추가, 아이디, 비밀번호 찾기 구현

회원 정보 변경시 session 건드리기

삭제시 로그아웃ㅇ로 이동

---

신지훈 : DB 수정 후 Book 패키지 (프로바이더 쪽 우선 작업)

- [x]  수익 관련 컬럼 생성
- 회원 카드에 지출 내역 컬럼 추가
- [x]  TempJihoon 정리하구 example 패키지 삭제
- config에서 Temp 클래스 참조하던 코드 주석처리

- 대여료 연체료는 DB에서 처리됬으나 멤버십 가입비를 앱에서 처리할 것
- [ ]  Book 패키지 (프로바이더 쪽 우선 작업) (컨트롤러 하지말고 서비스 반환까지만)
- [ ]  dto service로 넘겨서 처리, Dao 통해서 가져온 Book 도 service에서 dto로 변환

---

권능환 :  공지사항

---

김선기 : 

---

박주신 : 

---

오늘 남을 사람 체크 (강제 아님)

- [x]  박재민
- [x]  신지훈
- [x]  권능환
- [ ]  김선기
- [ ]  박주신

---