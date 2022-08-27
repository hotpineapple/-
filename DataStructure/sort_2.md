# 정렬
- 리스트의 항목을 특정한 순서에 따라 배열하는 것

### 병합정렬
- 평균 O(NlogN), 최악 O(NlogN)
- Devide and Conquer 알고리즘의 예
- 구현
    1. 반으로 나눔
    2. 각각 정렬함 (-> 1번 ... - 재귀)
    3. 다시 합침 - **이것이 중요!**
        - 맨 앞은 각각의 맨 앞 두 개 중에서만 고르면 됨
        - 두 개 중 더 작은 값 고르고 포인터 한 칸 뒤로 해서 안 고른 값과 비교 
        - 반복  
        - 한 번만 스캔하면 됨 O(N)
- 코드
    ```java
    import java.util.*;
    public class Main {
        public static void main(String args[]) {
            int[] arr = { 5, 3, 1, 2, 4 };
            int[] temp = { 5, 3, 1, 2, 4 };
            mergeSort(arr, temp, 0 , 4);
        }
        private static void mergeSort(int[] arr, int[] temp, int left, int right) {
            System.out.println("divied with left, right: "+ left+", "+right);
            if(right > left) {
                int mid = (left+right)/2;
                mergeSort(arr,  temp,  left,  mid); // sort left half (recursive)
                mergeSort(arr,  temp,  mid+1,  right); // sort right half (recursive)
                mergeNcompare(arr, temp, left, mid+1, right); // merge two sub array
            }
        }
        private static void mergeNcompare(int arr[], int temp[], int left, int mid, int right) {
            System.out.println("merge and compare from " + left + " to " + right);
            int size, left_end, temp_pos;
            left_end = mid-1;
            temp_pos = left;
            size = right - left + 1;

            // 1. 두 영역 함께 비교
            while((left <= left_end) && (mid <= right)){
                if(arr[left] <= arr[mid]){
                    temp[temp_pos] = arr[left];
                    temp_pos = temp_pos + 1;
                    left = left + 1;
                }
                else{
                    temp[temp_pos] = arr[mid];
                    temp_pos = temp_pos + 1;
                    mid = mid + 1;
                }

                System.out.println("step 1: "+Arrays.toString(temp));
            }

            // 2-1. 오른쪽 절반 다 선택됐다면 왼쪽 나머지 통째로 복사
            while(left <= left_end){ // 왼쪽 반쪽에서 선택
                // System.out.println(temp_pos+","+left);
                temp[temp_pos] = arr[left];
                left = left + 1;
                temp_pos = temp_pos + 1;
            }

            // 2-2. 왼쪽 절반 다 선택됐다면 오른쪽 나머지 통째로 복사 
            while(mid <= right){ // 오른쪽 반쪽에서 선택
                temp[temp_pos] = arr[mid];
                mid = mid + 1;
                temp_pos = temp_pos + 1;
            }
            System.out.println("step 2: "+Arrays.toString(temp));
            // 3. temp 를 arr로 복사
            for(int i = 0; i < size; i++) { 
                arr[i] = temp[i];
            }
        }
    }
    ```

### 힙 정렬
- 평균 O(NlogN), **최악 O(NlogN)**
- 선택정렬의 한 종류
- 제자리정렬이지만 안정적이지는 않음
- 최대힙/최소힙 이용
- 구현
    1. 항목들로 힙을 만든다 - O(NlogN) 또는 O(N)
    2. 힙의 루트노드를 꺼내서(=맨 마지막 리프노드와 바꿈) 배열 맨끝에 놓는다(최대힙,오름차순 기준) - N 번
    3. 맨 마지막 리프노드를 제외하고 힙을 유지한다 - O(logN)

### 퀵 정렬
- 평균 O(NlogN), **최악 O(N^2)**
- Divide and Conquer 의 한 예
- 실무에서 주로 사용
- 구현
    1. 특정 숫자를 고른다
    2. 이 숫자를 기준으로 작은 값들과 큰 값들로 나눈다 - **이것이 중요**
    3. 각각을 정렬한다 (-> 1번.. - 재귀) -> 전체 정렬 끝(병합은 필요없음)
- 피벗 고르기
    - 랜덤 인덱스로 고르기
    - 랜덤 인덱스로 3개 골라서 그 중 가운데 값으로 고르기   
- 코드
    ```c
        void quickSort(int arr[], int low, int high){
            int pivot;
            if(high > low){
                pivot = partition(arr, low, high);
                partition(arr, low, pivot - 1);    
                quickSort(arr, pivot + 1, high);
            }
        }

        int partition(int arr[], int low, int high){
            int left, right, pivot_item = arr[low];
            left = low;
            right = high;
            while(left <= right){
                while(left<right && arr[left] <= pivot_item) left++;

                while(left<right && arr[right] > pivot_item) right--;

                if(left < right) swap(arr, left, right);
            }
            arr[low] = arr[right];
            arr[right] = pivot_item;
            return right;
        }

        void swap(int arr[], int left, int right){
            int temp = arr[left];
            arr[left] = arr[right];
            arr[right] = temp;
        }
    ```

