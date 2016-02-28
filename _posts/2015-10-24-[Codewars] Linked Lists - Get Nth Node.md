---
layout: post
title:  "[Codewars] Linked Lists = Get Nth Node"
tags : "codewars"
---

# 문제
Description:

Linked Lists - Get Nth

Implement a GetNth() function that takes a linked list and an integer index and returns the node stored at the Nth index position. GetNth() uses the C numbering convention that the first node is index 0, the second is index 1, ... and so on. So for the list 42 -> 13 -> 666, GetNth() with index 1 should return Node(13);

getNth(1 -> 2 -> 3 -> null, 0).data === 1
getNth(1 -> 2 -> 3 -> null, 1).data === 2
The index should be in the range [0..length-1]. If it is not, GetNth() should throw/raise an exception. You should also raise an exception if the list is empty/null/None.

- Nth 번째 링크드 리스트의 data를 구하는 문제
- LinkedList에 관한 문제는 예전에도 풀었었는데, 재귀함수를 이용하면 깔끔하게 문제를 해결할 수 있어서 재귀로 문제를 풀려고 생각했었다.

# 나의 답
~~~ java
function Node(data) {
  this.data = data;
  this.next = null;
}

function getNth(node, index) {
  if(node === null || index < 0) throw exception();
  if(index === 0) return node;
  
  return getNth(node.next, index - 1);
}
~~~ java
- 재귀를 이용하여 Nth번째의 node 를 구한다.
- index가 0이 되면 그 단계의 node를 리턴하면 된다.

# Best Solution
~~~ java
function Node(data) {
  this.data = data;
  this.next = null;
}

function getNth(node, index) {
  if (node != null)
    return index == 0 ? node : getNth(node.next, index - 1);
  else
    throw "invalid argument";  
}
~~~ java
- 삼항연산자를 이용하여 좀 더 문제를 깔끔하게 해결할 수 있는 거 같다.
- 예외처리를 앞에 두는게 좋은지 이런 식으로 if - else로 처리하는게 좋은지는 잘 모르겠다.
- 개인적으로는 벨리데이트 -> 문제 해결 의 순으로 로직이 흐르는 게 좀 더 깔끔해 보이는 거 같다.


# 소감
- while과 if - else, 재귀함수를 사용하는 세 가지 패턴이 주로 존재했다.
- 삼항연산자는 세 경우 다 사용하는 게 좋은 거 같다.
- 재귀함수가 가장 깔끔하다. 링크드 리스트는 문제의 크기가 줄어가는 타입의 문제이기 때문에, 재귀를 사용하면 깔끔하게 해결할 수 있는 거 같다.
