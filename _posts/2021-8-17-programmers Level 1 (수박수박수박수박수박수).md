---
categorise: ['programing','programmers']
---
# 프로그래머스 Level 1 (수박수박수박수박수박수?)

##### 문제 설명

길이가 n이고, "수박수박수박수...."와 같은 패턴을 유지하는 문자열을 리턴하는 함수, solution을 완성하세요. 예를들어 n이 4이면 "수박수박"을 리턴하고 3이라면 "수박수"를 리턴하면 됩니다.

##### 제한 조건 

- n은 길이 10,000이하인 자연수입니다.

##### 입출력 예

|  n   |   return   |
| :--: | :--------: |
|  3   |  "수박수"  |
|  4   | "수박수박" |

##### 코드 작성

```
class Solution {
    public String solution(int n) {
        String answer = "";
        for(int i=1;i<=n;i++){
            if(i%2==1){
             answer+="수";
            }
            else{
             answer+="박";
            }
        }
        return answer;
    }
}
```

- 풀이 

이 문제는 패턴 파악을 하여 짝수번째랑 홀수번째 자리에 대해 리턴을 해주시면 됩니다.

##### 문제 출처 : https://programmers.co.kr/learn/courses/30/lessons/12922

