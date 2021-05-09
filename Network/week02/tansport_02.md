# 전송계층(Transport layer)

## Overview
1. 전송 계층의 UDP/TCP 공통기능 : Mux & Demux
2. UDP 세그먼트 구성과 동작 
3. TCP Introduction : RDT 원리
4. TCP 세그먼트 구성과 동작
    * 연결 지향 전송
    * 흐름제어
    * 혼잡제어

---

## (용어) 각 계층의 전송단위
* 응용계층 : 메시지, 예) HTTP 메시지
* 전송계층:  세그먼트, 예) TCP / UDP 세그먼트
* 네트워크계층 : 패킷, 예) IP 패킷
* 링크계층 : 프레임

<br>

## 01. 전송계층의 기본(UDP/TCP 공통)기능
: Multiplexing / Demultiplexing

* Multiplexing : 각 출발지 프로세스에서 세그먼트를 모아서 전송
* Demultiplexing : 전송받은 세그먼트를 각 목적지 프로세스로 나누어 전달 

### UDP와 TCP의 Demux 동작 비교
* UDP : connection-less, socket 사이 1:1 매핑관계 없음. 
    * port번호만 이용.
* TCP : connection-oriented.
    * TCP 소켓은 고유의 ID(Index) 를 가짐
    * id 구성 
        * src ip주소
        * src 포트번호
        * dest ip주소
        * dest 포트번호 
    * 포트번호만으로는 유일하게 구분할 수 없음. 따라서 이 네 가지의 조합을 이용하여 유일하게 구분 가능함.

    * (지난시간) 클라이언트 첫 요청은 서버의 통합창구 소켓으로 감 -> 연결 맺어지면 accept의 리턴값으로 고유 소켓아이디(src-dest 쌍)가 생기고 이를 이용하여 통신함.

---

## 02. UDP 

* Demux
* Checksum 필드를 이용하여 error detection(check), 오류 발생 시 Drop, 아니면 올려줌.

---
<br>

## 03. TCP Introduction : RDT(Reliable Data Transfer) 원리
: 데이터가 유실되지 않고 전달되는 것.

> "어떻게 unreliable underlying channel을 통함에도 불구하고 reliable 하도록 프로토콜을 디자인할 것인가?"

### rdt1.0 : reliable channel

<br>

### rdt2.0 : channel w/ bit error

* header : checksum / ACK or NAK 
    * checksum 으로 오류 detection
    * acknowledgements(ACKs) : receiver explicitly tells sender(=Feedback) that pkt received OK
    * negative acknowledgements(NAKs) : ~ tells that pkt had errors

* ACKs를 받으면 OK, NAKX를 받으면 재전송함.

<br>

### rdt2.0 의 치명적 결함(fatal flaw)

> feedback에서 에러가 발생한 경우(corner case) 무조건 재전송 -> 제대로 받았는데 ack에서 corrupted되어 재전송된다면? => rdt2.1 

<br>

### rdt2.1 
* header : checksum / ACK or NAK / Sequence No.
* Sequence number 이용.
    * 전송계층에서 전송순서대로 붙여서 보냄
    * 새로운메시지인지 중복된 메시지 구분하여 중복메시지는 drop 처리 가능

<br>

### rdt2.2 : a NAK-free 
: NAK 대신 ACK + 마지막으로 제대로 받은 시퀀스 번호
* header : checksum / ACK / Sequence No. 
* 마지막 전송한 시퀀스번호가 1일때, sender는 receiver로부터 1을 받을 때까지 재전송함.

<br>

### rdt3.0 : channel w/ errors & loss
: 예상시간 내에 receiver로부터 feedback 없으면 data 유실이 발생한 것으로 판단하여 재전송함.
* header : checksum / ACK / Sequence No. / timer
* timer 이용
    * timer 짧은 경우 : 네트워크 유실이 발생한 경우 빠른 대처 가능, 그러나 실제 loss 발생하지 않았는데도 불필요한 재전송 가능성
    * timer 긴 경우 : 실제로 loss 발생한 경우 reaction 늦음

* => trade-off 고려하여 engineering 가능.
