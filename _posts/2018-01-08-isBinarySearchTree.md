---
layout: post
title: "is Binary Search Tree"
tags:
- algorithm
---

* toc
{:toc}

# 문제
[링크](https://www.hackerrank.com/challenges/is-binary-search-tree/problem)

트리가 주어졌을 때 해당 트리가 BST 인지 판별하는 문제이다. 두 가지 방법이 있다.

# Solution 1
InOrder traversal 을 통하여, 해당 트리가 오름차순으로 출력되는지 확인하는 방법이다. 슥 생각했을 때는 메모리를 사용해야 할 거라는 생각이 들지만, 실제로 해당 트리에서 사용하는 값은 바로 앞에 숫자이다. 즉, 바로 앞에 숫자가 자기 자신보다 큰지만 확인하면 된다.

코드는 다음과 같다.

~~~java

/* The Node class is defined as follows:
class Node {
    int data;
    Node left;
    Node right;
}*/
Integer last;
boolean checkBST(Node root) {
    if(root == null) {
        return true;
    }
    
    if(!checkBST(root.left)) {
        return false;
    }
    
    if(last != null && root.data <= last ) {
        return false;
    }
    
    last = root.data;
    
    if(!checkBST(root.right)) {
        return false;
    }
    
    return true;
}
~~~

## 시간복잡도
- 모든 노드를 순회하면서 비교연산을 한다
- 노드의 총 개수를 N이라고 하면, 비교연산이 O(1) 이므로
- O(N)

## 공간복잡도
- 최대 트리의 Height 만큼의 공간을 사용한다.
- 최악의 경우는 Tree가 균형잡혀 있지 않은 경우고, 이 경우 O(N)

# Solution 2
Binary Search Tree의 속성을 활용한다. 만약 라면, 모든 노드에서 노드의 왼쪽 값은 노드보다 작고, 노드의 오른쪽의 모든 값은 노드보다 커야 한다.
이런 논리로, min값과 max값을 유지한 상태로 트리 전체를 순회하여, 모든 노드가 위의 값을 만족하는지 체크한다. min값과 max값을 파라미터로 넘긴다.

구현은 다음과 같다.

~~~java
boolean checkBST(Node root, Integer min, Integer max) {
    if(root == null) {
        return true;
    }
    
    if(min != null && min >= root.data) {
        return false;
    }
    
    if(max != null && max <= root.data) {
        return false;
    }
    
    
    if(!checkBST(root.left, min, root.data) || !checkBST(root.right, root.data, max)) {
        return false;
    }
    
    return true;   
}
~~~ 

## 시간복잡도
- 모든 노드를 한번씩 순회하기 때문에, 시간복잡도는 O(N)

## 공간복잡도
- 최대 height 만큼 호출되기 때문에, 트리가 균형잡혀 있지 않은 경우가 worst case. 공간복잡도는 O(N)
