---
layout: post
title: "utterances 적용"
tags:
- blog
category:
- etc
---

* toc
{:toc}

# 이슈사항
이젠 꽤 예전 일이다. 이전에 github page 에 스킨을 적용한 적이 있었다. 그 때 URL 에 한글이 들어가면 링크가 연결되지 않는 이슈가 있어서 어쩔 수 없이 URL 생성 설정
을 pretty 로 하지 못하였다. 그 이후로 어쩔 수 없이 쭉 찝찝하게 지내고 있었는데 최근에 깃허브 페이지에서 https 를 지원하게 되었다. https 로 전환하면서 URL 변경을 시도해보니 설정을 pretty 로 바꿔주는 것 만으로도 URL 이 제대로 동작하는 걸 확인할 수 있었다.

다시 이야기하면, 이 포스트는 이전에는 astrod.github.io/2018/05/28 이었다면, 지금은 astrod.github.io/blog/2018/05/28/utteranc-적용 으로 노출된다.

이렇게 러닝하는 블로그의 URL 을 변경하다 보니 다음과 같은 문제가 발생했다.

1. 구글 어널리스틱스가 제대로 동작하지 않는다.
2. 댓글 시스템(disque) 가 제대로 동작하지 않는다.

몇 명 찾아오지 않는 블로그이지만 댓글이 제대로 동작하지 않는다는 건 문제인 거 같아서 해결하려고 잠시 들여다봤는데, 그냥 이참에 이전 데이터는 모두 버리고 다른 라이브러리를 적용하기로 했다.

# utterances

[링크](https://utteranc.es/)

utteranc 는 깃허브의 이슈기능을 가지고 댓글을 생성해준다. 게시글 하나가 이슈 하나와 매핑되고, 게시글에 댓글을 달면 해당 이슈에 댓글이 달린다.
댓글 창은 iframe 으로 동작하며 해당 페이지가 로드될 때, 페이지의 URL, pathname, 혹은 title 을 가지고 issue search API 를 통해 해당 이슈에 달린 댓글을 로딩하여 댓글 창에 로딩시켜 준다.

만약에 내가 최초로 댓글을 달아서 이슈가 없다면? utterances-bot 이 자동적으로 이슈를 생성해준다고 한다.

utteranc 를 적용하려고 하는 이유는 다음과 같다.

1. 개발자 블로그이니 만큼 따로 번거로운 작업 없이 깃허브 계정만으로도 바로 댓글을 쓸 수 있었으면 좋겠다.
2. 익숙한 깃허브의 UI 를 보고싶다.
3. 다음에 또 이런 마이그레이션 작업이 필요할 수 있으니, 게시글과 댓글을 매핑하는 설정을 쉽게 변경할 수 있으면 좋겠다.

적용 방법은 위의 링크를 보면 자세히 나와있고, 간단히 이야기 드리면 다음과 같다.

## utterance app 을 설치한다
[링크](https://github.com/apps/utterances) 에서 utterance 앱을 설치한다. 우측 상단에 설치 버튼이 있다.
설치 버튼을 클릭하면 Repository accees 란에서 Only select repositories 를 선택한 후, 본인 github page 를 선택해주면 된다.

만약에 다른 래퍼지토리의 이슈 기능을 활용하고 싶다면, 다른 래퍼지토리를 선택해도 된다.

혹여나 fork 를 했다던가 래퍼지토리가 public 이 아니라면, public 으로 변경해주고 설정에서 issues 를 사용가능하게 체크해줘야 한다.

## Blog post <-> Issue 매핑 방식을 선택한다

위에서 이야기 했듯이, utterances 는 게시글 하나에 래퍼지토리의 이슈 하나가 연동되는 시스템이다. 즉, post 와 issue 를 매핑하는 방식을 택해야 한다.
다음의 다섯 가지 방식이 있다.

1. pathname
    - 포스트의 pathname 으로 이슈를 생성한다. 이 포스팅 같은 경우는 /blog/2018/05/28/utterances-적용 으로 이슈가 생성되고, 매핑된다.
2. page URL
    - 게시글의 URL 전체로 이슈를 매핑한다.
3. page title
    - 게시글의 제목으로 이슈를 매핑한다.
4. issue number
    - 이슈 번호를 가지고 매핑한다.
5. issue title contains specific term
    - 게시글 제목에 특정 단어가 들어가 있는지 체크하여 매핑한다.


나는 pathname 으로 했다. 이제 URL 이 바뀔 일이 없을거라고 믿었기 때문이다.

## Enable Utterances

댓글이 들어가야 될 장소에 다음 코드를 삽입한다.

~~~html
<script src="https://utteranc.es/client.js"
        repo="[ENTER REPO HERE]"
        issue-term="pathname"
        async>
</script>
~~~

선택한 매핑 타입에 따라 파라미터가 조금씩 달라진다. 내가 헤맨 부분은 repo 를 입력하는 부분인데, 다르게 입력하면 API 요청시에 래퍼지토리가 없다고 실패한다.
반드시 위에서 선택한 래퍼지토리의 pathname 을 넣어야 한다. 예를 들면, 내 블로그의 설정은 다음과 같다.

~~~html
<script src="https://utteranc.es/client.js"
        repo="astrod/astrod.github.com"
        issue-term="pathname"
        async>
</script>
~~~

이제 댓글을 입력할 수 있다.

# 잔여 이슈

적용 후 만족스러웠지만 해결하지 못한 이슈가 몇 개 있다.

## 배경색
내 블로그는 배경이 약간 누리끼리한데, utterances 에서 불러오는 iframe 은 순백색이다.
그래서 UI 가 아름답지 못하게 나온다. iframe 안의 CSS 를 변경하려고 여러 방법을 사용해 보았는데, host 가 동일한 경우가 아니면 변경이 어려운 거 같았다.
그냥 내 블로그의 배경을 흰색으로 변경하여 문제를 해결했다.

utterances 의 CSS 를 바꿀 방법이 있으면 좋았을 텐데.

## 이전 댓글 마이그레이션
이전 댓글을 마이그레이션 할 수 있으면 좋을 거 같은데, 방법을 잘 모르겠어서 마이그레이션 하지 않고 새출발하기로 결심했다.
이 블로그에서 그래도 사람들이 읽고 댓글 달아주는 글은 딥러닝 포스팅 몇 개 밖에 없었는데 이제 그 몇개 안되는 댓글도 다 날아갔다.

댓글 달아주신 분들 죄송해요.