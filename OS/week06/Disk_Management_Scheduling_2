# Disk Management and Scheduling (2)

## (앞 부분 개요)
### 01. Disk Structure
* logical block
* sector
* cylinder = track
### 02. Disk Management
* physical formatting
* partitioning
* logical formatting
* booting
---

## 03. Disk Scheduling

### 1. Access time 구성
1. seek time
    * 헤드를 해당 **실린더**로 움직이는 데 걸리는 시간
2. rotational latency
    * 헤드가 **원하는 섹터**에 도달하기까지 걸리는 회전지연시간
3. transfer time 
    * 실제 데이터의 전송시간

### 2. Disk Bandwidth
* 단위 시간 당 전송된 바이트 수

### 3. Disk Scheduling
* seek time을 최소화 하는 목표
* seek time = seek distance

---

## 04. Disk Scheduling algorithm

### 1. FCFS (First come first service)

### 2. SSTF (Shortest seek time first)
* 큐에 들어있는 요청 중 현재 헤드위치 기준 seek time이 가장 짧은 위치에 있는 요청을 가장 먼저 처리함.
* starvation 문제 발생

### 3. SCAN 
* disk arm이 디스크 한 쪽 끝에서 다른 쪽 끝으로 이동하며 가는 길목에 있는 모든 요청을 처리함.
* 다른 한 쪽 끝에서 다시 역방향으로 이동하며 오는 길목에 있는 모든 요청을 처리함.
* 엘리베이터 스케줄링이라고도 불림.
* 실린더 위치(양 끝 / 중심 부근)에 따라 대기시간이 다른 문제 발생

### 4. C-SCAN (Circular SCAN)
* 헤드가 한 쪽 끝에서 다른 쪽 끝으로 이동하며 가는 길목에 있는 모든 요청을 처리하고, 다른 쪽 끝에 도달하면 다시 출발점으로 온다.
* 균일한 대기시간 제공

### 5. N-SCAN 
* 지나가는 도중에 새로 들어온 요청은 다음 번(끝까지 갔다가 되돌아오면서)에 처리

### 6. LOOK & C-LOOK
* SCAN과 C-SCAN 개선
* 이동 방향으로 더 이상 **요청이 없으면 끝까지 가지 않고 곧바로 방향을 반대로** 바꿈.

### 7. disk scheduling 알고리즘 결정
* SCAN, LOOK 이 일반적
* File 할당 방법에 따라 영향
* os 커널 내부 모듈과는 별도의 모듈로 작성되는 것이 바람직함.

---
## 04. Swap-Space management

### Disk 사용 이유
* memory volitile 
    
    => fime system 영속적 저장
* 프로그램 실행 위한 공간 부족

### Swap-Space (Swap area)
* Virtual memory system 에서는 디스크를 memory의 연장 공간으로 사용
* 파일 시스템 내부에 둘 수도 있으나 별도 partition 사용이 일반적임.
    * 공간 효율성보다 속도 효율성이 우선
    * 일반 파일보다 훨씬 짧은 시간만 존재하고 자주 참조됨
    * 따라서 block 크기 및 저장 방식이 일반 파일시스템과 다름
    (파일시스템과 비교하여 큰 단위로 처리됨)

## 05. RAID (Redundant Array of Independent Disks)
### : 여러 개의 디스크를 묶어서 사용

<br>

### 사용 목적
* 디스크 처리 속도 향상
    * 여러 디스크에 block 내용을 **분산저장**하고 이를 병렬적으로 읽어옴 (Interleaving, striping)
* 신뢰성(Reliability) 향상
    * 동일 정보를 여러 디스크에 **중복 저장**
    * 하나의 디스크가 failure 시 다른 디스크에서 읽어옴(Mirroring, Shadowing)
    * 단순한 중복 저장이 아니라 일부 디스크에 (해시값) **parity**를 저장하여 오류 여부를 확인하면서도 공간의 효율성을 높일 수 있음.
