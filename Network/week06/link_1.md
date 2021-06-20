# 5. Link layer - 다중 엑세스 프로토콜

> Link layer : host와 dest의 라우터 사이의 전송을 더 자세히 보는 개념
>
> Link layer의 전송 단위: frame



두 개 이상의 패킷이 전송될 경우, 충돌이 발생해서 해당 패킷은 전송이 안됨

-> 충돌을 어떻게 해결할지가 매우 중요

<br>

## MAC 프로토콜

3개의 broad classes 존재



### 1. channel partitioning

- **TDMA (time division multiple access)**

  : 시간을 나눠서 엑세스 -> 충돌 발생 X

  ![image-20210620223337490](https://user-images.githubusercontent.com/77573938/122678573-5dd6a080-d222-11eb-8fca-10a0072d4d95.png)

- **FDMA (frequency division multiple access)**

  : 주파수를 나눠서 엑세스 -> 충돌 발생 X

- 공통 장점: 낭비 발생 가능



### 2. random access

: channel partitioning 방식의 단점을 보완하기 위한 방법

채널이 분할되지 않아 유연한 방법이되, 충돌 발생 가능

- 충돌 발생 시 recover 하는 방법 : slotted ALOHA, ALOHA, ***CSMA***, CSMA/CD, CSMA/CA

- **CSMA (carrier sense multiple access)**

  : 랜덤 엑세스 웹 프로토콜의 대표적인 방식.

  전송하기 전에 들어봄. 사용중으로 감지되면 전송 연기, 채널이 사용 중이 아니라면 전체 프레임 전송.

  but, 그럼에도 충돌 발생 가능

- CSMA collisions

  - CSMA를 사용함에도 충돌 발생 가능하는 이유: 전자기파의 진행 속도가 제한적이기 때문에

- CSMA/CD (collision detection)

  : 충돌을 발생할 수 있지만 의미 없는 시간이 날라가는걸 방지하기 위한 것.

  collision이 발생해서 탐지하는 순간 바로 전송을 멈춤

  ![image-20210620225406812](https://user-images.githubusercontent.com/77573938/122678569-5ca57380-d222-11eb-9825-4b5798790075.png)

  



### 3. taking turns





## 이더넷 (Ethernet)



