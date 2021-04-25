# Disk Management and Scheduling (1)

<br>

## 1. 디스크 구조

- **논리 블록 (logical block)**
  - 정보를 전송하는 최소 단위
  - 디스크의 외부에서 보는 디스크의 단위 정보 저장 공간들
  - 주소를 가진 1차원 배열처럼 취급함
- **섹터 (sector)**
  - 디스크를 관리하는 최소의 단위
  - logical block이 물리적인 디스크에 매핑된 위치
  - `sector 0` : 최외곽 실린더의 첫 트랙에 있는 첫 번째 섹터. 부팅과 관련된 정보가 저장됨.

<br>

## 2. 디스크 관리

- **Physical Formatting (Low-level Formatting)**

  - 디스크를 컨트롤러가 읽고 쓸 수 있도록 섹터 단위로 나누는 과정

  - 각 섹터는 **`header + 실제 data (보통 512bytes) + trailer`** 로 구성됨.

    (디스크를 읽고 쓰는데 부가적인 데이터가 header랑 trailer에 저장됨)

  - header와 trailer는 sector번호, ECC(Error-Correcting Code) 등의 정보가 저장되며 컨트롤러가 직접 접근하고 운영함.

    - ECC는 데이터를 축약한 코드로, 오류를 검출하는 용도로 사용된다.

- **Partitioning**

  - 디스크를 하나 이상의 실린더 그룹으로 나누는 과정
  - OS는 이것을 **독립적인 disk**로 취급함. (logical disk)
  - 이 Partitioning이 끝나게되면 각각의 Partition을 file system용도로 쓸 수도 있고(이게 Logical Formatting), swap area 용도로도 쓸 수 있음.

- **Logical Formatting**

  - logical disk에 파일 시스템을 설치하는 것
  - FAT, inode, free space 등의 구조를 포함.

- **Booting**

  - 부팅의 절차

    : 전원을 켜면 메모리는 다 비어있음. 그런데, CPU는 메모리만 접근할 수 있는 장치이지, 하드디스크는 직접 접근하지 못함. -> 메모리 영역 중 전원이 나가더라도 내용이 유지되는 소량의 메모리 부분인 ROM에 부팅을 위한 아주 간단한 로더가 저장되어 있음. -> 컴퓨터를 켜면 CPU제어권이 ROM의 주소를 가리키게 되고, 1-4의 과정을 수행한다.

    1. ROM에 있는 small bootstrap loader가 실행됨.

    2. loader는 하드디스크에서 sector 0 (boot block)을 load하여 실행.

       (sector 0 은 full Bootstrap loader program의 상태가 됨)

    3. boot block은 운영체제 커널의 위치를 찾아서 메모리에 올려서 실행함.

    4. 운영체제가 메모리에 올라가서 부팅이 이루어짐.

<br>

## 3. 디스크 스케줄링

[그림] 디스크의 구조

![image-20210425220332798](https://user-images.githubusercontent.com/77573938/115996335-d271d200-a619-11eb-949f-9412d274b195.png)

- **Access time의 구성**

  - 1) Seek time (탐색 시간)

    - 헤드를 해당 실린더로 움직이는데 걸리는 시간

    - 디스크를 접근 시간 중 거의 대부분을 차지함.

    - `실린더(cylinder)` : 여러 원판에서 같은 트랙에 위치한 것들을 모은 것

      (트랙이나 실린더나 의미 비슷해서 비슷하게 사용해도 상관없음)

  - 2) Rotational latency (회전 지연시간)

    - 헤드가 원하는 섹터에 도달하기까지 걸리는 회전지연시간

  - 3) Transfer time (전송 시간)

    - 실제 데이터의 전송 시간
    - 디스크 접근 시간 중 굉장히 작은 시간을 차지함.

- **Disk bandwidth**

  - 단위 시간 당 전송된 바이트의 수
  - 디스크의 성능을 나타낼 때 사용하는 용어
  - Disk bandwidth가 높아지려면(== 디스크가 효율적으로 동작하려면) 가능한 seek time을 줄이는 것이 좋다.

- **Disk Scheduling**

  - seek time은 최소화하는 것이 목표
  - seek time ≒ seek distance 

<br>

## 4. 디스크 스케줄링 알고리즘

> 디스크에 요청이 들어온 순서대로 처리하면 비효율적임

<br>

### 4-1. FCFS (First Come First Service)

: 들어온 순서대로 처리

- 헤드의 이동거리 길어짐  -> 매우 비효율적

![image-20210425224027566](https://user-images.githubusercontent.com/77573938/115996340-d43b9580-a619-11eb-9357-d00cc94156c9.png)

<br>

### 4-2. SSTF (Shortest Seek Time First)

: 현재 헤드 위치에서 제일 가까운 요청을 가장 먼저 처리

- 문제점 : starvation 문제 발생할 수 있음

![image-20210425224057160](https://user-images.githubusercontent.com/77573938/115996341-d43b9580-a619-11eb-819b-ab4e4bfdada5.png)

<br>

### 4-3. SCAN

: SCAN에 기반한 방법. **엘리베이터 스케줄링 알고리즘**이라고도 함.

- 가장 간단하면서도 획기적인 스케줄링 기법

![image-20210425224242155](https://user-images.githubusercontent.com/77573938/115996342-d4d42c00-a619-11eb-94ac-7ee76c39b57c.png)

- 큐에 어떤 요청이 들어왔는지 상관없이, disk arm은 디스크의 한쪽 끝에서 다른쪽 끝으로 이동하며 가는 길목에 있는 모든 요청을 처리한다.
- 다른 한쪽 끝에 도달하면 역방향으로 이동하며 오는 길목에 있는 모든 요청을 처리하며 다시 반대쪽 끝으로 이동한다.
- 장점 : 
  - 디스크 헤드의 이동 거리가 짧아짐
  - starvation 문제 발생 X
- 문제점 : 실린더 위치에 따라 대기 시간이 다름. 가운데 영역은 양끝에 있는 경우보다 예상 대기 시간이 더 짧음.

![image-20210425225101508](https://user-images.githubusercontent.com/77573938/115996347-d56cc280-a619-11eb-9b7d-d48f7df70645.png)

<br>

### 4-4. C-SCAN

: Circular SCAN의 약자. 

![image-20210425224720045](https://user-images.githubusercontent.com/77573938/115996343-d4d42c00-a619-11eb-8a8f-35e204897054.png)

- 헤드가 한쪽 끝에서 다른쪽 끝으로 이동하며 가는 길목에 있는 모든 요청을 처리
- 다른쪽 끝에 도달했으면 요청을 처리하지 않고 곧바로 출발점으로 다시 이동
- SCAN보다 이동 거리는 길어질 수 있지만, 균일한 대기 시간을 제공

![image-20210425224948945](https://user-images.githubusercontent.com/77573938/115996345-d56cc280-a619-11eb-836c-26e67c17a946.png)

<br>

### 4-5. N-SCAN

- SCAN의 변형 알고리즘
- 일단 arm이 한 방향으로 움직이기 시작하면, 그 시점 이후에 도착한 job은 가는 도중에 처리하지 않고, 되돌아올 때 서비스함. -> 대기시간의 편차를 좀 줄일 수 있음.

<br>

### 4-6. LOOK and C-LOOK

- SCAN과 C-SCAN의 약간의 비효율적인 측면을 개선한 방법

  (SCAN이나 C-SCAN은 요청이 있든없든 헤드가 디스크 끝에서 끝으로 이동하고 끝까지 다 찍고 턴해서 돌아왔음)

- LOOK과 C-LOOK은 헤드가 진행 중이다가 그 방향에 더 이상 기다리는 요청이 없으면 헤드의 이동 방향을 즉시 반대로 이동함.

[그림] C-LOOK. 요청없으면 끝(199)까지 안가고 183에서 방향을 틀고, 역시 0번까지 안가고 14에서 turn함.

![image-20210425225255229](https://user-images.githubusercontent.com/77573938/115996348-d6055900-a619-11eb-9d07-46aae9ba8e1b.png)

<br>

## 5. 디스크 스케줄링 알고리즘의 결정

- 현대 디스크 시스템에서는 SCAN에 기반한 알고리즘을 주로 씀
- SCAN, C-SCAN 및 그 응용 알고리즘인 LOOK, C-LOOK 등이 일반적으로 디스크 입출력이 많은 시스템에서 효율적인 것으로 알려져 있음.
- 파일의 할당 방법에 따라 디스크 요청이 영향을 받음.
- 디스크 스케줄링 알고리즘은 필요할 경우 다른 알고리즘으로 쉽게 교체할 수 있도록 OS와 별도의 모듈로 작성되는 것이 바람직하다.





