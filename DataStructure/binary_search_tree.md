## 이진검색트리(Binary Search Tree, BST)
- 노드의 데이터에 제한을 둔 이진트리 
    - 자기자신보다 작으면 왼쪽 서브트리, 크면 오른쪽 서브트리에 저장
- 일반 트리의 검색의 경우 전부 살펴봐야 하므로 O(n)인데 비해
- 이진 검색트리는 일반적으로 O(logn), **검색기능에 특화**
    - 탐색 시 왼쪽 또는 오른쪽 서브트리 중 한 개의 서브트리만 탐색함
    - skewed 트리의 경우(Worst case) O(n)
    - 그러므로 Balanced 하도록 유지시키는 것이 중요
- 중위탐색 하면 정렬된 리스트를 만들 수 있음
- 주어진 값 찾기
  ```java
  class BSTN {
    int data
    BSTN left;
    BSTN right;
  }
  static int val = 10;
  private static boolean findRecursion(int val, BinarySearchTreeNode node) {
	    if(node == null) return false;
	    else if(node.data > val) return findRecusion(val, node.left);
	    else if(node.data < val) return findRecusion(val, node.right);
	    else return true;
	}
  private static boolean findIteration(BSTN root){
    while(root != null){
      if(node.data > val)  root = root.left;
      else if(node.data < val) root = root.right;
      else return true;
    }
    return false;
  }
  ```
- MIN 값 찾기
  ```java
  class BSTN {
    int data
    BSTN left;
    BSTN right;
  }
  private static int findMinRecusion(BSTN node)
    if(node.left == null) return node.data;
    return findMinRecusion(node.left);
  }
  private static int findMinIteration(BSTN node){
    while(root != null){
      root = root.left;
    }
    return root.data;
  }
  ```
- 삽입
  - 조회와 같은 과정 거쳐서 null 인 자리에 삽입
  - 구현: 트리가 수정됨에 유의
  ```java
  private static BinarySearchTreeNode insertRecursion(int val, BinarySearchTreeNode node) {
    if(node== null) return new BinarySearchTreeNode(val, null, null);
    else if(val<node.data) node.left = insertRecursion(val, node.left);
    else if(val>node.data) node.right = insertRecursion(val, node.right);

    return node;
  }
  ```
- 삭제
  - 전임노드(오름차순 정렬 했을 때 내 바로 이전 값)
    - 왼쪽 서브트리의 MAX 값
    - 왼쪽 자식이 없다면 오른쪽으로 쭉 올라가다가 첫 번째 왼쪽 조상노드
  - 후임노드(오름차순 정렬 했을 때 내 바로 다음 값)
    - 오른쪽 서브트리의 MIN 값
    - 오른쪽 자식이 없다면 왼쪽으로 쭉 올라가다가 첫 번째 오른쪽 조상노드
  - 조회, 삽입에 비해 복잡함
  - 삭제되는 항목이 리프노드가 아닐 수도 있기 때문
  - 삭제되는 항목의 서브트리에 대한 처리를 해주어야 함
  ```java
  public static void main(String[] args) throws IOException {
		
    BinarySearchTreeNode n0 = new BinarySearchTreeNode(3,null,null);
		BinarySearchTreeNode n1 = new BinarySearchTreeNode(1,null,null);
		BinarySearchTreeNode n2 = new BinarySearchTreeNode(4,n0,null);
		BinarySearchTreeNode n3 = new BinarySearchTreeNode(6,null,null);
		BinarySearchTreeNode n4 = new BinarySearchTreeNode(8,null,null);
		BinarySearchTreeNode n5 = new BinarySearchTreeNode(2,n1,n2);
		BinarySearchTreeNode n6 = new BinarySearchTreeNode(7,n3,n4);
		BinarySearchTreeNode n7 = new BinarySearchTreeNode(5,n5,n6);

    deleteRecursion(5, root.left);
    System.out.println(findRecusion(5,n7)); 
	}
  private static boolean findRecursion(int val, BinarySearchTreeNode node) {
	    if(node == null) return false;
	    else if(node.data > val) return findRecusion(val, node.left);
	    else if(node.data < val) return findRecusion(val, node.right);
	    else return true;
	}
	private static BinarySearchTreeNode makeLeafOrOneChild(int val, BinarySearchTreeNode node) {
		
		if(node.data== val) {
			if(node.left != null && node.right != null) {
				// 전임노드와 교환
				int temp = node.data;
				BinarySearchTreeNode prev = findMaxRecursion(node.left);
				node.data = prev.data;
				prev.data = temp;
				
				return node;
			}
		}

		else if(val<node.data) node.left = makeLeaf(val, node.left);
		else if(val>node.data) node.right = makeLeaf(val, node.right);
		
		return node;
	}
	private static BinarySearchTreeNode deleteRecursion(int val, BinarySearchTreeNode node){
		if(node == null) return node;

		else if(val<node.data) node.left = deleteRecursion(val, node.left);
		else if(val>node.data) node.right = deleteRecursion(val, node.right);	
		else {
			// 리프노드
			if(node.left == null && node.right == null) {
				return null;
			}
			// 서브트리 하나 - 왼쪽
			else if(node.left != null && node.right == null) return node.left;
			// 서브트리 하나 - 오른쪽
			else if(node.left == null && node.right != null) return node.right;
      else {
				BinarySearchTreeNode root = makeLeafOrOneChild(val, node);
				deleteRecursion(val,root);
			}
		}
		
		return node;
    }
  ```

### 균형 이진검색트리(balanced binary search tree)
- 이진검색트리가 skew 되지 않도록 하여 장점을 살림(단순 연결리스트와 차별되도록)
- 즉 트리의 높이를 log(N)으로 조절하는 것
  - 왼쪽 서브트리와 오른쪽 서브트리의 높이 차를 적도록
  - (모든 노드에서) 높이차가 최대 0인 이진검색트리를 완전 균형 이진검색트리라고 함
  - (모든 노드에서) 높이차가 최대 1인 이진검색트리를 **AVL 트리**라고 함
- 높이 h 일때 AVL 트리의 노드개수의 최소값 N(h) 이 logN 임을 증명
  - 최대 노드개수 = 2^h+1 - 1
  - 최소 노드개수 = 1 + N(h-1) + N(h-2) -> 중요
    - 높이 1일 때 최소 2 (최대 3)
    - 높이 2일 때 최소 4 (최대 7) 
    - 높이 3일 때 최소 7 (최대 15)
    - 높이 4일 때 최소 12
    - 높이 5일 때 20
    - 높이 6일 때 33
    - 높이 7일 때 54
    - 높이 7일 때 88
    - ...
  - 결론: 개수 n일때 높이 1.44 logN (꽉 차지 않은 경우 logN보다 좀 더 높기 때문)
- AVL 트리 만들기
  - insert, delete 후 조정(=회전)을 통해
  - AVL 트리에 위반되는 노드의 서브트리 높이차는 2 임
  - 
  - 구현
  ```java
  class AVLTreeNode {
    int data;
    int height;
    AVLTree left;
    AVLTree right;
  
    public int getHeight(AVLTreeNode node) {
      if(node==null) return -1;
      return node.height;
    }
    
    public void insert() {
    
    }
    
    public void delete() {
    
    }
  }
  ```

### cf. 레드블랙트리
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
