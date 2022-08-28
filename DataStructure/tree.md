# 트리

### 분류
- 비선형 자료구조
    - 트리 : 일대다
    - 그래프 : 다대다
- 선형 자료구조 : 일대일
    - 배열, 연결리스트, 큐, 스택

##  트리란?
- 연결리스트와 유사하게 한 노드가 다른 노드(들)을 가리킴
- 용어
    - 루트 : 부모가 없는 노드
    - 간선 : 부모와 자식을 연결하는 선
    - 리프 : 자식이 없는 노드
    - 형제 : 같은부모를 가진 자식노드들 
    - 조상/자손 : 경로가 있는 경우
    - 레벨 : 주어진 깊이의 모든 노드의 집합
        - 루트노드는 레벨 0
    - 트리의 깊이 : 루트로부터 가장 멀리 떨어진 노드의 높이(!= 레벨)
    - 노드의 크기 : 자손(자식이아님)노드 개수 + 1(자기자신)
    - skew 트리 : 모든 노드가 오직 한개의 자식만을 가지는 트리
        - 모든 노드가 왼쪽(오른쪽) 자식만 가지면 왼쪽(오른쪽) skew 트리

### 이진트리 
- 자식이 없거나 한개 또는 두개의 자식만을 가지는 트리
- 포화이진트리
    - 자식노드가 없거나 두개이고 모든 리프노드의 레벨이 같은 이진트리
- 완전이진트리
    - 포화이진트리를 위에서 아래로, 왼쪽에서 오른쪽으로 채워가는 과정에 있는 트리
- 이진트리 구현
    ```java
    class BinaryTreeNode {
        int data;
        BinaryTreeNode left;
        BinaryTreeNode right;
    }
    ```
- 탐색(Traverse)
    - 전위탐색
        - 자기자신 - 왼쪽자식 - 오른쪽 자식
    - 중위탐색
        - 왼쪽자식 - 자기자신 - 오른쪽 자식
    - 후위탐색
        - 왼쪽자식 - 오른쪽자식 - 자기자신
    - 레벨 순서 탐색
        - 자기 자신을 방문할 때 자식 노드를 큐에 저장
    - 구현(코드는 전위탐색 기준)
        - 재귀호출
            - 시간복잡도: O(N)
            - 공간복잡도(스택깊이): O(N)
                - 평균 O(log N)
                - skewed 인 경우 worst case **O(N)**
                    - 이진트리의 성능을 떨어뜨림
                    - 수가 큰 경우 stack overflow
            ```java
            class Main {
                class BinaryTreeNode {
                    int data;
                    BinaryTreeNode left;
                    BinaryTreeNode right;
                }
                public static void main(String[] args) {
                    int[] arr = {1,2,3,4,5,6,7};
                    BinaryTreeNode bt = new BinaryTreeNode(arr);
                    preorder();
                }
                private static void preOrder(BinaryTreeNode node){
                    print(node.data);
                    preOrder(node.left);
                    preOrder(node.left);
                }
            }
            ```
        - Iteration
            - 스택 자료구조를 사용하여 돌아올 위치를 저장해야 함(재귀 방식에서의 콜스택 기능)  
            - 시간복잡도: O(N)
            - 공간복잡도: O(N)
            ```java
            
            ```
   
### 이진검색트리(Binary Search Tree, BST)
- 노드의 데이터에 제한을 둔 이진트리 
    - 자기자신보다 작으면 왼쪽 서브트리, 크면 오른쪽 서브트리에 저장
- 일반 트리의 검색의 경우 전부 살펴봐야 하므로 O(n)인데 비해
- 이진 검색트리는 일반적으로 O(logn), **검색기능에 특화**
    - skewed 트리의 경우(Worst case) O(n)
    - 그러므로 Balanced 하도록 유지시키는 것이 중요
- 중위탐색 하면 정렬된 리스트를 만들 수 있음

#### 레드블랙트리
- 자가 균형 이진 탐색 트리
- 컴퓨터 공학 분야에서 숫자 등의 비교 가능한 자료를 정리하는 데 쓰이는 자료구조
- 자료의 삽입과 삭제, 검색에서 최악의 경우에도 일정한 실행 시간을 보장(worst-case guarantees)
- 활용 
    - 리눅스 커널 프로세스들의 가상 메모리영역을 관리
    - 리눅스 커널 CPU 스케줄링을 위해 프로세스를 관리
    - 비정형 데이터를 처리하기 위한 NoSQL 시스템에서도 데이터의 메타데이터 등을 관리
    - 함수형 프로그래밍에서 쓰이는 연관 배열이나 집합(set)등을 내부 구현
    - 다른 자료구조 대비 메모리의 접근횟수 및 사용용량 측면에서 효율성이 높기 때문
- [참고1](https://koreascience.kr/article/JAKO201907752705892.pdf)
- [참고2](https://ko.wikipedia.org/wiki/%EB%A0%88%EB%93%9C-%EB%B8%94%EB%9E%99_%ED%8A%B8%EB%A6%AC)

