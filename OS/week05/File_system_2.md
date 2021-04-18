# File Systems Implementation (2) 

<br>

## Page Cache & Buffer Cache

[그림] 물리적인 메모리

![image-20210418141326066](https://user-images.githubusercontent.com/77573938/115136252-8f33c400-a059-11eb-84fe-cf1db081fb6e.png)

- **Page Cache**   ← *virtual memory system 관점에서 사용하는 용어*
  - 가상 메모리의 페이징 시스템에서 사용하는 page frame을 캐싱의 관점에서 설명하는 용어
  - Memory-Mapped I/O를 쓰는 경우 파일의 I/O에서도 page cache를 사용
  - 운영체제한테 주어지는 정보가 지극히 제한적이었음
  - 프로세스의 주소 공간을 구성하는 페이지가 swap area에 내려가 있는가, 혹은 페이지 캐시에 올라가 있는가? 를 의미함
- **Memory-Mapped I/O**
  - 파일의 일부를 가상 메모리에 mapping 시킴
  - 매핑시킨 영역에 대한 메모리 접근 연산은 파일의 입출력을 수행하게 한다. 따라서 파일을 접근할 때 read/write 콜을 하는 대신 메모리 영역에 맵핑시킨 영역을 참고하면 된다.
- **Buffer Cache**   ← *file system 관점에서 사용하는 용어*
  - 파일 시스템을 통한 I/O 연산은 메모리의 특정 영역인 buffer cache를 사용
  - 파일 사용의 locality를 활용
    - 파일의 데이터를 사용자가 요청했을 때, 운영체제가 읽어온 내용을 자기 영역의 일부에 저장해 놓고, 그 내용에 대한 요청이 다시 들어왔을 때 디스크까지 가지 않고 buffer cache에서 바로 읽어다 줌.
  - 모든 프로세스가 공용으로 사용됨
  - Replacement 알고리즘 필요 (LRU, LFU 등)
  - 파일 데이터가 File system 스토리지에 저장되어 있는가, 혹은 buffer cache에 올라가 있는가? 를 처리하는 것
- **Unified Buffer Cache**
  - 최근의 OS에서는 기존의 buffer cache가 page cache에 통합됨 (특히, 리눅스)
  - buffer cache도 page 단위로 관리한다고 알아두면 됨
  - page는 보통 4KB, block 하나는 512byte로 구성되었는데, page cache와 buffer cache가 통합(Unified)되면서 buffer cache에서도 page 크기로 block을 관리함 (즉, 버퍼 캐시에서도 페이지 단위인 4KB를 사용)

<br><br>

## 파일 입출력(I/O)

![image-20210418142311258](https://user-images.githubusercontent.com/77573938/115136255-9064f100-a059-11eb-921d-b7df82c47155.png)



### 1. Unified Buffer Cache를 이용하지 않는 File I/O

> 2가지 인터페이스가 존재



1. **Memory-Mapped I/O**
   - 처음 memory-mapped I/O를 쓰겠다는 `mmap()` 시스템 콜을 한다.
- 그러면 자신의 주소 공간 중 일부를 파일에 매핑하고, 디스크에서 버퍼 캐시로 내용을 읽어온 뒤, 그 내용을 page cache에 카피하면 그 이후에는 운영체제의 간섭없이 내 메모리의 영역에 메모리를 읽고 쓰는 방식으로 파일 입출력을 할 수 있다.
   - mapping은 했지만 아직 메모리로 읽어오지 않았다면 page fault가 발생한다.
   
2. **I/O using read() and write()**
   - 파일을 open한 뒤, read/write 시스템 콜을 한다.
   - 운영체제는 해당하는 파일 내용이 buffer cache에 있는지 확인해서 있으면 바로 전달해주고, 없으면 디스크 파일 시스템에서 읽어와서 buffer cache에 저장한 뒤 전달해준다.



- 차이점

  : `I/O using read() and write()` 방식의 경우, 그 내용이 buffer cache에 있든 없든 항상 운영체제에 요청해서 받아와야 한다.

  반면, `Memory-Mapped I/O` 방식은 단 페이지 캐시에 올라온 내용은 운영체제의 도움을 받지 않고 사용자 프로세스가 직접, 자기 메모리에 접근하듯이 접근할 수 있다. <- memory-mapped I/O를 사용하는 장점

- 공통점

  : 둘 중 어떤 방식을 쓰더라도 파일 입출력 시 버퍼 캐시를 통과해야 한다.

  buffer cache는 파일 입출력을 위해 미리 운영체제가 할당해 놓은 영역이고 그 루틴을 항상 통과해야하기 때문에 두 방법 모두 buffer cache에 있는 내용을 자신의 page cache에 한번 복제해야하는 overhead가 있다.

<br>

### 2. Unified Buffer Cache를 이용하는 File I/O

> 기존의 buffer cache처럼 운영체제가 공간을 따로 나눠놓지 않고 필요에 따라 page cache에서 공간을 할당해서 사용.
>
> 경로가 더 단순해짐.



1. **Memory-Mapped I/O**
   - 처음 운영체제에 자신의 주소 영역의 일부를 파일에 매핑하는 단계를 거치고 나면, 그때부터는 사용자 프로그램의 주소 영역에 page cache가 매핑된다.
   - 그래서 Unified Buffer Cache를 이용하지 않을 때처럼 buffer cache의 내용을 카피하는 것이 아닌, page cache 자체가 사용자 프로세스의 논리적 주소 영역에 매핑되었으므로, page cache에 직접 읽고 쓸 수 있게 된다.


2. **I/O using read() and write()**
   - 해당하는 내용이 디스크 file system에 있든 buffer cache(=page cache)에 올라와있든 상관없이 운영체제한테 CPU제어권이 넘어간다.
   - 운영체제는 이미 메모리에 올라와있는 내용은 사용자 프로그램의 주소 영역에 카피해주면 된다.
   - 메모리에 올라와있지 않은 내용은 디스크 file system에서 읽어와서 카피 후 사용자 프로그램한테 카피해서 전달한다.

<br><br>

## 프로그램의 실행

![image-20210418144405615](https://user-images.githubusercontent.com/77573938/115136257-90fd8780-a059-11eb-8adf-aeeefa990d7b.png)



> < *이전 수업 내용 - chapter 2* >
>
> - 프로그램이 File system에 실행파일 형태로 저장되어 있고, 이를 실행시키면 프로세스가 된다. 
>
> - 그러면 그 프로세스만의 독자적인 주소 공간인 Virtual Memory가 만들어지고, 이는 code, data, stack으로 구성된다. 
>
> - 주소변환을 해주는 하드웨어에 의해 당장 필요한 부분은 물리적 메모리에 올라가 있게 되고, 물리적 메모리는 공간이 한정되어 있기 때문에 쫓겨나는 내용들은 디스크의 swap area로 넘어간다.



이때, **code 부분을 간과하고 넘어갔었는데,**

- code 부분은 메모리에 올라갔다가 쫓겨날 때 사실 swap area로 내려가지 않는다. 

  -> why? 코드가 파일 시스템에 실행 파일의 형태로 이미 저장되어 있기 때문

- 그래서 만약 프로그램이 특정 코드에 접근하는 데 그 내용이 메모리에 올라와 있지 않으면, swap area가 아니라 파일 시스템의 실행 파일에 올려야 한다. (파일을 프로그램의 주소 영역에 매핑해서 쓰는 대표적인 예)

<br>

![image-20210418145910308](https://user-images.githubusercontent.com/77573938/115136259-91961e00-a059-11eb-8614-1cd0b59ed2da.png)

실행파일도 File system에 저장되어 있지만, 데이터 파일도 저장되어 있다.

- 프로그램이 실행되다 데이터파일을 읽을 때,  `mmap() I/O` 을 쓰는 경우
  - 프로그램이 운영체제한테 데이터파일의 일부를 주소공간에 mapping 해달라고 시스템 콜을 한다.
  - 그러면 운영체제가 이 데이터파일의 일부를 프로그램 주소공간 일부에 mapping 한다.
  - 프로그램이 실행되면서 해당 메모리 위치에 접근했을 때, 만약 그 내용이 메모리에 올라와있지 않았다면 page fault가 발생하고 CPU가 OS한테 넘어가서 page fault난 페이지를 물리적인 메모리에 올려두고, 그 이후부터는 가상메모리의 까만 부분이 물리적인 메모리의 까만 부분으로 mapping된다.
  - 그래서 그 시점부터는 프로세스B가 해당 데이터파일의 까만부분에 접근할 때는 OS의 도움을 받지 않고 물리적메모리에 바로 데이터를 읽고 쓸 수 있게 된다. 
  - 그러나 나중에 해당 내용(까만 부분)이 메모리에서 쫒겨날때는 swap area를 쓰는 게 아니라 `mmap()`이기 때문에 데이터파일에 수정된 내용을 작성하고 쫒아내야한다.
  - 또 다른 프로그램 A가 동일한 파일의 동일한 데이터에 대해 `mmap() I/O` 호출을 하면 그 내용이 share된다.

- `read()/write() system call` 을 할 경우
  - 데이터 파일의 특정 내용을 달라고 OS한테 시스템콜을 한다.
  - 그러면 운영체제는 자신의 buffer cache에 해당 데이터파일 내용을 읽어온다.
  - Unified Buffer Cache인 경우에는, page cache와 buffer cache를 겸하고 있기 때문에, 이미 물리적 메모리에 데이터가 올라와있다면 그 내용을 카피해서 사용자 프로세스한테 전달해준다.

