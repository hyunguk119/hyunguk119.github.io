---
categorise: ['programing','programmers']
---
# 프로그래머스 Level 1 (평균 구하기)

##### 문제 설명

정수를 담고 있는 배열 arr의 평균값을 return하는 함수, solution을 완성해보세요.

##### 제한 조건

- arr은 길이 1이상, 100 이하인 배열입니다.
- arr의 원소는 -10,000 이상 10,000 이하인 정수입니다.

##### 입출력 예

|    arr    | return |
| :-------: | :----: |
| [1,2,3,4] |  2.5   |
|   [5,5]   |   5    |

##### 코드작성

```
class Solution {
    public double solution(int[] arr) {
        double answer = 0;
        int total=0;
        for(int i=0;i<arr.length;i++){
            total +=arr[i];
        }
        answer =(double)total/arr.length;
        return answer;
    }
}
```

평균 구하는 문제이므로 double형 변수 answer을 선언을 합니다. 그와 동시에 int형 변수 total도 같이 선언을 해줍니다.

그 다음으로 arr의 모든 요소들을 int형 total에 더한 뒤 다시 double형 형태로 변환 시켜 arr를 length만큼 나누어 정답을 구하면 된다.

##### 문제 출처 : https://programmers.co.kr/learn/courses/30/lessons/12944

