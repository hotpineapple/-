# Memory Management (2)



## Implementation of Page Table

페이징 기법은 각각의 페이지들이 어느 위치에 올라가있는지 알기 위해 페이지별로 주소변환을 해주는 것이 필요.

프로그램마다 별도의 페이지 테이블이 존재해야해서 페이지 테이블의 용량이 굉장히 큼 → 레지스터에 넣은 수 없어서 물리적 메모리에 넣음. 

- page table은 main memory에 상주

- page-table base register (PTBR)가 page table을 가리킴

- Page-table length register (PTLR)가 테이블 크기를 보관

- **모든 메모리 접근 연산에는 `2번`의 memory access 필요** → 비용 큼 & 속도 낮아짐

  page table 접근 1번, 실제 data/instruction 접근 1번

  (메모리에 접근하기 위한 주소변환 시 페이지 테이블에 접근, 페이지테이블이 물리적메모리에 존재하기 때문에 또 한번 접근)

- **속도 향상을 위해** associative register 혹은 translation look-aside buffer(`TLB`) 라 불리는 고속의 lookup hardware cache 사용

### TLB

- 주소변환을 위한 캐시 메모리

- CPU가 논리적인 주소를 주면 메모리 상의 페이지 테이블에 접근하기 전에 TLB를 먼저 검색한다. 
  정보가 저장되어 있다면 TLB를 통해 바로 주소변환이 이루어지므로 `1번`의 memory access 필요

- TLB에 없는 경우라면 페이지 테이블을 통해 일반적인 주소변환을 하고 메모리 접근을 하여 `2번`의 memory access 필요

- TLB는 페이지 테이블의 정보 전체를 갖고 있는게 아닌, 빈번히 참조되는 엔트리 몇개만을 가지고 있음

  → 주소변환된 프레임번호 `f`와, 몇번째 엔트리를 주소변환한게 f인지를 나타내는 논리적인 번호 `p`를 쌍으로 가지고 있어야 함

- TLB는 **특정 항목이 아닌 전체를 검색해야 함.** → 시간 오래 걸림 ⇒ parallel search가 가능한 associative registers를 이용해서 구현하고 있음.

- 페이지 테이블은 각각의 프로세스마다 존재 (프로세스마다 논리적인 주소체계가 달라서) 
  → 페이지 테이블의 일부를 다루는 TLB도 프로세스마다 존재

- TLB는 context switch 때 flush를 시켜 모든 엔트리를 비워야 함 (프로세스마다 주소변환 정보가 다르기 때문에)

![image-20210404213738155](https://user-images.githubusercontent.com/77573938/113511065-f56c1180-9598-11eb-9f90-a6c14a0c1c36.png)



- 실제로 메모리에 접근하는 시간
  - α : TLB에서 주소변환 정보가 찾아지는 비율
  - 결론적으로 페이지테이블만 있을 때 접근하는 시간보다 훨씬 적게 걸린다

![image-20210404215714906](https://user-images.githubusercontent.com/77573938/113511066-f604a800-9598-11eb-837f-8089273152a1.png)



<br><br>

## Two-Level Page Table

페이지 테이블이 두 단계로 존재

- 두단계로 존재하는 이유?  **페이지테이블을 위한 공간이 줄어들음**

![image-20210404220506127](https://user-images.githubusercontent.com/77573938/113511067-f69d3e80-9598-11eb-8bc6-8c11363b38e4.png)

현대의 컴퓨터는 address space가 매우 큰 프로그램 지원 (최근에는 64 bit addresss 사용)

32 bit addresss 사용시 2³² byte (4기가)의 주소공간 존재 (* 1비트 = 2바이트 = 2가지 정보 표현 가능)

- 페이지 크기가 4K시 1M개의 page table 엔트리 필요

- 각 페이지 엔트리가 4byte시 프로세스당 4MB의 page table 필요

- 그러나, 대부분의 프로그램은 4GB의 주소 공간 중 **지극히 일부분만 사용하므로 page table 공간이 심하게 낭비 됨 → page table 자체를 page로 구성. 즉, 2단계 page table 사용**

  **사용되지 않는 주소 공간에 대한 outer-page table의 엔트리 값은 NULL !**

<br>

*** 2단계 페이징에서 기억할 점 !**

- 안쪽 페이지 테이블(page of page table)의 크기는 page 크기와 동일하다. 즉, 안쪽 테이블은 테이블 자체가 페이지화되서 페이지 어딘가에 들어가 있게 됨. 

  (페이지 크기 하나가 4KB이기 때문에 안쪽 페이지 테이블 크기 역시 4KB)

- 일반적으로 페이지 테이블 엔트리 크기 하나가 4byte이므로 4KB/4byte = 1KB개의 엔트리를 넣을 수 있음.

<br>

[그림] 2단계 페이징에서의 Address-translation scheme

![image-20210404222435222](https://user-images.githubusercontent.com/77573938/113511069-f735d500-9598-11eb-9dce-6584b7f11fe1.png)

<br>

## Two-Level Paging Example

4KB = 2¹²byte이므로 구분하기 위해서는 12bit의 page offset 필요

![image-20210404223711298](https://user-images.githubusercontent.com/77573938/113511070-f735d500-9598-11eb-91cb-74418bf73246.png)



