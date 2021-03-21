# CPU Scheduling (1)

## 01. CPU Bursts & I/O Bursts in Program Execution

- CPU burst time : CPU가 작업을 수행하는 시간
- I/O burst time : I/O 요청에 대한 응답을 기다리는 시간



### 컴퓨터 프로그램이 실행될 때 거치는 단계

CPU에서 instruction을 실행하는 단계(**CPU burst**)와 IO작업(**IO burst**)을 하는 단계를 번갈아가며 실행한다.

![image-20210321213629528](https://user-images.githubusercontent.com/77573938/111907358-4a3b6280-8a98-11eb-99a0-015a82bdd2bc.png)



- IO가 빈번히 끼어들지 않는 프로그램의 경우에는 CPU burst가 길게 나타난다.

- 사람하고 interaction하는 프로그램의 경우 중간중간 화면출력과 키보드 입력 등 중간에 IO가 끼어들게 되면서 자연스럽게 CPU를 연속적으로 쓰는 단계(CPU burst)가 짧아지게 된다.

<br>

## 02. CPU-burst time의 분포

- **IO bound job : CPU burst가 짧은 경우**
  - 중간에 IO가 많이 끼어들어서 CPU burst가 짧게 나타남
- **CPU bound job : CPU burst가 아주 긴 경우**
  - 중간에 IO가 끼어들지 않아서 CPU burst가 굉장히 길게 나타남

![image-20210321213253290](https://user-images.githubusercontent.com/77573938/111907362-4f001680-8a98-11eb-936d-b401bc511a7b.png)

- CPU burst가 짧은 경우가 빈번하고(IO bound job), CPU burst가 아주 긴 경우는(CPU bound job) 간혹 나타난다.
- 컴퓨터 프로그램은 모두 동일한 패턴을 가지는게 아니라 CPU burst가 긴 것과 짧은 것이 다 섞여 있어서 CPU Scheduling이 필요하다.
- 특히, **IO bound job은 사람하고 interaction** 하기 때문에 너무 늦게 CPU를 주게되면 응답시간이 길어져서 사람이 오래기다려야 한다. 



## 03. CPU Scheduling

:  현재 레디큐에 들어와있는 CPU를 얻고자하는 프로세서 중에서 어떤 프로레서한테 CPU를 줄것인지를 결정하는 메커니즘.

- CPU 스케줄링의 두가지 이슈

  1. **어떤 프로세스한테 CPU를 할당할 것인가?**
  2. **일단 줬으면 계속 쓰게 할 것인가?**
     - 이 프로그램이 CPU를 다 쓰고 IO를 하러 나갈 때까지 CPU를 계속 주느냐? or 이 CPU burst가 너무 길기 때문에 중간에 CPU를 뺏어서 다른 프로세서한테 CPU를 넘겨줄 것이냐?

- ***중요 이슈 !!***

  - CPU burst가 굉장히 큰 프로그램(CPU bound job)한테 CPU가 넘어가면 그 뒤에 도착하는 IO bound job들은 CPU를 조금만 주면 바로 쓰고 나갈 프로그램임에도 불구하고 긴 프로세서가 CPU를 내주지 않으면 전부 기다려야 함.

    -> CPU scheduling은 IO bound job (사람하고 interaction하는 job) 들이 지나치게 오래 기다리지 않도록 하는 방향으로 이루어져야 한다.

- 공평한 스케쥴링도 필요하지만 효율성도 중요하다.

<br>

### CPU Scheduler & Dispatcher

- CPU Scheduler (OS 안에서 수행되는 코드)

  : ready 상태의 프로세스 중에서, 이번에 CPU를 줄 프로세스를 고른다. (결정)

- Dispatcher (OS 안에서 수행되는 코드)

  : CPU의 제어권을 CPU scheduler에 의해 선택된 프로세스에게 넘긴다. (준다)

  이 과정을 문맥교환(context switch)이라고 한다.

<br>

#### CPU 스케줄링이 필요한 경우

: 프로세스에게 다음의 상태 변화가 있을 때.

1. Running -> Blocked (ex. I/O 요청하는 시스템콜)

2. Running -> Ready (ex. 할당시간 만료로 timer interrupt)

3. Blocked -> Ready (ex. I/O 완료 후 인터럽트)

4. Terminate

<br>


### 스케줄링 방법

- **Non-preemptive Scheduling (비선점형 스케줄링)** : 강제로 CPU를 빼앗지 않는 방법
- **Preemptive Scheduling (선점형 스케줄링)** : 강제로 CPU를 빼앗을 수 있는 방법
  - *강제로 빼앗는 수단으로 timer라는 하드웨어를 두고 timer interrupt를 둬서 뺏을 수 있었음.* 
  - 현대적인 CPU 스케줄링이 대부분 사용하는 방식

<br>

### Scheduling Criteira (성능 척도)

: Scheduling Algorithms을 평가하는 방법

| 시스템 입장에서 성능척도                                     | 프로그램 입장에서 성능척도                                   |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| **CPU Utilization (CPU 이용률)** <br>: 전체 시간 중 CPU가 놀지 않고 일한 시간의 비율.<br>  가능하면 놀지않고 최대한 일하는게 좋음 | **Turnaround time (소요시간, 반환시간)** <br/>: CPU를 쓰러 들어와서 다 쓰고 나갈 때까지 걸린 시간.<br>  짧아야 좋음 |
| **Throughput (처리량)** <br/>: 주어진 시간동안 몇 개의 작업을 처리하는가.<br/>  가능하면 많이 처리하도록 하는게 좋음 | **Waiting time (대기시간)** <br/>: CPU가 서비스를 받기 위해 Ready Queue에서 기다린 시간. |
|                                                              | **Response time (응답시간)** <br/>: Ready Queue에 들어와서 처음으로 CPU를 얻기 까지 걸린 시간. |

- **주의 )** 성능척도는 프로세스가 처음 시작해서 종료 될 때까지를 의미하는 것이 아님!  
  		  CPU 관점만 따지고 있기 때문에 CPU burst로 계산. 즉, Ready Queue에 들어온거에 한에서 스케줄링이 계산되고 있는 것임!

<br>

## 04. CPU Scheduling Algorithms

### FCFS (First-Come, First-Served)

: 먼저 온 순서대로 처리

- **Non-preemptive** -> 효율적이지는 않음.



[Example]


| Process | Burst Time |
| :-----: | :--------: |
|   P1    |     24     |
|   P2    |     3      |
|   P3    |     3      |

- Example 1 

  - 프로세스 도착 순서 :  P1, P2, P3
  - Waiting time : P1=0; p2=24; p3=27

  - Average wating time : (0+24+27)/3 = 17

- Example 2

  - 프로세스 도착 순서 :  P2, P3, P1
  - Waiting time : P1=6; p2=0; p3=0
  - Average wating time : (6+0+3)/3 = 3

-> 프로세스 도착 순서가 중요

- **Convoy effect** : 긴 프로세스가 하나 도착해서 짧은 프로세스들이 지나치게 오래 기다려야 하는 현상

---

### SJF (Shortest-Job-First)

: CPU burst(작업 시간)가 가장 짧은 프로그램부터 먼저 처리

- Non-preemptive

  - 지금 기다리는 프로세스 중에서 CPU를 제일 짧게 쓰는 프로세스한테 CPU를 줬으면, 더 짧은 프로세스가 도착하더라도 이전 프로세스의 CPU burst가 완료될 때까지 CPU를 보장해줌.

- Preemptive

  - CPU를 줬다 하더라도 더 짧은 프로세스가 도착하면 CPU를 뺏어서 새로운 프로세스에게 CPU를 넘겨줌. -> **SRTF (Shortest-Remaining-Time-First)**

- SJF is optimal 

  : 주어진 프로세스들에 대해 **mimimum average waiting time** 을 보장 *<- Preemptive 버전 !*



[Example]


| Process | Arrival Time | Burst Time |
| :-----: | :--------: | :--------: |
|   P1    |     0.0     |7|
|   P2    |     2.0     |4|
|   P3    |     4.0     |1|
|   P4    | 5.0 |4|

- Example 1)  Non-preemptive

  - 처리 순서 :  P1(0-7초) -> P3(7-8초) -> P2(8-12초) -> P4(12-16초)
  - Average wating time : (0+6+3+7)/4 = 4
- Example 2)  Preemptive

  - 처리 순서 :  P1(0-2초) -> P2(2-4초) -> P3(4-5초) -> P2(5-7초) -> P4(7-11초) -> P1(11-16초)
    - 0초 시점에는 P1 혼자 도착해서 P1이 CPU를 얻었지만, 2초 시점에 P2가 더 짧은 프로세스이므로 빼앗게 된다. 마찬가지로 4초 시점에 P3가 가장 짧은 프로세스여서 선점하여 처리한 후, 다시 2초가 남은 P2가 작업을 수행하게 된다.
  - Average wating time : (9+1+0+2)/4 = 3
    - Non-preemptive 버전보다 더 짧은 시간이 걸림 (mimimum)



**=> 그럼 SJF 알고리즘이 베스트 아니냐? -> No. 두가지 문제점 있음**

1. Starvation (기아 현상)
   
   - SJF는 극단적으로 CPU사용이 짧은 job을 선호하기 때문에 CPU 사용이 긴 프로세스는 영원히 서비스를 못 받을 수 있다는 단점 존재
   
2. CPU 사용시간을 미리 알 수 없다는 것
   - **exponential averaging** ; 과거의 CPU burst time을 통해 시간을 추정(estimate)하는 방법

     ![277E5B4157479EE416](https://user-images.githubusercontent.com/77573938/111907367-57585180-8a98-11eb-8fdc-fbd16ddaa536.png)

     ​		α 와 (1-α)가 1보다 작으므로, 후속되는 각 항목은 이전 항목보다 적은 가중치 값을 가짐

---

### Priority Scheduling 

: 우선 순위가 가장 높은 프로세스에게 CPU 할당

- Non-preemptive

  - 우선순위가 가장 높은 프로세스한테 CPU를 이미 할당한 경우, 우선순위가 더 높은 프로세스가 도착하더라도 일단 CPU를 한번 줬기 때문에 빼앗을 수 없음
- Preemptive

  - 우선순위가 가장 높은 프로세스한테 CPU를 이미 할당한 상태에서, 우선순위가 더 높은 프로세스가 도착한다면 CPU를 빼앗음
- 우선순위를 정의하는 방법 ; 각 프로세스마다 우선순위를 정수로 표현한다. (smallest integer - highest priority)
- SJF는 일종의 Priority scheduling 이다.  ->  *priority = predicted next CPU burst time*
- 문제점
  - **Starvation (기아 현상)** : 우선순위가 낮은 프로세스가 지나치게 오래 기달려서 경우에 따라 영원히 CPU를 얻지 못하는 문제점
  - 컴퓨터 시스템은 효율성 추구가 가장 중요하지만, 특정 프로세스가 지나치게 차별 받는것도 막아야 한다.
- 해결방법
  - **Aging (노화)** : 아무리 우선순위가 낮은 프로세스라고 하더라도 오래 기다리게 되면 우선순위를 조금씩 높여줌.
  - 아주 오래 기다리면 우선순위가 높아져, 언젠가는 CPU를 얻게되어 Starvation을 막을 수 있다.

---

### RR (Round-Robin)

: 빙빙 돌아가면서 할당

- **Preemptive**

- 현대의 컴퓨터 시스템에서 사용하는 방식
  - CPU를 줄 때 할당시간을 세팅해서 넘겨주고, 할당시간이 끝나면 timer interrupt를 걸어 CPU Scheduling을 하는 것이 RR 스케줄링에 기반한 방법이다.
- 각 프로세스는 동일한 크기의 **할당시간(Time quantum, Time Slice)** 을 가지며, 그 시간이 끝나면 timer interrupt에 의해 CPU를 빼앗겨 ready queue의 제일 뒤에 가서 다시 줄을 선다.

- 장점 :  Response time이 빨라짐 (조금씩 CPU를 줬다 뺏었다하기 때문에 아주 짧은 시간만 기다리면 CPU를 한번씩 할당 받을 수 있음)
- 대기시간은 CPU를 사용하려는 시간에 비례함
- Performance    *(q: Time quantum)*
  - q large -> FCFS
  - q small -> context switch 오버헤드가 커져 시스템 전체의 성능이 나빠질 수 있음. 

[Example] **RR with Time Quantum = 20**


| Process | Burst Time |
| :-----: | :--------: |
|   P1    |     53     |
|   P2    |     17     |
|   P3    |     68     |
|   P4    |     24     |

![image-20210321212713440](https://user-images.githubusercontent.com/77573938/111907371-5aebd880-8a98-11eb-8736-a74921131dd0.png)


- 일반적으로 **SJF보다 average turnaround time이 길지만 response time은 더 짧다**

---


### Multilevel Queue (다단계 큐)

: 큐를 여러개 돌림

- Ready queue를 여러개로 분할
  - foreground - interactive job (human interaction)
  - background - batch형 job (no human interaction)
- 각  큐는 독립적인 스케줄링 알고리즘을 가짐
  - foreground - RR
  - background - FCFS
- 큐에 대한 스케줄링이 필요
  - **Fixed priority scheduling** (우선 순위를 아주 강하게 적용하는 방식)
    - foreground가 비어있을 때만 background한테 CPU할당
    - 그러나, 이 경우 starvation이 발생할 가능성이 있다.
  - **Time slice**
    - 각 큐에 CPU time을 적절한 비율로 할당
    - eg.  80% to foreground in RR, 20% to background in FCFS



![image-20210321213752263](https://user-images.githubusercontent.com/77573938/111907377-63441380-8a98-11eb-8de4-cb1809b50c92.png)

#### Process groups ; 프로세스를 그룹화

프로세스의 우선순위가 있음. highest priority부터 우선 수행하며, 전부 수행되면 그 다음 lower priority를 수행함

- System processes : OS에서 작업. 가상 메모리, IO, 통신. 가장 빨리 처리해야 함
- Interactive processes : 사용자랑 interaction하는 프로그램
- Interactive editing processes : 편집하는 프로그램
- Batch processes : CPU만 오랫동안 사용하는 job들. 대화형이 아닌 프로세스. 꾸러미로 일괄적으로 처리
- Student processes

-> 프로세스는 여러개인데 CPU는 하나라 고려해야할 사항 존재

- 프로세스를 어느 줄에 넣을 것인가
- 우선순위가 낮은 프로세스는 starvation을 겪음

---

### Multilevel Feedback Queue

: 프로세스가 다른 큐로 이동 가능

![image-20210321215434905](https://user-images.githubusercontent.com/77573938/111907381-663f0400-8a98-11eb-8a0f-d5ac73324419.png)

- 복수 개의 Queue
- 일반적으로, 처음 들어오는 프로세스는 우선순위가 가장 높은 큐에 넣는다. 우선순위가 가장 높은 큐는 RR에서 time quantum을 아주 짧게 주며 밑의 큐로 갈수록 RR의 time quantum을 점점 길게 준다. 가장 아래의 큐는 FCFS 방식 사용한다.
- 처음에 들어올때는 어떤 프로세스던지 짧은 시간을 주기 때문에 CPU 사용시간의 예측이 필요 없다.