---
title: 13. 지식 공유 - ajax - Betty
author: namu6747
date: 2022-09-06 09:13:00 +0900
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

&nbsp;**_ajax 로 hello world 는 할 줄 알아야지_**

ajax 를 직접 사용해보며 겪은 시행착오에 대한 내용이다.  
팀원들에게도 알려주려고 여러 경우의 수를 추가하여 작성했다.  

<br/>
<hr/>

# @RequestBody

## 아무 것도 모를 때

아무 생각이 없다.  

대충 json 객체를 선언하고 음..  
key 값엔 쌍따옴표를 안해도 알아서 해준다고 하니깐 편하게 하자  
서버에 PostMapping 을 선언했으니 type 은 post .,.  
api 통신이니깐 보낼 데이터는 내가 선언한 data 객체를 보내자.  
json? 그거 @RequestBody 아닌가??  

```javascript
<script>
let data = { code : "text"};

$.ajax({
	type : "post",
	url : "${path}/uri",
	data : data,
	success : success
});
</script>

@PostMapping("uri")
public void requestParam(@RequestBody String code){
	System.out.println(code) ==> code=text // 원하는 결과가 아님
}
```

뭘까? code 의 출력 결과가 text 는 아니더라도  
code=text ?? 이건 좀 아닌데 콜론도 아니고.  
아무튼 키와 밸류가 합쳐서 들어왔구나 뭐가 문제지?  
key 를 “code” 로 일치시켰고 내가 원하는건 text 문자열 그자체인데  
왜 code=text 가 넘어오는 거지? 내가 이걸 왜 변형하고 있어야되는거지?  

<hr/>

## Content-type 이 문제인가?

아 !! 난 json 방식의 통신을 하고 싶은데

프로토콜 메시지 바디의 형식을 표현해주는 application/json 을 

요청 헤더에 적어주지 않아서 서버가 몰랐구나?

컨텐츠 타입을 지정해보자.

```javascript

<script>
let data = { code : "text"};

$.ajax({
	type : "post",
	url : "${path}/uri",
	data : data,
	contentType : "application/json",
	success : success
});
</script>

@PostMapping("uri")
public void requestParam(@RequestBody String code){
	System.out.println(code) ==> code=text // 원하는 결과가 아님
}

```

왜 결과가 똑같지?

“위 결과는 스프링에서

RequestResponseBodyMethodProcessor - Read "application/json;charset=UTF-8" to ["code=text"]

이 방식으로 해석한다. 하지만 이는 컨텐츠 타입을 지정하지 않았던 첫번째도 동일하게 수행됐다.”

<hr/>

## 객체가 아닌 문자를 보내야되나?

아 그러면.. 내가 객체를 보내서 그런걸까? 

그러면 stringify 메소드를 이용하여 JSON 형태의 “문자열”로 바꾼다면 해결되지 않을까?

```javascript

<script>
let data = { code : "text"};

$.ajax({
	type : "post",
	url : "${path}/uri",
	data : JSON.springify(data),
	contentType : "application/json",
	success : success
});
</script>

@PostMapping("uri")
public void requestParam(@RequestBody String code){
	System.out.println(code) ==> {"code":"abc"} // 원하는 결과가 절대 아님
}

```

이게 뭐지? 문자열로 변경하니깐 오히려 순수한 텍스트 그자체가 나타나네

어떻게 하라는 거지?

<hr/>

## 문자열을 보냈으니 컨텐츠 타입을 제거?

아,, 내가 문자열로 변환해서 보낸다면 JSON 객체가 아니잖아?

컨텐츠 타입을 제거하면 되지 않을까?

```javascript

<script>
let data = { code : "text"};

$.ajax({
	type : "post",
	url : "${path}/uri",
	data : JSON.springify(data),
	success : success
});
</script>

@PostMapping("uri")
public void requestParam(@RequestBody String code){
	System.out.println(code) ==> %7B%22code%22%3A%22text%22%7D= 
																// 원하는 결과가 절대 아님
}

```

문자열로 변환하고 컨텐츠 타입을 제거했으니

기본 인코딩인 application/x-www-form-urlencoded;charset=UTF-8 으로 처리되고 있구나

UTF-8 형식이긴한데, 뭔가 이상하게 해석된다?? 

저건 분명 인코딩 문제인데 ..

도대체 어떻게 해야 text라는 문자열만 전달할 수 있을까?

<hr/>

## 계속 문자가 순수하게 출력되네? 그러면 text?

짜증난다. 벌써 몇 번짼데 text 하나를 전달하지 못할까?

그냥 “text” 를 데이터에 넣고 전송해볼래

```javascript

<script>
let data = { code : "text"};

$.ajax({
	type : "post",
	url : "${path}/uri",
	data : "text",
	contentType : "application/json",
	success : success
});
</script>

@PostMapping("uri")
public void requestParam(@RequestBody String code){
	System.out.println(code) ==> text // 원하는 결과
}

```

뭐지? 왜 되는 걸까?

아 @RequestBody 라는 건 순수하게 메시지 바디를 읽는 건데 내가 String 타입으로 받고 있었으니 이건 당연한 결과구나.. 

뭐지? 그러면 그냥 값만 보내면 되지 왜 객체를 선언하는 무의미한 짓을 하는걸까?

참고로 순수 텍스트만 보낼때, 컨텐츠 타입은 제거해도 동일한 결과가 나타난다.

<hr/>

## 쓸모없어보이는데? 이걸 어디쓰지?

아 맞아. 나는 테스트를 한다고 String 을 받았지만, 결국 객체를 받을 일이 생기잖아?

객체를 직접 정의하고 테스트 해볼까?

음 일단 객체는 처음이니, 성공했던대로 가보자!

```javascript
@Setter @Getter
	static class Data {
		String code;
	}

(컨트롤러 클래스 내부에 정적 멤버 클래스를 선언했다)

<script>
let data = { code : "text"};

$.ajax({
	type : "post",
	url : "${path}/uri",
	data : "text",
	contentType : "application/json",
	success : success
});
</script>

@PostMapping("uri")
public Data testMulti(@RequestBody Data data) 
```

뭐지?

HttpMessageNotReadableException: JSON parse error:

이런 에러가 나타나네..

아,, 객체인데 key and value 로 주는 게 정석이잖아?

다시 data 객체를 넣어볼까?

<hr/>

## 객체에는 객체를 넘겨야 정상이지

객체끼리 주고 받고, 다른 설정은 건드린 게 없으니깐 당연히 되야겠지?

```javascript
<script>
let data = { code : "text"};

$.ajax({
	type : "post",
	url : "${path}/uri",
	data : data,
	contentType : "application/json",
	success : success
});
</script>

@PostMapping("uri")
public Data testMulti(@RequestBody Data data) 
```

HttpMessageNotReadableException: JSON parse error…..

왜 또 파싱할 수 없다는 걸까….

음… 이번엔 정말 json 컨텐츠 타입은 문제 없는 것 같다.

그러면 한번 stringify 메소드를 다시 한번 사용해볼까?

<hr/>

## JSON 객체를 문자열로 변환하면?

```javascript
<script>
let data = { code : "text"};

$.ajax({
	type : "post",
	url : "${path}/uri",
	data : JSON.stringify(data),
	contentType : "application/json",
	success : success
});
</script>

@PostMapping("uri")
public Data testMulti(@RequestBody Data data) {
  System.out.println(data.getCode()) ==> text // 완전 원하던 결과
}
```

우와 뭔진 몰라도 해석해주구나…  주신이의 RequestBody 정복 끝

<hr/>

## 정리

1. **RequestBody** 는 순수한 텍스트를 읽어준다.  
하지만 다양한 메시지 컨버터들에 치여 예상치 못한 결과가 나타나기도 한다.  
텍스트 자체를 전달하고 싶다면, 객체를 보낼 게 아니라 값만 전달해야 된다.  

2. 객체를 전달하고 싶다면 클라이언트 측에서 **stringify** 메소드를 사용해야 된다.  
**RequestBody** 의 역할은 JSON 타입 자체의 해석이 아닌, JSON 형태의 문자열을 해석해주고
Jackson 라이브러리의 힘을 빌려 필드에 바인딩 해주는 것.

3. json 형태의 문자열은 일반 문자열과 구분할 수 없다.  
클라이언트 측에서 메시지 바디에 들어갈 문자열이 json 타입임을 선언해줘야하는데,   
이 바디에 대한 정보를 알려주는 HTTP 프로토콜의 영역이 Header 이다.  
Header 의 내용 중 하나가 Content-type 인데  
**Content-type** 은 HTTP 바디가 어떤 MIME 타입임을 알려준다.  
그래서 Content-type 이 "**application/json**" 인 **문자열**을  
@RequestBody 와 jackson-databind 가 해석 후 인자에 바인딩한다.  
(참고로 http message body 는 payload 라고도 불리는 걸로 알고 있다.)

<br/>
<hr/>
<br/>

# @RequestParam

**_ajax는 ajax인데,, ajax는 json 통신 아닌가?_**  

(@RequestParam 의 경우 생략 가능한 어노테이션이다.)

## 컨텐츠 타입을 명시하지 않았을 때

```javascript
ContentType 명시하지 않을 시 기본 값 : application/x-www-form-urlencoded

<script>
let data = { code : "text"};

$.ajax({
	type : "post",
	url : "${path}/uri",
	data : data,
	success : success
});
</script>

@PostMapping("uri")
public void requestParam(Data data){
	System.out.println(data.getDate()) ==> text // 바로 성공
}

```

urlencoded 형태로 인식되기 때문에 key, value를 영리하게 해석하고 넣어준다.

<hr/>

## 컨텐츠 타입을 json으로 지정했을 때

```javascript

<script>
let data = { code : "text"};

$.ajax({
	type : "post",
	url : "${path}/uri",
	contentType: "application/json",
	data : data,
	sucess : success
});
</script>

@PostMapping("uri")
public void requestParam(Data data){
	System.out.println(data.getCode()) ==> null
}

```

@ResponseBody 의 힘을 빌리지 못해 해석할 수 없어 null 이 발생한다.

<hr/>

## 순수 text를 전달했을 때

순수 text 니깐 key,value 형태가 아니니 Json 형태도, json도 아니다.

contentType 을 제거하자.

이 형태론 객체의 필드도 알아낼 수 없을테니, 객체가 아닌 문자열로 받자.

```javascript

$.ajax({
	type : "post",
	url : "${path}/uri",
	data : "text",
	sucess : success
});
</script>

@PostMapping("uri")
public void requestParam(String code){
	System.out.println(code) ==> null 어림도 없다.
}

```

여기서 ContentType을 제거하더라도 마찬가지다.

<hr/>

## 다시 JSON 을 넘긴다면?

```javascript

<script>
let data = { code : "text"};

$.ajax({
	type : "post",
	url : "${path}/uri",
	data : data,
	sucess : success
});
</script>

@PostMapping("uri")
public void requestParam(String code){
	System.out.println(code) ==> text
}

```

결국 다시 key, value 형태로 넘겨주니 해결됐다.

비록 내가 넘긴 건 json 이지만..

<hr/>

## 정리

```
@RequestParam 으로 데이터를 전달하기 위해선 반드시 key,value 형태여야 가능하다.

내가 보내는 메시지 바디의 형식이 JSON 임을 명시했다면 @RequestBody,
명시하지 않았다면 @RequestParam 으로 처리하면 된다.
```

<hr/>

# JQuery.ajax

```javascript
$.ajax({
    url: '${path}/staff/books/plz', 
    // 요청 url. Mapping이 Method와 함께 일치해야함
    type: 'put', 
    // HTTP Method GET, POST, PUT, PATCH, DELETE
    data: code, 
    // json 객체 { "code" : "code" }
    contentType: "application/json", 
    // @RequestBody 가 해석할 수 있도록 지정 (요청 관련)
    // Get 요청의 경우 메시지 바디가 아닌 쿼리 스트링으로 전송되어
    // @RequestBody 가 해석할 수 없다.
    dataType: 'json', 
    // @Responsebody 해석(응답 관련)
    // Accept를 application/json 으로 변경하여 반드시 json만 받음
    // 이 부분은 어짜피 @ResponseBody 를 선언한 메소드의 반환값은
    // 별도의 Accept-header 가 없다면 알아서 json으로 반환하기 때문에
    // 당장 명시하지 않아도 지장 없을 것
    success: function(result){
        console.log(result.code);
        console.log(result.title);
        console.log(result.content);
        $('.modal-title').text(result.title);
        $('.bookName').val(result.content);
    }
    // 일반 속성 접근법으로 객체에 접근할 수 있고 배열일 땐 [idx] 사용
    fail: function(error){
        console.log(error);
    //fail 은 서버에서의 예외가 전파됐을 때 success 대신 실행됨.
})
```
