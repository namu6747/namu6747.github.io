---
title: 자바스크립트 오브젝트 모델
author: namu6747
date: 2022-05-22 09:11:00 +0900
categories: [공부, 프론트엔드]
tags: [자바스크립트, DOM]
---

DOM(Document Object Model)

HTML은 사람이 편리하게 다룰 수 있는 문자열이지 
컴퓨터가 알아듣고 사용하기에 좋은 언어는 아닙니다

HTML을 동적으로 보다 효율적으로 변경하기 위해서 
HTML 문서(Docuemnt) 를 자바스크립트가 이해할 수 있는 
객체(Object) 의 형태로 모델(Model)링 하여 
자바스크립트에서 조작을 할 수 있도록 만든 
⭐️ interface가 바로 이 DOM(Document Object Model)인 것입니다.

이후 DOM API를 최대한 편하게 쓰기 위해서 노력했고 
jQuery라는 선구자가 있어서 DX를 개선을 했기에 
document.querySelectorAll과 같은 API가 표준 API가 될 수 있었죠. 
하지만 DOM API를 그냥 쓰는 것은 좋은 방법은 아닙니다.

지금도 마찬가지입니다. 가급적 DOM API를 사용하지 않고 
다른 방식으로 개발을 하려고 하고 있죠. 
HTML은 사람이 읽기 편하고 생성하기 편한 문자열로 되어 있으나 
DOM은 컴퓨터가 이해할 수 있도록 하나씩 조작을 해야하도록 만들어졌습니다.

그러니 현대의 프레임워크에서는 HTML을 조작하는 감각으로 DX를 제공하고 
내부에서는 VirtualDOM과 같은 방식을 이용해서 
DOM API 사용을 최적화 하려고 하고 있죠.

그렇기에 현재는 DOM API의 기본적인 선택과 조작, 이벤트 연동, 노드의 추가 삭제등에 
대해서 DOM를 다룰 일은 별로 없습니다.

프레임워크를 쓰고 있다면 주로 .getBoundingClientRect와 같이 UI를 고도화하기 위한 
UI속성들을 조회하거나 .scrollIntoView나 animate와 같은 UI를 다루는 방법들을 알아야 합니다.

프레임워크를 떠나 모두에게 배포를 하는 UI 컴포넌트를 만들어야 한다면 
DOM API를 깊게 알아야 할 수도 있습니다. 특정 프레임워크에 종속이 되면 안될테니까요.

아니면 직접 프레임워크나 라이브러리를 만들어야 한다면 
DOM API에 대한 깊은 이해가 필요하겠죠.

리액트는 가상 돔을 채용하여 대중화시켰다.

DOM 위에 BOM이 있다.
DOM인 window.document 외에도
navigator, screen, history, loaction, console 등이 있다.

alert => window.alert , document.write => window.document.write 처럼
최상위에 있는 window 객체를 편의를 위해 숨겼을 뿐이다

DOM을 이용해 javascript에서 node를 제어하기 위해서 선택자를 불러와야 한다.
단일 선택자와 다중 선택자로 나뉘고 다중 선택자는 배열에 저장된다.
배열에 저장되므로 반복문 등으로 활용할 수 있다. 

*Node* By~를 제외하면 선택자 입력이 css와 같고 선택자를 따옴표로 감싸준다.
getElementById
querySelector

getElementsByClassName
getElementsByName
getElementsByTagName
querySelectAll

content가 존재하는 요소를 불러왔을 때 몇 가지의 경우가 존재한다.
console.log(node); => 요소 전체가 출력됨.
console.log(node.innerHTML); => 해당 요소 계층 자식들의 값이 tag를 포함에 출력됨
console.log(node.textContent); => tag값을 제외한 text가 출력됨
console.log(node.innerText); => textContent와 같지만 trim()이 적용된 값이 출력됨

DOM X javascript를 이용해 요소의 style 값도 조정가능함. inline tag로 적용됨.
css는 콜론만 있으면 되지만 javascript는 equal에 속성값을 문자열로 적어야함.(변수와의 구분)
css : <style>node{color:black;}</style>
javascript : node.style.color = "white";

해당 요소의 속성 값을 얻고, 추가할 수 있음.
getAttribute("att-name"), setAttribute("attribute-name","attribute-value")

javascript로 HTML 문서 내 DOM을 활용할 때 tag 요소 뿐만 아니라 content값도 작성할 수 있다.
ele = document.createElement("any");
ele.innerHTML = "";
같은 방식으로, 생성된 ele 요소에는 여태까지 다뤘던 요소의 모든 것들이 적용된다.
비슷한 것으로,
document.createTextNode(); 는 text 정보가 저장된 객체를 생성한다.
?element를 생성한 것과 달리 style 속성은 따로 적용되지 않는 것 같다.
text를 생성할 일이 있을 때 append, pretend method로 위치 지정

create가 된다면 remove도 된다.
removeChild로 기준이되는 요소를 정한 뒤 순차적으로 제거할 수 있다.

기준이 되는 요소를 정한 뒤 부모, 자식 요소를 찾을 수 있음.
node.(childNodes, firstChild, lastChild, parentNode, nextSibling, previousSibling)
child, parent, sibling 정도만 기억하면 됨.

form filter selector
input:type 형식으로 선택 가능


property는 attribute에 대한 DOM 안에서의 표현이다.
DOM 안에 존재하고 동적이다.
