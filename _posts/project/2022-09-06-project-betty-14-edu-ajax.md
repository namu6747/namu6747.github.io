---
title: 14. 지식 공유 - ajax
author: namu6747
date: 2022-09-06 09:13:00 +0900
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
<br/>

&nbsp;**_ajax 로 hello world 는 할 줄 알아야지_**

처음 ajax 를 통해 서버와 통신하며 겪은 시행착오에 대한 내용이다.  
팀원들에게도 알려주려고 여러 경우의 수를 추가하여 작성했다.  

<br/>
<hr/>

# @RequestBody

## 아무 것도 모를 때
<br/>

일단 편하게, 생각나는대로 해보자.

json 객체를 선언하고,  
key 에는 쌍따옴표를 생략해도 된다고 했고,  
post 방식. 자주 사용하던 것부터 해봐야지.  
json 객체를 그대로 보내고,  
@RequestBody 로 받으면 되겠지?

```javascript
<script>
let data = { code : "text"};

$.ajax({
	type : "post",
	url : "${path}/uri",
	data : data
});
</script>

@PostMapping("uri")
public void test(@RequestBody String code){
	System.out.println(code) 
	출력 ==> "code=text" // 원하는 결과가 아님
}
```
<br/>

기본 컨텐츠 타입인 **"application/x-www-form-urlencoded"** 이  
JSON 객체를 "code=text" 로 변형시켰고,  
payload 를 그대로 읽는 @RequestBody 는 String 타입의 변수에  
payload 의 값을 그대로 전달했다.  

<br/>
<hr/>

## Content-type 이 문제인가?
<br/>

요즘 데이터는 json 으로 주고 받는게 대세라고 했는데,  
컨텐츠 타입을 지정해주지 않아서 이런 결과가 나왔겠지?  

```javascript

<script>
let data = { code : "text"};

$.ajax({
	type : "post",
	url : "${path}/uri",
	data : data,
	contentType : "application/json",
});
</script>

@PostMapping("uri")
public void test(@RequestBody String code){
	System.out.println(code) 
	출력 ==> "code=text" // 원하는 결과가 아님
}

```

```
	RequestResponseBodyMethodProcessor 
	- Read "application/json;charset=UTF-8" to ["code=text"]
```
<br/>

로그엔 이렇게 나타난다.  
여기에 대해선 나도 의문이 있다.  
json 타입인걸 명시했는데 왜 기본 타입과 동일한 결과 나타나는 것일까?  

많은 생각이 드는데,  
JSON 객체를 전송한 거지, JSON 형태의 문자열을 보낸 게 아니다.  
**"application/json"** 은 payload 의 정보를 제공한 건 맞지만,  
payload 의 변형을 불러일으킨 건 아니다.  

컨텐츠 타입과 상관없이 JSON 객체는 HTTP 프로토콜로 데이터가 옮겨질 때,  
이미 "code=text" 형태로 지정된 것 같다.  

이 구조에서 컨텐츠 타입은 서버에서  
어떤 메시지 컨버터를 사용하여 요청 데이터를 해석할 지에 대한 힌트인 것 같다.  

<br/>
<hr/>

## 객체가 아닌 문자를 보내야되나?
<br/>

**stringify** 라는 메소드를 본 것 같은데,,,,  

```javascript

<script>
let data = { code : "text"};

$.ajax({
	type : "post",
	url : "${path}/uri",
	data : JSON.stringify(data),
	contentType : "application/json",
});
</script>

@PostMapping("uri")
public void test(@RequestBody String code){
	System.out.println(code) 
	출력 ==> {"code":"abc"} // 원하는 결과가 절대 아님
}

```
<br/>

여기까진 전부 String 타입에 @RequestBody 를 그대로 넣고 있다.  
"data : " 에 JSON 객체를 보내냐, JSON 형태의 문자열을 보내냐에 따라  
등호와 콜론이 스위치 되고 있다.  

<br/>
<hr/>

## 문자열을 보냈으니 컨텐츠 타입을 제거?
<br/>

JSON 형태의 문자열을 보낸 거라 JSON 객체가 아니라서 그럴까?  

```javascript

<script>
let data = { code : "text"};

$.ajax({
	type : "post",
	url : "${path}/uri",
	data : JSON.springify(data)
});
</script>

@PostMapping("uri")
public void test(@RequestBody String code){
	System.out.println(code) 
	출력 ==> "%7B%22code%22%3A%22text%22%7D="
	// 원하는 결과가 절대 아님
}

```

이 결과는 뭔가 여태까지의 실험을 다 부정당하는 느낌이다.  
이건 진짜 URL 인코딩이다.  
내 수준이 아직 바닥임을 알 수 있다.  
어디서 이 인코딩이 적용된 건지,  
위의 내용들은 어쩄든 모두 json 형태로 해석된 건지.  
당장 알 수가 없다.  

<hr/>

## 계속 문자가 순수하게 출력되네? 그러면 text?
<br/>

```javascript

<script>
let data = { code : "text"};

$.ajax({
	type : "post",
	url : "${path}/uri",
	data : "text",
	contentType : "application/json",
});
</script>

@PostMapping("uri")
public void test(@RequestBody String code){
	System.out.println(code)
	출력 ==> text // 원하는 결과
}

```
<br/>

이건 사실 생각보다 특수한 상황이다.  
@RequestBody 와 String 타입 매개변수가 하나만 존재하는.  
그냥 문자열을 그대로 받을 수 있다.  

<br/>
<hr/>

## 쓸모없어보이는데? 이걸 어디쓰지?
<br/>

테스트는 간단하게 하자는 나쁜 습관 때문에,  
문자열만을 주고 받는 심플한 상황을 연출했지만  
데이터를 주고 받을 때 json 형식의 문자열을 주고받는 데 익숙해져야 된다.  

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
});
</script>

@PostMapping("uri")
public Data testData(@RequestBody Data data) 
```
<br/>

`HttpMessageNotReadableException: JSON parse error:`

예외가 터진다.  
String 일 때는 key 여부 상관 없이 들어갔지만,  
객체의 필드에 바인딩 시키기 위해선 JSON 형식을 넘겨야 된다.  

<br/>
<hr/>

## 객체에는 객체를 넘겨야 정상이지
<br/>

자 그럼, 객체를 주고 받고, json 타입을 명시해준다면 되지 않을까?  

```javascript
<script>
let data = { code : "text"};

$.ajax({
	type : "post",
	url : "${path}/uri",
	data : data,
	contentType : "application/json",
});
</script>

@PostMapping("uri")
public Data testData(@RequestBody Data data) 
```

`HttpMessageNotReadableException: JSON parse error…..`

예외가 터진다.  
여기까지 봤다면 JSON 객체가 payload 에 실릴 때  
어떤 형식으로 변환되는 것을 알 수 있다.  
자바에서 배웠던 ObjectStream 이 생각난다.  
헤더같은 게 덧붙어서 이런 결과가 일어나는 것 같다.  

<hr/>

## JSON 객체를 문자열로 변환하면?
<br/>

```javascript
<script>
let data = { code : "text"};

$.ajax({
	type : "post",
	url : "${path}/uri",
	data : JSON.stringify(data),
	contentType : "application/json",
});
</script>

@PostMapping("uri")
public Data testMulti(@RequestBody Data data) {
  System.out.println(data.getCode()) 
  출력 ==> text // 완전 원하던 결과
}
```

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
});
</script>

@PostMapping("uri")
public void test(Data data){
	System.out.println(data.getDate()) 
	출력 ==> text // 바로 성공
}

```

@RequestBody 에서 컨텐츠 타입을 명시하지 않고 JSON 객체를 보냈을 때를 생각해보면  
"code=text" 라고 나왔다.  
근데 이건 사실 @RequestParam 이 아닌 **@ModelAttribute** 다.  

<hr/>

## 컨텐츠 타입을 json으로 지정했을 때
<br/>

```javascript

<script>
let data = { code : "text"};

$.ajax({
	type : "post",
	url : "${path}/uri",
	contentType: "application/json",
	data : data,
});
</script>

@PostMapping("uri")
public void test(Data data){
	System.out.println(data.getCode()) ==> null
}

```
<br/>

컨텐츠 타입이 json 인데 @RequestBody 를 사용하지 않았다.  
게다가 stringify 함수를 사용하지도 않았다.  
바인딩 실패.  

<br/>
<hr/>

## 순수 text를 전달했을 때
<br/>

아까처럼 String 타입에 urlEncoded 를 사용하여 text 자체만 보내보자.  

```javascript

$.ajax({
	type : "post",
	url : "${path}/uri",
	data : "text",
});
</script>

@PostMapping("uri")
public void requestParam(String code){
	System.out.println(code) 
	출력 ==> null // 어림도 없다.
}

```
<br/>

드디어 @RequestParam 이 적용되는 부분이다.  
하지만 ReqeustParam 은 Key=value 형태로 넘겨야 받을 수 있다.  

<br/>
<hr/>

## 다시 JSON 객체를 넘긴다면?

```javascript

<script>
let data = { code : "text"};

$.ajax({
	type : "post",
	url : "${path}/uri",
	data : data,
});
</script>

@PostMapping("uri")
public void requestParam(String code){
	System.out.println(code) 
	출력 ==> text
}

```

<hr/>

# 간단 요약

json 형태의 **문자열**을 보내고 싶다면  
@RequestBody 를 이용하라  

JSON **객체**를 보내고 싶다면  
@RequestBody 를 우선 생략하라  



<hr/>

# JQuery.ajax

```jsx
$.ajax({
    url: '${path}/plz', 
    // HTTP Method GET, POST, PUT, PATCH, DELETE
    type: 'put', 
    // json 객체 let code = { "code" : "code" }
	// 보내고 싶은 데이터
    data: code, 
    // 요청 데이터의 컨텐츠 타입을 지정
    // Get 요청의 경우 메시지 바디가 아닌 쿼리 파라미터로 전송
    // @RequestBody 가 해석할 수 없음.
    contentType: "application/json", 
	// 응답 데이터의 컨텐츠 타입을 지정
	// Accept를 application/json 으로 변경
	// json 일 경우, 서버에서 @ResponseBody 를 사용
    dataType: 'json', 
    success: function(result){
        $('.bookName').val(result.content);
    }
	// 상태코드가 4xx, 5xx 일 때 실행됨
    error: function(error){
        console.log(error);
})
```

`$.ajax()` 는 반환값이 있다.  
바로 jqXHR 이라는 객체인데,  
`$.ajax()`.done, .fail, .always 등의 메소드를 사용할수 있다.  