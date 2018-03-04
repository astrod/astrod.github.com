---
layout: post
title: "Quick Sort"
tags:
- algorithm
category:
- algorithm
---

* toc
{:toc}

# Quick Sort

퀵 소트는 가장 대표적인 정렬 방식 중 하나이며, 또한 대표적인 재귀방식 정렬이기도 하다. 이 포스팅에서는 java를 사용한다.

## Code
~~~java
	public void quickSort(int [] arr, int start, int end) {
		if(start < end) {
			int pivot = findPivot(arr, start, end);
			quickSort(arr, start, pivot - 1);
			quickSort(arr, pivot+1, end);
		}
	}

	private int findPivot(int[] arr, int start, int end) {
		
		int boundaryOfLow = start - 1;
		int pivot = arr[end];
		
		for(int i = start; i<end; i++) {
			if(pivot > arr[i]) {
				swap(arr, ++boundaryOfLow, i);
			}
		}
		swap(arr, ++boundaryOfLow, end);
		
		return boundaryOfLow;
	}

	private void swap(int[] arr, int i, int j) {
		int temp = arr[i];
		arr[i] = arr[j];
		arr[j] = temp;
	}
~~~

## 설명
배열이 있다고 가정하자.

~~~java
int [] array = {3, 5, 9, 4, 6}
~~~

이 배열에서 한 수를 임의로 추출한다. (이 포스팅에서는 배열의 가장 마지막 수를 추출한다)
위의 코드에서는 6일 것이다.

이제 배열을 앞에서부터 순회하며 6보다 큰 숫자는 오른쪽으로, 6보다 작은 숫자는 왼쪽으로 보낸다. 위의 코드에서 이 역할을 하는 함수가 findPivot 이다.

한번 피봇을 구하면 위의 배열은 이렇게 정돈된다.

~~~java
int [] array = {3, 5, 4, 6, 9}
~~~

이렇게 한 다음에, findPivot은 6의 index(위의 코드에서는 3)을 리턴한다.

그러면 이제 0번째 수부터 2 번째 수까지 같은 방식으로 피봇을 뽑아 정렬한다.
3번째 수부터 3번째 수까지 피봇을 뽑아 정렬한다. (퀵소트에서는 숫자가 하나 남으면 정렬된 것으로 본다)

그러면 3,5,4는 {3,4,5} 로 정렬될 것이다. 피봇 4를 기준으로 다시 findPivot을 시도하면 3,5는 각각 숫자가 하나이므로 정렬된 상태이다.

따라서 최종 리턴값은

~~~java
int [] array = {3, 4, 5, 6, 9}
~~~

가 된다.

즉, 피봇을 기준으로 피봇보다 작은 숫자는 좌측, 큰 숫자는 우측으로 두는 연산을 배열이 모두 정렬될때까지 수행하는 것이다.

## 시간 복잡도

결국 퀵소트의 시간 복잡도는 피봇을 얼마나 잘 뽑느냐에 달려 있다. 재귀트리로 증명해보면, 피봇이 배열을 1:9 로 분할해도 시간 복잡도는 O(nlogn)으로 수렴하게 된다.

그러나, 극단적인 상황이 존재하긴 한다. 대표적으로는

~~~java
int [] arr = {1,2,3,4,5}

int [] arr = {5,4,3,2,1}
~~~

과 같은 정렬되어 있거나, 역으로 정렬되어 있는 숫자이다. 시간 복잡도를 측정해보면 두 배열 모두 O(n^2)이 된다. 

이런 상황을 방지하기 위해, 랜덤하게 피봇을 정할 수도 있다.

1. 먼저 배열 내에서 랜덤한 숫자를 하나 고른다.
2. 그 숫자를 배열의 가장 마지막 숫자와 swap 한다.
3. 배열의 마지막 숫자를 피봇으로 삼아 연산을 수행한다.

이렇게 랜더마이즈하게 퀵소트를 수행할 수 있다.

