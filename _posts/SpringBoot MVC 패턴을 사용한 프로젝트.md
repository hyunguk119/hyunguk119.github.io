# SpringBoot MVC 패턴을 사용한 프로젝트

### 프로젝트 내용

- 선한영향력 크롤링하기
- 데이터 : 지역, 가게, 주소, 전화번호, 후원
- 기능 : 가게 등록, 중복, 삭제 등
- DB에 저장

### MVC 패턴이란

MVC는 각각 Model, View, Controller를 나타낸다.

사용자가 웹 애플리케이션을 이용하기 위해 URL 요청을 하면, 해당 요청은 Controller에 들어오게 된다.

Controller은 해당 요청을 담당하는 메소드로 안내하고, 요청에 포함된 정보를 가지고 Model에 접근할 수 있다.

Model은 데이터베이스같은 비즈니스 로직을 처리하는 일을 수행하고 Controller로 다시 돌아온다.

Controller의 처리가 끝났다면 그 결과를 사용자들이 화면을 통해 볼 수 있도록 View로 이동시켜준다.

![img](https://media.vlpt.us/images/phjppo0918/post/95f1526c-4fea-406d-a7ec-a8a7acbd3d4c/image.png)

- 컨트롤러 : 웹 MVC의 컨트롤러 역할
- 서비스 : 핵심 비즈니스 로직 구현
- 도메인 : 비즈니스 도메인 객체(DTO)
- 리포지토리 : 데이터베이스에 접근, 도메인 객체를 DB에 저장하고 관리(DAO)



나는 이렇게 MVC를 구현하고 있다.

- 뷰 : 프론트엔드에서 담당한다. 요청을 보내고, 응답을 받아 이를 보여준다.(하지만 나는 프론트엔드 대신 swagger를 사용하여 뷰를 나타낸 거다.)
- 컨트롤러 : 컨트롤러에서 담당한다. 들어온 요청에 따라 서비스의 적절한 연산을 수행하고, 결괏값을 응답으로 가공하여 보내준다.  또한 인터셉터, 예외 핸들러, 컨트롤러 어드바이스 등 통신 과정에 영향을 주는 다른 클래스들을 포함한다.
- 모델 : 도메인 모델이 비즈니스 로직과 상태를 가지고 있는다. 서비스는 이러한 도메인 모델의 생성과 연산을 적절히 수행하며, 그 과정에서 트랜잭션을 보장한다.
- 기타 : DB에 저장하고 삭제하고 가져오고 생성하는 기능(이거는 따로 정리를 할 것이다.)

### DTO란 

DTO(Data Transfer Object)는 계층간 데이터 교환을 위한 객체(Java Beans)이다.

DB에서 데이터를 얻어 Service나 Controller 등으로부터 보낼 때 사용하는 객체를 말한다.

![스크린샷, 2021-09-28 07-03-10](/home/hyunguk/%EC%82%AC%EC%A7%84/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7,%202021-09-28%2007-03-10.png)

**HYUNGUK(DTO)**

```
package com.example.hyungukservice.domain;

import lombok.*;

import javax.persistence.Column;
import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.Id;

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

### Repository 구현

#### DAO란

실제로 DB에 접근하는 객체이다.

Persistence Layer(DB에 data를 CRUD하는 계층)이다.

Service와 DB를 연결하는 고리의 역할을 한다.

![스크린샷, 2021-09-28 07-07-55](/home/hyunguk/%EC%82%AC%EC%A7%84/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7,%202021-09-28%2007-07-55.png)

후에 repository 변동 용이성을 위해서 interface를 제작한다.

```
package com.example.hyungukservice.repository;

import com.example.hyungukservice.domain.HyunGuk;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;

@Repository
public interface HyunGukRepository extends JpaRepository<HyunGuk,Long> {
}

```

### Service 구현

1. 선한영향력 목록을 크롤링하면서 DB에 저장하기
2. DB에 저장된 목록을 이용하여 생성, 삭제, 중복 검사 등 기능 구현

```
package com.example.hyungukservice.service;

import com.example.hyungukservice.domain.HyunGuk;
import com.example.hyungukservice.repository.HyunGukRepository;
import org.jsoup.Jsoup;
import org.jsoup.nodes.Document;
import org.jsoup.nodes.Element;
import org.jsoup.select.Elements;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import javax.annotation.PostConstruct;
import java.io.IOException;
import java.util.ArrayList;
import java.util.List;

@Service
public class HyunGukService {
    @Autowired
    private HyunGukRepository hyunGukRepository;

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
            for (Element content : contents1){ // 반복문 사용으로 DB에 저장하
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
    public List<HyunGuk> getShop(){ // 모든 정보 출력하는 기능
        List<HyunGuk> list = hyunGukRepository.findAll();
        return list;
    }
    public String getShopName(String shop) throws IllegalAccessException{ // 가게 이름을 주면 있으면 가게 정보가 나오고 없으면 에러 표시를 한다.
        List<HyunGuk> list = hyunGukRepository.findAll();
        for (HyunGuk hyunGuk : list){
            if (hyunGuk.getShop().equals(shop)){
                return hyunGuk.getShop();
            }
        }throw new IllegalAccessException("error");
    }
    public Long getShopIdByShopName(String shop) throws IllegalAccessException { // 가게 이름을 주면 가게에 할당된 id를 주는 기능
        List<HyunGuk> list = hyunGukRepository.findAll();
        for(HyunGuk hyunGuk : list){
            if(hyunGuk.getShop().equals(shop)){
                return hyunGuk.getId();
            }
        }throw new IllegalAccessException("error");
    }
    public void AllDeleteShop(){ // 모든 정보 삭제하는 기능
        hyunGukRepository.deleteAll();
    }
    public void DeleteShopById(Long id){ // 가게에 해당된 id를 입력하면 id에 해당된 가게 정보는 전부 삭제하는 기능
        List<HyunGuk> list = hyunGukRepository.findAll();
        for(HyunGuk hyunGuk : list){
            if(hyunGuk.getId().equals(id));
            hyunGukRepository.deleteById(id);
        }
    }
    public boolean CheckShop(String shop){ // 가게 이름을 입력하여 있으면 true 없으면 false 나오는 기능
        List<HyunGuk> list = hyunGukRepository.findAll();
        for (HyunGuk hyunGuk : list){
            if (hyunGuk.getShop().equals(shop))
                return true;
        }
        return false;
    }
    public HyunGuk CreateShopImFormationCheckDuplication(String area, String shop, String address, String phone, String sponsor) throws IllegalAccessException { // 중복있는지 확인 하고 있으면 에러를 표시하고 없으면 생성하면서 DB에 저
        List<HyunGuk> list = hyunGukRepository.findAll();
        for (HyunGuk hyunGuk : list){
            if (hyunGuk.getArea().equals(area)){
                throw new IllegalAccessException("error");
            }else if (hyunGuk.getShop().equals(shop)){
                throw new IllegalAccessException("error");
            }else if (hyunGuk.getAddress().equals(address)){
                throw new IllegalAccessException("error");
            }else if (hyunGuk.getPhone().equals(phone)){
                throw new IllegalAccessException("error");
            }else if (hyunGuk.getShop().equals(sponsor)){
                throw new IllegalAccessException("error");
            }
        }HyunGuk hyunGuk = HyunGuk.builder()
                .area(area)
                .shop(shop)
                .address(address)
                .phone(phone)
                .sponsor(sponsor)
                .build();
        return hyunGukRepository.save(hyunGuk);
    }
}

```

### Controller 구현

1. @Controller

 \- Controller의 역할을 수행 한다고 명시(해당 클래스를 Controller로 사용한다고 Spring FrameWork에 알린다.)

  필요한 비즈니스 로직을 호출하여 전달할 모델(Model)과 이동할 뷰(View) 정보를 DispatherServlet에 반환 한다.

 \- Bean으로 등록

 \- @Component의 구체화 된 어노테이션

2. @RestController

@RestController는 Spring MVC Controlle에 @ResponseBody가 추가된 것입니다. 당연하게도 RestController의 주용도는 Json 형태로 객체 데이터를 반환하는 것입니다. 개인적으로는 VueJS + Spring boot 프로젝트를 진행하며 Spring boot를 API 서버로 활용할 때 또는 Android 앱 개발을 하면서 데이터를 반환할 때 사용하였습니다.

3. @RequiredArgsConstructor

이 어노테이션은 초기화 되지않은 final 필드나, @NonNull이 붙은 필드에 대해 생성자를 생성해 줍니다. 주로 의존성 주입(Dependency Injection) 편의성을 위해서 사용되곤 합니다.

4. @ResponseBody

메소드에 @ResponseBody 로 어노테이션이 되어 있다면 메소드에서 리턴되는 값은 View 를 통해서 

출력되지 않고 HTTP Response Body 에 직접 쓰여지게 됩니다.

이때 쓰여지기 전에 리턴되는 데이터 타입에 따라 MessageConverter 에서 변환이 이뤄진 후 쓰여지게 됩니다.

5. @RequestMapping(value = "/", method="...")의 어노테이션의 사용을 조금 더 명시적고 효율적인 방법으로 사용하기 위해 @GetMapping, @PostMapping, DeleteMapping, @PutMapping, @PatchMapping의 메소드가 생기게  되었습니다.

```
package com.example.hyungukservice.controller;


import com.example.hyungukservice.domain.HyunGuk;
import com.example.hyungukservice.service.HyunGukService;
import lombok.RequiredArgsConstructor;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.*;

import java.util.List;

@Controller
@RestController
@RequiredArgsConstructor
public class HyunGukController {
    @Autowired
    private HyunGukService hyunGukService;

    @GetMapping("getShop") // 모든 가게 정보 가져오기
    @ResponseBody
    public List<HyunGuk> getShop(){
        return hyunGukService.getShop();
    }
    @GetMapping("getShopIdByShopName") // 가게이름을 주면 해당 id를 준다
    @ResponseBody
    public Long getShopIdByShopName(String shop) throws IllegalAccessException {
       return hyunGukService.getShopIdByShopName(shop);
    }
    @DeleteMapping("AllDeleteShop") // 모든 가게 정보를 삭제한다
    @ResponseBody
    public void AllDeleteShop(){
        hyunGukService.AllDeleteShop();
    }
    @DeleteMapping("DeleteShopById") // id를 주면 id에 해당하는 가게 정보를 전부 삭제
    @ResponseBody
    public void DeleteShopById(Long id){
        hyunGukService.DeleteShopById(id);
    }
    @GetMapping("CheckShop") // 가게가 있는지 없는지 확인
    @ResponseBody
    public boolean CheckShop(String shop){
        return hyunGukService.CheckShop(shop);
    }
    @PostMapping("CreateShopImFormationCheckDuplication") // 중복 가게가 있는지 확인하고 있으면 에러 없으면 생성
    @ResponseBody
    public HyunGuk CreateShopImFormationCheckDuplication(String area, String shop, String address, String phone, String sponsor) throws IllegalAccessException {
        return hyunGukService.CreateShopImFormationCheckDuplication(area,shop,address,phone,sponsor);
    }
    @GetMapping("getShopName") // 가게 이름을 주면 그 가게 해당하는 정보를 가져온
    @ResponseBody
    public String getShopName(String shop) throws IllegalAccessException {
        return hyunGukService.getShopName(shop);
    }
}

```

### swagger

​    Swagger(스웨거)는 개발자가 REST API 서비스를 설계, 빌드, 문서화할 수 있도록 하는 프로젝트이다.(Open Api Specification(OAS)를 위한 프레임워크 입니다)

#### Gradle dependency 추가

implementation group: 'io.springfox', name: 'springfox-swagger2', version: '2.9.2'

implementation group: 'io.springfox', name: 'springfox-swagger-ui', version: '2.9.2'

#### Swagger UI 환경 설정 파일 추가

```
package com.example.hyungukservice;

import io.swagger.annotations.SwaggerDefinition;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.Configuration;
import springfox.documentation.swagger2.annotations.EnableSwagger2;

@SwaggerDefinition
@EnableSwagger2
@Configuration
@SpringBootApplication
public class HyunGukServiceApplication {

    public static void main(String[] args) {
        SpringApplication.run(HyunGukServiceApplication.class, args);
    }

}
```

![스크린샷, 2021-09-28 07-17-40](/home/hyunguk/%EC%82%AC%EC%A7%84/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7,%202021-09-28%2007-17-40.png)

이렇게 MVC 패턴 프로젝트를 마침겠습니다.