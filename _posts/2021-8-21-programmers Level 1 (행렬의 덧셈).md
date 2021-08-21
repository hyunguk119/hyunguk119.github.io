# 프로그래머스 Level 1 (행렬의 덧셈)

##### 문제 설명

행렬의 덧셈은 행과 열의 크기가 같은 두 행렬의 같은 행, 같은 열의 값을 서로 더한 결과가 됩니다. 2개의 행렬 arr1과 arr2를 입력받아, 행렬 덧셈의 결과를 반환하는 함수, solution을 완성해주세요.

##### 제한 조건

- 행렬 arr1,arr2의 행과 열의 길이는 500을 넘지 않습니다.

##### 입출력 예

| arr1          | arr2          | return        |
| ------------- | ------------- | ------------- |
| [[1,2],[2,3]] | [[3,4],[5,6]] | [[4,6],[7,9]] |
| [[1],[2]]     | [[3],[4]]     | [[4],[6]]     |

##### 코드 작성

```
class Solution {
    public int[][] solution(int[][] arr1, int[][] arr2) {
        int[][] answer = new int[arr1.length][arr1[0].length];
        for(int i=0;i<arr1.length;i++){
            for(int j=0;j<arr1[0].length;j++){
                answer[i][j]=arr1[i][j]+arr2[i][j];
            }
        }
        return answer;
    }
}
```

- 풀이

첫 번째로는 2차원 배열 arr1과 arr2는 행과 열의 길이가 같은 행렬이기 때문에 answer의 크기를 arr1의 행의 길이와 arr1[0]의 열의 길이로 선언했습니다.

다음으로는 for문을 사용하여 arr1의 행의 길이만큼 돌리고, 중첩으로 for문을 한번 더 돌렸습니다. 안에 있는 for문은 각 행의 열의 길이만큼 돌린 다음 arr1과 

arr2의 값을 하나씩 더해서 answer배열에 넣은 다음 리턴를 해주었습니다.

##### 문제 출처 : https://programmers.co.kr/learn/courses/30/lessons/12950