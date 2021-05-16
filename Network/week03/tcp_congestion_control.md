# TCP 프로토콜

## Overview

1. RDT(Reliable Data Transfer) 프로토콜 - TCP 이해를 위한 간단한 프로토콜
2. TCP 개념과 동작
3. 흐름 제어
4. 혼잡 제어

---

<br>

## 02. TCP 개념과 동작 (요약)

### RDT 와의 차이점 : Pipelined

> window size 만큼의 대량의 메시지를 한꺼번에 보내고 피드백 받음

### TCP 특징

- point-to-point
  : 한 쌍의 소켓 사이의 통신
- **reliable**, in-order byte stream
- pipelined
- full duplex data
- connection oriented
- **flow control**
- **congestion control**

### TCP 세그먼트 구조(구성)

#### 1) sequence numbers 필드

- receiver의 트래킹을 가능하게 하여 reliable 하도록 함.
- 세그먼트는 byte 단위로, 맨 앞의 바이트의 sequence number (최소값)가 그 바이트를 포함하는 세그먼트의 대표 sequence number가 됨.

#### 2) acknowledgements 필드

- seq #100 의미 : 99번까지 순서를 유지하며 완벽하게 받았고 100번을 기다리고 있다.

---

<br>

## 03. 흐름제어(flow control) (요약)

- receiver buffer 상황을 고려하여 보내는 양을 조절함

- receiver buffer의 남은 공간에 대한 정보를 header에 담아 sender에 report하는 방법으로 구현 가능함.

---

<br>

## 04. 혼잡제어

> 네트워크 상황을 고려하여 보내는 양을 조절.

### 04-1. congestion

- 라우터 큐의 한정된 크기에 따른 데이터의 유실, 이의 재전송으로 인하여 전송계층은 응용계층의 양보다 더 많은 양의 데이터를 주고 받는다.

* 모든 sender 가 가능한 최대의 데이터를 보내면 오히려 통신 속도가 떨어지거나 데이터 유실이 발생한다.

- data segment 의 ack를 이용하여 네트워크 상태를 추측하여 데이터 양을 결정함.

### 04-2. 전략 : additive increase & multiplicative decrease

- (참고) sender buffer의 window size 에 영향을 미치는 factor

  - receiver window : 리시버가 받아들일 수 있는 데이터의 양
  - congestion window : 네트워크이 받아들일 수 있는 데이터의 양

  - 이 둘 중 작은 값으로 설정함.

* 어떻게 congestion window를 컨트롤하는가?

  1. segment 보내고 그 feedback을 받으면 congestion window 크기를 linear하게 늘임

  2. 혼잡이 발생하면 window 크기를 **절반**으로(=multiplicatively) 줄임

### 혼잡제어의 Phase

#### 1) 처음 : Slow start

- 세그먼트 1개에서 시작해서 Threshold 값까지 exponetially(1,2,4,8, ...) 증가, 이후 linearly 증가

* threshold: 혼잡 발생 가능성 있는 최소값

#### 2) 다음 : Slow start to CA(Congestion Avoidance)

: Loss 가 발생하면 네트워크 혼잡이 있다고 판단

- loss 판단 근거
  - timer expired(=timeout) : 다 안 감
  - triple duplicated ack : 다른 바이트들은 감

#### v1. TCP Tahoe

: 혼잡 발생 시 1개로 다시 시작

#### v2. TCP Reno

: 혼잡 판단 근거가 timer expired 라면 slow start로 돌아가서 segment 1개로 시작
tiple duplicated ack 면 절반으로 줄이고 리니어 증가 시작
