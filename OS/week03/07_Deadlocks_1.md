# Deadlocks (1)



## 교착상태(deadlock)

- 두 개 이상의 작업이 서로 상대방의 작업이 끝나기만을 기다리고 있기 때문에 결과적으로 아무것도 완료되지 못하는 상태.

- 일련의 프로세스들이 서로가 가진 _자원(resource)_을 기다리며 block된 상태.
  - _자원(resource)_ : 하드웨어, 소프트웨어 등을 포함하는 개념 (ex. IO device, semaphere 등)
    - 프로세스가 자원을 이용하는 절차 : Request, Allocate, User, Release

<br>

## Deadlock 발생의 4가지 조건

4가지 조건이 모두 성립할 때 deallock이 발생한다. 4가지 중 하나라도 만족하지 않으면 교착상태는 발생하지 않는다.

### 1. Mutual exclusion (상호 배제)

매 순간 하나의 프로세스만이 독점적으로 자원을 사용할 수 있다.

### 2. No preemption (비선점)

다른 프로세스에 할당된 자원은 사용이 끝날 때까지 강제로 빼앗을 수 없어야 한다.

### 3. Hold and wait (보유 대기)

자원을 가진 프로세스가 다른 자원을 기다릴 때 보유 자원을 내놓지 않고 계속 가지고 있어야 한다.

### 4. Circular wait (순환 대기)

자원을 기다리는 프로세스간에는 사이클이 형성되어야 한다.

- `Pn-1`은 `Pn`이 가진 자원을 기다리고, `Pn`은 `P0`가 가진 자원을 기다린다.

<br>

---

## Resource-Allocation Graph (자원할당그래프)

> deadlock 확인 방법

✔ **그래프에 cycle이 없으면 deadlock이 아니다.**

✔ **그래프에 cycle이 있는 경우,**

- **자원 당 인스턴스가 only 하나라면 deadlock이 있다.**

- **자원의 인스턴스가 여러개 있는 경우라면, deadlock일 수도 있고 아닐 수도 있다.**

<br>

[그림 1] cycle이 없는 그래프 → deadlock (X)

![image-20210328055642670](https://user-images.githubusercontent.com/77573938/112754614-d65d0500-9017-11eb-8e90-ecd94a6d32ea.png)

- 자원`R` → 프로세스`P` (assignment edge) : 자원이 프로세스에 속해있다는 의미
- 프로세스`P` → 자원`R` (request edge) : 프로세스가 자원을 요청한 상태 (요청만 하고 아직 획득하지는 못한 상태)
- 사각형 안의 점 : 자원의 인스턴스들의 개수

<br>

[그림 2] cycle이 있는 그래프

![image-20210328060239316](https://user-images.githubusercontent.com/77573938/112754616-d6f59b80-9017-11eb-9126-892ee45b9507.png)

- 첫번째 그래프 : deadlock (O)

  Why?  비록 R2자원의 인스턴스가 두개이지만, cycle이 두개로 만들어져서 (R2→P1→R1→P2→R3→P3→R2 / R2→P2→R3→P3→R2) 더 이상 진행이 불가능한 상태가 되었기 때문이다.

- 두번째 그래프 : deadlock (X)

  Why?  R1과 R2의 인스턴스가 두개이고, deadlock의 가능성이 있는 프로세스 P1과 P3에는 각각 하나씩만 할당되어있기 때문이다. P4와 P2에 할당된 자원이 다 사용된 후 반납되면, available해져 P1과 P3가 자원을 이용할 수 있게 되어 cycle이 없어진다.

<br>

---

## Deadlock 처리 방법

> _2-avoidance알고리즘, 3, 4번은 Deadlocks (2).md 에 정리했습니다._

숫자가 작을 수록 (Prevention) 처리 방법이 강하다.

1. Deadlock Prevention (예방법)
2. Deadlock Avoidance (회피법)
3. Deadlock Detection and Recovery
4. Deadlock Ignorance

<br>

## 1. Deadlock Prevention (예방법)

데드락이 생기지 않게 방지하는 방법. - 교착 상태 발생 조건 중 하나를 제거함으로써 해결하는 방법이다.

**데드락을 원천적으로 막을 순 있지만 자원에 대한 이용률이 낮아지고, starvation 문제가 발생하며, 전체 시스템의 성능이 나빠진다.**

- 방법 ; 자원 할당 시 Deadlock의 4가지 필요 조건 중 어느 하나가 만족되지 않도록 한다.
  - Mutual Exclusion
    - 이 조건은 공유할 수 없는 자원인 경우 배제할 수 없는 조건이다. (자원을 여러 프로세스가 공유해서 사용가능하다면 애초에 데드락이 걸리지도 않기 때문에)
  - Hold and Wait
    - 방법 1) 프로세스 시작 시 모든 필요한 자원을 할당받게 한다.
    - 방법 2) 자원이 필요한 경우 보유 자원을 모두 보내 놓고 다시 요청한다.
  - No Preemption
    - 자원을 점유하고 있는 프로세스가 다른 자원을 요구할 때 점유하고 있는 자원을 반납하고, 요구한 자원을 사용하기 위해 기다리게 한다.
    - 주로 CPU, memory 에서 사용한다. (CPU와 memory는 state를 쉽게 save하고 restore 할 수 있기 때문)
  - Circular Wait
    - 모든 자원 유형에 할당 순서를 정라여 정해진 순서대로만 자원을 할당한다.

<br>

## 2. Deadlock Avoidance (회피법)

데드락이 생기지 않게 방지하는 방법. - 교착 상태가 발생하면 피해나가는 방법이다.

- 자원 요청에 대한 부가적인 정보를 이용해서 데드락의 가능성이 없는 경우(safe)에만 자원을 할당한다.
  
  - **프로세스가 처음 시작될 때 프로세스가 평생 사용할 자원의 최대 양을 미리 알고 있다고 가정하고 데드락을 피해간다.**
- safe state : 시스템 내 프로세스들에 대한 safe sequence가 존재하는 상태
- safe sequence : 프로세스의 sequence `P1, P2, ..., Pn`이 safe하려면 Pi의 자원요청이 **가용자원 + 모든 Pi의 보유자원**에 의해 충족되어야 한다.
- **deadlock avoidance 방식은 최악의 상황을 가정하기 때문에 deadlock 가능성이 있는 요청에 대해서는 애초부터 받아드리지 않는다.** -> available한 자원이 있다고 다 내어주지 않음.

### _Avoidance 알고리즘_ 

- 자원의 인스턴스가 자원당 only 1개씩 있을 때 - 자원할당 그래프 알고리즘 (Resource Allocation Graph algorithm) 사용
- 자원의 인스턴스가 자원당 여러 개 있을 때 - 은행원 알고리즘 (Banker's Algorithm) 사용

