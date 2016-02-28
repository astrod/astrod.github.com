---
layout: post
title:  "jekyll과  github으로 블로그 작업 기록"
tags : 
- blog
- jekyll
---

# 동기
사실 별 이유는 없었고, 블로그를 하나 가졌으면 했다. 어떤 걸로 블로그를 만들지 고민하다가, 우연한 기회에 github으로 블로그를 만들 수 있다는 걸 알게 되었다.

# jekyll
jekyll은 텍스트 파일을 블로그로 만들어주는 기능을 한다. github와 연동하여 사용하면 깃헙에 쉽게 본인의 사이트를 업로드 할 수 있다.

## install
맥 기준으로

~~~ java
sudo gem install jekyll
~~~

을 하면 jekyll을 설치할 수 있다.

## 계정 주소 페이지 생성
~~~ java
jekyll new 깃허브아이디.github.com 
~~~
을 하면 깃허브사용자명.github.com이라는 디렉토리가 생길 것이다.
이제 이 디렉토리를 깃 레퍼지토리에 push하면 깃허브를 사용할 수 있다.

## 로컬 서버 띄우기
~~~ java
cd 깃허브사용자명.github.com
jekyll serve --watch
~~~
이렇게 하면 로컬에 서버를 띄울 수 있다.
localhost:4000으로 접속하면 로컬에 뜬 서버가 보일 것이다. 사용자명.github.com 내의 파일을 수정하면 로컬 서버에 즉각적으로 수정 내역이 보이기 때문에 편리하게 사용할 수 있다.

## 디렉토리 구조
설정파일은 _config.yml이다. 설정, 혹은 전역 변수를 추가하려면 이 파일에 추가하면 된다. 그리고 자신이 작성할 블로그글은 _post안에 YYYY-MM-DD-포스트명.md 로 추가하면 된다. 디렉토리 구조는 다음과 같다.

- config.yml // 설정파일
- _layouts // 기본 레이아웃 폴더
- _posts // 포스트를 관리하는 폴더
- _site // 빌드된 블로그 파일이 존재하는 곳
- index.html // 메인 페이지 html

## 포스팅
 위에서 명시했듯이, 포스팅을 하기 위해서는 _posts폴더 내에 YYYY-MM-DD-포스트명.md 파일을 추가하면 된다. 파일 내에는 파일이 어떤 레이아웃을 사용할지, 어떤 타이틀을 사용할지, 태그는 어떤 걸 추가할지에 대해 명시해야 한다.
 
 ~~~ java
 ---
 layout : post
 title : Hello, Jekyll
 ---
 ~~~
 각 블로그 포스트에는 레이아웃과 타이틀이 반드시 들어가야 한다.
 
## 참고 사이트
지킬 Document 사이트 : http://jekyllrb-ko.github.io/


