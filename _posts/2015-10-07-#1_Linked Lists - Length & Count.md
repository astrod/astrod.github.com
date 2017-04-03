---
layout: post
title:  "Linked Lists - Length & Count"
tags: 
- algorithm
---

##Problom

~~~ java
Description:

Linked Lists - Length & Count

Implement Length() to count the number of nodes in a linked list.

length(null) === 0
length(1 -> 2 -> 3 -> null) === 3
Implement Count() to count the occurrences of an integer in a linked list.

count(null, 1) === 0
count(1 -> 2 -> 3 -> null, 1) === 1
count(1 -> 1 -> 1 -> 2 -> 2 -> 2 -> 2 -> 3 -> 3 -> null, 2) === 4
I've decided to bundle these two functions within the same Kata since they are both very similar.

The push() and buildOneTwoThree() functions do not need to be redefined.
~~~
> linked List의 길이와 데이터를 구하는 문제. 어렵지 않은 문제라고 생각했으나 best 답변을 보니 생각보다 머리를 굴릴 요소가 많았다.

##My Solution

~~~ java
function Node(data) {
  this.data = data;
  this.next = null;
}

function length(head) {
  var count = 0;
  while(head) {
    count++;
    head = head.next;
  }
  return count;
}

function count(head, data) {
  var count = 0;
  while(head) {
    if (head.data === data) {
      count++;
    }
    head = head.next;
  }
  return count;
}
~~~

##Best Solution
~~~ java
function Node(data) {
  this.data = data
  this.next = null
}

function length(head) {
  return head ? 1 + length(head.next) : 0
}

function count(head, data) {
  if (!head) return 0
  return (head.data === data ? 1 : 0) + count(head.next, data)
}
~~~

> 굉장히 우아하게 답을 구했다. 리커시브가 옳은 경우인듯? 나중에 누구한테 리커시브 설명해 줄 일이 있으면 링크드 리스트의 길이를 구하는 문제를 보여주면 될 거 같다.