---
layout: post
title:  "스프링 데이터 JPA"
tags: 
- TIL
- spring
---

## 개요
스프링 데이터 JPA는 중복해서 발생하는 데이터 조회 쿼리를 제거하기 위해 만들어졌다. 예를 들어

`public interface MemberRepository extends JpaRepository<Member, Long> {
	Member findByUsername(String username);
}`
