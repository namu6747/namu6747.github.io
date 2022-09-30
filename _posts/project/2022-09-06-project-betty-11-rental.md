---
title: 11. 대여 관련
author: namu6747
date: 2022-09-06 09:10:00 +0900
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
14. [지식 공유 - ajax](/posts/project-betty-14-edu-ajax/)
15. [DTO, Form, VO, Entity](/posts/project-betty-15-object/)
16. [후기](/posts/project-betty-16-review/)

<br/>
<hr/>
<br/>

대여 예약, 수령, 반납에 대한 내용이다.  
"시간적 여유가 있었으면" 하는 아쉬움이 남아있다.  

<br/>
<hr/>


## 도서 예약
<br/>

![Desktop View](/assets/img/betty/rental/reserve.png)


```jsx
$('#reservBtn').on('click', function(){
    let isbn = '${book.code}';
    let memberId = '${user.id}';
    let date = $('#reserv').val();
    
    let data = { "memberId" : memberId, "isbn" : isbn, "date" : date };
    
    if(date == ''){
        return;
    } else {
        $.ajax({
            type: 'post',
            url: '${path}/rentals/reserve',
            contentType: 'application/json',
            data: JSON.stringify(data),
            dataType: 'json',
            success: function(data){
                alert(data.id+ ' 님의 예약 완료');
            },
            error: function(request,status,error){
                alert("fail");
            }	
            
        })
    }
    
})

```

```java
@PostMapping("reserve")
@ResponseBody
public ReserveBook reserve(@RequestBody ReserveBookDto reserveBookDto) {
    Integer num = bs.findExistNum(reserveBookDto.getIsbn());
    ReserveBook reserve = reserveBookDto.createReservBook(num);
    int result = rs.reserveBook(reserve);
    return reserve;
}
```

도서 상세 게시글에서의 대여 예약 장면과  
이벤트 핸들링이다.  
필요한 정보를 가져와 post 방식으로 비동기 요청을 보낸다.  
방법이야 가지각색이지만 json 형태로 시도했다.  

json 형태를 받기 위해 @RequestBody 와 Dto 객체를 이용했다.  
(쿼리문은 기본 CRUD 수준이라 설명은 생략)  

1. 도서의 재고가 존재하는지 확인
2. 대여 예약 테이블에 Insert

반환을 대여 예약 객체로 했는데 딱히 사용하진 않았다.  
ajax 를 사용하면서 가장 힘들었던 건,  
무엇을 반환하고 무엇을 출력해줄까에 대한 스트레스였다..   
그냥 숫자 하나를 반환하고 예외 처리만 잘해주는 게 세상 편한 느낌이다.  

<br/>
<hr/>

## 도서 수령
<br/>

<!--  -->
![Desktop View](/assets/img/betty/rental/offline.png)

```jsx
<form action="${path}/offline/${user.id}/receipt" method="post">
    <table class="table">
        <thead>
            <tr>
                <th colspan="3">도서명</th>
                <th colspan="2">예약일</th>
                <th>수령</th>
            </tr>
        </thead>
        <tbody>
            <c:choose>
                <c:when test="${!empty reserves}">
                    <c:forEach var="reserve" items="${reserves}">
                        <tr>
                            <td colspan="3">${reserve.title}</td>
                            <td colspan="2"><f:formatDate value="${reserve.date}"
                                    pattern="yyyy-MM-dd" /></td>
                            <td>

                                <button class="btn btn-danger btn-sm" name="code"
                                    value="${reserve.code}">수령</button>
                            </td>
                        </tr>
                    </c:forEach>
                </c:when>
                <c:otherwise>
                    <tr>
                        <td colspan="6">대여 예약된 책이 없습니다.</td>
                    </tr>
                </c:otherwise>
            </c:choose>
        </tbody>
    </table>
</form>
```
```java
@PostMapping("receipt")
@Transactional
public String rentalReceipt(@PathVariable String id, rentalDto rsv) {
    String code = rsv.getCode();
    rs.reserveCancle(id, code);
    Integer num = bs.findExistNum(code);
    rs.rentalBook(id, code, num);
    MemberCard mc = ms.findGradeById(id);		
    ms.updateLend(id, mc.getPremiumGrade());		
    return "redirect:/offline/"+id;
}
```

오프라인 화면은 나름 Betty 의 시그니쳐이다.  
사용자가 **"실제"**로 도서관에서 할 수 있는 행위 중 최소한을 화면에 그렸다.  

수령 버튼 자체에 ISBN 을 담고, 유저 아이디는 경로 변수에 담았다.  
대여에 필요한 정보는 사실 이게 끝이다.  

서버 쪽 로직은 미숙한 것 같지만  
Betty 는 더이상 손 볼 생각이 없기 때문에 이거로 만족해야겠다.  

1. 대여 예약 테이블에서 삭제하고,  
2. 다시 도서 코드를 통해 실물 재고를 확인하고,  
3. 대여 중 테이블로 옮기고,  
4. 멤버십 카드를 통해 대여료를 지불한다.  

이후 다시 오프라인 화면으로 리다이렉트 시킨다.  

<br/>
<hr/>

## 도서 반납
<br/>

![Desktop View](/assets/img/betty/rental/rental.png)

대여 예약 후 수령이 완료된 도서는 이렇게 반납 해야될 목록에 나타난다.  
로직은 도서 수령 쪽과 거진 동일한 수준이다.  
<hr/>
