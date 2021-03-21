# Chapter 6. Process Synchronization (1)

<br>

## 1. Process Synchronization (프로세스 동기화)

> 운영체제에서 Synchronization(동기화)란 ?
>
> -> 프로세스 또는 스레드 들이 수행되는 시점을 조절하는 것

<br>

### Process Synchronization 문제 발생 이유

- 데이터가 저장되어 있는 위치에서(storage-Box) 읽어와서 연산을 한 후(Execution-Box) 다시 원래 위치로 반영하기 때문에 발생.

- 데이터를 읽기만 하면 별로 문제가 되지 않는데, 읽어온 데이터를 연산&수정, 저장하는 방식이 문제를 야기한다.

- **즉, 공유 데이터(shared data)의 동시 접근(concurrent access)이 데이터의 불일치 문제(inconsistency)를 발생시킬 수 있다.**

  -> 일관성(consistency) 유지를 위해서는 협력 프로세스(cooperating process) 간의 실행 순서(orderly execution)를 정해주는 매커니즘이 필요하다. 

![image-20210321233514290](https://user-images.githubusercontent.com/77573938/111911957-eec6a000-8aaa-11eb-9ac8-8acf37c3d035.png)

<br><br>

## 2. 경쟁 상태 (Race condition)

: 두 개 이상의 concurrent한 프로세스들이 공유된 자원에 접근하려고 할 때 동기화 메커니즘 없이 접근하려고 하는 상황. 

즉, **동시에 접근하려고 해서 문제가 생기는 경우**

- 데이터의 최종 연산 결과는 마지막에 그 데이터를 다룬 프로세스에 따라 달라진다.
- Race condition을 막기 위해서는 concurrent process는 동기화(synchronize) 되어야 한다.

<br>

![image-20210321231740590](https://user-images.githubusercontent.com/77573938/111911966-f423ea80-8aaa-11eb-9966-8cd0e936cec0.png)

- count를 1씩 증가하고 뺀다면 count의 변화는 0가 되어야 한다. 그러나, 위 그림처럼 Storage box를 여러개의 Execution box가 공유하는 경우, `count++`이 데이터를 가져가서 1을 증가시키는 동안 `count--`가 그 데이터를 또 읽어간다. 
- 1 더하고 1 뺐음에도 불구하고 더한 것은 반영이 안되고 `count--`가 더하기 전의 데이터를 읽어가서 연산했기 때문에 1을 뺀 결과만 저장된다.

<br>

### 운영체제 커널과 관련되서 생기는 문제가 중요하다 !

- 프로세스는 일반적인 경우라면 자기 주소 공간만 접근하기 때문에 Race Condition이나 Process Synchronization과 관련된 문제가 발생할 가능성은 없다.
- 그러나, 프로세스들이 직접 시행할 수 없는 부분(운영체제한테 대신 요청해야 하는 부분)에 대해서는 System call을 하기 때문에 커널의 코드가 그 프로세스를 대신해서 실행되어 커널에 있는 데이터에 접근한다. 이런식으로 여러개의 커너 모드가 실행 될 경우 Race condition 문제가 발생할 수 있다.

- 또한, 커널의 코드가 실행 중일 때 인터럽트가 들어오면 역시 커널의 데이터를 건들이는 것이기 때문에 Race condition 문제가 발생할 수 있다.

<br><br>

## OS에서 Race condition은 언제 발생하는가 ?

### 1. kernel 수행 중 인터럽트 발생 시

커널의 코드가 수행되는 도중에 인터럽트가 발생해 인터럽트 처리를 하면, 커널의 데이터를 양쪽에서 건들이는 도중에 CPU를 얻어가 처리했기 때문에 문제가 발생한다.

![image-20210321235348902](https://user-images.githubusercontent.com/77573938/111911970-f7b77180-8aaa-11eb-83ec-5159900c6c30.png)

- 일반적으로 `count++` 과 같이 고급언어로 된 문장이 CPU 내부에서는 여러 개의 instruction을 통해서 수행된다. 
- 이때, interrupt가 들어오면 작업을 멈추고 interrupt 처리 루틴으로 넘어간다 -> interrupt handler
-  interrupt handler도 커널의 코드이기 때문에 `count--` 이 실행된다. 인터럽트 처리가 다 끝나면 다시 `count++`로 되돌아온다.
- 그런데 결과적으로는 1 감소시킨 것은 반영이 안되고 1 증가시킨 것만 반영이 된다. 



#### 해결방법

- 중요한 변수를 건들이는 동안에는 인터럽트가 들어와도 작업이 끝날 때까지 인터럽트 처리를 하지 않는다.
- 작업이 끝난 후, 인터럽트 처리 루틴으로 넘어간다.

<br>

### 2. Process가 system call하여 kernel mode로 수행 중인데 context switch가 일어나는 경우

![image-20210321235643009](https://user-images.githubusercontent.com/77573938/111911973-fb4af880-8aaa-11eb-9cca-932db2b0fc17.png)

- 어떤 프로세스는 본인의 코드만 실행하는게 아니라 system call을 통해 OS한테 서비스를 대신해달라고 요청하는 경우가 있다.
- 프로그램은 user mode와 kernel mode를 번갈아가며 실행하는데
- 이 때, 프로그램이 CPU를 독점적으로 쓰는 것이 아니라 할당시간이 있어 할당시간이 끝나면 CPU를 반납하게 된다.

![image-20210321235741832](https://user-images.githubusercontent.com/77573938/111911982-fede7f80-8aaa-11eb-9fce-0b5cf528966f.png)

- 프로그램A가 CPU를 사용하고 있다 할당시간이 끝나면, B로 CPU가 넘어간다. 마찬가지로 B의 할당시간이 끝나면 다시 A한테 CPU가 넘어간다.

- 그런데, 위 그림의 경우, system call을 하여 kernel의 코드가 실행되면서 `count++` 이 되고 있는 **도중에** 할당시간이 끝나 CPU가 B한테 넘어갔다. B에서 또 system call을 하여 `count++` 하는 작업을 했고, 할당시간이 끝나 A한테 다시 CPU가 넘어왔다. 그러면 `count++` 이 2번 실행되어 총 2가 증가되어야 하는데 **B에서 증가된 것은 반영이 안된다.** 

  -> Why?  B에서 1 증가되기전에 A에서 `count++` 을 읽어들였기 때문에 그 시점의 context를 가지고 1 증가시킨 후 저장했기 때문이다.



#### 해결방법

- 프로세스가 kernel mode에 있을 때는 할당 시간이 끝나도 CPU를 뺏기지 않도록 한다. 
- 할당 시간이 끝났더라도 kernel mode가 끝나고 user mode로 빠져나올 때 CPU를 preempt한다.

<br>

### 3. Multiprocessor에서 shared memory 내의 kernel data

![image-20210321235825046](https://user-images.githubusercontent.com/77573938/111911990-06058d80-8aab-11eb-8737-4efbfa0ca621.png)

- 근본적으로 작업 주체가 여럿이기 때문에 발생하는 문제이므로 앞의 방법으로는 해결 할 수 없다.



#### 해결방법

- CPU가 여러개인 환경에서는 데이터에 접근할 때 `lock`을 걸어 다른 CPU가 접근하지 못하도록 한 후, 데이터를 변경하고 저장이 끝나면 `unlock`을 한다.
- 또는, 커널에 접근하는 CPU를 매 순간 하나만 접근할 수 있게하여 문제를 해결한다. (커널 전체를 하나의 lock으로 막는 방법)
  - 그러나, 이 방법은 CPU가 여러개 있더라도 커널에 접근할 수 있는 CPU는 매순간 하나밖에 없어 매우 비효율적이다.
  - -> 커널 전체에 lock을 거는 방법보다는 각각의 데이터에 lock을 거는 방법을 권장한다.

<br><br>

## [Example]

두개의 프로세스가 공유데이터에 접근해서 하나는 1 증가시키는 작업을 하고 하나는 1 감소시키는 작업을 한다면, consistency가 깨진 불일치 문제가 발생할 수 있다.



![image-20210322000836769](https://user-images.githubusercontent.com/77573938/111911996-09991480-8aab-11eb-802f-a5380fbf2395.png)

**Q.** 사용자 프로세스 P1 수행 중 timer interrupt가 발생해서 context switch가 일어나 P2가 CPU를 잡으면 문제가 발생하는가?

**A.** 보통은 문제가 생기지 않는다. 특별히 두 프로세스 간에 공유데이터를 사용하는 경우나, P1의 실행 도중 커널의 데이터를 건들이는 동안에 CPU가 P2로 넘어간다면 문제가 발생한다.

*보통 프로세스들은 자기 자신만의 독자적인 주소 공간의 데이터를 취급하기 때문에 **단순히 프로세스 P1에서 P2로 넘어간다고 문제가 발생하지는 않는다!***

<br><br>

## The Critical-Section Problem

> Race condition 문제를 해결하는 방법

- **Critical-Section : 공유 데이터를 접근하는 코드**

![image-20210322002220502](https://user-images.githubusercontent.com/77573938/111912000-0b62d800-8aab-11eb-8e6e-42c31ab05730.png)

- n개의 프로세스가 공유데이터를 동시에 사용하기를 원하는 경우, 각 프로세스의 code segment에는 critical section이 존재한다.
- **하나의 프로세스가 공유데이터에 접근하는 코드를 실행중이면** (==critical section에 있으면), CPU를 뺏겨서 다른 프로세스로 CPU가 넘어가더라도 **다른 모든 프로세스는 critical section에 들어갈 수 없어야 한다.**
-  critical section에서 빠져 나왔을 때, 공유데이터에 접근할 수 있어야 한다.