프로세스란 실행중인 프로그램을 의미한다.

# 프로세스의 문맥(context)

⇒ 시스템이 time sharing (프로세스들이 번갈아가면서 실행) 하기 위해 필요하다.

### 1. **하드웨어**

1. 프로세스가 어디까지 실행되었는가 (=PC가 프로세스 주소공간의 code의 어느 부분을 가리키고 있는지)
2. register들의 값

### 2. **주소공간** (memory 영역)

1. code
2. data (변수 값 등)
3. stack (프로세스의 함수 호출 히스토리 등)

### 3. **커널 자료 구조**

1. PCB(Process control block)
    - 운영체제가 프로세스를 관리하기 위한 자료구조
    - 프로세스가 실행될 때마다 주소공간의 data에 생성됨
2. 커널 스택
    - 프로세스가 시스템 콜(I/O작업 등)을 하면 PC가 커널의 code를 가리키고 해당 instruction을 실행한다.
    - 이 과정에서 함수를 호출할 경우커널 주소공간의 stack 중 **해당 프로세스의 커널스택 영역에** 관련 정보를 쌓게 된다.

![커널]()

- 그림설명 : 프로세스가 CPU를 잡게 되면, CPU의 PC는 이 프로세스 주소공간의 code 한 부분을 가리키고 있다. 가리키는 instruction을 읽어들여 레지스터에 저장하고  이를 ALU를 통해 산술/논리연산을 하게 된다.

# 프로세스의 상태(Process State) (1)

프로세스는 상태가 변경되며 수행된다.

### 1. Running

CPU를 잡고 instruction을 수행중인 상태

### 2. Ready

CPU를 기다리는 상태(당장 실행할 code가 메모리에 올라와 있는 등 다른 모든 조건은 만족)

### 3. Blocked(wait, sleep)

CPU가 주어져도 당장 instruction을 수행할 수 없는상태

예) 프로세스가 요청한 이벤트(I/O 등)가 즉시 만족되지 않아 이를 기다리고 있는 경우

예) memory에 필요한 code가 없어 디스크에서 읽어와야 하는 경우


# 문맥 교환

CPU를 한 프로세스에서 다른 프로세스로 넘겨주는 과정을 의미한다.

이 과정에서 **운영체제**는 

1. CPU를 내어주는 프로세스의 상태(PC, 레지스터 값 등)를 그 프로세스의 PCB(메모리 > 커널 주소공간의 data에 위치)에 저장
2. CPU를 얻는 프로세스의 상태를 그 프로세스의 PCB에서 읽어옴


### 헷갈리기 쉬운 개념!!

- System call 이나 interrupt 발생 시 반드시 context switch가 일어나는 것은 아니다.

    ⇒ 즉 CPU 가 운영체제로 넘어가는 것은 문맥교환이 아니며, 문맥교환은 **서로 다른 사용자 프로세스 간** CPU사용권 이동을 말한다.

1. 일반적으로 프로세스가 System call을 하거나 프로세스의 실행 도중 interrupt 가 발생하면 운영체제로 CPU가 넘어가고, 처리 완료 후 운영체제는 System call을 발생시킨 프로세스 혹은 interrupt가 발생했을 당시 실행중이던 프로세스로 다시 CPU를 넘긴다.
    - context 일부를 PCB에 save 해야 하지만 2에  비해 부담이 적다.
2. 반면 Timer interrupt 혹은 I/O 요청을 위한 System call의 경우 운영체제는 기존에 실행중이던 프로세스가 아닌 다른 프로세스로 CPU를 넘긴다. (=문맥교환)
    - 이 경우 기존 프로세스가 사용하던 cache 메모리를 flush하는 등 부담이 크다.

# 프로세스를 스케줄링하기 위한 큐

실제 큐에 저장되는(head와 tail이 가리키는) 것은 커널의 PCB의 pointer이다.

### 1. Job queue

현재 시스템 내 모든 프로세스의 집합

### 2. Ready queue

현재 메모리 내에 있으면서 CPU를 기다리는 프로세스의 집합

### 3. Device queue

각 I/O Device 의 처리를 기다리는 프로세스의 집함


# 스케줄러(Scheduler)


### 1. Long-term 스케줄러(장기스케줄러, Job 스케줄러)

- 시작 프로세스 중 어떤 것들을 ready queue로 보낼지 결정
- **프로세스에 "메모리"** 및 각종자원을 **주는 문제**
- **degree of multi-programming**을 제어
- 일반적인 time sharing system은 장기스케줄러를 갖지 않는다(무조건 ready 상태로 감).

    ⇒ 대신 중기스케줄러를 이용하여 degree of mp 제어함.

### 2. Short-term 스케줄러(단기 스케줄러, CPU스케줄러)

- 어떤 프로세스를 다음 번에 **running** 시킬지 결정
- **프로세스에 "CPU"를 주는 문제**
- 충분히 빨라야 함(millisecond 단위)

### 3. Medium-term 스케줄러(중기 스케줄러, Swapper)

- 메모리의 여유 공간 마련을 위해 프로세스를 통째로 **메모리** → 디스크로 쫒아냄
- **프로세스에게서 "메모리"를 뺏는 문제**
- degree of multi-programming을 제어
- 프로세스 상태 중 **suspended* 상태를 추가함**

### cf. degree of multi-programming 제어와 CPU 성능 이슈

1. 메모리에 너무 적은 프로그램이 올라간 경우 :

    I/O 처리를 기다리는 동안 CPU는 아무것도 할 수 없으므로 성능 저하

2. 메모리에 너무 많은 프로그램이 올라간 경우 :

    프로그램 당 할당된 메모리 영역이 적어 수행할 instruction을 디스크에서 더 자주 읽어와야 하므로 성능 저하

# 프로세스의 상태(Process State) (2)

### 1. Running

### 2. Ready

### 3. Blocked(wait, sleep)

- 프로세스가 I/O등의 이벤트를 기다리는 동안 **자발적으로** 이동한 상태
- CPU는 이 프로세스 관련 일을 하고 있지 않지만, 프로세스 관점에서 보면 I/O처리는 계속 하고 있는 중임

### 4. Suspended(stopped)

- **외부적인 이유**로 프로세스의 수행이 정지된 상태
- 프로세스는 통째로 디스크에 swap out됨
- 예)
    1. 사용자가 프로그램을 일시정지시킨 경우(리눅스 ctrl+z 등의 break key) 
    2. 시스템이 여러 이유로 프로세스를 잠시 중단 시킴(메모리에 너무 많은 프로세스가 올라와 있을 때)

### (유의) Block과 Suspended의 차이점

: Block 상태는 자신이 요청한 event가 완료되면 Ready 상태가 되고, Suspended 상태는 외부에서 resume 해주어야 active 상태가 된다.


### (유의) 프로세스 상태는 사용자 프로그램 관점이다.

1. 프로세스 주소공간의 코드가 실행 : 프로세스가 **user mode로 실행**중(running)이라고 한다.
2. 커널 주소공간의 코드가 실행될 때 : 프로세스가 **monitor(혹은 kernel) mode로 실행**(running)중이라고 한다.
- 프로세스 실행 중 이 프로세스와 관련없는 interrupt가 발생하여 CPU가 운영체제로 넘어가더라도, 다른 프로세스로 넘어가기 전까지는 이 프로세스가 monitor(혹은 kernel) 모드로 실행중이라고 간주한다.


suspended block에서도 I/O작업은 가능하며 완료될 경우 suspended ready로 이동한다.
