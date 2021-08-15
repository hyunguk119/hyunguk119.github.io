# 프로그래머스 Level 1 (짝수와 홀수)

##### 문제 설명

정수 num이 짝수일 경우 Even을 반환하고 홀수인 경우 Odd를 반환하는 함수, solution을 완성해주세요.

##### 제한 조건

- num은 int 범위의 정수입니다.
- 0은 짝수입니다.

##### 입출력 예

| num  | return |
| :--: | :----: |
|  3   | "Odd"  |
|  4   | "Even" |

##### 코드 작성

```
class Solution {
    public String solution(int num) {
        if(num%2 ==0){
            return "Even";
        }
        else{
            return "Odd";
        }
    }
}
```

% 연산자를 사용하여 num을 2로 나누었을 때 나머지가 0이면 짝수가 반환 되고 나머지는 홀수로 반환해서 답을 구하시면 됩니다.

##### 문제 출처 : https://programmers.co.kr/learn/courses/30/lessons/12937