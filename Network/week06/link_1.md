# 5. Link layer - 다중 엑세스 프로토콜

> Link layer : host와 dest의 라우터 사이의 전송을 더 자세히 보는 개념
>
> Link layer의 전송 단위: frame



두 개 이상의 패킷이 전송될 경우, 충돌이 발생해서 해당 패킷은 전송이 안됨

-> 충돌을 어떻게 해결할지가 매우 중요

<br>

## MAC 프로토콜

**공유된 채널에 다수의 사용자가 접속할 수 있도록 도와주는 정의한 프로토콜**

3개의 broad classes 존재



### 1. channel partitioning

*사용자가 많은 경우 효율적*

\- 이동통신 LTE에서 사용

- **TDMA (time division multiple access)**

  : 시간을 나눠서 엑세스 -> 충돌 발생 X

  ![image-20210620223337490](https://user-images.githubusercontent.com/77573938/122678573-5dd6a080-d222-11eb-8fca-10a0072d4d95.png)

- **FDMA (frequency division multiple access)**

  : 주파수를 나눠서 엑세스 -> 충돌 발생 X

- 공통 장점: 낭비 발생 가능



### 2. random access

: channel partitioning 방식의 단점을 보완하기 위한 방법

채널이 분할되지 않아 유연한 방법이되, 충돌 발생 가능.

*사용자가 적은 경우 효율적*

\- **이더넷, 와이파이에서 사용**

- 충돌 발생 시 recover 하는 방법 : slotted ALOHA, ALOHA, ***CSMA***, CSMA/CD, CSMA/CA

- **CSMA (carrier sense multiple access)**

  : 랜덤 엑세스 웹 프로토콜의 대표적인 방식.

  전송하기 전에 들어봄. 사용중으로 감지되면 전송 연기, 채널이 사용 중이 아니라면 전체 프레임 전송.

  but, 그럼에도 충돌 발생 가능

- CSMA collisions

  - CSMA를 사용함에도 충돌 발생 가능하는 이유: 전자기파의 진행 속도가 제한적이기 때문에

- CSMA/CD (collision detection)

  : 충돌을 발생할 수 있지만 의미 없는 시간이 날라가는걸 방지하기 위한 것.

  ![image-20210620225406812](https://user-images.githubusercontent.com/77573938/122678569-5ca57380-d222-11eb-9825-4b5798790075.png)

  collision이 발생해서 탐지하는 순간 바로 전송을 멈춤 -> 즉, 전송이 안됨. 재전송해야함

  => `Binary Exponential Backoff`  (충돌나면 처음엔 조금 시간 갖고 또 충돌나면 시간 간격을 좀 더 길게 갖는 방법)

  *cf.* 유선상황에서는 충돌감지가 쉬움. 반면, 무선 상황에서는 충돌상황 감지가 이더넷(유선상황)보다는 어려움. 무선은 외부로부터 노이즈를 보호해줄 수 있는 상황이 아니기 때문에

  



### 3. Taking turns

: channel partitioning 방법과 random access 방법의 장점을 합해 만들고자 한 방법.

서로서로 번갈아가면서 보내도록 하는 방식. 이 방식은 참여하고 있는 모든 node들이 항상 보낼게 있다라는 전제 하에서 돌아가기 때문에 보낼게 없을 때의 리소스는 그냥 날라감. (별로 좋지 않은 방법 & 현실적으로 잘 안쓰임)

**3-1. polling**

이론적으로는 많이 쓰이나 실제로는 master 가 다운되면 slaves들도 다운되기 때문에 별로 안쓰임.

![image-20210627105228431](https://user-images.githubusercontent.com/77573938/123549167-c4b80480-d7a2-11eb-9320-2744521a6156.png)


**3-2. token passing**

: token을 넘겨주는 방식.

token이라는 special한 message를 가지고 있는 호스트만이 프레임을 전송할 수 있다고 규정.

\- 단점: single point of failure (토큰 잃어버리면 전체가 망함)

![image-20210627111008067](https://user-images.githubusercontent.com/77573938/123549168-c5e93180-d7a2-11eb-963e-3e840b52a030.png)



<br>

## 이더넷 (Ethernet)

> 유선 상황에서 MAC 프로토콜

- 전세계의 사무실이나 가정에서 일반적으로 사용되는 LAN(근거리통신망)에서 가장 많이 활용되는 기술 규격.

- 이더넷은 OSI 모델의 물리 계층에서 신호와 배선, 데이터 링크 계층에서 MAC 패킷과 프로토콜의 형식을 정의한다.

<br>

### physical topology

- 과거에는 BUS형이였으나 현대에는 star형

![image-20210627111751618](https://user-images.githubusercontent.com/77573938/123549170-c681c800-d7a2-11eb-923c-b329f8ad3b55.png)



### 이더넷 구조

![image-20210627112017974](https://user-images.githubusercontent.com/77573938/123549171-c681c800-d7a2-11eb-8569-f08264deb413.png)

- Header의 **dest address, source address** <- IP address(X), MAC address(O) 링크계층의 주소임.
- data 필드에 IP패킷이 들어감
- 맨 뒤에 CRC 필드가 존재



### Ethernet uses CSMA/CD

- random access를 하되, 충돌 CSMA/CD 처리



### 예시

source: A / dest: Google

- 링크 계층의 목표 : 구글까지 가야 할 패킷이 각 링크에서 충돌나지 않고 잘 가야함

CSMA/CD는 프레임을 라우터에 전달했을 때 피드백을 안 주지만 (즉, 이더넷 상황에서는 피드백 안줌)

유선 상황에서는 외부로부터 노이즈가 없기 때문에 내부에서 **충돌만 없으면** frame은 다음 단계 라우터까지 99.99% 잘 간다.

반면, 충돌이 있을 경우에는 재전송해야 함

**=> CSMA/CD 핵심: 100% 확률의 "충돌 감지"** 



충돌이 발생했음에도 불구하고 탐지가 안되는 경우가 있냐? 있음. 이 경우 어캄?

CSMA/CD 제대로 동작 안함.
