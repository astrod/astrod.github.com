---
layout: post
title:  "스프링 데이터 JPA - Specification까지"
tags: 
- TIL
- spring
category : 테스트
---

# 개요
스프링 데이터 JPA는 중복해서 발생하는 데이터 조회 쿼리를 제거하기 위해 만들어졌다. 예를 들어

~~~ java
public interface MemberRepository extends JpaRepository<Member, Long> {
	Member findByUsername(String username);
}
~~~
이와 같이 선언을 해 주면, MemberRepository의 CRUD 메서드를 어플리케이션 실행 시점에 스프링 데이터 JPA가 생성하여 주입해준다. 즉, 개발자가 직접 구현체를 개발하지 않아도 되어 편리하다.

그렇다면 직접 작성한 조회 메서드(MemberRepository.findByUsername)같은 메서드는 어떻게 해야 할까? 이런 메서드는 명명규칙에 맞춰서 추상 메서드를 정의하면, 자동으로 스프링 데이터 JPA가 메서드 이름을 분석하여 쿼리를 실행한다.

# 사용방법
 스프링 데이터 JPA는 간단한 CRUD기능을 지원하는 JpaRepository 인터페이스를 제공한다. 스프링 데이터 JPA를 사용하는 가장 단순한 방법은, 이 인터페이스를 상속바든ㄴ 것이다. 그리고 제네릭에 엔티티 클래스와 식별자 타입을 설정해주면 된다.
 
 
~~~ java
public interface MemberRepository extends JpaRepository<Member, Long> {}
~~~

위의 코드를 보면 JpaRepository에 엔티티(Member)와 식별자 타입(Long)을 지정했다. 이제부터 MemberRepository에서 JpaRepository에서 제공하는 기능들을 사용할 수 있다.

## 쿼리 메소드

### 메소드 이름으로 쿼리 생성
 이메일과 이름으로 회원을 조회하려면 다음과 같은 메소드를 정의하면 된다.
 
 ~~~ java
 public interface MemberRepository extends Repositroy<Member, Long> {
 	List<Member> findByEmailAndName(String email, String name);
 }
 ~~~
 이 메소드를 실행하면 스프링 데이터 JPA가 메소드 이름을 분석하여 JPQL을 생성 - 실행한다.
 
 ~~~ java
 select m from Member m where m.email = ?1 and m.name = ?2
 ~~~
 이와 같은 쿼리를 만들어준다. 물론 메소드 이름은 정해진 규격에 맞춰서 지어야 한다.
 
 
### JPA NamedQuery
  JPA NamedQuery는 메소드 이름으로 JPA Named 쿼리를 호출할 수 있다. XML에 query name을 지정한 다음에 JPA에서 쿼리 네임을 설정해주면 된다.
  
### @Query, 리포지토리 메소드에 쿼리 정의
리포지토리 메소드에 쿼리를 정의할 수 있다. 

~~~ java
public interface MemberRepository extends JpaRepository<Member, Long> {
	@query("select m from Member m where m.username = ?1")
	Member findByUsername(String username);
}
~~~  
메소드에 JPA 쿼리를 작성할 수 있다. 또는 네이티브 SQL도 사용할 수 있다. 이 경우에는 위치 기반 파라미터가 0부터 시작해야 한다.

~~~ java
public interface MemberRepository extends JpaRepository<Member, Long> {
	@query(value = "SELECT * FROM MEMBER WHERE USERNAME = ?0",
		nativeQuery = true)
	Member findByUsername(String username);
}
~~~

### 반환 타입
쿼리를 실행시켰을 때 결과가 한 건 이상이면 컬렉션 인터페이스를 사용하고, 단건이면 반환 타입을 지정해야 한다.

~~~ java
List<Member> findByName(String name); // 컬렉션
Member findByEmail(String email); // 단건
~~~

- 조회 결과가 없다면 컬렉션은 빈 컬렉션을 반환하고, 단건은 null을 반환한다.
- 단건을 조회했는데 결과가 2건 이상이면 NonUniqueResultException을 던진다.

### 페이징과 정렬
파라미터에 Pageable을 사용하면 반환 타입으로 List나 Page를 사용할 수 있다. 반환 타입으로 Page를 사용하면 스프링 데이터 JPA는 전체 건수를 조회하는 Count 쿼리를 추가로 호출한다.

정의 코드

~~~ java
public interface MemberRepository extends JpaRepository<Member, Long> {
	Page<Member> findByNameStartingWIth(String anme, PAgeable Pageable);
}
~~~

사용 예제 코드

~~~ java
//페이징 조건과 정렬 조건 설정
PageRequest pageReuqest = new PageRequest(0, 10, new Sort(Direction.DESC, "name"));

Page<Member> result = memberRepository.findByNameStartingWith("김", pageRequest);

List<Member> members = result.getContent(); // 데이터
int totalPages = result.gettotalPages(); // 페이지 수
boolean hasNextPage = result.hasNextPage(); //다음 페이지 존재 여부
~~~
## 명세(Specification)
 도메인 주도 설계에서는 명세라는 개념이 있다. 스프링 데이터 JPA는 JPA Criteria로 이 개념을 사용할 수 있도록 지원한다.
명세의 핵심 개념은 predicate인데, 이는 참이나 거짓으로 평가되는, 연산자로 조립할 수 있는 개념이다. 예를 들어 데이터를 검색하기 위한 제약 조건(where id = ?, date < ?)에서 제약조건 하나하나를 predicate라고 할 수 있다.
 
~~~ java
 public interface OrderRepository extends JpaRepository<Order, long>m JpaSpecificationExecutor<Order>
~~~
 이와 같이 JpaSpecificationExecutor인터페이스를 상속받는다. 사용 코드는 다음과 같다.
 
~~~ java
 import static jpabook.jpashop.domain.spec.OrderSpec.*;
 
 public List<Order> findOrders(String name) {
 	List<Order> result = orderRepository.findAll(
 			where(memberName(name)).and(isOrderStatus())
	);
	
	return result;
}
~~~


이를 사용하려면 memberName와 isOrderStatus를 정의해야 한다. 이는 OrderSpec안에 정의되어 있어야 한다. Specification을 정의하려면 Specification인터페이스를 구현하는 클래스(OrderSpec)에서 toPredicate메소드를 구현하면 된다. 

## 사용자 정의 리포지토리 구현
 사용자 정의 레포지토리를 구현하는 기능도 존재한다. 먼저 사용자 정의 인터페이스를 작성해야 한다.

~~~ java
public interface MemberRepositoryCustom {
	public List<Member> findMemberCustom();
}
~~~
다음으로는 사용자 정의 인터페이스를 구현한 클래스를 작성한다. 이 경우 이름을 짓는 규칙이 있는데
*리포지토리 인터페이스 이름 + Impl*로 지어야 한다. 이렇게 지으면 스프링 데이터 JPA가 사용자 정의 구현 클래스로 인식하게 된다.

~~~ java
public class MemberREpositoryImpl impliments MemberREpositoryCustom {
	
	@Override
	public List<Member> findMEmberCustom() {
		//...사용자 정의 구현
	}
}
~~~
이제 리포지토리 인터페이스에서 사용자 정의 인터페이스를 상속받으면 된다.

~~~ java
public interface MemberRepository extends JpaRepository<Member, Long>, MemberRepositoryCustom {

}
~~~

	






