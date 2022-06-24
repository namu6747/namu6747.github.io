---
title: 자바스크립트
author: namu6747
date: 2022-05-22 09:11:00 +0900
categories: [공부, 프론트엔드]
tags: [자바스크립트, 프론트엔드]
---

정수만 입력받고 싶다면 불필요한 조건을 만들지 말고 isNaN부터 활용해주자.
선언은 사용하는 위치 가까운 곳에 둔다.
자바스크립트에서는 하이픈(-)을 사용할 수 없다.

Number()=숫자로만 구성된 문자열을 실수화

javascript에서 문자열 + 숫자는 문자열이다.
숫자끼리 먼저 더하도록 하고 싶으면 괄호 결합

inline tag 내 event 속성값은 순수 javascript code이다.

?함수 내에서 arguments[]를 이용하여 저장된 인자를 확인할 수 있다.

익명 함수는 function (s1,s2)가 아닌 (s1,s2) => {}; 로도 나타낼 수 있다.(중괄호도 생략)

함수 내에서 var 선언을 또 하면 블록 스코프 내 새로운 지역변수가된다.
var는 변수 중복 선언 가능, let,const는 불가능
최근에는 많은 개발자가 안전 등의 이유로 익명 함수를 선호하는 편.
익명 함수는 함수 뒤에서 호출 불가 절차따라 코드가 해당 줄을 읽을 때 생성
선언적 함수는 함수 코드 이전에도 호출 가능 코드 실행전에 생성됨 (호이스팅)

내장 함수에는 isNaN isFinite Number parseInt parseFloat String eval 등이 있다.

prototype: 메서드를 외부에서 추가하고 호출할 수 있다
string.prototype.trim() 내장 객체에서 trim() 도 메서드 중 하나이다.


한 계층에 여러 형제 요소가 존재할 때
last-child 선택자는 기준 점을 상대적으로 잡기 때문에 적용이 안 될 때가 있다.
first-child의 경우 h1:first-child 시 h1중 첫 번째를 사용하지만
last-child를 바로 적용한다면 
예를 들어,
<body>
 <h1></h1>
 <h1></h1>
 <h2></h2>
</body>
이 상황에서 h1:last-child는 h2를 선택하게 된다.
last-child 대신 last-of-type 를 이용하면 해결할 수 있다.

array.forEach(function(value, index, array)){};
forEach를 이용하면 배열의 값, index, 배열 자체를 읽을 수 있다.

map method를 이용해서 기존의 배열로 새로운 배열을 만들 수 있다.
filter method를 이용해서 기존의 배열에서 원하는 type만 뽑아서 새로운 배열을 만들 수 있다.

Math도 객체이다.
속성: PI, E, LN2, LN10, LOG2E, LOG10E, SQRT1_2, SQRT1_2
method: abs, max, min, round, ceil, floor, random, sin, tan, sqrt
abs:절대값 | max,min:수치 비교 | round:반올림 | ceil,floor:올림,내림 | random:0~1사이의 난수

Date 객체엔 현재 시각, 시각 지정, 국제 시각 등 종류가 많다.

Event 
event <=> function 연결 시 ( )붙이면 1회 즉시 실행됨. 
inline 내 event="", node.event = function,  node.addEventListener 
addEventListener는 하나의 event에 다양한 행동 가능 < 가장 현대적인 모델
event model의 parameters는 event object를 의미. 
주요 property: target, type, clientX,Y , screenX,Y ,  button
preventDefault() = return false
stopPropagation = 버블링 방지(하위 요소에 event 효과 전파)
캡처링이 먼저 실행되고 버블링 실행

흔히 아는 pop-up window 는 open()으로 만들 수 있음.

Interval method 는 일정 시간마다 지정한 함수를 반복적으로 실행시킬 수 있음.
setInterval(fucntion(){},ms);
Timeout은  설정한 시간이 흐른 뒤에 지정한 함수를 한 번만 실행함.
clearInterval, clearTimeout은 set method를 변수에 저장시킨 뒤 사용.


배열이 있을 때 반복문을 사용할 수 있고
반복문에서 배열을 사용할 때 of,in 등의 문법을 사용할 수 있다.
항상 중복되는 문장을 어떻게 축약할 지 고민해야한다.

.each() method는 배열의 반복처리를 간단하게 해결 가능.

애니메이션이 복잡할수록 JS가 유리하다.

attribute 는 HTML 요소의 추가적인 정보를 전달하고
"name = value" 쌍의 형식이다.


?구조체 함수.
*클래스:구조체에 구조체를 항상 인자로 가지는 함수
클래스를 통해 만들어진 결과물을 값과 동작을 함께 가지고 있는 것이
주위 사물과 유사하다고 하여 object라 부른다
객체의일부분만 재사용 하는 걸 상속받는다고 하고 extend를 통해
상속받은 걸 새로운 class명을 통해 활용하는 것을 추상화라고 한다.

?값을 변경할때 변수가 선언된 위치에서 부터 3~7줄 범위내에서 수정되며 
선언된 함수의 scope를 벗어나지 않는 값이라면 복사해서 값을 옮기는 행위는 리소스 낭비입니다.

let 보다 const를 쓰려 해라
함수형 프로그래밍은 불변성의 유지로  부터 시작된다.
Do Not This:
let foo = 100
...
foo = somthing(foo, "bar")

Do This:
const foo = 100
...
const new_foo = somthing(foo, "bar")

Array,Date의 Mutation Method을 가급적 사용하지 마라.
push, pop, shift, sort, reverse 등 객체를 변화시키는 method를 가급적  spread operator로 대체하거나
값을 복사해서 사용.
Do Not This:
const example = (arr:number[], date:Date) => {
  arr.push(4)
  arr.sort()
  date.setMonth(10)}

Do This:
const example = (arr:number[], date:Date) => {
  const new_arr = [...arr, 4]
  const sorted_new_arr = [...new_arr].sort()
  const new_date = new Date(date).setMonth(10)
  return [new_arr, new_date]}

가급적 object의 필드에 대입연산자를 쓰지 마라.
Do Not This:
const example = (obj:Object) => {
  obj.foo = 200}

Do This:
const example = (obj:Object) => {
  return {...obj, foo: 200}}

