# System Structure & Program Execute (1)

![image-20210314171536718](https://user-images.githubusercontent.com/77573938/111075524-b4e61e80-852b-11eb-8e4a-47804d2ac7cb.png)


| Computer | I/O device |
| -------- | ---------- |
| CPU      | I/O device |
| Memory   | Disk       |

CPU와 I/O device는 성능(처리하는 속도)이 상당히 차이난다. CPU가 월등히 빠르다.

<br>

## 1. CPU (중앙 처리 장치)

>  명령을 수행하고 데이터를 처리. 제어장치와 연산장치로 구성되어 있음

- CPU는 I/O device에 직접 접근하지 않고 Memory의 instruction만 실행한다. 
- I/O는 CPU가 직접하는 것이 아니라 I/O controller 한테 시킨다.
- CPU는 instruction 실행하고 Interrupt line 체크하고 Interrupt 들어온게 없으면 다음 instruction 실행하는 작업을 반복한다.
- **registers** : CPU 안의 memory보다 더 빠르면서 정보를 저장할 수 있는 작은 공간

- **Interrupt line** : 키보드에서 어떤 입력이 들어온다거나 디스크에서 어떤 것을 읽어와야하는 등의 I/O device들을 접근하는 instruction 을 한다.



### timer

: 특정 프로그램이 CPU를 독점하는 것을 막기 위한 하드웨어 장치

- 처음에는 OS가 CPU를 갖고 있다가 여러 사용자 프로그램이 실행되면 timer에 정해진 시간을 할당한 다음 그 프로그램한테 CPU를 넘겨준다.
- 무한루프를 도는 프로그램이 CPU한테 넘어가면 프로그램이 종료되지도 않고 I/O를 하지도 않아서 CPU가 다른 프로그램한테 넘어갈 수 없게 되는데, 이런 일을 막고 time sharing 구현을 위해 이용된다.
- 만약 timer가 interrupt를 걸어오면 CPU는 하던 작업을 잠시 멈추고 CPU의 제어권이 사용자 프로그램으로부터 OS로 자동적으로 넘어간다.



### Mode bit

: CPU에서 실행되는 것이 운영체제인지 사용자 프로그램인지 구분해준다.

- 0 : 모니터모드(커널모드) 
  - 운영체제가 CPU에서 수행중일 때는 메모리 접근 뿐 아니라 I/O device를 접근하는 instruction도 할 수 있다
- 1 : 사용자모드
  - 사용자 프로그램이 CPU를 갖고 있을 때는 제한된 instruction만 CPU에서 실행할 수 있게 된다 (보안상의 목적)
  - interrupt가 들어오면 CPU제어권이 운영체제에게 넘어가게 되면서 mode bit은 자동으로 0으로 바뀐다



## 2. Memory (주 기억장치)

>  CPU의 작업 공간

- I/O 장치를 접근하는 모든 instruction은 보안 상의 이유로 운영체제를 통해서만 가능하다. 사용자 프로그램은 직접 I/O에 접근할 수 없다.



### DMA controller (Direct Memory Access)

: 직접 메모리를 접근 할 수 있는 컨트롤러

- CPU는 메모리 접근도 할 수 있고 local buffer 접근도 할 수 있는데 이렇게 하다보니 CPU가 interrupt를 너무 많이 당해 효율적으로 작동하지 못한다. 
- DMA controller를 둠으로써 CPU가 이러한 방해를 받는 것을 막는다.



### memory controller 

: 메모리를 전담하며, 특정 메모리 영역을 동시에 접근하면 문제가 발생할 수 있어서 어떤 것을 먼저 접근하게 할지 조율하는 역할을 한다.



## 3. I/O device (입출력장치)

- I/O device : 컴퓨터의 2차 부품으로 컴퓨터 외부와의 소통(Input/Output)을 담당한다.
  - input : I/O device의 데이터가 Computer 안으로 들어가는 것
    - 키보드, 마우스
  - output : Computer가 데이터를 받아 처리한 후, 그 결과를 I/O device로 내보내는 것
    - 모니터, 프린터

- 하드 디스크 (보조 기억장치) : input과 output 역할을 모두 수행한다.
  - 그래픽 카드, 네트워크 카드, 하드디스크/SSD



### Device controller 

: 각각의 I/O device들을 전담하는 일종의 작은 CPU 같은 것

- Disk에서 헤드가 어떻게 움직이고 어떤 데이터를 읽을지 디스크의 내부를 통제하는 역할을 한다.

- I/O는 실제 device와 local buffer 사이에서 일어나고, I/O가 끝난 경우 interrupt로 CPU에 그 사실을 알린다.

- device controller는 자신의 local buffer에만 접근 가능하다

- 실제 데이터를 저장하는 local buffer를 가진다.

  #### local buffer 

  : 메인 CPU의 작업공간인 메인 Memory가 있듯이, device controller들의 작업공간 역할을 한다. 일종의 data register.

  - ex. memory에 있는 데이터를 파일에 저장하고 싶다면, 데이터 자체는 local buffer에 넣고 파일에 저장하라는 명령은 제어 레지스터를 통해 CPU가 I/O controller 한테 전달한다.
  - 실제 데이터는 local buffer에 담고, 화면에 출력하라는 지시는 제어 레지스터를 통해서 한다.

- device driver (장치구동기) : OS 코드 중 각 장치별 처리루틴 → software

- device controller (장치제어기) : 각 장치를 통제하는 일종의 작은 CPU → hardware



## 4. I/O의 수행

- 모든 입출력 명령은 특권명령이라 사용자 프로그램이 직접 I/O 하지 못하고 운영체제를 통해서만 I/O장치에 접근할 수 있다.
- **시스템콜 (system call) **: 사용자 프로그램이 운영체제의 서비스를 받기 위해 커널 함수를 호출하는 것
  - 사용자 프로그램이 운영체제의 코드를 직접 수행하는 것이 불가능하기 때문에, OS의 함수 호출을 하기 위해서는 사용자 프로그램이 Interrupt line을 세팅한다. 
  - 그러면 CPU제어권이 운영체제한테 넘어가 부탁한 일을 운영체제가 할 수 있게 된다.

![image-20210314200212748](https://user-images.githubusercontent.com/77573938/111075526-b7487880-852b-11eb-8c45-7e35ab8d5265.png)



### 인터럽트 (Interrupt)

현대의 운영체제는 인터럽트에 의해 구동된다. ← 운영체제는 CPU를 사용할 일이 없고 인터럽트가 들어올 때만 CPU가 운영체제한테 넘어가는 것을 의미한다.

- **Interrupt (하드웨어 인터럽트)** : 하드웨어가 발생시킨 인터럽트. 일반적으로 말하는 인터럽트는 이것을 의미
- **Trap (소프트웨어 인터럽트)** : 사용자 프로그램이 I/O를 요청하기 위해 OS에게 시스템 콜을 할 때 발생하는 인터럽트

- <과정> 

  1. 사용자 프로그램이 **I/O를 요청**하기 위해 OS에게 시스템 콜을 한다. (_**Trap**_)
  2. OS가 Disk한테 I/O하라고 일을 시킨다. 그럼 CPU는 다른 프로그램한테 넘어간다.
  3. Disk가 시킨일이 다 끝나면 그때 _**하드웨어 인터럽트**_가 걸리고, 일을 **다 했다고** controller가 CPU한테 **알린다.**

  ⇒ I/O를 하기 위해서는 소프트웨어 인터럽트와 하드웨어 인터럽트 모두 필요하다.

- 인터럽트 벡터 : 해당 인터럽트의 처리 루틴 주소를 가지고 있다.

- 인터럽트 처리 루틴 : 해당 인터럽트를 처리하는 커널 함수. (각각의 인터럽트 종류마다 어떤 일을 해야하는지 운영체제 코드에 정의되어 있음)



_I/O 수행 예시 )_

- 사용자 프로그램이 디스크에서 파일을 읽어야 한다면 시스템 콜을 통해 운영체제한테 함수를 호출한다.
- CPU에서 instruction을 순차적으로 실행하다가 함수호출이나 if문 같은 것을 만족하지 않는 경우 instruction의 메모리 위치를 점프한다. 즉, 자신의 프로그램 안에서 함수 호출을 하는 것은 메모리 안에서 메모리 주소를 바꾸는 것이라 가능하다.
- 그러나, I/O 요청을 하기 위해서 운영체제의 함수를 호출하는것은(== 시스템 콜) 메모리 주소를 바꾸는 걸로는 해결되지 않는다.
- **사용자 프로그램 안에서만 점프와 함수호출이 가능**하기 때문에 I/O를 호출하기 위해서 OS에 해당하는 주소로 넘어가야 하는데 mode bit이 1이라 OS로는 점프가 불가능하다.
- 대신, **프로그램이 직접 interrupt line을 세팅하는 instruction을 수행**한다. 그러면, CPU는 다음 instruction을 수행하는 것이 아니라 interrupt가 들어왔기 때문에 mode bit이 0으로 바뀌고 CPU 제어권이 OS한테 넘어간다.
- 디스크에서 파일을 읽어오라는 걸 요청하기 위해 시스템 콜을 했다면 운영체제가 CPU를 갖고 있기 때문에 disk controller 한테 그걸 읽어오라고 할 수 있게된다. 


