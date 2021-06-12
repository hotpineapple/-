# 라우팅 알고리즘 - 01. 링크상태 알고리즘

## 4장 네트워크 계층 Overview
1. 네트워크 계층 서비스 - IP 프로토콜
2. NAT(Network Address Translation) 
3. 라우팅 알고리즘
  3-1. 링크상태 알고리즘
  3-2. 거리 벡터 알고리즘
4. 계층적 경로 변경

---

<br>

## 라우터의 동작
: 각 라우터의 포워딩 프로시저가 다익스트라 알고리즘을 이용하여 포워딩 테이블 값을 세팅하고, 이 테이블의 값에 따라 데이터를 포워딩 => Interplay between routing, forwarding

## 다익스트라 알고리즘

### 1. 초기화(Initializtion)
: 연결된 라우터에 대해 

### 2. 반복문(Loop)
: 

### 3. Post processing

### 최종적으로 만들어진 라우터(정점) U의 포워딩 테이블 

|destination|forwarding|

|-----------|----------|

|V|W|

|W|W|

|X|X|

|Y|W|

|Z|W|

