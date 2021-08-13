# 프로그래머스 Level 1 (짝수와 홀수)

#### 문제 설명

정수 num이 짝수일 경우 "Even"을 반환하고 홀수인 경우 "Odd" 를 반환하는 함수, solution을 완성해주세요.

#### 제한 조건

- num은 int 범위의 정수입니다.
- 0은 짝수입니다.

#### 입출력 예		

| num  | return |
| :--: | :----: |
|  3   | "Odd"  |
|  4   | "Even" |

#### 코드작성

```
class Solutiom{
	public String solution(int num){
		if(num%2 == 0){
			return "Even";
		}
		else{
			return "Odd";
		}
	}
}
```

저는 이문제를 보자마자 산술연산자랑 if else를 활용하여 num이 짝수일 경우에는 "Even" 출력 홀수일 경우에는 "Odd"를 출력하도록 만들었습니다. 이문제 같은 경우는 1단계 연습문제라서 그런지 상당히 쉬운 문제여서 쉽게 풀 수 있었습니다.