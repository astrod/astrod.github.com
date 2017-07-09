---
layout: post
title: "Insertion Sort"
tags:
- algorithm
---

* toc
{:toc}

# 들어가며
Insertion Sort는 O(n^2)의 시간복잡도를 가진 정렬 알고리즘이다. 내가 알고 있는 O(n^2)의 시간복잡도를 가진 알고리즘은 버블소트, 셀렉션소트, 인서션소트 이 세 가지인데, 이 중 가장 효율적인 알고리즘이기도 하다.

# Code

~~~java
public void insertionSort(int[] arr) {
		if(arr.length < 1) {
			return;
		}
		
		for(int i = 1; i<arr.length; i++) {
			int target = arr[i];
			int subIdx = i - 1;
			
			while(subIdx >= 0 && arr[subIdx] > target) {
				arr[subIdx + 1] = arr[subIdx];
				subIdx--;
			}
			arr[subIdx + 1] = target;
		}
	}
~~~

# 설명
정렬되지 않은 배열이 하나 있다고 가정하자.

~~~java
int [] arr = {2, 3, 1, 5, 4}
~~~

이 배열의 첫번째 값만 확인해보자.

~~~java
int [] arr = {2}
~~~

이렇게 놓고 보면, 위의 배열은 정렬되어 있는 상태이다.

그러면 여기서 3까지 고려해 보자.

~~~java
int [] arr = {2, 3}
~~~

이 상태도 역시 정렬되어 있는 상태이다. 그러면 다음 값을 생각해 보자.

~~~java
int [] arr = {2, 3, 1}
~~~

1을 고려대상으로 삼은 상황에서, 이미 앞의 두 수 (2,3)은 정렬되어 있는 상태이다. 이제 1을 자기 왼쪽에 있는 값과 비교하여, 자기 왼쪽에 있는 값이 더 크다면 그 값을 오른쪽으로 한칸 움직인다.

3 > 1 이므로

~~~java
int [] arr = {2, 1, 3}
~~~

2 > 1 이므로

~~~java
int [] arr = {1, 2, 3}
~~~

이제 이 상황에서 5를 생각해 보자.

~~~java
int [] arr = {1, 2, 3, 5}
~~~

3 < 5 이므로  값의 변화는 없다.

마지막 수인 4를 고려해보자.

~~~java
int [] arr = {1, 2, 3, 5, 4}
~~~

5 > 4 이므로 4를 왼쪽으로 한칸 움직인다.

~~~java
int [] arr = {1, 2, 3, 4, 5}
~~~

위와 같은 과정을 거쳐, 배열을 정렬할 수 있다.

# 시간 복잡도
O(n^2) 이다. 최악의 경우를 고려해 보면, 배열이 역정렬되어 들어온 경우이다.

~~~java
int [] arr = {5, 4, 3, 2, 1}
~~~

이렇게 되면, 

1. 맨 처음에는 배열을 한 개 탐색 (1) 하고 4를 5의 위치에 집어넣는 연산(c)을 수행해야 한다.
2. 그 다음에는 배열을 두 개 탐색 (2) 하고 3을 배열의 맨 처음에 놓는 연산(c)을 수행해야 한다.

이런 과정을 거치다 보면, 마지막에는 n-1개의 배열을 탐색해야 한다.

그러므로 시간복잡도의 총합은 1+2+...+ n - 1 이고, 이는 n(n-1)/2이므로 O(n^2)이다.

# selection sort VS insertion sort
이 두 알고리즘은 insertion sort가 조금 더 효율적인데, 그 이유는 selection sort는 최소값을 찾기 위해 전 배열을 모두 찾아야 하기 때문이다. 즉, 항상 1+2+...+n-1 의 연산이 요구된다. 반면에 insertion sort는 최악의 경우에만 1+2+...+n-1이기 때문에 insertion sort가 조금 더 빠르다.





