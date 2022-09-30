---
title: TEMP JPA - 인프런
author: namu6747
date: 2021-09-27 09:01:00 +0900
categories: [공부, 스프링]
tags: [자바, 스프링, 웹]
---

https://docs.spring.io/spring-framework/docs/5.3.22/reference/html/data-access.html#orm

스프링 프레임워크는 JPA 와의 통합을 지원한다.  
리소스 관리를 위한 native hibernate,  
dao 구현체, 트랜잭션 전략 을 지원한다.  

ORM 툴에 관한 것들을 의존성 주입을 통해 설정할 수 있다.

ORM 을 사용할 떄의 이점

- 간단한 테스트
- 공통 데이터 접근 예외
- general resouce management
- 트랜잭션 관리

JPA 를 사용하기 위해서는   
**org.springframework.orm.jpa** 의존성 추가

JPA 구현체인
**hibernate-entitymanager** 추가

스프링 환경에서 JPA 세팅을 위한 세 가지 방법

LocalEntityManagerFactoryBean  
이 객체는 JPA 만 사용하는 환경에서 EntityManagerFactory 객체를 생성하고,
PersistenceProvider 를 자동으로 찾아 사용한다.  

Obtaining an EntityManagerFactory from JNDI  

LocalContainerEntityManagerFactoryBean  


spring-orm, hibernate-entitymanager, hikariCP 만 있을 때,  
빈이 정상적으로 생성되었다.  