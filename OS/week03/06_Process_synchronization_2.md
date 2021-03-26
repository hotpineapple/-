# Process Synchronization 프로세스 동기화 (Concurrency Control, 병행제어)

## 지난 강의 요약(Introduction)

### 1. data 를 읽어서 연산 후 결과를 다시 저장할 때, **읽어간 순서에 따라** 저장된 결과가 달라질 수 있다.

⇒ Synchronization 문제

|Execution-box|Storage-box|
|—-|——-|
|CPU|Memory|
|컴퓨터 내부|디스크|
|프로세스|해당 프로세스의 주소공간|

### 2. Race Condition

한 개의 Storage box를 여러 Execution box에서 접근하는 경우,

=> **최종적으로 S-box에는 count-1 (마지막 프로세스의 실행결과)만 남겨짐** (원치 않는 결과)

### Race Condition 이 발생하는 경우

- 멀티 프로세서 환경에서 발생 가능함
- 싱글 프로세서 환경이라도 프로세스 간 공유 메모리를 사용할 경우 발생 가능함
- **(중요) 여러 프로세스의 시스템콜에 의해서 서로 다른 운영체제 커널 코드가 커널 data에 접근하는 경우 발생가능함**
- **(중요) 시스템콜이 요청한 커널 코드 수행 중 인터럽트 발생 시 ISR(인터럽트처리루틴) 실행을 위한 커널 코드 실행이 요구되는 경우 발생 가능함**

## OS 의 Race condition(경쟁조건)
1. kernel 함수 수행 중 인터럽트 발생
2. 한 Process의 kernel mode로 수행 도중 다른 프로세스의 CPU 선점
3. 멀티프로세서 환경에서의 kernel data

### 1. kernel 수행 중 인터럽트 발생

#### count ++ 을 구성하는 instruction

1. load : 메모리 → 레지스터로 count 값 가져옴
2. count 값 증가(increment)
3. store : 레지스터 → 메모리로 count 값 저장함

- 시나리오 : 1~3 수행 중간에 interrupt가 발생하면 또다른 다른 kernel 함수인 ISR이 수행됨.

- 해결방법 : 인터럽트가 들어오면 즉시 ISR을 수행하는 것이 아니라 실행중인 kernel 함수가 끝날때까지 인터럽트를 disable 시킨다.

### 2. 프로세스가 커널모드로 실행 중 다른 프로세스가 CPU 선점

- 시나리오 :

1. 프로세스가 시스템콜을 통해 운영체제에 특정 작업을 요청
2. 커널코드가 실행되어 커널 data 접근 중(=프로세스가 커널모드로 실행)에 
3. 이 때, 이 프로세스의 할당시간이 끝나서 CPU가 다른 프로세스로 넘어가고
4. 그 프로세스의 시스템 콜이 다시 커널코드를 실행하는 경우

- 해결방법 : 프로세스가 커널모드로 실행 중일 때에는 프로세스에 할당 시간이 끝나더라도 곧바로 CPU를 빼앗지 않고, 커널모드가 끝나면 빼앗음.

### 3. Multiprocessor 환경에서 서로 다른 CPU의 공유 메모리 내 kernel data 동시 접근

- 문제 1,2의 해결방법으로는 해결 불가능
- 해결방법 : 데이터를 읽어갈 때 데이터에 Lock 을 걸고 변경 및 저장 후 Lock을 다시 푼다.
    - 방식 1 : **개별 변수에 Lock/Unlock**
    - 방식 2 : 매 순간 하나의 CPU만 커널에 접근가능하도록 ⇒ 비효율적

## 6장 본론

- 데이터의 일관성을 보장하기 위해서 협력 프로세스 간 실행 순서를 정하는 매커니즘이 필요
- **Race condition을 방지하기 위해 동시에 실행되는 프로세스(concurrent)의 동기화(synchronization)가 필수적이다.**

- Critical section : 공유 데이터에 접근하는 코드를 말함
    - P1의 X = X + 1;
    - P2의 X = X - 1;

공유 데이터에 접근하는 코드 앞뒤로 Entry / Exit Section을 둔다.

Entry section에서 lock을 걸어서 다른 프로세스가 critical section을 수행할 수 없도록 하고 Exit section에서 lock을 푼다.

- Mutual Exclusion (상호배제) : 한 프로세스가 크리티컬 섹션을 수행중이면 다른 모든 프로세스들은 자신의 크리티컬 섹션에 들어가면 안됨.
- Progress : 크리티컬 섹션에 어떤 프로세스도 들어가있지 않은 경우에는 프로세스가 들어가도록 허용한다.
- Bounded Waiting (유한대기) : 크리티컬 섹션에 들어가기 위해 지나치게 많이 기다리는 Starvation 이 생겨서는 안된다.

⇒ 해결을 위한 알고리즘들..

(다시 한번 강조하심) issue : critical section의 코드가 여러 개의 instruction으로 구성되어 있고 그 중간에 cpu를 뺏기는 것임.

### 알고리즘 1

- 동기화를 위한 변수

```c
int turn; // initially turn = i in Process i
```

- 프로세스 P0과 P1 가 있다.
- 프로세스 P0 의 코드

```c
do {
	while(turn != 0); /* My turn? 내 차례가 아니면 내 차례가 될 때까지 while 돌면서 기다림. */
	critical section
	turn = 1;         /* Now it's your turn 다른 프로세스로 차례를 바꿔줌 */
	remainder section
} while(1);
```

- 프로세스 P1 의 코드

```c
do {
	while(turn != 1); /* My turn? */
	critical section
	turn = 0;         /* Now it's your turn */
	remainder section
} while(1);
```

만족 : mutual exclusion

문제점 : 아무도 크리티컬 섹션에 없는데도 불구하고 크리티컬 섹션에 들어가지 못할 수 있다. 최초 한번 들어가야 턴이 넘어감

P0은 빈번하게 크리니컬 섹션에 들어가고 싶으나 P1은 한 번만 들어가고 더이상 안들어간다면 P0도 영원히 못들어감 왜냐면 P1이 turn을 바꾸어주지 않기 때문임. 

### 알고리즘 2

- flag변수를 사용 :크리티컬 섹션에 들어가고 싶다는 의중을 표현
- Process i 기준
    1. 들어가고자 한다면 flag[i]를 true로 세팅
    2. flag[j] 도 true라면 기다림
    3. flag[j] 가 false라면 바로 들어감
    4. 크리티컬 섹션에서 나올 때 flag[i] = false 로 되돌려놓음
- 문제점 : flag[i] = true 까지 수행 후 크리티컬 섹션에 들어가기 전에 cpu 선점당하면 둘다 들어가지 못한 상태에서 기다림. (역시 progress 를 충족하지 못함)

### 알고리즘 3

- turn 과 flag를 모두 사용
- Process i의 코드

```c
do {
	flag[i] = true;              /* My intention is to enter... */
	turn = j;                    /* Set to his turn => ????? */
	while(flag[j] && turn == j); /* wait only if ... */ //둘 다 들어가고 싶으면 turn을 따지고, 그게 아니면 그냥 들어간다는 뜻?
	critical section
	flag[i] = false;
	remainder section

} while(1);
```

- 피터슨 알고리즘은 세 조건을 모두 충족한다.
- 문제점 : busy waiting (=spin lock)

    : 프로세스가 할당된 시간동안 계속 cpu와 memory를 쓰면서 wait (다른 프로세스가 바꿔주기 전까지는 while 문 조건 변하지도 않는데 계속 체크)

    ⇒ 해결법 : 뒤에....

### 지금까지의 문제상황은 고급언어에서의 하나의 실행문이 여러 개의 instruction으로 이루어져 있어 그 사이 cpu선점으로 인해 발생한다.

### ⇒ 하나의 실행문이 하나의 instruction 이라면 쉽게 해결될 것이다.

⇒ hardware 가 ***atomic*** test & modify를 지원한다면?

(메모리에서 읽어서 처리후 저장하는 것을 하나의 동작으로)

: 메모리에서 어떤 값을 읽으면 그 값을 1로 세팅함(원래 0이엇든 1이엇든 간에)

## 추상 자료형 Semaphores

- 추상 자료형 : 오브젝트와 오퍼레이션으로 구성됨

    프로그래머에게 논리적으로만 제공되는 자료형이며 실제 구현은 시스템마다 다름.

- 추상 자료형인 세마포어를 통해 프로그래머가 보다 간단한 연산으로 lock을 걸고 풀 수 있다.

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/0a06b4d2-85c2-4b0d-8540-e55874bba78c/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/0a06b4d2-85c2-4b0d-8540-e55874bba78c/Untitled.png)

1. 세마포어의 변수 S(정수) : LOCK이 아닌(=사용가능한?) 자원의 개수
    - 한 프로세스가 P 연산을 하면 S가 한 개 감소
    - 한 프로세스가 V 연산을 하면 S가 한 개 증가
2. 세마포어의 두 가지 (atomic) 연산
    - P 연산 : 공유데이터를 획득하는 과정 (자원을 LOCK)
    - V 연산 : 공유데이터를 사용 후 반납하는 과정(자원을 UNLOCK)
- 그러나 세마포어 사용해도 여전히 busy wait 문제 있다.

- mutex : mutual exclusion
- busy-wait 가 아닌 block & wakeup (= sleep lock,) 방식으로 semaphore 를구현하면?

    : 못쓰면 쓸데없이 while문 돌면서 기다리는 게 아니라  resource 큐에 넣어놓고 잠들도록 함, 이후에 공유자원 락이 풀리면 깨움

## 세마포어의 Busy-wait  해결 방법 : Block & wakeup

- Semaphore의 S : 앞의 자원의 개수와는 의미가 다름
- Semaphore의 P연산 :
    - 자원의 유무에 관계없이 우선 S 감소
    - 자원의 여분 이 있으면 획득
    - 없으면 자원을 기다리는 프로세스 넣고 잠들게 함
- Semaphore의 V연산 :
    - 자원반납하며 S 증가
    - 자원을 기다리는 프로세스가 있다면 (S≤0) 깨움

프로세스의 상태를 바꿔주는 것 또한 Overhead이므로

⇒ 크리티컬 섹션의 길이에 따라.

(일반적으로는 블럭앤 웨이컵 방식을 씀)

자원의 개수가 여러개인 경우 :counting semaphore

자원이 하나인 경우 : binary semaphore

세마포어 사용시 주의할 점 : 원치 않는 문제

## Deadlock and Starvation

- 예) 하드디스크 a 의 내용을 b에 copy 하는 작업은두 자원 모두 획득을 해야 실행 가능하다.
- 세마포어 Q와 S
- 서로 다른프로세스가 Q와 S를 하나씩 차지하고  계속 기다림

⇒ 해결방법 : 자원을획득하는 순서를 정함 (예: S를 먼저 얻어야 Q를 얻을 수 있도록)

## Process Synchronization 과 관련된 Classical Problems(고전적 문제들)

### 1. Bounded-Buffer Problem (=Producer-Consumer Problem)

유한한 크기의 공유 버퍼

다수의 Producer(생산자) 프로세스와 다수의 Consumer(소비자) 프로세스가 있다.

- producer : 락을 걸고 데이터를 넣음, 락을 품
- 컨슈머 : 락을 걸고 데이터를 뺌, 락을 품: 락을 걸고 데이터를 뺌, 락을 품

- Semaphore 변수
    - mutex : lock 걸기 위한 변수
    - full : 내용이 들어있는 버퍼 개수 저장
    - empty : 비어있는 버퍼 개수 저장
- 생산자 프로세스 입장에서의 자원 : empty 버퍼
- 소비자 프로세스 입장에서의 자원 : full 버퍼

### 2. Readers-Writers Problem

주로 DB Read/ write시 발생

- 공유 데이터 : DB
- 읽는 프로세스 : 읽고잇는데 다른 리더 프로세스가 도착 시 걔도 읽을 수 잇음. 락은걸어야함 쓰는 프로세스는 막아야하니까
- 쓰는 프로세스 : 쓰고 잇는데 다른 롸이터 프로세스가 도착하면 락이 걸림(독점)
- 리더 카운트 변수
- db : DB r/w 에 대한 Lock
- mutex : readcount 에 대한 lock
- starvation
- 해결방법

### 3. Dining-Philosophers Problem

두번째 해결방안 구현 코드(세마포어)

세마포어와 모니터의 목적 : 프로세스 동기화문제를 프로그래머가 보다 쉽게 해결하기 위해 제공됩

모니터

두번째 해결방안 구현 코드(모니터)

프로그래머의 실수가 시스템에 중대한 영향

모니터 내부에 공유데이터와 그 접근을 위한 프로시저가 구현되어잇음

1. 프로시저를 통해서만 공유데이터 접근
2. 동시에 여러 프로시저 실행 불가능

⇒ 장점 : 프로그래머가 락을 걸고 풀 필요가 없다.
