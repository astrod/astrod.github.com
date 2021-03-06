---
layout: post
title: "Minimum Spaning Tree - 크루스칼 알고리즘"
tags:
- algorithm
category:
- algorithm
---

* toc
{:toc}

# Minimum Spaning Tree

## 들어가며
이 포스트에서는 minimum spaning tree를 다룬다(이하 MST). MST는 weighted 그래프에서 (Cycle 존재하는 경우) Weighted 가 최대가 되면서 모든 vertex를 거치는 Tree를 구하는 방법이다. 이하 이 트리를 spaning tree라고 한다.

Minimum spaning tree를 구하는 데는 그리드 알고리즘을 사용하게 된다. 즉, 웨이트가 가장 작은 것부터 하나하나 골라서 트리를 생성하다 보면, 웨이트가 가장 최소값이 되는 트리를 고를 수 있지 않을까? 하는 생각부터 시작하는 것이다.

## 핵심 개념
위의 알고리즘으로부터 발상을 전개해나가면 다음과 같이 생각할 수 있다.
당연한 이야기지만 weight가 가장 작은 엣지부터 하나하나 골라서 트리를 생성해간다면, 그 트리의 weight는 가장 최소일 것이다. 

그런데 만약 엣지를 하나 추가했는데 cycle이 생긴다면? 문제가 발생할 것이다.
즉 알아야 할 것은 다음과 같다 : 엣지를 추가할 때 이 트리에 사이클이 발생하는가?

사이클을 감지하기 위해서 각각의 vertex 에 id를 부여한다. id는 유니크하면서 auto-increment한 값이라고 가정한다.

그리고 edge(u, v)를 그래프에 추가할 때, 아래와 같은 연산을 수행한다.

1. u와 v의 id가 다르다면 엣지를 그래프에 추가한다.
2. vertices 개수가 많은 쪽으로 id를 갱신한다.
3. u와 v의 id가 같다면 이미 u,v를 연결하는 경로가 존재하는 것이므로 사이클이 발생한다.

이와 같은 연산을 수행하면 사이클을 찾을 수 있다.

## 프로세스
1. edge의 weight를 기준으로 edge를 정렬한다.
2. weight가 가장 낮은 엣지부터 추출한 후에, 엣지의 vertex의 id를 비교, id가 다르면 엣지를 취하고 두 트리 중에 id가 많은 쪽으로  id를 다 변경한다.

## 시간 복잡도
1. 정렬한다 - O(ElogE)
2. 엣지를 순회하면서 weight가 최소인 엣지를 추리고, 두 트리를 합친다 - O(E + VlogV)

두 트리를 합칠 때는 엣지의 개수만큼 루프를 돌면서(E) + degree가 2 인 재귀트리를 그리게 된다(VlogV).





