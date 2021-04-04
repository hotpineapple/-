# Memory Management (3) 퀴즈 답안
> OX, 단답형, 약서술 문제입니다.

#### 1. Multilevel paging 기법에서 메모리 접근 시간을 줄이기 위해 이용되는 캐시메모리는?

답 : TLB(Table Lookaside Buffer)

#### 2. Memory Protection을 위해 페이지 테이블의 엔트리가 페이지 프레임 번호 이외에 가지는 속성(bit) 두 가지는?

답 : Protection bit 과 Valid/Invalid bit

#### 3. Inverted Page Table을 이용한 주소 변환 시 논리 주소의 pid를 인덱스로 하여 페이지 프레임 번호에 접근가능하다.(O/X)

답 : X

해설 : Inverted Page Table은 물리 메모리의 페이지 프레임 기준이므로 pid를 인덱스로 접근할 수 없습니다.

순차적으로 혹은 병렬적으로 테이블을 모두 탐색하며 논리 주소의 pid와 p에 해당하는 엔트리를 찾고, 이 엔트리의 f값을 이용하여 물리메모리 주소를 얻습니다.
