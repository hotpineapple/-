# File system (Introduction)

## 00. File and File system

<br>

> "주소로 접근하는 메모리와 달리 *이름으로 접근*한다."

### File
* **A named collection** of related information
* 일반적으로 비휘발성(non-volatile)의 보조기억장치(예:하드디스크)에 저장
* 데이터를 저장하는 것 뿐 아니라 다양한 저장장치를 관리하는 데 사용됨.운영체제는 file이라는 동일한 논리적 단위로 다양한 저장장치를 관리함. (=> Device special file)
* 파일과 관련된 Operation
    * create, read, write, reposition(lseek)*, delete, open, close 등
    
    * lseek : 파일을 읽을 때 특정 위치부터 읽도록 현재 포인터를 수정하는 연산

    * open : 파일을 읽거나 쓰기 전 먼저 선행되어아 하는 연산. 뒤에서 다시 다룬다.

### File 의 attribute (혹은 file 의 metadata)
* 파일 자체의 내용이 아니라 *파일을 관리하기 위한 각종 정보*들
    * 파일 이름, 유형, 저장된 위치, 파일 사이즈
    * 접근 권한(읽기/쓰기/실행), 시간(생성/변경/사용), 소유자 등

### File system
* *운영체제에서 파일을 관리하는 부분*
* 파일 및 파일의 메타데이터, 디렉토리(**계층구조**) 정보 등을 관리
* 파일의 저장 방법 결정
* 파일 보호 등

## 01. Directory and Logical Disk

### Directory
* 파일의 메타데이터 중 일부를 보관하고 있는 일종의 특별한 파일
* 그 디렉토리에 속한 파일 이름 및 파일 attribute
* 디렉토리와 관련된 Operation
    * search for a file, create a file, delete a file
    * list a file, rename a file, traverse the file system

### Partition(=Logical Disk) : 운영체제가 보는 disk
* 하나의 (물리적) 디스크 안에 여러 파티션을 두는 것이 일반적임.
* 여러 개의 물리적인 디스크를 하나의 파티션으로 구성하기도 함.
* (물리적) 디스크를 파티션으로 구성한 뒤, 각각의 파티션에 **file system**을 깔거나 **swapping**(=>in virtual memory system) 등 다른 용도로 사용할 수 있음.

---

## 02. open()
: 파일의 **메타데이터**를 메모리에 올려놓는 것(retrieves metadata from disk to main memory)

* metadata 는 파일이 디스크에 저장되어있는 주소 정보를 포함하고 있음.

1. 사용자 프로그램 A가 open("/a/b") 시스템 콜
2. CPU 제어권이 운영체제로 넘어감
3. 운영체제는 커널 주소공간(커널 메모리영역)의 open file table(=> global한 특성을 가짐)에 root directory의 metadata를 올린다.
4. root의 content는 그 디렉토리 하위의 파일의 메타데이터임.
5. 운영체제는 open file table에 a의 metadata를 커널메모리영역에 올린다.
6. a의 content는 b의 metadata임.
7. 운영체제는 커널메모리 영역의 프로세스 A에 대한 PCB에 b의 metadata를 file descriptor 변수(fd)로 저장한다.
8. CPU 제어권이 사용자프로그램으로 넘어감.
9. 사용자 프로그램 A가 read(fd) 시스템 콜
10. CPU 제어권이 운영체제로 넘어감.
11. 운영체제는 파일 b를 읽어 커널 메모리 영역에 COPY 한 후 사용자 프로그램에 전달한다.(=Buffer caching)

* 두 종류의 table의 특성
    * per-process file descriptor (fd) table
    * system-wide open file table 

### open("/a/b/c")

* 디스크로부터 파일 c의 메타데이터를 메모리로 가지고 옴
* 이를 위하여 directory path를 search
* Directory path 의 search에 너무 많은 시간 소요
* Open file table
* File descriptor (file handle, file control block)
---

## 03. File Protection, 접근권한

: 각 파일에 대해 **1. 누구에게** 2. 어떤 유형의 접근(read/write/execution)을 허락할 것인가를 결정

(주소공간과 달리 파일은 일반적으로 서로 다른 사용자 프로그램이 모두 사용 가능하기 때문)


* Access control Matrix

    ||file1|file2|file3|
    |--|--|--|--|
    |**user1**|rw|rw|r|
    |**user2**|rw|r|r|
    |**user3**| r||

    => sparse matrix가 될 것임. 공간 사용 비효율적

    * Access control list : 파일 별 누구에게 어떤 접근 권한이 있는지 linked list로 표현
    * Capability list : 사용자 중심으로 접근권한 표현

<br>

* Grouping
    * 전체 user를 owner, group, public의 세 그룹으로 구분
    * 각 파일에 대해 세 그룹의 접근 권한(r,w,x)를 3비트 씩 표시 => 총 9비트


* Password
    * 파일마다 password를 두는 방법

---
## 04. File system의 Mounting methods


: 서로 다른 파티션의 파일 시스템의 root 디렉토리 파일에 접근가능하도록 하는 방법

* root file system의 특정 디렉토리 이름에 또 다른 파일 시스템의 root를 mount
