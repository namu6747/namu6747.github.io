---
title: 11. 오프라인 관련 - Betty
author: namu6747
date: 2022-09-06 09:11:00 +0900
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

나름 뿌듯했던 체크인 좌석 출력파트다.  
오랜만에 2중 for 문을 사용하여 추억을 환기했던 시간이다.  

이 파트 하나로  
템플릿 리터럴, ajax 동기화 처리, js, jquery 에 대한 이해도가 대폭 상승했다.  

<br/>
<hr/>

## 오프라인 화면
<br/>

![Desktop View](/assets/img/betty/offline/room.png)

앞으로 소개할 건 네모 박스 하나에 빨간색 배경을 입히기 위한 작업들이다.  

<br/>
<hr/>

## 체크인과 체크아웃
<br/>


```jsx
$('#checkInBtn').on('click',function(){
			let seat = $(this).data('seat');
			
    $.ajax({
        type: 'post',
        url: "${path}/offline/${user.id}/checkIn",
        data: { seat : seat },
        success: function(data){
            alert("클라이언트 요청  "+ seat + " 서버 응답 "+ data);
            location.reload();
        },
        error: function(data){
            alert("요청실패");
        } 
    })
})

$('#checkOutBtn').on('click', function(){
    
    $.ajax({
        
        type: 'post',
        url: '${path}/offline/${user.id}/checkOut',
        success: function(data){
            alert('서버 응답 : '+ data);
            location.reload();
        },
        error: function(data){
            alert('요청 실패' + data);
        }
    })
})
```

<br/>

```java
@PostMapping("checkIn")
@ResponseBody
public int checkIn(@PathVariable String id, Integer seat) {
    int result = os.checkIn(id, seat);
    return result;
}

@PostMapping("checkOut")
@ResponseBody
public int checkOut(@User Member user) {
    int result = os.checkOut(user.getId());
    return result;
}
```

Restful 방식으로 구성하고 싶었으나 단순 CRUD 이외의 영역엔 머리가 안 따라줬다.  

체크인 버튼을 누르면 chk_real 테이블에 기입되고  
체크아웃 버튼을 누르면 chk_real 에서 삭제와 동시에 chk_log 테이블에 추가된다.  
이는 DB 트리거로 구성되어 있다.  

Delete 방식의 전송을 사용하면 될 것 같은데,  
뭔가 애매한 게, 체크인 체크아웃이란 단어에 너무 매몰되어 그런 것 같다.  
Rest 에 집착하는 내 모습에 대해 생각이 많다.  

하나의 테이블에서 이루어지는 게 아닌,  
연관 관계가 이루어진 테이블에 대해 다룰 때 이런 생각이 드나 보다.  


<br/>
<hr/>

## 본격 스터디존(체크인 상황) 출력
<br/>

**_우선 날코드에 주석을 달아보자_**

```jsx
<script>
    $(function(){
		
        // 세션에 저장된 사용자가 체크인 중인 지 판별한다.
        let checkUserCheckIn = checkReal();
		
        // 1 ~ 16 까지의 table 을 그린다. 
        initRoom();

        // 그려진 table 에 전체 체크인 목록을 가져와 빨간색 배경을 칠한다.  
        initSeats();
		
        // 좌석 박스를 클릭했을 때 active 상태로 변경시킨다.
        $('.seat .able').on('click', function(){

            // 현재 사용자가 체크인 중이라면 아무 이벤트를 발생시키지 않고 종료. 
            if(checkUserCheckIn) return;

            // 빨간색 배경이 칠해졌거나, 이미 체크한 좌석일 때 공석으로 전환
            if($(this).hasClass("disabled") || $(this).hasClass("active")){
                $(this).removeClass("active");
                return;
            }
			
            // 정상적으로 공석을 선택했을 때 active 상태로 전환
            $(this).addClass('active');

            // 체크인 버튼의 data-seat 속성에 좌석 번호를 넣는다.            
            let seat = this.innerText;
            $('#checkInBtn').data('seat', seat);
        })
    })
	
    
    function initRoom(){
        // table
        let room = "";

        // tr
        let row = "";

        // td
        let cell = "";

        // 4개의 행을 그린다.
        for(let i = 0; i <= 3; i++){

            // 행과 셀의 매개 변수다
            let k = i * 4;

            // 셀을 그리는 작업을 4회 반복한다.
            for(let j = 1+k; j <= 4+k; j++){

                // 2, 5번 째 칸을 빈 칸으로 만드는 분기문
                if(j==(1+k) || j==(3+k)){

                    // 템플릿 리터럴 내부에 js 변수를 사용한다.
                    // jsp el 과의 충돌 때문에 escape 작업('\')이 필요하다.
                    cell =`<td class="able" value="\${j}">\${j}</td>`;
                    cell +=`<td></td>`;
                } else{
                    cell =`<td class="able" value="\${j}">\${j}</td>`;
                }

                // 셀을 그릴 때 tr 에 바로바로 추가해준다. append 와 비슷하다.
                row += cell;
            }
            
            // 하나의 tr 이 완성되면 table 에 append 한다.
            room = `<tr>\${row}</tr>`;
            $('#room').append(room);
            
            // row 를 blank 로 초기화하지 않으면 매 행 누적 된다.
            row = "";
        }
    }
	
	function initSeats(){

        // 좌석 상황을 불러 온다.
        $.getJSON("${path}/offline/${user.id}/room",

            // data 는 체크인 중인 좌석 목록으로, List<Integer> 이다. 
            function(data){

                // td 의 value 가 체크인 중인 좌석 번호와 동일하다면, 
                // 공석 상태를 제거하고 빨간색 배경을 칠해준다.
                $(data).each(function(i,e){
                    $(`td[value='\${e}']`).switchClass('able', 'disabled');
                })
        })
	}
	
	function checkReal(){

        // 체크인 여부를 return 하기 위한 변수다.
        let isReal = "";
        $.ajax({
            // isReal 을 정상적으로 반환하기 위한 동기화 설정
            async: false,
            type: "get",
            url: "${path}/offline/${user.id}/checkReal",
            dataType: 'json',
            success: function(data){
                // true or false 를 반환한다.
                isReal = data;
            }
        })
        return isReal;
	}
</script>
```

물론 허점은 많다.  
active 시켰을 때 플래그를 세워 다른 공석을 선택할 수 없게 만들거나,  
active 된 좌석이 있을 때 다른 공석을 선택한다면 active 상태이던 좌석을 해제시키는  
그런 귀여운 로직이 더 필요하다.  

**하지만 이건 그저 노동일 뿐이므로 우선 생략했다.**  

오랜만에 이중 for 문을 써보고 템플릿 리터럴 안에 변수를 우겨 넣고 그런 건 중요하지 않다.  

_여기서 제일 큰 소득은 ajax 의 응답 데이터로 하여금 반환값을 전달하려할 때,  
동기화 설정을 적용했다는 거다._  

~~ajax 는 비동기 통신이다.~~ 라는 강박에서 벗어난 계기다.  


