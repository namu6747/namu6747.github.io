---
title: 6. 검증 - Betty
author: namu6747
date: 2022-09-06 09:06:00 +0900
categories: [프로젝트, Betty]
tags: [스프링, 자바, 포트폴리오]
---

## &nbsp;&nbsp;&nbsp; index
1. [서론](/posts/project-betty-0/)
2. [기획 및 명세](/posts/project-betty-1-concept/)
3. [패키지 트리](/posts/project-betty-2-package-tree/)
4. [프로젝트 환경](/posts/project-betty-3-config/)
5. [메시지와 국제화](/posts/project-betty-4-message/)
6. [예외 다루기](/posts/project-betty-5-exception/)
7. [검증](/posts/project-betty-6-validation/)
8. [계정 관련](/posts/project-betty-7-sign/)
9. [권한 인터셉터](/posts/project-betty-8-interceptor/)
10. [도서 관련](/posts/project-betty-9-book/)
11. [대여 관련](/posts/project-betty-10-rental/)
12. [오프라인 관련](/posts/project-betty-11-offline/)
13. [about ajax](/posts/project-betty-12-ajax/)
14. [팀원 교육 - ajax](/posts/project-betty-13-edu-ajax/)
15. [DTO, Form, VO, Entity ?](/posts/project-betty-14-object/)
16. [후기](/posts/project-betty-15-review/)

<hr/>
<br/>

검증은 회원가입 검증에 대한 이야기를 할 생각이다.  

javax 와 hibernate 를 활용한 서버 검증  
수업 때 배운 JQuery.validate 를 활용한 클라이언트 검증  

두 가지 이야기다.  


<br/>
<hr/>

# 클라이언트 검증 (프론트)

## html

```html
아이디
<div class="input__item">
    <span><i class="bi bi-person-circle"></i></span>
    <input type="text" name="id" id="id" placeholder="아이디를 입력 하세요" /> 
    <div class="result"></div>
</div>
```

간단한 html 태그 그룹이고 특이한 점은  
자식 요소 중 마지막에 result class 의 div 태그가 존재한다.  
이 곳은 JQuery.validate 의 결괏값이 들어갈 장소다.  

## js

```jsx
const regexID = /^[0-9a-zA-Z]{3,10}$/;

$("#signUpForm").validate({
        onkeyup : function(el){
            $(el).valid();
        },

        rules : {	
            id : { 
                required : true,
                remote : { type : "GET", url : "${path}/sign/up/idCheck" },
                regex : regexID
            }
        },

        messages : {
            id : {
                required : "아이디를 작성하세요.",
                remote : "이미 존재하는 아이디입니다.",
                regex : "영어와 숫자로 3~10글자 이내로 작성하세요."
            }
        },

        errorClass : "text-danger",

        errorElement : "div",

        errorPlacement : function(error, element){
                error.insertAfter(element);
        }
});

```

아주 간단히 요약하면 이런 식이다.  

이메일, 문자 인증도 포함되어 있어서  
submit 이전에 마저 검증이 필요한 부분을  
submitHandler 설정을 통해 수행할 수도 있다.  

keyup event 가 발생했을 때, 제약에 따라 error 문구를 발생시킨다.  
나도 읽히는 그대로만 알고 있어서 자세히 설명할 수준은 안되는 것 같다.  

<br/>
<hr/>

# 서버 검증 (백)

## form object
<br/>

<!-- sign up form -->
![Desktop View](/assets/img/betty/validation/signupform.png)

validation 을 활용하기 위해 나름 성의를 갖췄다.  
@NotBlank, @NotNull, @NotEmpty 가 존재하는데  
@NotBlank 가 가장 꼼꼼하므로 선택했다.  
null 과 "" 를 모두 불허한다.  

생년월일은 @DateTimeFormat 을 이용했다.  
문자열을 보고 Date 객체로 변환한다.  

Betty 에선 아찔한 순간이 하나 있다.  
데이터베이스에서 생년월일 컬럼을 Timestamp 타입으로 지정했던 것이다.  
바로 Date -> Timestamp 변환 과정이었다.  

자세힌 모르지만 Timestamp 객체를 생성할 떄 1970년도 이전 날짜는 받지 않았다.  
이는 협정 세계시(UTC)와 관련된 것일 텐데,  
당장 자세히 알아볼 거리는 아니라서 인정했다.  

**_그냥 데이터베이스 컬럼 타입을 바꾸면 되지 않나?_**

이 말도 틀린 건 아니다.  
하지만 난 DB 설계를 팀원에게 믿고 맡겼다.  
기능상 큰 문제가 없다면 지적하고 싶지 않기도 했고,  
그럼에도 Timestamp 를 선택한 이유가 있을거라 생각했기 때문이다.  

<br/>
<hr/>

## BindingResult

<!-- sign up member -->
![Desktop View](/assets/img/betty/validation/signupmember.png)

열심히 form 객체에 제약을 걸어놨더라도  
매개변수에 @Valid(or @Validated) 를 선언하지 않으면 말짱도루묵이다.  
검증이 필요한 객체 **바로 다음**에 BindingResult 객체를 명시하면  
검증 부적합 판별 내용에 관한 에러 내용을 받을 수 있다.  

여기선 간단히 에러가 있다면 회원가입 화면으로 리다이렉트시키는 걸로 마무리했다.  
아무래도 검증 처리는 나름 배워야할 게 많기 때문에 이 이상은 후순위로 미뤘었다.  

<br/>
<hr/>


# 두 번의 검증

서버에선 항상 클라이언트에서 날아온 데이터를 의심하라고 배웠다.  
나는 앞으로도 두 번의 검증 절차를 수행할 것이다.  
서버 검증은 여태까진 사실 대충한 수준이지만 다른 예시를 많이 찾아볼 생각이다.  




