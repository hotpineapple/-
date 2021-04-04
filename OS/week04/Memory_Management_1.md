# Chapter 8. Memory Management (1)



# 1. 메모리 관리 (Memory Management)



## 메모리 주소

Memory : 주소를 통해 접근하는 매체 → 주소 생김

프로그램이 실행되면 독자적인 주소 공간이 형성됨

### Logical Address (= virtual address) (논리적 주소)

**CPU가 보는 주소!!!**

프로그램마다 가지고 있는 메모리 주소

각 프로세스마다 0번지부터 시작

### Physical Address (물리적 주소)

메모리에 실제 올라가는 위치

<br>

## 주소 바인딩 (Address Binding)

> 주소를 결정하는 것

프로그램마다 0번지부터 시작되는 독자적인 주소가 있지만 실행되려면 물리적인 메모리 어딘가로 올라가야함 → 주소 바뀜

- Symbolic Address → Logical Address → Physical Address

<br>

주소 변환이 일어나는 3가지 시점

### 1. Compile time binding ; 컴파일 시에 이루어지는 방식

- 물리적 메모리 주소가 컴파일 시 알려짐
- 시작 위치 변경 시 재컴파일
- 컴파일러는 `절대 코드(absolute code)` 생성
- 물리적인 메모리에 다른 주소가 많이 비어있음에도 불구하고 항상 컴파일 시의 주소와 동일해야해서 대단히 비효율적임 → 요즘 컴퓨터 시스템에서 사용 안함.

### 2. Load time binding ; 실행이 시작될 때 이루어지는 방식

- Loader의 책임하에 물리적 메모리 주소 부여
- 컴파일러가 재배치 가능코드(relocatable code)를 생성한 경우 가능

### 3. Execution time binding (=Run time binding) ; 실행이 되는 중에도 변환이 일어나는 방식

- 수행이 시작된 이후에도 프로세스의 메모리 상 위치를 옮길 수 있음
- CPU가 주소를 참조할 때마가 binding을 점검 (address mapping table)
- <u>하드웨어적인 지원이 필요</u>
  - e.g.)  base and limit registers, **MMU**



![image-20210404154211338](https://user-images.githubusercontent.com/77573938/113511025-b63dc080-9598-11eb-8654-e08ce2b22774.png)

<br>

## Memory-Management Unit (MMU)

> logical address를 physical address로 매핑해주는 Hardware device (주소변환을 지원해주는 하드웨어)

- `MMU scheme` : 사용자 프로세스가 CPU에서 수행되며 생성해내는 모든 주소값에 대해 base register의 값을 더하는 것

- 실제 사용자 프로그램은 logical address만 다루며, 실제 physical address는 볼 수 없으며 알 필요도 없다.

- 레지스터 2개로 주소변환을 함
  - **Relocation register (= base register)**: 접근할 수 있는 물리적 메모리 주소의 최소값
  - **Limit register** :  논리적 주소의 범위
  - 먼저 Limit register로 logical address의 범위를 체크한다.

![image-20210404160142732](https://user-images.githubusercontent.com/77573938/113511028-b8078400-9598-11eb-8ffd-7b89f8b5c397.png)

<br>

[그림] P1이 CPU에서 실행중인 상황

![image-20210404160000356](https://user-images.githubusercontent.com/77573938/113511026-b8078400-9598-11eb-91df-e7f7d34f2c38.png)

- P1의 logical memory :  0-3000. 이 프로그램이 physical memory 상에서는 14000-17000에 올라가 있음. 

- CPU가 346번지를 달라고 했다면 주소변환은 물리적 주소의 시작위치인 14000과 346을 더하면 된다. 14346을 읽어서 CPU한테 갖다주면 됨. 

- Relocation register (= base register)에는 프로그램의 시작위치를 더해놓는다. 

- Limit register에는 프로그램의 최대 크기를 저장하고있음. 

  저장하는 이유?  만약 악의적인 프로그램인 경우 본인의 크기가 3000임에도 4000번째 메모리를 요청하면 18000번의 다른 프로그램이 존재하는 메모리 주소를 요청, 다른 프로그램을 악의적으로 사용할 수 있게 되는 것을 방지하고자.

<br>

## 관련 용어

### 1. Dynamic Loading

> loading : 메모리로 올리는 것

- 프로세스 전체를 메모리에 미리 다 올리는 것이 아니라 해당 루틴이 불려질 때, 필요할 때마다 그때그때 메모리에 load 하는 것
  - 주의)  **현재 컴퓨터 시스템**도 프로그램을 실행시키면 통째로 올라가는 것이 아니라 당장 필요한 부분만 메모리에 올라가고 필요없으면 나가는 방식. 그러나 이게 오리지널 Dynamic Loading의 개념이 아님! 이건 **운영체제가 관리하는 Paging System에 의한 것!**
  - 그러나 요새는 용어를 섞어서 사용한다. 프로그래머가 명시적으로 Dynamic Loading해서 이뤄지는게 Dynamic Loading의 원래 의미이지만, 프로그래머가 명시하지 않고 운영체제가 알아서 해주는 것도 Dynamic Loading이라고 말함.
- memory utilization의 향상
- 가끔씩 사용되는 많은 양의 코드의 경우 유용 (예: 오류 처리 루틴)
- 운영체제의 특별한 지원없이 프로그램 자체에서 구현 가능 (OS는 라이브러리를 통해 지원 가능 → 프로그래머가 자세히 코딩할 필요 없음)



### 2. Overlays

> Dynamic Loading과 거의 유사. 역사적으로 좀 다르다. 
>
> Overlays는 초창기 컴퓨터 시스템에서 워낙 메모리 크기가 작아 프로그램 하나도 메모리에 올리는게 부족해서 프로그래머가 수작업으로 쪼개서 올렸었다.

- 메모리에 프로세스의 부분 중 실제 필요한 정보만을 올림
- 프로세스의 크기가 메모리보다 클 때 유용
- 운영체제의 지원 없이 사용자에 의해 구현
- 작은 공간의 메모리를 사용하던 초창기 시스템에서 수작업으로 프로그래머가 구현
  - `Manual Overlay`
  - 프로그래밍이 매우 복잡했다.



### 3. Dynamic Linking

> linking : 여러 군데 존재하던 컴파일된 파일들을 묶어 하나의 실행파일을 만드는 과정 
> (프로그램 작성한 다음 컴파일 하고 link해서 실행파일을 만듦.)

- Linking을 실행시간(execution time)까지 미루는 기법
- Static linking
  - 라이브러리가 프로그램의 실행파일 코드에 포함됨
  - 동일한 라이브러리를 각각의 프로세스가 메모리에 올리므로 메모리 낭비 (eg.  printf 함수의 라이브러리 코드)
- Dynamic linking
  - 라이브러리 코드가 **실행파일 만들 때 포함되지 않고** 실행되면 연결(link) 됨
  - 실행파일에는 라이브러리가 별도의 파일로 존재하고 라이브러리를 찾을 수 있는 stub이라는 작은 코드를 둠
  - *Shared library (공유 라이브러리)* : Dynamic linking을 해주는 라이브러리
  - 라이브러리가 이미 메모리에 있으면 그 루틴의 주소로 가고 없으면 디스크에서 읽어옴
  - 운영체제의 도움이 필요



### 4. Swapping

> 프로세스를 메모리에서 하드디스크로 통째로 쫒아내는 것

![image-20210404164605130](https://user-images.githubusercontent.com/77573938/113511029-b8a01a80-9598-11eb-95ba-a5c3a2d12b9c.png)

- Swapping : 프로세스를 일시적으로 메모리에서 backing store로 쫒아내는 것
  - `Swap out` : 메모리에서 통째로 쫒겨나서 하드디스크(backing store)로 내려가는 것
  - `Swap in` : backing store로 쫒겨났던게 나시 메모리로 올라오는 것
- Backing store (=swap area) :  디스크
  - 많은 사용자의 프로세스 이미지를 담을 만큼 충분히 빠르고 큰 저장 공간

- Swap in / Swap out
  - 일반적으로 **중기 스케줄러(swapper)** 에 의해 swap out 시킬 프로세스 선정
  - priority-based CPU scheduling algorithm
    - CPU priority가 낮은 프로세스를 swapped out 시킴
    - CPU priority가 높은 프로세스를 메모리에 올려놓음
  - Compile time 혹은 load time binding에서는 원래 메모리 위치로 swap in 해야 함 → Swapping의 효과를 충분히 발휘 X.
  - Execution time binding에서는 추후 빈 메모리 영역 아무 곳에나 올릴 수 있음 → Swapping이 효율적으로 사용됨
  - swap time은 대부분 transfer time (swap되는 양에 비례하는 시간)임

<br><br><br>

---

물리적인 메모리를 어떻게 관리할 것인가?

# 2. Allocation of Physical Memory

[그림] 물리적인 메모리

![image-20210404170240657](https://user-images.githubusercontent.com/77573938/113511030-b8a01a80-9598-11eb-8ef9-87f4ad1cef44.png)

낮은 주소 부분에 운영체제 커널이 항상 상주하고 있고, 높은 주소 영역에는 사용자 프로그램들이 올라가 있다.

- OS 상주 영역
  - interrupt vector와 함께 낮은 주소 영역 사용
- 사용자 프로세스 영역
  - 높은 주소 영역 사용

<br>

## 1. Contiguous allocation (연속 할당)

> 각각의 프로세스가 메모리의 연속적인 공간에 적재되도록 하는 방식



![image-20210404172246396](https://user-images.githubusercontent.com/77573938/113511031-b938b100-9598-11eb-88eb-8fb8cadd6f5a.png)



### 1.1. 고정 분할 방식 (Fixed Partition Allocation)

- 물리적 메모리를 주어진 개수만큼의 영구적인 **`Partition` 으로 미리 나누어** 각 분할에 하나의 프로세스를 적재하여 실행하도록 하는 방식
- **외부 단편화 (외부조각)** 및 **내부 단편화 (내부조각)** 가 발생할 수 있음 → 융통성이 매우 떨어지는 방식
  - `외부 단편화 (external fragmentation)` : 빈 파티션이지만 프로세스보다 크기가 작아 이용될 수 없음
    - 프로그램 크기보다 분할의 크기가 작은 경우
    - 아무 프로그램에도 배정되지 않은 빈 곳인데도 프로그램이 올라갈 수 없는 작은 분할
  - `내부 단편화 (Internal fragmentation)` : 파티션에 프로세스를 적재했을 때, 공간이 남아 메모리 낭비 발생
    - 프로그램 크기보다 분할의 크기가 큰 경우
    - 하나의 분할 내부에서 발생하는 사용되지 않는 메모리 조각
    - 특정 프로그램에 배정되었지만 사용되지 않는 공간

 

### 1.2. 가변 분할 방식 (Variable Partition Allocation)

- 프로그램이 실행될 때마다 차곡차곡 메모리에 올려두는 방식

- 프로그램의 크기를 고려하여 파티션의 크기 및 개수를 동적으로 바꾸는 방식
- 어떤 프로그램이 종료되었을 때, 그 빈 파티션에서 **외부 단편화**가 발생할 수 있음

- 프로그램이 실행되다 종료되면 `hole`이 생김
  - Hole : 가용 메모리 공간

![image-20210404173004962](https://user-images.githubusercontent.com/77573938/113511032-b938b100-9598-11eb-84bb-71edd4681f68.png)

#### Dynamic Storage-Allocation Problem

: 가변 분할 방식에서 크기가 n인 프로세스를 적재할 가장 적절한 hole을 찾는 문제

- **First Fit**
  - 크기가 n 이상인 것 중, 최초로 찾은 hole에 할당

- **Best Fit**
  - 크기가 n 이상인 것 중, 가장 크기가 작은 hole에 할당
  - hole들의 리스트가 크기 순으로 정렬되지 않은 경우 모든 hole의 리스트를 탐색해야 함
  - 많은 수의 아주 작은 hole들이 생성됨

- **Worst Fit**
  - 크기가 가장 큰 hole에 할당
  - 역시 모든 리스트를 탐색해야 함
  - 상대적으로 아주 큰 hole들이 생성됨

** First-fit 과 Best-fit이 Worst-fit 보다 속도와 공간이용률 측면에서 효과적인 것으로 알려짐 (실험적인 결과)



#### compaction

: 사용중인 메모리 공간을 한군데로 밀어서 hole을 하나로 몰아넣는 방법

- 외부 단편화 문제를 해결하는 한가지 방법
- hole들을 한 곳으로 몰아 매우 큰 block을 만듦
- 매우 비용이 많이 드는 방법
- 최소한의 메모리 이동으로 compaction하는 방법 (매우 복잡한 문제)
- compaction은 프로세스의 주소가 실행 시간에 동적으로 재배치 가능한 경우에만 수행될 수 있음

<br>


## 2. Noncontiguous allocation (불연속 할당)

> 하나의 프로세스가 메모리의 여러 영역에 분산되어 올라가는 방식

현대 컴퓨터 시스템에서 사용하는 방식



### 2.1. 페이징 (Paging)

![image-20210404180732596](https://user-images.githubusercontent.com/77573938/113511034-b9d14780-9598-11eb-824d-2e55df9d3c50.png)

프로세스의 가상 메모리를 **동일한 사이즈의 페이지 단위로 나누어** 물리적 메모리에 **불연속적으로** 저장하는 방식

일부는 backing storage에, 일부는 physical memory에 저장

- 물리 메모리를 동일한 크기의 `Frame` 으로 나누고, 논리 메모리를 동일 크기의 `page`로 나눔 (frame과 같은 크기)

- 장점 : 페이징 기법을 사용하면, hole들의 크기가 균일하지 않아서 어디에 넣을 것인지 or hole들을 한군데로 밀어넣어야 하는 것들을 고려하지 않아도 됨. 물리적인 메모리에 페이지의 크기만큼 비어있기 때문에 프로그램의 어떤 페이지든지 그냥 들어갈 수 있기 때문.

- 단점 : 대신 불연속 할당 방법을 사용하면 주소 변환이 복잡.

  **모든 프로세스가 각각의 주소 변환(논리주소→물리주소)을 위한 `page table` 을 가지며,** 이 테이블은 프로세스가 가질 수 있는 페이지의 개수만큼 주소 변환 엔트리를 갖음

- 특정 프로세스의 몇 번째 페이지가 물리적 메모리의 몇 번째 프레임에 들어 있다는 페이지별 주소 변환 정보를 유지하고 있어야함

- **외부 단편화 X , 내부 단편화 O**



### 2.2. 세그먼테이션 (Segmentation)

프로세스의 가상 메모리를 **의미 단위인 세그먼트로 나누어** 물리 메모리에 적재하는 방식

- 일반적으로 `code, data, stack` 부분이 하나씩의 세그먼트로 정의
- 논리적인 단위인 세그먼트로 나누었기 때문에 그 크기가 균일하지 않음
- 페이징 기법과는 달리, 프로그램을 의미 단위로 나누었기 때문에 부가적인 메모리 관리 오버헤드가 뒤따름
- 세그먼트는 의미 단위이기 때문에 공유와 보안에 있어 페이징 기법보다 효과적
- 세그먼트의 길이가 동일하지 않으므로 외부 단편화 발생

 

### 2.3. 페이지드 세그먼테이션 (Paged Segmentation)

페이징 기법과 세그먼테이션 기법의 장점만을 취하는 주소 변환 기법

- 프로그램을 의미 단위인 세그먼트로 나누지만, 세그먼트가 임의의 길이를 가질 수 없고 반드시 동일한 크기의 페이지들의 집합 으로 구성
- 물리 메모리에 적재하는 단위는 페이지 단위
- 하나의 세그먼트 크기를 페이지 크기의 배수로 함으로써 외부 단편화 해결
- 세그먼트 단위로 프로세스 간의 공유나 프로세스 내의 접근 권한 보호로 페이징 기법의 약점 보완

 