# TCP 프로토콜

## Overview

1. RDT(Reliable Data Transfer) 프로토콜 - TCP 이해를 위한 간단한 프로토콜
2. TCP 개념과 동작
3. 흐름 제어
4. 혼잡 제어

---

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

## 03. 흐름제어(flow control) (요약)

- receiver buffer 상황을 고려하여 보내는 양을 조절함

- receiver buffer의 남은 공간에 대한 정보를 header에 담아 sender에 report하는 방법으로 구현 가능함.

---

## 04. 혼잡제어 (수정 예정)

> 네트워크 상황을 고려하여 보내는 양을 조절.

### congestion :

* liminted 라우터 큐
* data loss 에 따른 재전송

###
