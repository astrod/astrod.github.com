---
layout: post
title:  "[Codewars] Delete Nth occurrence of element"
tags : "algorithm"
---

* toc
{:toc}

# 문제
Enough is enough!

Alice and Bob were on a holiday. Both of them took many pictures of the places they've been, and now they want to show Charlie their entire collection. However, Charlie doesn't like this sessions, since the motive usually repeats. He isn't fond of seeing the Eiffel tower 40 times. He tells them that he will only sit during the session if they show the same motive at most N times. Luckily, Alice and Bob are able to encode the motive as a number. Can you help them to remove numbers such that their list contains each number only up to N times, without changing the order?

Task

Given a list lst and a number N, create a new list that contains each number of lst at most N times without reordering. For example if N = 2, and the input is [1,2,3,1,2,1,2,3], you take [1,2,3,1,2], drop the next [1,2] since this would lead to 1 and 2 being in the result 3 times, and then take 3, which leads to [1,2,3,1,2,3].

~~~ java
Example

  deleteNth ([1,1,1,1],2) // return [1,1]

  deleteNth ([20,37,20,21],1) // return [20,37,21]
~~~

- 문제를 약간 이해하기 어려웠다.
- 스토리를 읽은 뒤에야 문제를 이해할 수 있었다 -_-;;
- 뒤에 나오는 숫자 만큼만 앞에 배열에서 수가 반복되게 만들면 됨.
- 맨 처음에는 Array.map을 사용하려고 했지만, map은 배열의 숫자를 다르게 만들어 리턴할 수 없음. 결국 Array.filter를 사용하기로 마음먹음

# My Solution

~~~ java
function deleteNth(arr,x){
  var map = {};
  return arr.filter(function(value) {
    map[value] === undefined ? map[value] = 1 : map[value] += 1;
    if(map[value] <= x) {
      return value;
    }
  });
}
~~~

- Array.filter를 사용하여 필터에 맞지 않는 값을 걸러냄
- 초기에 map에 값을 세팅하는 줄이 삼항연산자가 약간 마음에 들지 않는 게 문제

# Best Solution
~~~ java
function deleteNth(arr,x){
  var count = {};
  return arr.filter(function(a){
    count[a] = ~~count[a]+1;
    return count[a]<=x;
  })
}
~~~
- 다들 삼항연산자가 마음에 들지 않는 건 매한가지
- 이 분은 다른 방법으로 해결함

~~~ java
function deleteNth(arr, x) {
  return arr.reduce(function(p, c) {
    if(count(p, c) < x) p.push(c);
    return p;
  }, []);
}

function count(arr, v) {
  var count = 0;
  arr.forEach(function(c) {if(c == v) count++;});
  return count;
}
~~~
- 어제부터 reduce를 사용한 코드를 많이 봄
- reduce에 대해 정리해봐야겠음