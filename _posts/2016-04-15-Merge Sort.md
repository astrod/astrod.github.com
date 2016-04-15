---
title : "Merge Sort"
tags :
- algorithm
---

# Merge Sort
merge sort는 O(nlogn) 의 정렬 알고리즘이다. 분산 시스템이나 배열이 너무 커서 모든 배열을 메모리에 올려서 작업을 할 수 없을 때, 위의 정렬 방식을 사용한다. 배열을 모두 메모리에 올릴 수 있다면 Quick Sort가 더 효율적인 정렬 방식이다.

merge sort는 배열을 재귀적으로 다 분할해서, 합치면서 정렬을 한다. 즉 

~~~java
int [] arr = {4,5,9,3,2};
~~~

와 같은 배열이 있다면, 배열 전체를 재귀적으로 분할한다. 실제로는 이렇게 되지는 않지만 의미상으로는

~~~java
int [] arr1 = {4};
int [] arr2 = {5};
int [] arr3 = {9};
int [] arr4 = {3};
int [] arr5 = {2};
~~~

이렇게 분할한다. 배열의 원소가 한 개가 되면 자동적으로 정렬된 상태가 되기 때문이다. 그다음에 배열을 합치면서(merge) 배열을 정렬한다. 
즉, merge sort의 가장 핵심적인 문제는 이것이다.

**두 개의 정렬된 배열을 어떻게 하면 합칠 수 있지?**

코드는 다음과 같다.

# Code

~~~java
	public void mergeSort(int [] arr, int start, int end) {
		int [] arr2 = new int[arr.length];
		
		if(start < end) {
			int mid = (start + end) / 2;
			mergeSort(arr, start, mid);
			mergeSort(arr, mid+1, end);
			merge(arr, arr2, start, mid, end);
		}
	}
	
	private void merge(int[] arr, int[] arr2, int start, int mid, int end) {
		arrayCopy(arr, arr2, start, end);
		int startIdx = start;
		int midIdx = mid + 1;
		
		for(int i = start; i<=end; i++) {
			if(startIdx > mid) {
				arr[i] = arr2[midIdx++];
			} else if (midIdx > end) {
				arr[i] = arr2[startIdx++];
			} else if (arr2[startIdx] > arr2[midIdx]) {
				arr[i] = arr2[midIdx++];
			} else {
				arr[i] = arr2[startIdx++];
			}
		}
	}

	private void arrayCopy(int[] arr, int[] arr2, int start, int end) {
		for(int i = start; i <=end; i++) {
			arr2[i] = arr[i];
		}
	}
~~~

## 설명
mergeSort 메소드에서 재귀적으로 배열을 분할한다. 그렇게 되면, 두 번째 mergeSort 함수가 호출된 다음에 [start, mid], [mid+1, end]의 두 배열은 각각 정렬된 상태가 된다.

그러면 그 두 배열을 merge 메소드에서 합치게 된다. 배열을 합치기 전에, 원래 배열의 값을 값을 할당한 다른 배열에 복사한 다음에, 그 배열을 참조하여 두 개의 배열을 합치게 된다.

## 시간 복잡도 / 공간 복잡도
재귀트리를 그려보면 시간 복잡도가 O(nlogn)임을 알 수 있다. 다만 공간 복잡도 측면에서 봤을 때는, merge하는 전체 배열의 크기만큼의 공간이 더 필요함을 알 수 있다.

