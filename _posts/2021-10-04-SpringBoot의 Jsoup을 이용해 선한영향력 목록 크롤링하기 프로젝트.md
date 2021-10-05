# SpringBoot의 Jsoup을 이용해 선한영향력 목록 크롤링하기 프로젝트

### Jsoup

Jsoup이란 HTML을 가져오고 파싱할 수 있게 도와주는 자바 라이브러리입니다.

이 라이브러리를 사용하면 자바에서 간단하게 크롤링 및 파싱을 할 수 있습니다.



### Jsoup의 주요 요소

| 클래스명   | 설명                                                         |
| :--------- | :----------------------------------------------------------- |
| Document   | Jsoup 얻어온 결과 HTML 전체 문서                             |
| Element    | Document의 HTML 요소                                         |
| Elements   | Element가 모인 자료형, for나 while 등 반복문 사용이 가능하다. |
| Connection | Jsoup의 connect 혹은 설정 메소드들을 이용해 만들어지는 객체, 연결을 하기 위한 정보를 담고 있다. |
| Response   | Jsoup가 URL에 접속해 얻어온 결과. Document와 다르게 status 코드, status 메시지나 charset 같은 헤더 메시지와 쿠키등을 가지고 있다. |



### 프로젝트 설명

현재 시간을 기준으로 선한영향력 목록을 가져오는 프로젝트입니다.

완성한 모습은 다음 사진과 같다.

![스크린샷, 2021-09-28 05-16-29](/home/hyunguk/%EC%82%AC%EC%A7%84/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7,%202021-09-28%2005-16-29.png)



### 개발 환경

- Intellij Ultimate
- Gradle 7.1.1
- Java 11
- SpringBoot 2.5.3
- Ubuntu os



### 의존성 추가

start.spring.io에서 추가할 수 있는 의존성입니다.

- Lombok
- Tymeleaf
- Web
- Test
- JPA
- devtools



### 구현

- **의존성 추가**

Jsoup을 사용하기 위해 의존성을 추가해줍니다.

```
implementation group: 'org.jsoup', name: 'jsoup', version: '1.11.3'
```



- **객체 만들기**

![스크린샷, 2021-09-28 05-25-30](/home/hyunguk/%EC%82%AC%EC%A7%84/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7,%202021-09-28%2005-25-30.png)

위 정보들을 담을 멤버변수(지역, 매장명, 주소, 전화번호, 후원안내)를 포함한 객체들을 만들어줍니다.

```
@ToString
@Entity
@Getter
@Setter
@NoArgsConstructor
public class HyunGuk {
    @Id
    @GeneratedValue
    private Long id;
    @Column
    private String area;
    @Column
    private String shop;
    @Column
    private String address;
    @Column
    private String phone;
    @Column
    private String sponsor;
    @Builder
    public HyunGuk(String area, String shop, String address, String phone, String sponsor){
        this.area=area;
        this.shop=shop;
        this.address=address;
        this.phone=phone;
        this.sponsor=sponsor;
    }
}

```

코드의 가독성을 위해 Lombok을 사용했습니다.

값을 가져오기 위해 **@Getter**를, 콘솔에서 값을 확인하기 위해 **@ToString**을, 값을 주입하여 객체를 만들기 위해 **@Builder**어노테이션을 추가해주었습니다.

(**@Setter**를 추가해주고 set방식으로 값을 주입해주어도 상관없습니다.)



### Service 만들기

이제 객체도 준비되었기 때문에 Jsoup으로 크롤링하는 메소드를 만들 차례입니다.

```
@Service
public class GoodInfluenceService {

	private static String GOOD_INFLUENCE_URL = "http://16680010.com/gnuboard5/bbs/board.php?bo_table=branches";

	@PostConstruct
	public void getKoreaCovidDatas() throws IOException {

        Document doc = Jsoup.connect(GOOD_INFLUENCE_URL).get();
		System.out.println(doc);

  }

}
```

가장 먼저 해당 URL을 GET 형식으로 가져온 문서를 모두 가져오는 코드를 작성합니다.

**@PostConstruct** 어노테이션을 사용해 시작하자마자 메소드를 실행하게 설정해줍니다.![스크린샷, 2021-09-28 05-44-23](/home/hyunguk/%EC%82%AC%EC%A7%84/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7,%202021-09-28%2005-44-23.png)

콘솔창을 보면 해당 페이지의 html 전체가 찍히는 것을 확인할 수 있습니다.

하지만 이 데이터들을 가공하기에는 데이터의 양이 너무 많아 파싱을 통해 원하는 데이터를 추려보겠습니다.![스크린샷, 2021-09-28 05-45-59](/home/hyunguk/%EC%82%AC%EC%A7%84/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7,%202021-09-28%2005-45-59.png)

목록에 있는 페이지에 들어가서 확인해보면, 필요한 데이터들은 table - tbody 에 있는 tr 태그안에 있다는 것을 확인 할 수 있습니다.

Jsoup에서 제공해주는 파싱 메소드인 select()를 이용해서 수정해보겠습니다.

```
@Service
public class GoodInfluenceService {

	private static String GOOD_INFLUENCE_URL = "http://16680010.com/gnuboard5/bbs/board.php?bo_table=branches";

	@PostConstruct
	public void getKoreaCovidDatas() throws IOException {

        Document doc = Jsoup.connect(GOOD_INFLUENCE_URL).get();
        Elements contents = doc.select("table tbody tr");
        
		System.out.println(contents);

  }

}
```

![스크린샷, 2021-09-28 05-51-41](/home/hyunguk/%EC%82%AC%EC%A7%84/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7,%202021-09-28%2005-51-41.png)

table안의 tbody안의 tr 태그는 하나의 **Element**가 되고, 

Element를 담는 리스트 같은 역할을 하는 것이 **Elements**입니다.

select()의 반환 타입은 Elements입니다.

이제 이 Element들을 이용해서 객체에 값을 넣어주어야 합니다.![스크린샷, 2021-09-28 05-54-26](/home/hyunguk/%EC%82%AC%EC%A7%84/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7,%202021-09-28%2005-54-26.png)

값들이 td에 있다는 것을 알 수 있습니다.

```ㅇ
@Service
public class GoodInfluenceService {

	private static String GOOD_INFLUENCE_URL = "http://16680010.com/gnuboard5/bbs/board.php?bo_table=branches";

	@PostConstruct
	public void getKoreaCovidDatas() throws IOException {

        Document doc = Jsoup.connect(GOOD_INFLUENCE_URL).get();
        Elements contents = doc.select("table tbody tr");
        
        for (Element content : contents){
            Elements tdContents = content.select("td");
            HyunGuk hyunGuk = HyunGuk.builder()
                    .area(tdContents.get(0).text())
                    .shop(tdContents.get(1).text())
                    .address(tdContents.get(2).text())
                    .phone(tdContents.get(3).text())
                    .sponsor(tdContents.get(4).text())
                    .build();
            System.out.println(hyunGuk.toString());
       }
  }

}
```

위와 같이 코드를 수정해줍니다.

for-each문을 돌려서 추가로 td 태그를 가지고 있는 요소들을 검색해서 tdContents에 넣습니다.

HyunGuk 객체를 만들고, text()를 이용하여 태그의 값을 가져와서 태그 순서대로 값을 세팅해줍니다.

![스크린샷, 2021-09-28 06-01-46](/home/hyunguk/%EC%82%AC%EC%A7%84/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7,%202021-09-28%2006-01-46.png)

값이 잘 나오는 것을 확인할 수 있습니다.

이런 식으로 반복문을 돌리면서 웹 크롤링을 할 수 있습니다.



### NEXT PAGE 파싱하기

![스크린샷, 2021-09-28 06-08-07](/home/hyunguk/%EC%82%AC%EC%A7%84/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7,%202021-09-28%2006-08-07.png)

위에 사진처럼 하단에 1~10 이후에도 다음 페이지에도 많은 목록 들이 있습니다. 하지만 위에 있는 처음에 설명한 방법처럼 하면 해당 URL에 있는 정보만 파싱하기 때문에 다음 페이지에 있는 목록은 파싱을 못합니다. 그래서 저는 다음 페이지도 파싱하기 위해서 페이지 목록을 하나씩 돌리면서 파싱하기로 했습니다. 

![스크린샷, 2021-09-28 06-08-35](/home/hyunguk/%EC%82%AC%EC%A7%84/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7,%202021-09-28%2006-08-35.png)

근데 상단에 있는 URL를 보면 http://16680010.com/gnuboard5/bbs/board.php?bo_table=branches&page= 여기 까지는 같고 그 뒤부터는 숫자만 바뀌는 것을 볼 수 있습니다. 그래서 저는 URL 끝에 있는 숫자만 남기고 다 자른 다음에 마지막 숫자가 나오면 그 숫자만큼 반복문을 돌려서 모든 페이지를 파싱하기로 했습니다.

![스크린샷, 2021-09-28 06-12-45](/home/hyunguk/%EC%82%AC%EC%A7%84/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7,%202021-09-28%2006-12-45.png)

일단 처음으로 필요한 페이지 정보 데이터들이 span - a 에 있다는 것을 확인할 수 있습니다. 

그래서 Jsoup에서 제공해주는 파싱 메소드인 select()를 이용해서 수정해보겠습니다.

```
@Service
public class HyunGukService {

    private static String HYUN_SERVICE_URL = "http://16680010.com/gnuboard5/bbs/board.php?bo_table=branches"; // 선한영양력 URL
    @PostConstruct
    public void getGoodInfluence() throws IOException {

        Document document = Jsoup.connect(HYUN_SERVICE_URL).get();
        Elements elements = document.select("span a");
```

위에 있는 코드 처럼 작성한 뒤 필요한 페이지 정보 데이터를 가져온 다음 URL를 자른 수 있도록 코드를 작성하였습니다.

```
@Service
public class HyunGukService {

    private static String HYUN_SERVICE_URL = "http://16680010.com/gnuboard5/bbs/board.php?bo_table=branches"; // 선한영양력 URL
    @PostConstruct
    public void getGoodInfluence() throws IOException {

        Document document = Jsoup.connect(HYUN_SERVICE_URL).get();
        Elements elements = document.select("span a");
        List<Element> str = new ArrayList<>(elements.subList(10,11));
        String str1 = str.toString();
        String str2 = str1.substring(49,51);
        int i = Integer.parseInt(str2);

```

이렇게 코드를 작성해주면 페이지를 표시하는 숫자만 남습니다.

숫자를 i 라는 변수에 넣고 반복문 사용하여 반복을 해주었습니다.

```
@Service
public class HyunGukService {

    private static String HYUN_SERVICE_URL = "http://16680010.com/gnuboard5/bbs/board.php?bo_table=branches"; // 선한영양력 URL
    @PostConstruct
    public void getGoodInfluence() throws IOException {

        Document document = Jsoup.connect(HYUN_SERVICE_URL).get();
        Elements elements = document.select("span a");
        List<Element> str = new ArrayList<>(elements.subList(10,11));
        String str1 = str.toString();
        String str2 = str1.substring(49,51);
        int i = Integer.parseInt(str2);

        for (int i1=1 ; i1<= i;i1++) {
            String NEXT_URL = "http://16680010.com/gnuboard5/bbs/board.php?bo_table=branches&page="+i1; // 페이지 목록 하나씩 돌리면서 파싱하기
            Document document1 = Jsoup.connect(NEXT_URL).get();
            Elements contents1 = document1.select("table tbody tr");
            for (Element content : contents1){
                Elements tdContents = content.select("td");
                hyunGukRepository.save(HyunGuk.builder()
                        .area(tdContents.get(0).text())
                        .shop(tdContents.get(1).text())
                        .address(tdContents.get(2).text())
                        .phone(tdContents.get(3).text())
                        .sponsor(tdContents.get(4).text())
                        .build());
            }
        }

    }
```

위에 있는 코드처럼 반복문을 돌리면서 URL 끝에 있는 숫자만 변경해주면서 목록 하나씩 돌리면서 파싱할 수 있습니다.

그 결과 

![스크린샷, 2021-09-28 05-16-29](/home/hyunguk/%EC%82%AC%EC%A7%84/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7,%202021-09-28%2005-16-29.png)

맨 처음에 있는 사진 처럼 모든 목록에 있는 정보들을 파싱할 수 있었습니다.



이렇게 웹 크롤링 하는 방법을 마침겠습니다.

