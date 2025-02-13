---
title: 자바 제네릭
author: namu6747
date: 2022-07-30 09:02:00 +0900
categories: [공부, 자바]
tags: [자바, 제네릭]
---

# 제네릭
제네릭 타입이 없다면 Object 객체를 생성하여 이용할 수 있지만
이땐 명시적 형변환을 요하는 상황이 반드시 나타난다
결국 반드시 실수하는 경우가 생길 것이다 => RuntimeException
*제네릭이 등장하면서 자료형에 의존적이지 않은 클래스를 정의할 수 있다*
하지만 단순히 제네릭이 타입 의존성에서 벗어난다는 건 착각이다
컴파일 환경에서 raw타입과 달리 타입에 대한 제약이 생긴다
이 때문에 타입 검사가 엄격히 이루어져 컴파일 단계에서
Runtime Exception을 방지할 수 있다
이는 자바스크립트의 var와 비교해서 생각해보면 된다
런타임 환경에서의 제네릭 타입은 호환을 위해 타입 소거가 발생한다
(와일드 카드는 런타임 환경에서 타입이 실체화됨)

인스턴스 생성 시 타입 인자를 통해 자료형을 결정하는 것이 제네릭이다
보편적으로 사용하는 타입 매개변수
- E : Elements
- K : Key
- N : Number
- T : Type
- V : Value
매개변수화 타입도 타입 인자로 줄 수 있다
```
타입 인자를 제한했을 때 클래스 내부에서 
동일한 타입 매개변수로 선언된 필드에
제한의 기준이 되는 클래스의 메소드를 사용하여 접근할 수 있다
```
하나의 클래스와 하나 이상의 인터페이스에 대해 동시에 제한할 수 있다
정적 메소드의 제네릭은 직접 선언해줘야 한다
(제네릭 메소드 = 제네릭 정적 메소드)
제네릭 클래스는 인스턴스 생성 시 자료형이 결정되는 반면에,
제네릭 메소드는 메소드 호출시에 자료형이 결정된다

제네릭 메소드를 이용할 때 컴파일러는 전달되는 인자를 보고 타입 유추
이러한 자료형의 유추는 오토 박싱까지 감안하여 이뤄진다
제네릭 메소드도 타입 인자의 제한과 메소드 활용이 똑같이 이루어진다
자바7부터 컴파일러의 자료형 유추 범위가 넓어져서 
제네릭 메소드의 인자가 없더라도 메소드 부분의 제네릭 선언은 생략할 수 있다

## 와일드 카드
제네릭 선언된 정적 메소드의 기본형과
선언 없이 인자에 <?>를 작성하는 것은 상호 대체 가능한 동일한 메소드이다
Box<Object>를 매개변수로 작성하면 Box<Integer>도 받을 수 있을 것 같지만
Object와 Integer가 상속 관계라 할 지 언정 
Box<Object>와 Box<Integer>는 상속 관계가 아니다.

```Box<? extends Number> ```
Number 클래스와 그를 상속한 클래스만 가능한 와일드 카드의 선언
extends: 상한 제한, super: 하한 제한
매개변수화 타입에서 (Box<T> box) T의 범위를 제한한다.
제한이 없을 때의 안정성을 생각해보면, 
와일드카드는 어렵지만 절대 필요없진 않다.

*필요한 만큼만 기능을 허용하여, 
코드의 오류가 컴파일 과정에서 최대한 발견되도록 한다*

제네릭 메소드의 매개변수화 타입에 와일드 카드의 상한 제한을 선언하면
매개변수화 타입을 참조하는 변수를 대상으로 저장하는 기능(Setter)의
메소드 호출은 불가능하다

Box<? extend Toy> 의 경우 
Toy 클래스를 상속하는 다른 클래스의 인스턴스가 
 형 변환 되어 저장되었을 수도 있다. 
그러므로 컴파일러는 사전에 setter 메소드를 막아버렸다
컴파일 에러는 자바에서 막아버리는 것이다

반대의 겨우인 하한 제한에서는 getter 후 
특정 클래스 타입의 참조 변수에 대입하는 것을 막는다.

따라서 와일드 카드에서의 타입 제한은 
box가 참조하는 인스턴스를 대상으로 꺼내거나 
 저장하는 기능의 메소드 호출을 불가능하게 만든다.
```
public static void outBox(Box<? extend Toy> box) {};
public static void outBox(Box<? extend Robot> box) {};
```
제네릭 메소드의 정의로 Type Erasure를 피해간다.

제네릭 클래스를 상속할 때 슈퍼 클래스의 타입 인자에 대한 지정을 
서브 클래스에서 해줘야한다.
하지만 extends Class<String> 과 같이 상속과 동시에 지정해주면
서브 클래스를 제네릭으로 정의할 필요 없다.
인터페이스를 상속할 때도 모든 게 동일하게 적용된다

*Comparable 분석*
Collections.sort 를 사용하기 위해선 아래 메소드의 문장을 설명할 수 있어야 한다.
```public static <T extends Comparable<? super T>> void sort(List<T> list){};```
Comparable<Car> 인터페이스를 구현한 Car 클래스를 상속한 
 SubCar 클래스를 생각해보면 List<SubCar>가 대입 됐을때
```<SubCar extends Comparable<SubCar>> void sort(List<SubCar> list)```
위와 같이 변경되어 Super 클래스가 Comparable을 구현한 상태일 때
 SubCar로도 이 메소드를 사용할 수 있다.
