---
title:  "github+jekyll 블로그에 disqus 적용 삽질기"
tags:
- blog
- disqus
---

# 들어가며
예전부터 블로그에 댓글을 달 수 있게 하고 싶었는데(누가 오겠냐만은...) 최근에 회사 동료분께 disqus라는 시스템이 있다는 이야기를 들었다. 댓글을 따로 관리할 필요가 없다고 하셔서, 한번 적용해 보고 싶어졌다. 그 때는 이런 사태가 올 줄은 몰랐던 것이었다....!

# 홈페이지 가입
[이곳](https://publishers.disqus.com/) 으로 접속하여, 아래쪽의 Engage를 클릭한다.

그 후에 메인 화면에서 start Using Engage를 선택하면 회원 가입 창이 나온다.
Username, Email address, password를 입력하면 가입할 수 있다.

# 세팅 
회원 가입 후에 Set Up Disqus On a New Site 라는 타이틀이 있는 화면에서, 사이트 이름과 블로그 이름, 카테고리를 설정할 수 있다. 모두 다 설정이 완료되면, 플렛폼을 설정하면 된다. 나는 github blog에 붙일 것이므로 Universal Code 카테고리를 선택한다.
카테고리를 선택하면 복사할 코드가 나온다. 코드를 그대로 복사한다. 나는 jekyll을 적용하였으므로 그걸 기준으로 설명하면, 폴더 내에  _include라는 폴더가 있다. 페이지 내부에서 사용할 수 있는 html 모듈을 만들 수 있는 페이지이다. comments.html 이라는 이름의 html 페이지를 하나 만들고 위의 코드를 그대로 복사 붙여넣기 하자

## 삽질(1)
코드를 자세히 읽어보면 그대로 복사 - 붙여넣기를 하면 되는 게 아니라, 내부의 변수값을 바꿔야 한다. 즉

~~~javascript
var disqus_config = function () {
this.page.url = PAGE_URL; // Replace PAGE_URL with your page's canonical URL variable
this.page.identifier = PAGE_IDENTIFIER; // Replace PAGE_IDENTIFIER with your page's unique identifier variable
};
~~~

이 부분에서 url / indentifier 값을 따로 줘야 한다. 이 값을 주는게 힘들었는데, 맨 처음에는
{% raw %}

~~~javascript
var disqus_config = function () {
this.page.url = "{{ page.url }}"; 
this.page.identifier = "{{ page.url }}";
};
~~~

이렇게 설정해줬었다. 그런 다음에, _layout/post.html 에서 환경변수에 

~~~
comments : true
~~~

를 지정해주었다. 모든 포스팅은 layout으로 post를 설정하기 때문에, 이렇게 하면 모든 포스트에 별 설정 없이 적용할 수 있겠지...?
그 다음에  {{ content }} 아랫 부분에 

~~~
{% include comments.html %}
~~~
를 추가해주었다.

이렇게 되면 comments.html에 써 둔 내용이 아랫부분으로 들어가게 된다.
자 그러면 문제 없이 되겠지...짠!

뭔가 잘 안 되었다.

### 이유
이유를 잘 모르겠다고 생각했다. url / identifier 에 한글 유니코드 인코딩이 들어가서 그런가? 만약 이 문제면 방법이 없었다. 포스트를 쓸때마다 UUID를 주는 거는 너무 번거롭고 귀찮을테니까 차라리 지칼 부트스트랩이나 옥토퍼스트를 붙여서 사용하는게 낫다고 생각했다.

멘탈을 부여잡고 구글링을 해 보니 다들 별 문제없이 붙이는 거 같았다. 이상하다. 왜 나는 안 되는 거지...일단은 post.html에 설정해 둔 값들은 남겨두고 구분자와 url을 살펴보기로 했다. 이쪽 문제인 거 같다.

## 삽질(2)
~~~javascript
var disqus_config = function () {
this.page.url = "{{page.url}}"; 
this.page.identifier = "{{page.url}}";
};
~~~

여기서 page.url을 site.url로 변경해서 돌려보았다. 역시 안 된다. 이것저것 삽질을 하다 보니 위의 셋팅만으로 localhost:4000 에서 댓글이 나오기 시작했다. (아직도 뭘 만져서 된건지는 잘 모르겠다. 허나 위의 세팅을 하면 로컬 화면에서 댓글을 볼 수는 있을 것이다)

그러나 모든 포스팅의 댓글이 하나로 합쳐져서 나오기 시작했다. 구분자가 제대로 동작하지 않는 거 같았다. 그러면 date를 넣어볼까?

~~~javascript
var disqus_config = function () {
this.page.url = "{{page.url}}"; 
this.page.identifier = "{{page.date}}";
};
~~~

page.date 변수는 페이지의 등록된 날짜를 가져오는 변수이다. 나는 하루에 한 개 이상의 포스팅을 하지 않으므로 (...) 저 변수 또한 유니크할거라고 생각했다. date를 넣어도 되지 않았다. 그러면 page.id 변수는?

~~~javascript
var disqus_config = function () {
this.page.url = "{{page.url}}"; 
this.page.identifier = "{{page.id}}";
};
~~~

page.id 는 유니코드로 인코딩되지 않은 한글값을 가져와서 구분자로 사용한다. 역시나 잘 되지 않는다. 여기서 문제를 깨달았다. 구분자가 문제가 아니구나.
잘 모르겠으니 disqus의 변수 페이지를 자세히 읽어 보았다. this.page.title이라는 변수가 있구나. 이걸 추가해봐야겠다. 

~~~javascript
var disqus_config = function () {
this.page.url = "{{page.url}}"; 
this.page.identifier = "{{page.id}}";
this.page.title = "{{ page.title }}";
};
~~~

역시나 효과가 없었다.

이쯤 되니 url이 문제인 거 같다는 생각을 지울 수가 없었다. 변수에 관한 docs를 읽어보니 url에 값을 주지 않으면 window.location.href의 값을 그대로 가져온다는 글이 있었다. 그러면 지워서 해볼까...?

지...지우니까 댓글이 포스팅마다 따로따로 나온다?! 그렇다. this.page.url에는 페이지의 풀 url을 넣어야 하는 것이었다.

최종적인 comments.html의 변수 세팅은 이렇게 되었다.

~~~javascript
var disqus_config = function () {
this.page.url = "{{site.url}}" + "{{page.url}}";
this.page.identifier = "{{page.id}}";
this.page.title = "{{ page.title }}";
};
~~~

그러나 여전히 로컬 환경에서만 댓글 페이지가 뜨고, 마스터에 푸시한 다음 리얼 환경에서는 댓글 페이지가 뜨지 않는 현상은 고칠 수 없었다. 여러 번 시도해 보고 의심하게 된 건, 작성한 페이지를 깃헙 블로그에서 빌드해서 보여 주는데, 변경사항이 없으면 빌드를 안 하는 게 아닐까...하는 생각이 들었다. 그러면 강제로 빌드를 해 줘야 하나?

근데 스테틱 페이지를 어떻게 빌드를...? 이런 생각을 하다 보니 설정을 고치면 될 거 같다는 생각이 들었다. 일단 가장 최근에 올린 포스팅 하나의 설정변수를

~~~
---
title:  "타이틀"
comments : true
tags:
- blog
- disqus
---
~~~

이렇게 바꿔서 마스터에 푸시를 해 보았다. 된...된다? 그렇다. 이유는 잘 모르겠지만 설정변수에 comments : true를 붙이면 되는 거 같다. 이게 빌드를 다시 해서 되는건지, _post.html의 설정변수에 comments : true를 붙이는 게 효과가 없는 건지는 잘 모르겠는데, 여튼 된다! (환호성)

그렇지만 포스팅을 쓸 때마다 설정변수에 값을 추가해주거나....아니 이거는 괜찮은데, 이전 포스팅 모두 설정변수에 값을 넣는 건 넘나 귀찮다...

## _config.yml 수정
config 파일을 수정하여 모든 포스팅의 설정변수에 디폴트 값을 넣어줄 수 있다.

~~~
defaults:
  -
    scope:
      path: "" // 모든 페이지의 글에
      type: "posts" // layout : post 인 글들만
    values:
    	// 이 두 줄의 값을 자동으로 세팅해준다. post 페이지에서 따로 세팅을 오버라이드 할 수도 있다.
      layout: "post" 
      comments : true
~~~

이렇게 해 주니 잘 나온다! 우와...감동...! 

## 결론
지칼 부트스트랩이나 옥토퍼스트를 쓰면 훨씬 짧은 시간에 이쁘게 만들 수 있었을 텐데 내가 뭘 하고 있는 거지...?!

그래도 뿌듯함은 비교할 수 없다. 다음에는 GA를 붙여볼까 고민하고 있다. 그 전에 포스팅을 열심히 해야지.

{% endraw %}

