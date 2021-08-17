# 프로그래머스 Level 1 (완주하지 못한 선수)

##### 문제 설명

수많은 마라톤 선수들이 마라톤에 참여하였습니다. 단 한 명의 선수를 제외하고는 모든 선수가 마라톤을 완주하였습니다.

마라톤에 참여한 선수들의 이름이 담긴 배열 praticipant와 완주한 선수들의 이름이 담긴 배열 completion이 주어질 때, 완주하지 못한 선수의 이름을 return 하도록 solution 함수를 작성해주세요.

##### 제한사항

- 마라톤 경기에 참여한 선수의 수는 1명 이상 100,000명 이하입니다.
- completion의 길이는 participant의 길이보다 1 작습니다.
- 참가자의 이름은 1개 이상 20개 이하의 알파벳 소문자로 이루어져 있습니다.
- 참가자 중에는 동명이인이 있을 수 있습니다.

##### 입출력 예

| participant                                   | completion                            | return   |
| --------------------------------------------- | ------------------------------------- | -------- |
| ["leo","kiki","eden"]                         | ["eden","kiki"]                       | "leo"    |
| ["marina","josipa","nikola","vinko","filipa"] | ["josipa","filipa","marina","nikola"] | "vinko"  |
| ["mislav","stanko","mislav","ana"]            | ["stanko","ana","mislav"]             | "mislav" |

##### 입출력 예 설명

예제 #1

"leo"는 참여자 명단에는 있지만, 완주자 명단에는 없기 때문에 완주하지 못했습니다.

예제 #2

"vinko"는 참여자 명단에는 있지만, 완주자 명단에는 없기 때문에 완주하지 못했습니다.

예제 #3

"mislav"는 참여자 명단에는 두 명이 있지만, 완주자 명단에는 한 명밖에 없기 때문에 한명은 완주하지 못했습니다.

##### 코드 작성

```
import java.util.*;

class Solution {
    public String solution(String[] participant, String[] completion) {
        String answer = "";
        Map<String,Integer> hash=new HashMap<>();
        for(String player : participant) hash.put(player,hash.getOrDefault(player,0)+1);
        for(String player : completion) hash.put(player, hash.get(player)-1);

        for(String key : hash.keySet()){
            if(hash.get(key)!=0){
                answer=key;
            }
        }
        return answer;
 }
}
```

- 풀이

이 문제 같은 경우는 participant 배열과 completion 배열의 관계만 잘 생각해보면 쉽게 풀 수 있는 문제 입니다.  입출력 예제 #1 로 설명 하자면 completion 배열에 존재하지 않는 "leo"는 완주하지 못한 선수입니다. 이 점만 잘 참고 해주시면 금방 풀 수 입니다.

저같은 경우에는 HashMap을 이용해서 풀이를 하였습니다. participant의 참가자를 key 값으로, value는 +1를 하였고 completion에 참가자도 key 값으로, value는 -1를 했습니다. completion 배열에 value -1를 해준 이유는 completion 배열에는 한명의 선수가 완주하지 못하였기 때문에 value -1를 해주었습니다. 그 다음으로는 key값과 value값이 0이 아닌 경우라는 조건을 만들어서 완주하지 못한 선수를 구할 수 있습니다.

##### 문제 출처 : https://programmers.co.kr/learn/courses/30/lessons/42576

