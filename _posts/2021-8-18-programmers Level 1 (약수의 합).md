---
categorise: ['programing','programmers']
---
# 프로그래머스 Level 1 (약수의 합)

##### 문제 설명

정수 n을 입력받아 n의 약수를 모두 더한 값을 리턴하는 함수, solution을 완성해주세요.

##### 제한 사항

- n은 0이상 3000이하인 정수입니다.

##### 입출력 예

|  n   | return |
| :--: | :----: |
|  12  |   28   |
|  5   |   6    |

##### 입출력 예 설명

입출력 예 #1

12의 약수는 1,2,3,4,6,12입니다. 이를 모두 더하면 28입니다.

입출력 예 #2

5의 약수는 1,5,입니다. 이를 모두 더하면 6입니다.

##### 코드 작성

```
class Solution {
    public int solution(int n) {
        int answer = 0;
        
        for(int i=1;i<=n;i++){
            if(n%i==0){
                answer +=i;
            }
        }
        return answer;
    }
}
```

- ##### 풀이

% 연산자를 사용하여 나머지가 0이 나오는 숫자들은 모두 더한 다음 리턴 해주시면 됩니다.

##### 문제 출처 : https://programmers.co.kr/learn/courses/30/lessons/12928

