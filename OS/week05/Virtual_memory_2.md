# Virtual Memory (2)

## 00. 캐슁 환경의 교체 알고리즘 (cont')

<br>

> "그런데 페이징 시스템에서 LRU, LFU 알고리즘을 사용 가능한가?"

### 동작 
1. CPU가 주소공간의 instruction 읽음
2. 하드웨어가 논리주소 -> 물리주소 주소 변환
3. Page fault 발생시 CPU제어가 운영체제한테 넘어감
4. 메모리가 가득 찬 경우 필요한 만큼 backing store로 내쫒아야함

=> 이 때, 운영체제가 어떤 페이지가 가장 최근에 사용되지 않았는지 혹은 가장 참조횟수가 적은 페이지가 무엇인지 알 수 있는가? NO.

주소변환한 페이지가 물리메모리에 존재하여 참조가능할 때 CPU 제어는 운영체제가 아닌 실행 중이던 프로세스가 계속 가지고 있기 때문이다. Page fault가 발생해야 비로소 운영체제로 넘어간다.

> [결론] Virtual memory system 에서 LRU LFU 알고리즘을 사용할 수 없다 => 그러면 무슨 알고리즘을 사용하지?

## 01. Clock 알고리즘

: LRU의 근사 알고리즘 (가장 최근에 사용되지 않은 페이지는 아니지만 비슷한 맥락)

*  Second chance, NUR(Not Recently Used), NRU 알고리즘이라고도 불림

### 01-1. 동작
1. 하드웨어는 페이지가 참조되면 circular list의 ref bit 세팅
2. 운영체제는 이 ref bit을 이용하여 어떤 페이지를 내쫒을 지 결정 
    * ref bit 확인 후  0으로 바꿔가며 이동함
    * ref bit 0이면 위치 저장 , 한바퀴 돌고 다시 왔는데도 0이면 내쫒음

### 01-2. 개선
* modified bit 을 추가
* reference bit=1 : 최근에 참조된 페이지
* modified bit=1 : 최근에 변경된 페이지 => backing store 에 그냥 내쫒는 것이 아니라 변경사항 반영해야 함

---

<br>

## 02. Page Frame의 Allocation

<br>

> "멀티프로그래밍 환경에서 각 프로세스에 얼마의 페이지 프레임을 할당할 것인가?"

* (예) for 문을 백만 번 도는데 이 loop는 페이지 세 개를 참조한다. 그런데 이 프로세스에 페이지 두 개만 할당한다면? => 백만번 페이지 폴트남

01. 프레임 개수에 따라
    * Equal allocation
    * Proportional allocation
    * Prioirty allocation
    * 예) Working set, PFF
02. replacement 범위에 따라
    * Global replacement
    * Local reaplacement

### 02 - Overview : Degree of multi programming 이 너무 높을 때 발생하는 문제와 이를 방지하는 Scheme

1. Thrashing
2. Working set
3. PFF(Page Fault Frequency)

---
<br>

### 1. Thrashing
: 동시에 너무 많은 프로그램이 메모리에 올라가 있는 경우 프로세스의 원활한 수행에 필요한 최소한의 page frame 수를 할당받지 못한 경우 page fault rate가 매우 높아져 cpu utilization이 낮아지는 현상

=> 프로세스 당 페이지 프레임 개수를 일정 이상 유지해줘야함 

<br>

### 2. Working set

- (참고) **Locality** of reference
    : 프로세스는 특정 시간동안 일정한 주소를 집중적으로 참조
    * 집중적으로 참조되는 page의 집합을 locality set 이라고 한다.
- Working-set Model
    * locality 기반
    * 프로세스는 **해당 프로세스의 working set 에 포함된 페이지 모두가 메모리에 올라와 있어야 수행**되며 그렇지 않으면 모든 frame을 반납 후 suspended.

- working set 결정
    * working set window
     : 현재로부터 (working set) window의 크기만큼 과거 시점까지 참조됐던 페이지를 워킹 셋으로 결정, 윈도우 사이즈만큼 유지.

<br>

### 3. PFF Scheme
: **page fault rate의 상한값과 하한값을 설정**하여
1. 어떤 프로세스의 page fault rate가 상한값을 넘으면 frame 을 더 할당
2. 어떤 프로세스의 page fault rate가 하한값을 넘으면 frame 할당량을 줄임
3. 빈 frame 이 없으면 일부 프로세스 swap out.

#### 3-1. page size 결정
* 보통 4kB
* page size 작으면
    * 단점
        * 페이지수 증가
        * 페이지 테이블 크기 증가
        * disk transfer 효율성 감소
    * 장점
        * internal fragmentation감소
        * 필요한 정보만 메모리에 올라와서 메모리 이용이 효율적
            * locality 활용 측면에서는 좋지 않음

* => 최근에는 대용량 페이지를 사용하는 메모리 시스템이 나오고 있다.
