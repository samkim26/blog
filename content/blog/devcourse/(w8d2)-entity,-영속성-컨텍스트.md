---
title: (W8D2) Entity, 영속성 컨텍스트
date: 2021-09-29 02:09:79
category: devcourse
draft: false
---

## Entity, EntityManagerFactory, EntityManager

### Entity

RDB의 Table과 매핑되는 객체

### **EntityManagerFactory**

- Entity를 관리하는 EntityManager를 생산하는 공장이다.
- Thread Safe하다.

### EntityManager

- Entity를 저장하고, 수정하고, 삭제하고, 조회하는 (CRUD) 등 Entity와 관련된 모든 일을 처리한다.
- Thread Safe 하지 않다. 여러 Thread에서 동시에 접근할 경우 동시성 이슈가 발생할 수 있다.



## 영속성 컨텍스트(Persistence Context)

- Entity를 영구 저장하는 환경
- EntityManager는 Entity를 영속성 컨텍스트에 보관하고 관리한다.
- 특징
  - 1차 캐시 - Entity가 영속성 컨텍스트에 영속되면, 1차 캐시에 담기게 된다.
  - 동일성 보장 - 1차 캐시를 거치는 조회로 Entity의 동일성이 보장된다.
  - 트랜잭션을 지원하는 쓰기 지연
  - 변경 감지 - Dirty Checking (Dirty란 '상태의 변화가 생긴'의 뜻으로 이해)
  - 지연 로딩 - Entity 조회 시점이 아닌 Entity 내 연관관계를 참조할 때 해당 연관관계에 대한 SQL이 질의된다.

쓰기 지연 SQL 저장소 및 트랜잭션에 의해 아래와 같이 동작한다.

1. 트랜잭션이 커밋 되기 직전까지 모든 쿼리문은 영속성 컨텍스트 내부의 쓰기 지연 SQL 저장소에 저장된다.
2. 트랜잭션을 커밋하면 쓰기 지연 저장소에 저장된 쿼리를 DB에 보낸다.
3. 만약 트랜잭션 내부에서 오류가 나서 롤백을 해야한다면 쿼리를 보내지 않는다.

JPA에서는 Entity를 조회하면 해당 Entity의 조회 상태 그대로 **스냅샷**을 만들어 놓는다.
그리고 트랜잭션이 끝나는 시점에는 이 **스냅샷과 비교해서 다른점이 있다면 Update Query를 데이터베이스로 전달**한다.
이러한 상태 변경 검사의 대상은 **영속성 컨텍스트가 관리하는 Entity에만 적용**된다.



## Entity Life Cycle

- 비영속 (new / transient) : 영속성 컨텍스트와 전혀 관계가 없는 상태
- **영속 (managed)** : 영속성 컨텍스트에 저장된 상태
- 준영속 (detached) : 영속성 컨텍스트에 저장되었다가 분리된 상태
- 삭제 (removed) : 삭제된 상태



## 참고

- [더티 체킹이란?](https://jojoldu.tistory.com/415)

