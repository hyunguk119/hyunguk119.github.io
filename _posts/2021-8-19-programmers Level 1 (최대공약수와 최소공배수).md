---
categorise: ['programing','programmers']
---
# 프로그래머스 Level 1 (최대공약수와 최소공배수)

##### 문제 설명

두 수를 입력받아 두 수의 최대공약수와 최소공배수를 반환하는 함수, solution을 완성해 보세요. 배열의 맨 앞에 최대공약수, 그다음 최소공배수를 넣어 반환하면 됩니다. 예를 들어 두 수 3,12의 최대공약수는 3, 최소공배수는 12이미로 solution(3,12)는 [3,12]를 반환해야 합니다.

##### 제한 사항 

- 두 수는 1이상 1000000이하의 자연수입니다.

##### 입출력 예

|  n   |  m   | return |
| :--: | :--: | :----: |
|  3   |  12  | [3,12] |
|  2   |  5   | [1,10] |

##### 입출력 예 설명

입출력 예 #1

위의 설명과 같습니다.

입출력 예 #2

자연수 2와 5의 최대공약수는 1, 최소공배수는 10이므로 [1,10]을 리턴해야 합니다.

##### 코드 작성

```
class Solution {
    public int[] solution(int n, int m) {
        int[] answer = new int[2];
        answer[0] = gcd(n,m);
        answer[1] = n*m /answer[0];
        return answer;
    }
    static int gcd(int a,int b){
        while(b!=0){
            int r=a%b;
            a=b;
            b=r;
        }  return a;
    }
}
```

- 풀이

저는 유클리드 호제법을 이용하여 문제를 풀이하였습니다. 

일단 answer[0]에 유클리드 호제법을 사용하여 저장을 했습니다. 그 다음으로 answer[1]에는 최소 공배수를 이용해서 최대공약수를 구했습니다.

유클리드 호제법 대한 내용은 밑에 링크를 통해서 공부를 했습니다

##### 유클리드 호제법 링크 : https://namu.wiki/w/%EC%9C%A0%ED%81%B4%EB%A6%AC%EB%93%9C%20%ED%98%B8%EC%A0%9C%EB%B2%95

##### 문제 출처 : https://programmers.co.kr/learn/courses/30/lessons/12940

