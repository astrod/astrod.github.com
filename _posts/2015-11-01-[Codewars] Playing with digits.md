---
layout: post
title:  "[Codewars] Playing with digits"
tags : "codewars"
---

# 문제
Some numbers have funny properties. For example:

~~~ java
89 --> 8¹ + 9² = 89 * 1

695 --> 6² + 9³ + 5⁴= 1390 = 695 * 2

46288 --> 4³ + 6⁴+ 2⁵ + 8⁶ + 8⁷ = 2360688 = 46288 * 51
~~~ java

Given a positive integer n written as abcd... (a, b, c, d... being digits) and a positive integer p we want to find a positive integer k, if it exists, such as the sum of the digits of n taken to the successive powers of p is equal to k * n. In other words:

Is there an integer k such as : (a ^ p + b ^ (p+1) + c ^(p+2) + d ^ (p+3) + ...) = n * k
If it is the case we will return k, if not return -1.

Note: n, p will always be given as strictly positive integers.

~~~ java
digPow(89, 1) should return 1 since 8¹ + 9² = 89 = 89 * 1
digPow(92, 1) should return -1 since there is no k such as 9¹ + 2² equals 92 * k
digPow(695, 2) should return 2 since 6² + 9³ + 5⁴= 1390 = 695 * 2
digPow(46288, 3) should return 51 since 4³ + 6⁴+ 2⁵ + 8⁶ + 8⁷ = 2360688 = 46288 * 51
~~~ java

- 설명이 약간 복잡한데 ...위의 공식에서 p랑 n이 주어져 있을 때, k를 구하는 문제이다. k가 없다면 -1을 리턴한다.
- 별로 어려운 문제는 아니었던 듯. 그냥 숫자를 자릿수대로 분리한다음에 Math.pow함수를 이용하여 값을 구하고 삼항연산자를 활용하기로 결정

# My Solution

~~~ java
function digPow(n, p){

  var arr = n.toString();
  var sum = 0;
  for(var i = 0; i<arr.length; i++) {
    var target = parseInt(arr.charAt(i), 10);
    sum += Math.pow(target, p++);
  }
  return sum % n === 0 ? sum / n : -1;
}

~~~ java

- 크게 어려울 건 없었다. for문 보다는 다른 방법을 사용하고 싶었고, charAt 대신에 인자로 받은 걸 한방에 배열로 바꿀 방법이 있었으면 했는데...

# Best Solution

~~~ java
function digPow(n, p){
  var ans = (''+n).split('')
    .map(function(d,i){return Math.pow(+d,i+p) })
    .reduce(function(s,v){return s+v}) / n
  return ans%1 ? -1 : ans    
}//z.
~~~ java
- ''+n을 이용하면 parseInt없이 숫자로 변경할 수 있다. (그러나 나는 pasreInt쪽이 좀 더 좋은 거 같다. 더 명시적이니까.)
- map을 이용하여 값을 구하고
- reduce로 값을 더한다.

~~~ java
function digPow(n, p){
  var k = n.toString().split('').reduce(function(res, val, idx) {
    return res + Math.pow(val, p + idx);
  }, 0) / n;
  
  if (Math.floor(k) != k) return -1;
  return k;
}
~~~ java
- 이런 방식을 많이 사용한 듯
- reduce를 이용해서 배열의 합을 한번에 구할 수 있다는 걸 기억해 두자.
- each, every, reduce, map과 같은 함수들의 사용법을 익혀 두어야 할 거 같다.