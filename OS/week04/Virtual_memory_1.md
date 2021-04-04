# Virtual Memory (1)

## 1. Page fault

### 1-1. Demand Paging
: 실행중인 프로세스의 모든 페이지를 메모리에 올리지 않고 실제로 필요한 페이지만을 올리는 기법
* 장점
    * I/O 양의 감소
    * (물리) Memory 사용량 감소
    * 빠른 응답시간
    * 더 많은 사용자 수용

### 1-2. Page fault
* 페이지 테이블 각 엔트리의 valid bit 초기 값 : invalid set
* address translation(주소변환) 시 참조한 엔트리의 invalid bit 이 set 되어 있으면 => "page fault" 
* page fault trap 발생 시 운영체제로 CPU가 넘어간다.

### 1-3. 운영체제의 Page fault 처리루틴(Handler)

1. 유효하지 않은 참조인지 확인
    * 이 프로세스가 사용하지 않는 주소를 참조 시도
    * protection violation : 예) code에 write 시도
    * 유효하지 않은 경우 프로세스 종료(abort)


2. empty(free) page frame을 찾는다
    * 꽉 차있는 경우 replace 필요


3. 해당 페이지를 disk 에서 memory의 한 page frame으로 읽어온다
    1. disk I/O가 끝나기 까지 이 프로세스는 blocked (ready qeuue의 다른 프로세스에게 CPU preempt 당함)
    2. disk read 가 끝나면 page table 의 해당 entry에 valid bit = Valid 로 세팅
    3. ready queue 에 process insert


4. 이 프로세스가 CPU를 잡고 다시 running

5. 중단되었던 instruction 재개

## 2. Replacement algorithm Overview : 

## 메모리에 free frame이 없을 때, 어떤 page를 backing store로 내쫒을 것인가?

1. Offline Optimal(=Min, Belady's) Algorithm

2. FIFO (First In First Out)

3. LRU (Least Recently Used)

4. LFU (Least Frequently Used)

--- 

<br/>

## 1. Offline Optimal(=Min, Belady's) Algorithm
: 모든 시점의 페이지 참조를 알고 있다고 가정하여 replace 할 페이지를 판단하는 알고리즘

* 다른 알고리즘에게 upper bound 를 제공하는 역할


<br>

## 2. FIFO (First In First Out)
: 가장 먼저 참조된 페이지를 swap


<br>

## 3. LRU (Least Recently Used)
: 가장 오래 전에 마지막으로 참조된 페이지를 swap

* 연결 리스트로 구현하여 O(1)의 시간 복잡도
    * 페이지가 참조되면 tail로 이동
    * 페이지 swap 필요 시 리스트의 head를 swap 


<br>

## 4. LFU (Least Frequently Used)
: 가장 적게 참조된 페이지를 swap

* 최소힙으로 구현하여 O(logN)의 시간 복잡도
    * 참조횟수 비교 및 트리 재구성에 O(log n), 루트 제거에 O(1) 

