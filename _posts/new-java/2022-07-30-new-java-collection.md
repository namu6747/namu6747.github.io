---
title: 자바 컬렉션 프레임워크
author: namu6747
date: 2022-07-30 09:03:00 +0900
categories: [공부, 자바]
tags: [자바, 컬렉션프레임워크]
---

# 컬렉션 프레임워크

*java.util.concurrent*
- 동기화된 컬렉션은 멀티 스레드 환경에서 하나의 스레드가
 요소를 안전하게 처리하도록 도와주지만, 전체 요소를 빠르게 처리하진 못한다
- 스레드가 작업할 때 락이 발생하기 때문에 병렬 처리가 불가능
- 자바에서는 이 문제를 해결하기 위해 concurrent 라이브러리를 제공
- 이 구현체들은 부분(segment) 잠금을 사용하기 때문에 병렬 작업 수행 가능
*hashcode*
- signature 에 Hash 가 붙어있을 때 hashcode 를 구현하면 접근 성능이 좋아진다


## List

*ArrayList*
- 동적 가변 배열
- 비동기 방식. 스레드 안전하지 못함
- 멀티 스레드 환경에서는 Collecion.sychronizedList 사용
- 특정 인덱스에 객체 삽입 시 전체 인덱스가 밀리므로 
 인덱스 변경이 빈번하다면 LinkedList 를 사용

*LinkedList*
- 각 노드가 데이터와 포인터를 가지고 연결된 구조
- 노드의 포인터가 이전 이후 노드와의 연결을 담당
- 인덱스 구조가 아니므로 삽입 삭제에 유리함

*Vector*
- Collecion.sychronizedList 로 대체

*Stack*
- Deque 로 대체

## Queue

*Queue interface*
- 인터페이스이므로 구현체가 필요하며 주로 LinkedList 를 이용함
- 노드 연결인지 오브젝트 배열인지의 여부에따라 LinkedList or ArrayDeque

*Priority Queue*
- FIFO가 아닌 특정 우선 순위에 따라 우선 순위가 높은 요소가 먼저 삭제
- Default로 낮은 숫자가 높은 우선 순위를 가진다
- 참조 타입일 경우 Comparator 또는 Comparable 을 통해 정렬 방식을 구현
- null 을 허용하지 않는다

*Deque interface*
- Queue 의 삽입 삭제가 한 쪽에서만 가능하다면 Deque 는 양쪽에서
 삽입 삭제가 가능하다
- 사용 방식에 따라 Stack 이 될 수 있고 Queue 가 될 수 있다

*ArrayDeque*
- 사이즈 제한이 없는 가변 배열이며 null 비허용, 비동기 원형 Queue 방식
- 성능상 Stack, LinkedList 를 대체할 수 있다. 기존 Queue 대신 사용하면 된다

## Set

*Set*
- 집합. 중복 저장 불가, 데이터의 순서를 보장하지 않음
- equals 를 이용한 중복 검사

*LinkedHashSet*
- 입력 순서를 보장함
- LinkedHashMap 을 통해 구현되어 있다

*HashSet*
- hash 알고리즘을 사용하여 검색 속도가 빠르다
- HashMap 을 통해 구현되어 있다

*TreeSet*
- 정렬을 적용할 수 있어 특정 구간의 집합 요소를 탐색할 때 유용하다
- Tree 자체가 일정 순서에 의해 정렬된 자료구조
- TreeMap 을 통해 구현되어 있다

## Map

*Map*
- 값을 키애 매핑하는 객체다
- key 중복 저장 불가, 데이터의 순서를 보장하지 않음
- equals 를 이용한 key 중복 검사

*HashMap*
- 정렬되지 않은 Map 제공
- 하나의 null key 와 다수의 null value 를 허용
- 멀티 스레드 환경에서는 ConcurrentHashMap 사용

*HashTable*
- ConcurrentHashMap 으로 대체

*LinkedHashMap*
- 입력 순서를 보장함
- 변경은 느리지만 HashMap 보다 빠른 조회 성능

*TreeMap*
- 정렬 된 Map
- Red Black Tree 로 구현되어 있다
- Default로 낮은 숫자가 높은 우선 순위를 가진다
- 참조 타입일 경우 Comparator 또는 Comparable 을 통해 정렬 방식을 구현







