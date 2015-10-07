##Problom

Description:

Linked Lists - Length & Count

Implement Length() to count the number of nodes in a linked list.

>length(null) === 0 <br>
length(1 -> 2 -> 3 -> null) === 3 <br>
Implement Count() to count the occurrences of an integer in a linked list.

>count(null, 1) === 0 <br>
count(1 -> 2 -> 3 -> null, 1) === 1 <br>
count(1 -> 1 -> 1 -> 2 -> 2 -> 2 -> 2 -> 3 -> 3 -> null, 2) === 4 <br>
I've decided to bundle these two functions within the same Kata since they are both very similar.

The push() and buildOneTwoThree() functions do not need to be redefined.

> linked List의 길이와 데이터를 구하는 문제. 어렵지 않은 문제라고 생각했으나 best 답변을 보니 생각보다 머리를 굴릴 요소가 많았다.

##My Solution
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

