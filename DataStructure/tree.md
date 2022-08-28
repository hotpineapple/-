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
    - 인덱스의 규칙을 이용하여 배열로 간단히 구현 가능
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
            import java.io.BufferedReader;
            import java.io.IOException;
            import java.io.InputStreamReader;
            import java.util.*;
            import java.util.StringTokenizer;

            public class Main {
                public static class BinaryTreeNode {
                    int data;
                    BinaryTreeNode left;
                    BinaryTreeNode right;
                    public BinaryTreeNode() {}
                    public BinaryTreeNode(int data, BinaryTreeNode left, BinaryTreeNode right) {
                        this.data = data;
                        this.left = left;
                        this.right = right;
                    }
                }
                public static void main(String[] args) throws IOException {
                    BinaryTreeNode n7 = new BinaryTreeNode(7,null,null);
                    BinaryTreeNode n6 = new BinaryTreeNode(7,null,null);
                    BinaryTreeNode n5 = new BinaryTreeNode(7,null,null);
                    BinaryTreeNode n4 = new BinaryTreeNode(7,null,null);
                    BinaryTreeNode n3 = new BinaryTreeNode(7,n6,n7);
                    BinaryTreeNode n2 = new BinaryTreeNode(7,n4,n5);
                    BinaryTreeNode n1 = new BinaryTreeNode(7,n2,n3);

                    preOrder(n1);
                }	
                private static void preOrder(BinaryTreeNode node){
                    if(node == null) return;

                    System.out.println(node.data);
                    preOrder(node.left);
                    preOrder(node.right);
                }
            }

            ```
        - Iteration
            - 스택 자료구조를 사용하여 돌아올 위치를 저장해야 함(재귀 방식에서의 콜스택 기능)  
            - 시간복잡도: O(N)
            - 공간복잡도: O(N)
            ```java
            BinaryTreeNode n7 = new BinaryTreeNode(7,null,null);
            BinaryTreeNode n6 = new BinaryTreeNode(7,null,null);
            BinaryTreeNode n5 = new BinaryTreeNode(7,null,null);
            BinaryTreeNode n4 = new BinaryTreeNode(7,null,null);
            BinaryTreeNode n3 = new BinaryTreeNode(7,n6,n7);
            BinaryTreeNode n2 = new BinaryTreeNode(7,n4,n5);
            BinaryTreeNode n1 = new BinaryTreeNode(7,n2,n3);
            
            Stack<BinaryTreeNode> stack = new Stack<>();
            
            BinaryTreeNode = n1;
            while(true) {
                while(root!=null){
                    System.out.println(root.data);
                    stack.push(root);
                    root = root.left;
                }
                if(stack.isEmpty()) break;
                root = stack.pop();
                root = root.right;
            }
            ```
