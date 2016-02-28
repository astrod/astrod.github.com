---
layout: post
title:  "[Codewars] Implementing Array.prototype.groupBy method"
tags : "codewars"
---

# 문제
Description:

Add a groupBy method to Array.prototype so that elements in an array could be grouped by the result of evaluating a function on each element.

The method should return an object, in which for each different value returned by the function there is a property whose value is the array of elements that return the same value.

If no function is passed, the element itself should be taken.

~~~ java
Example:

[1,2,3,2,4,1,5,1,6].groupBy()
{
  1: [1, 1, 1],
  2: [2, 2],
  3: [3],
  4: [4],
  5: [5],
  6: [6]
}

[1,2,3,2,4,1,5,1,6].groupBy(function(val) { return val % 3;} )
{
  0: [3, 6],
  1: [1, 4, 1, 1],
  2: [2, 2, 5]
}
~~~ java

- 문제를 접했을 때 큰 생각이 없었다. 전략이 떠오르지 않으면 안되는데...
- 맨 처음에 prototype 안에서 배열에 어떻게 접근하는지 잘 몰라서 좀 고생했다. 알고 보니 this 객체로 접근할 수 있다더라...

# My Solution
~~~ java
Array.prototype.groupBy = function(fn) {
  var o = {};
  this.forEach(function (data) {
      var key;
      if(fn) {
        key = fn(data);
      } else {
        key = data;
      }
      if(o[key]) {
        o[key].push(data);
      } else {
        o[key] = [data];
      }
    });
  return o;
}
~~~ java

- this 객체로 배열에 접근. fn이 있는지 없는지 찾은 다음에 key를 구한다. key를 가지고 리턴할 객체를 구성한다.
- 실제로 필드에서 사용할 때는 fn을 체크하는 로직 또한 들어가야 할 것 같다.
- 예를 들면 fn이 함수인지, 존재하는지, 함수이면 어떻게 동작하고 함수가 아닐 때는 어떻게 동작하는지 같은 로직을 추가해야 사용할 수 있을 거 같다.

# Best Solution
~~~ java
Array.prototype.groupBy = function(fn) {
  return this.reduce(function(o, a){
    var v = fn ? fn(a) : a;
    return (o[v] = o[v] || []).push(a), o;
  }, {});
}
~~~ java
- 왜 reduce를 사용할 생각을 못했지? 리듀스를 썼다면 코드가 훨씬 더 우아해 졌을 거 같다.
- 보통 베스트 솔루션들은 리듀스를 사용했다.

~~~ java
const _ = require('lodash');
Array.prototype.groupBy = function(fn){
  return _.groupBy(this, fn);
}
~~~ java
- 그렇군. 현명하다...