# Socket Programming

## 01. 소켓이란?
* an interface between application and network 
* OS 라는 우체통의 입구와 같음.
* 다른 프로세스로 데이터 전송하는 데 이용 

---
## 02. 소켓의 종류

### 1. SOCK_STREAM (소켓 스트림) : TCP 기반 소켓
* relaible
* in-order guaranteed
* connection-oriented
* **bidirectional**
### 2. SOCK_DGRAM (소켓 데이터그램) :UDP 기반 소켓
* unreliable
* no order guaranteed
* no notion of "connection" - app indicates dest. for each pocket
* can send or receive

---

## 03. Sockets API
> OS의 시스템 콜 중 네트워크 관련 시스템 콜의 집합

* Creation and Setup
* Establishing a Connection (TCP)
* Sending and Receiving Data
* Tearing Down a Connection (TCP)

### (Overview) Socket Function의 큰 그림 (TCP case)

![이미지](https://github.com/hotpineapple/study-for-Tech-Interview/new/main/Network/image/socket1.png)

#### 서버

1. `socket()` : 소켓을 생성
2. `bind()` : 소켓을 포트에 바인드
3. `listen()` : 
4. `accept()` : 클라이언트의 요청을 받아들임.
5. `read()`

#### 클라이언트

1. `socket()` : 소켓 생성, 서버에 연결
2. `connect()` : 랜덤 포트 배정
3. `write()` 

---

### 1. Socket Creation and Setup
#### Function : socket

#### Function : bind

#### Function : listen

#### Function : accept

### 2. Establishing a Connection (TCP)
#### Function : connect 

### 3. Sending and Receiving Data
#### Function : write


#### Function : read

### 4. Tearing Down a Connection (TCP)

#### Function : close

> Tip : Release of ports
