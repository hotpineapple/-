# Memory Management (4)

## 1. Overview : Allocation of Physical memory issue

1. Contiguous allocation (연속할당)

    1-1. Fixed partition allocation (고정분할)

    1-2. Variable partition allocation (가변분할)

2. Noncontiguous allocation (불연속할당)

    2-1. Paging

    2-2. **Segementation**

    2-3. **Paged Segmentation, Segment with page**

--- 

<br/>

## 2. Segmentation 기법

: 프로세스의 주소공간을 의미단위(segment)로 나누어 물리 주소로 변환하는 기법

<br/>

### 기본 구성
* 논리 주소 : < segment번호, segment 내 offset> 
* Segment table
: segment의 (물리 메모리의)시작 주소(=base) 와 segment의 길이 정보(=limit)를 저장한다. 만약 limit 보다 큰 값을 가지고 주소 변환을 시도하면 trap(SW interrupt) 를 발생시킨다.
* STBR(Segment-table base register) : 물리 메모리 상에서 segment table의 위치
* STLR(Segment-table length register) : 프로그램이 사용하는 segment의 개수

### 특징
* 공유 및 보안 측면에서 paging 기법보다 유리
* 외부 조각(hole) 발생 가능

<br/>

## 3. Paged Segmentation(Segmentation with paging) 기법

: 프로세스의 segment를 다시 여러 개의 page로 구성하는 기법

<br/>

![페이지드 세그멘테이션](https://github.com/hotpineapple/study-for-Tech-Interview/tree/main/OS/week04/image/pagedsegmentation.JPG)

### 참조 과정 

1. logicla address : s와 d로 구성

* s : segment 번호
* d : segment 내 offset
    * p : 페이지 번호
    * d' : 페이지 내 offset

2. segment table의 s번째 엔트리 참조 => s번 segment의 length(=limit)와 이 segment에 대한 page table(=base) 접근 

3. d와 segment length 비교하여 d가 더 크면 trap 발생(addressing error)

4. 2번에서 s로 접근한 page table 의 p번째 엔트리 참조 => 물리 메모리의 page frame 주소 f(=base) 얻음

5. f와 d'를 이용하여 물리 메모리 주소 참조 가능

### 특징
* 기존 Segmentation 방식의 hole 발생 문제 해결

---

<br/>

## 4. Chapter 마무리

### 유의할 점
: 논리주소의 물리주소변환은 하드웨어(MMU)의 역할이지 운영체제의 역할이 아니다.
