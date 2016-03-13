---
layout: post
title:  "@Transactional 어노테이션"
tags : "spring"
---

# 동기
회사에서 배치 관련 작업을 하다가, 트렌젝션 관련한 이슈를 만난 적이 있었다. JPA를 사용하여 코딩을 하고 있었는데, 트렌잭션이 밀릴까봐 업데이트를 칠 때마다 sleep을 조금씩 주었는데, JPA를 사용하다 보니 업데이트를 치는 부분이 하나의 트렌젝션으로 모두 묶여서 배치가 끝나는 순간 DB로 날아갈까봐 걱정이 되었다.그래서 @Transactional 어노테이션을 사용했었는데, 트렌젝션을 한번쯤 정리해야 할 거 같다는 생각이 들었다.

# @Transactional
선언적 트렌젝션을 사용하는 방법은 두 가지가 있다. 첫 번째는 tx 네임스페이스를 사용하는 건데, 이 글에서는 그 방법에 대해서는 다루지 않겠다. 

먼저 <tx:annotation-driven /> 태그를 사용하면 등록된 빈 중에서 @Transactional이 붙은 클래스나 인터페이스/메소드를 찾아서 트랜젝션 어드바이스를 적용해준다.

~~~ java
@Transactional
public interface MemberDao {
    public void add(Member m);

    public void add(List<Member> members);

    public void deleteAll();

    @Transactional(readOnly=true)
    public long count();
}
~~~ java

- 인터페이스에 @Transactional을 붙이면 인터페이스 안에 모든 메소드에 적용된다.
- @Transactional 을 메소드에 붙이면 메소드 단위에만 적용할 수도 있다.
- 우선순위는 클래스의 메소드 > 클래스 > 인터페이스의 메소드 > 인터페이스 순으로 설정이 우선적으로 적용된다.