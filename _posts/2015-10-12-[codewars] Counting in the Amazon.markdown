---
layout: post
title:  "[codewars] Counting in the Amazon"
tags : algorithm
---

* toc
{:toc}

# 문제
The Arara are an isolated tribe found in the Amazon who count in pairs. For example one to eight is as follows:

1 = anane 
2 = adak 
3 = adak anane 
4 = adak adak 
5 = adak adak anane 
6 = adak adak adak
7 = adak adak adak anane
8 = adak adak adak adak 

Take a given number and return the Arara's equivalent.

e.g.

countArara(3) 'adak anane'

countArara(8) 'adak adak adak adak'

~~~ java
문제를 맨 처음 보고 생각한 건 재귀함수를 사용할 수 있을 거 같다는 생각이었다.
종료조건은 1/2로 두고, 2의 배수가 아니면 anane를 호출하고, 2의 배수면 adak를 호출하는 식으로 작업을 했다.
~~~

# 나의 답
~~~ java
function countArara(n) {
    if(n === 1) return "anane";
    if(n === 2) return "adak";

    if(n % 2 === 0) {
      return countArara(n-2) + ' adak';
    } else {
      return countArara(n-1) + ' anane';
    }
}
~~~

`
아쉬웠던 건 초기조건을 제거하지 못한 것이다. 1 / 2 인 경우에 행하는 종료조건도 제거하고 싶었으나, 마음대로 되지 않았다.
`

# Best Solution
~~~ java
function countArara(n) {
  switch (n) {
    case 0:  return '';
    case 1:  return 'anane';
    case 2:  return 'adak';
    default: return 'adak ' + countArara(n-2);
  }
}
~~~
`스위치-케이스문을 사용하면 좀 더 깔끔하게 조건을 처리할 수 있었을 거 같다.
이 분은 나랑 반대로 앞에서부터 재귀를 돌렸는데 이렇게 하는게 좀 더 깔끔해 보인다`

