# 정렬
- 리스트의 항목을 특정한 순서에 따라 배열하는 것

### 왜 정렬해야 하지?
- 문제의 복잡도를 감소시킴
- 검색의 시간복잡도를 감소시킬 수 있음

## 분류 방법(특성들)
- 비교 횟수에 의해
    - 최선 O(NlogN) / 최악 O(N^2)
    - 비교에 기반하지 않은 선형 정렬 알고리즘도 있음
        - 계수정렬, 버킷정렬, 기수정렬
- 교환 횟수에 의해
- 메모리 사용에 의해
    - 예) 병합정렬
    - 데이터를 임시저장하기 위한 보조공간
    - 제자리(In-place) 정렬의 경우 필요 보조공간 없음
- 재귀에 의해
    - 예) 퀵소트
    - 선택정렬과 삽입정렬은 재귀적이지 않음
- 안정성에 의해
    - 같은 원소가 두 개 이상일 때, 정렬 전 / 후에 위치의 전후관계가 동일하면 안정함 
- 적응성에 의해
    - 미리 정렬되어있는 경우 시간 복잡도가 감소하는 경우 적응성이 높음
    - 퀵소트의 경우 오히려 시간복잡도가 증가함 

### 버블정렬
- 평균 O(N^2), 최악 O(N^2)
- 제자리 정렬
- 가장 간단한 정렬
- 구현
    1. 인접한 원소 비교하며 정렬 기준에 따라 교환/그대로 
        - 맨 앞 or 뒤 원소는 고정됨, 다음 턴에서는 비교 제외
    2. 이를 N번(턴) 반복
        - 미리 정렬된 경우 한 번도 교환 X, 다음 라운드를 안 돌림으로써 개선 가능
### 선택정렬
- 평균 O(N^2), 최악 O(N^2)
- 제자리 정렬
- 구현(오름차순 예)
    1. 현재 턴의 가장 작은 값 고름
    2. 현재 턴의 타겟값과 교환 (타겟값은 제일 왼쪽부터 매 턴마다 한 칸씩 오른쪽으로 이동함), 다음 턴에서는 가장 작은 값 후보에서 제외
    3. 1 ~ 2 를 반복
- 장점
    - 구현이 쉬움
- 단점
    - 확장성이 좋지 않음(평균적으로 O(N^2))

### 삽입정렬
- 평균 O(N^2), 최악 O(N^2)
- 단순하고 효율적임
- 카드 정렬 예 
- 구현(오름차순 예)
    1. 항목 중 하나(일반적으로는 정렬된 리스트와 인접한 원소)를 정렬된 리스트의 정렬 기준에 맞게 정확한 위치에 삽입함
        - 항목을 맨 뒤에 놓고 항목 제외 맨 오른쪽부터 비교함
            - 크면 땡겨서 카피, 작으면 그자리에 항목을 넣음
    2. 이를 반복함
- 장점
    - 온라인 정렬 가능 (원소 하나씩 주어져도 정렬 가능)
    - 이미 정렬된 경우 O(N)

```java
public class Main {
    public static void main(String args[]) {
        int[] arr = { 5, 3, 1, 2, 4 };
        int[] temp = new int[5];
        
        // insertionSort(arr);
        // selectionSort(arr);
        // bubbleSort(arr);
    }
    private static void bubbleSort(int[] arr) {
        for(int end = arr.length-1 ; end > 0 ; end--) { // set end index
            
            int aux = 0;
            int temp = arr[aux];
            while(aux<end){ // swap if necessary until end index 
                if(arr[aux] > arr[aux+1]){
                    arr[aux] = arr[aux+1];
                    arr[aux+1] = temp;
                    System.out.println(Arrays.toString(arr));
                }
                aux++;
            }
        }
        
        System.out.println(Arrays.toString(arr));
    }
    private static void selectionSort(int[] arr) {
        for(int index = 0 ; index < arr.length-1 ; index++) { // set target
            System.out.println(Arrays.toString(arr));
        
            int target = arr[index];
            int min = arr[index+1];
            int minIdx = index+1;
            for(int j=index+2;j<arr.length;j++) { // find minunum value
                if(min>arr[j]){
                    min = arr[j];
                    minIdx = j;
                }
            }
            // swap target and mininum value
            arr[minIdx] = target;
            arr[index] = min;
        }
        
        System.out.println(Arrays.toString(arr));
    }
    private static void insertionSort(int[] arr) {
        for(int index = 1 ; index < arr.length ; index++) { // set target
            System.out.println(Arrays.toString(arr));
            
            int target = arr[index];
            int aux = index - 1;

            while((aux >= 0) && (arr[aux] > target)) { // insert into subsequence which is located at the left side of target
               System.out.println("substitute arr[" + (aux+1) + "] to arr[" + aux + "]");
               arr[aux + 1] = arr[aux];
               aux--;
            }
            System.out.println("substitute arr[" + (aux+1) + "] to arr[" + index + "]");
            arr[aux + 1] = target;
        }
        
        System.out.println(Arrays.toString(arr));
    }
}
```
