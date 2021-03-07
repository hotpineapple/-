# System Structure & Program Execute (2), Process (1)---
# 컴퓨터 시스템 기본 구조

### 1. Computer = Host (CPU + Memory)

### 2. Disk & I/O Device w/ Device controller, Local buffer

- CPU는 기본적으로 매 번 **program counter(pc) 레지스터가 가리키는 memory 주소의 instruction**(기계어, 보통 4 Byte)을 수행하며 이후 pc값은 4만큼 증가한다.
- cf. 레지스터는 cpu 내부에 위치한다.
- CPU는 매 instruction을 수행하기 전 먼저 Interrupt line을 확인하여 interrupt가 있는지 확인한다.
- **만약 interrupt가 있으면** 그 시점의 pc값을 저장한 후 **CPU 제어를 운영체제 내부의 커널 함수(ISR, Interrupt Service Routine)로 넘긴다.**
    - 인터럽트 벡터 : 해당 인터럽트의 처리루틴의 주소를 가지고 있음
    - 인터럽트 처리 루틴(=인터럽트 핸들러) : 해당 인터럽트를 처리하는 커널 함수(실제 실행해야할 일)
- **mode bit** : 사용자 프로그램과 운영체제가 실행가능한 instruction을 구분함으로서 시스템을 보호하는 목적
    - 0 : instruction set의 모든 instruction 실행 가능
        - 운영체제가 CPU 제어권을 갖는 경우
        - I/O 작업(I/O Device 접근) 관련 instruction은 mode bit=0일 때만 실행 가능

            ⇒ 사용자 프로그램 실행 중  I/O작업 필요할 때 **System call**(SW Interrupt, Trap)을 통해 운영체제에 CPU 제어권을 넘긴다. 

    - 1 : 한정된 instruction만 실행 가능
        - 사용자 프로그램이 CPU 제어권을 갖는 경우 (제어권을 넘기기 전 mode bir=1 설정)
        - 해당 프로그램의 메모리 영역만 접근 가능

### 인터럽트

- 하드웨어 인터럽트 (Interrupt)
    - by I/O device controller : 입출력 작업 완료시 interrupt를 발생시킨다.(=interrupt line 세팅)
    - by Timer : 사용자 프로그램이 무한 루프를 돌며 무한정 CPU를 점유하는 것을 방지하기 위한 목적의 하드웨어

        (사용자 프로그램에 CPU 제어권을 넘겨주기 전에 timer에 시간을 셋팅한 후 mode bit=1 셋팅한다.)

        동작 : 할당된 시간이 종료되면 timer가 interrupt를 걸고 CPU는 해당 사용자 프로그램으로부터 CPU 제어권을 빼앗아 운영체제로 넘긴다.

- 소프트웨어 인터럽트 (Trap)
    - Exception : 프로그램이 오류를 범한 경우

        예) 사용자 프로그램이 다른 사용자 프로그램이나 운영체제의 메모리에 접근하고자 할 때, 수를 0으로 나누려고 할 때

        결과 : 자동으로 운영체제로 CPU 제어권이 넘어가며 운영체제는 해당 프로그램을 강제 종료시키는 등의 대응을 한다.

    - **System cal**l : 사용자 프로그램이 (운영 체제의) 커널함수를 호출하는 경우



# 동기식 입출력, 비동기식 입출력

## 동기식(Synchronous) 입출력

: 사용자 프로그램이 운영체제에 I/O 요청 후 입출력 작업이 완료될 때까지 CPU점유하며 대기 → 작업이 완료되면 해당 사용자 프로그램으로 다시 제어가 넘어감.

- 일관성을 보장한다.
- read 작업은 동기식 처리하는 경우 많다.

### 구현방법 1

: 해당 사용자 프로그램이 요청한 입출력 작업이 완료될 때 까지 운영체제가 CPU 점유 → CPU와 다른 I/O장치들의 낭비

- ㅡ매 시점 하나의 입출력장치의 입출력만 수행 가능
- cf. 입출력 작업은 많은 시간을 소요한다.

### 구현방법 2

: 입출력이 완료될 때까지 **해당 프로그램에게서 CPU를 빼앗아** 입출력 처리를 기다리는 큐에 해당 프로그램을 저장하고, 다른 프로그램에게 CPU제어를 넘긴다.

- 동시의 여러 I/O장치들이 각 I/O작업 수행 가능

## 비동기식(Asynchronous) 입출력

사용자 프로그램이 System call 하여 커널함수에 의해 I/O가 시작된 후 입출력 작업이 끝나기를 기다리지 않고 **CPU제어가 해당 사용자 프로그램으로 즉시 넘어감**

- 이 사용자 프로그램은 실행중인 I/O와 무관한 작업을 계속한다.
- write는 비동기식으로 처리하는 경우 많다.

## 두 경우 모두 입출력작업의 완료 시 인터럽트로 CPU에 알린다.

[그림] (a) 동기식 입출력과 (b) 비동기식 입출력

![System%20Structure%20&%20Program%20Execute%20(2),%20Process%20(1%20a6ce353c5557424e9e453cf0e985289c/Untitled.png](System%20Structure%20&%20Program%20Execute%20(2),%20Process%20(1%20a6ce353c5557424e9e453cf0e985289c/Untitled.png)

- 이미지 출처 : [https://www.cs.uic.edu/~jbell/CourseNotes/OperatingSystems/13_IOSystems.html](https://www.cs.uic.edu/~jbell/CourseNotes/OperatingSystems/13_IOSystems.html)
- 수정사항 : (a) user / kernel 영역 구분 → (b)와 같음



# DMA(Direct Memory Access)

- 예 ) 키보드에서 문자를 입력한 경우 CPU는 키보드 컨트롤러가 발생시킨 interrupt에 의해 하던 작업을 멈추고 운영체제에 제어를 넘겨 **키보드의 로컬 버퍼에 있는 데이터를 Byte단위로 memory에 복사**해온다.
- CPU가 너무 많은 interrupt를 당하여 제어권을 운영체제로 넘길 때 마다 큰 **오버헤드가 발생**하며 사용자 프로그램의 정상 실행에 영향을 미친다.
- DMA Controller는 CPU 외에 memory에 직접 접근 및 처리 가능한 HW장치이다.
- DMA Controller는 CPU를 대신해서 로컬 버퍼의 데이터에 대해 Byte가 아닌 **Block 단위**로 memory 작업을 완료한 후 CPU에 interrupt 보낸다.



# 입출력 instruction 종류

1. 메모리만 접근하는 instruction(LOAD / STORE) 과는 별개의 I/O 수행하는 별개의 special instruction으로 입출력작업
2. Memory mapped I/O
    - 메모리에 접근하는 instruction을 사용하여 입출력작업 수행 가능
    - 메모리주소에 대해서 연장 주소를 부여하여 동일 instruction 사용함

---

# 저장장치 계층 구조

- 속도
- 용량(비용에 반비례)
- 휘발성/비휘발성
- Primary / Secondary
    - primary memory : CPU 가 직접 접근 및 처리 가능, byte단위
    - secondary : sector 단위

![System%20Structure%20&%20Program%20Execute%20(2),%20Process%20(1%20a6ce353c5557424e9e453cf0e985289c/Untitled%201.png](System%20Structure%20&%20Program%20Execute%20(2),%20Process%20(1%20a6ce353c5557424e9e453cf0e985289c/Untitled%201.png)

- 레지스터는 CPU내부에 있다.
- Cache : CPU 연산속도와 Dram 접근 속도 차이를 줄이기 위한 목적. 같은 데이터에 대한 재접근이 빈번한 경우를 가정한다.

    용량 상 memory의 모든데이터를 저장할 수 없으므로 기존의 데이터를 삭제할 필요가 있다. 이와 같은 정책상 이슈 → 메모리 관리 챕터에서 다시 다룰 것. 



# 프로그램의 실행 (= Memory Load)

- **프로그램은 실행파일 형태로 디스크에 저장되어 있다.**
- **이 파일을 실행시키면 파일 시스템으로부터 virtual memory를 거쳐 memory 에 올라와서 프로세스가 된다.**
    - cf. 프로세스란 실행중인 프로그램을 의미함.
- 각각의 프로그램은 주소공간을 가진다.
- 커널 또한 주소공간을 가진다.
    - cf.  커널은 운영체제의 핵심영역으로 좁은 의미의 운영체제를 커널이라고 하기도 한다.
- 주소공간은 가상메모리에 저장되어있다.
    - **Virtual memory 의미** : 실제로  각 프로그램의 주소공간의 구성요소들은 physical memory와 swap area에 나누어서 저장되어 있다. (당장 필요한 요소 : physical memory / 그렇지 않은 요소 : swap area)

        ⇒ 주소변환(Address Transition) 장치가 필요하다.

    - Swap area : 메인 메모리의 용량의 한계를 해결하기위해 메인 메모리의 연장선으로서 사용하는 디스크의 특정 영역을 의미 → 후반에 다시 다룰 것.
        - file system 과 swap area 모두 물리적으로는 하드디스크의 일부이나 용도에 따라 나뉜 것.
        - file system은 전원이 꺼져도 데이터가 유지되어야 하는 영역인 반면 swap area는 상관 없다.

![System%20Structure%20&%20Program%20Execute%20(2),%20Process%20(1%20a6ce353c5557424e9e453cf0e985289c/Untitled%202.png](System%20Structure%20&%20Program%20Execute%20(2),%20Process%20(1%20a6ce353c5557424e9e453cf0e985289c/Untitled%202.png)

### 각 프로세스(프로그램)의 주소공간 구성요소

- 코드 : 실행할 기계어
- 데이터 : 자료구조, 전역변수 등
- 스택 : 함수 호출 시 사용

### 커널의 주소공간 내용

![System%20Structure%20&%20Program%20Execute%20(2),%20Process%20(1%20a6ce353c5557424e9e453cf0e985289c/Untitled%203.png](System%20Structure%20&%20Program%20Execute%20(2),%20Process%20(1%20a6ce353c5557424e9e453cf0e985289c/Untitled%203.png)

- *data
    - PCB : Process Control Block 프로세스를 관리하기 위한 자료구조
    - 하드웨어를 관리하기 위한 자료구조
- stack : 커널을 호출한 프로세스마다 영역을 분리하여 함수 호출 시 데이터 저장함.

### 사용자 프로그램이 사용하는 함수와 커널 함수

![System%20Structure%20&%20Program%20Execute%20(2),%20Process%20(1%20a6ce353c5557424e9e453cf0e985289c/Untitled%204.png](System%20Structure%20&%20Program%20Execute%20(2),%20Process%20(1%20a6ce353c5557424e9e453cf0e985289c/Untitled%204.png)

라이브러리 함수는 사용자프로그램의 실행파일에 포함되어 있다.

### 다이어그램

![System%20Structure%20&%20Program%20Execute%20(2),%20Process%20(1%20a6ce353c5557424e9e453cf0e985289c/Untitled%205.png](System%20Structure%20&%20Program%20Execute%20(2),%20Process%20(1%20a6ce353c5557424e9e453cf0e985289c/Untitled%205.png)
