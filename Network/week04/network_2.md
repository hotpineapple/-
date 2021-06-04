# Network Address Translation

## 4장 네트워크 계층 Overview

1. 네트워크 계층 서비스 - IP 프로토콜
2. NAT(Network Address Translation) 
3. 라우팅 알고리즘
4. 거리 벡터 알고리즘
5. 계층적 경로 변경

---

<br>

## 1 (요약)
* 라우터들은 IP 패킷만 이해할 수 있으므로 네트워크 계층에서는 IP 프로토콜이 중요함
* IP 주소는 네트워크 아이디(prefix, subnet)와 호스트 아이디로 계층화되어있음

---

<br>

## 2. NAT(Network Address Translation) 

> IPv4의 패킷 헤더의 SRC,DEST 주소의 표현가능한 공간이 부족한 문제, 그러나 현실적으로 IPv6 프로토콜을 이해하도록 모든 라우터를 교체하는 것은 쉽지 않다.

### 2-1. NAT 정의
: src 주소를 내부적으로만 유일한 IP 주소에서 외부에서 사용가능(globally unique)하도록 gateway의 IP주소로 변환하여 내보냄

### 2-2. 문제점
* 위의 방식은 클라이언트일 때, 즉 요청을 할 때는 문제가 없다. 그러나 요청을 기다리는 서버일 때는 dest 주소를 어떻게 해야하지?
* 네트워크 계층에서 TCP 헤더를 변경함

### 2-3. DHCP(Dynamic Host Configuration Protocol)
: Host가 네트워크 접속을 요청하면 (동적으로) IP 주소를 배정받는 프로토콜

* DHCP 서버는 67번 포트를, 클라이언트는 68번 포트를 사용함

#### Scenario
* 클라이언트가 브로드캐스트 요청을 보낸 뒤 
* 서버로부터 오퍼를 받으면 
* 해당 오퍼에 대한 요청을 브로드캐스트로 보내어 다른 서버가 오퍼를 취소하도록 함

## IP fragment and reassembly

