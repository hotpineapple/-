# 라우팅 알고리즘 - 02. 거리 벡터 알고리즘

> distance vector algorithm : 내가 알고 있는 정보를 이웃이랑만 전달  ( *cf.* link-state : broadcast )

- 각 노드들은 모든 목적지에 대해 거리값을 가지고 있음 
  → [dv1(Y), dv2(W), dv1(Z), ... ] : distance의 array == vector => `distance vector` 

<br>

## Bellman-Ford equation

![image-20210614033420527](https://user-images.githubusercontent.com/77573938/121820483-fa062200-cccd-11eb-9000-bad1f52d6637.png)



### 예시

![image-20210614033621832](https://user-images.githubusercontent.com/77573938/121820487-fbcfe580-cccd-11eb-9f63-66d4b865fd56.png)

<br>

## 거리 벡터 알고리즘 (Distance vector algotirhm)

### 1-1. 작동 원리 요약

1. wait - 가만히 있다가 
2. recompute - 현재 least-cost가 변했거나 이웃으로부터 distance vector 메세지를 받는 이벤트가 발생하면 distance vector 값을 다시 계산
3. notify - distance vector 값이 변경된 경우 이웃에게 값 전달



### 1-2. 작동 원리 세부과정

- `Node X`, `Node Y`, `Node Z` : 각각 **독립적으로 분산되서** 연산됨

- X입장에서는 dx(X), dx(Y), dx(Z) 만 아는 상태.

  X 입장에서는 Y에서 X까지의 최단거리는 정보교환 안했기 때문에 모름. -> dy(X), dz(X) 값 :  ∞ 

- 검정 화살표 : 본인 노드와 연결된 이웃노드들에게 distance vector 정보 넘겨줌

- Bellman-Ford 공식 사용해서 distance vector 재계산 

  -> distance vector 값 변경된 Node들은(node x, node z) 이웃에게 변경된 값 notify 해주기

  -> 새로 값 받았으므로 다시 계산

  -> ... -> 최종적으로 stable 한 상태가 됨

![image-20210614034818617](https://user-images.githubusercontent.com/77573938/121820488-fc687c00-cccd-11eb-8a10-f15d2b3463cf.png)





### 2. 안정화된 상태에서, 링크상태에 변화가 생길 경우



#### 2-1. 링크 상태가 좋게 변화한 경우

![image-20210614042613903](https://user-images.githubusercontent.com/77573938/121820489-fc687c00-cccd-11eb-850a-a0fd12359921.png)

금방 계산 끝나서 안정화 됨.



- **변경 전 stable한 상태**

|       |  X   |  Y   |  Z   |
| :---: | :--: | :--: | :--: |
| **X** |  0   |  4   |  5   |
| **Y** |  4   |  0   |  1   |
| **Z** |  5   |  1   |  0   |



- **1로 변경된 후** 

  X와 Y는 자기 링크가 변했으므로 자신의 distance vector값 다시 계산. (node Z는 본인과 상관없는 링크이기 때문에 아무런 액션 안취함)

  - Node X
  
    |       |  X   |   Y   |   Z   |
    | :---: | :--: | :---: | :---: |
    | **X** |  0   | **1** | **2** |
    | **Y** |  4   |   0   |   1   |
    | **Z** |  5   |   1   |   0   |

  - Node Y
  
    |       |   X   |  Y   |   Z   |
    | :---: | :---: | :--: | :---: |
    | **X** |   0   |  4   |   5   |
    | **Y** | **1** |  0   | **1** |
    | **Z** |   5   |  1   |   0   |







#### 2-2. 링크 상태가 안좋게 변화한 경우

![image-20210614042652400](https://user-images.githubusercontent.com/77573938/121820490-fd011280-cccd-11eb-9381-e164edfa4aa8.png)

`count to infinity` problem 발생 



- **60으로 변경된 후**

  마찬가지로 X와 Y만 재계산

  - Node X

    |       |  X   |   Y    |   Z    |
    | :---: | :--: | :----: | :----: |
    | **X** |  0   | **51** | **50** |
    | **Y** |  4   |   0    |   1    |
    | **Z** |  5   |   1    |   0    |

  - Node Y

    - Dy(X) = min { C(Y,X)+Dx(X)=60+0 , C(Y,Z)+Dz(X)=1+5 } = 6.

      but, 최종적으로 stable한 값은 51임 !!!

    - Z 입장에서는 X,Y 값이 바뀌었으니까 새로운 값으로 바뀌고 다시 계산 -> `7 1 0`. => not stable !!!

    - Z 값 변경되었으니까 또 이웃인 X,Y 한테 notify -> recompute -> ... -> **"count to infinity problem"** 발생

    |       |   X   |  Y   |   Z   |
    | :---: | :---: | :--: | :---: |
    | **X** |   0   |  4   |   5   |
    | **Y** | **6** |  0   | **1** |
    | **Z** |   5   |  1   |   0   |

    - 이 상황에서 51로 업데이트가 안된 근본적 이유?  -> `Dz(X)=5` 로 계산. 즉, 이웃으로부터 받은 distance 값에 다시 나한테 돌아오는 경로를 포함하는 값인지 안 적혀있어서 



- count to infinity problem 해결책 : **`poisoned reverse`**

: 역류로 돌아오는 것을 막기위해 reverse 하는 방법.

Z가 Y를 통해 X에 도달하는 경우(Dz(X)  = dz(y)+dy(x)) , Z는 Y에게 X까지의 거리를 **∞ 로 넘겨줌**.

