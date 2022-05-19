# SpringBootBoard

* 프로젝트 기본 구조
  * 브라우저에서 들어오는 Request는 GuestbookController라는 객체로 처리
  * GuestbookRepository는 Spring Data JPA를 이용해서 구성, GuestbookServicempl클래스에 주입
  * 마지막 결과는 Thymeleaf를 이용해서 레이아웃 템플릿 활용 처리
  * 브라우저에서 전달되는 Request는 GuestbookController에서 DTO 형태로 처리
  * GuestbookRepository는 엔티티 타입을 이용하므로 중간에 Service 계층에서는 DTO와 엔티티 변환 처리


  * MariaDB Java Client 다운로드 - MVNrepository.com 접속 - 2.7.1 Gradle 태그 복사
![image](https://user-images.githubusercontent.com/86938974/168707594-97eedceb-ec83-4051-b7de-02bd966d8705.png)

![image](https://user-images.githubusercontent.com/86938974/168707822-f9e5cd7a-a1a9-4afe-b2c4-ad14cac7e414.png)

- build.gradle의 dependencies에 코드 추가
```
dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-data-jpa'
    implementation 'org.springframework.boot:spring-boot-starter-thymeleaf'
    implementation 'org.springframework.boot:spring-boot-starter-web'
    compileOnly 'org.projectlombok:lombok'
    developmentOnly 'org.springframework.boot:spring-boot-devtools'
    annotationProcessor 'org.projectlombok:lombok'
    providedRuntime 'org.springframework.boot:spring-boot-starter-tomcat'
    testImplementation 'org.springframework.boot:spring-boot-starter-test'
    // https://mvnrepository.com/artifact/org.mariadb.jdbc/mariadb-java-client
    implementation group: 'org.mariadb.jdbc', name: 'mariadb-java-client', version: '2.7.1'

}
```
- 포트번호 수정과 DB 연결(application.properties)
- MariaDB관련 JDBC 드라이버 추가, JPA와 관련된 설정 추가
```
server.port=8181
spring.datasource.driver-class-name=org.mariadb.jdbc.Driver
spring.datasource.url=jdbc:mariadb://localhost:3306/bootex
spring.datasource.username=bootuser
spring.datasource.password=bootuser

spring.jpa.hibernate.ddl-auto=update
spring.jpa.properties.hibernate.format_sql=true
spring.jpa.show-sql=true

spring.thymeleaf.cache=false
```
- java8time 3.0.4 다운
![image](https://user-images.githubusercontent.com/86938974/168710402-f733b8b2-cb9c-4814-8acb-2775d4291b92.png)

- build.gradle에 dependencies에 추가
```
dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-data-jpa'
    implementation 'org.springframework.boot:spring-boot-starter-thymeleaf'
    implementation 'org.springframework.boot:spring-boot-starter-web'
    compileOnly 'org.projectlombok:lombok'
    developmentOnly 'org.springframework.boot:spring-boot-devtools'
    annotationProcessor 'org.projectlombok:lombok'
    providedRuntime 'org.springframework.boot:spring-boot-starter-tomcat'
    testImplementation 'org.springframework.boot:spring-boot-starter-test'
    // https://mvnrepository.com/artifact/org.mariadb.jdbc/mariadb-java-client
    implementation group: 'org.mariadb.jdbc', name: 'mariadb-java-client', version: '2.7.1'
    // https://mvnrepository.com/artifact/org.thymeleaf.extras/thymeleaf-extras-java8time
    implementation group: 'org.thymeleaf.extras', name: 'thymeleaf-extras-java8time', version: '3.0.4.RELEASE'

}
```

* 컨트롤러/화면 관련 준비
![image](https://user-images.githubusercontent.com/86938974/168710851-0e5806f8-1566-40ca-b23f-4749145b570e.png)

  * thymeleaf 파일이 들어감 -> templates
![image](https://user-images.githubusercontent.com/86938974/168711040-803a108b-c495-41b1-b983-661c9c007e3f.png)

![image](https://user-images.githubusercontent.com/86938974/168712646-f9e5ccae-0311-4594-909f-a07927fcb228.png)

  * GuestbookController.java
```
package org.zerock.guestbook.controller;

import lombok.extern.log4j.Log4j2;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;

@Controller
@RequestMapping("/guestbook")
@Log4j2
public class GuestbookController {
    //목록
    @GetMapping({"/", "/list"})
    public String list(){
        // void라고 쓰면 GetMapping 주소와 같은 html을 찾아서 가지만,
        // 없으므로 다른 파일명일때 그 곳으로 가기위해 사용하는 문법
        return "/guestbook/list";
    }
}

```
  * guestbook -> list.html 생성
```
<!DOCTYPE html>
<html lang="en" xmlns:th = "http://www.thymeleaf.org">

<th:block th:replace="~{layout/basic :: setContent(~{this::content})}">
    
    <th:block th:fragment="content">
        
        <h1>GuestBook List Page</h1>    
        
    </th:block>
    
</th:block>
```

  * layout/basic.html 코드
```
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<th:block th:fragment="setContent(content)"></th:block>
<head>

  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
  <meta name="description" content="">
  <meta name="author" content="">

  <title>Simple Sidebar - Start Bootstrap Template</title>

  <!-- Bootstrap core CSS -->
  <link th:href="@{vendor/bootstrap/css/bootstrap.min.css}" rel="stylesheet">

  <!-- Custom styles for this template -->
  <link th:href="@{/css/simple-sidebar.css}" rel="stylesheet">

</head>

<body>

<div class="d-flex" id="wrapper">

  <!-- Sidebar -->
  <div class="bg-light border-right" id="sidebar-wrapper">
    <div class="sidebar-heading">Start Bootstrap </div>
    <div class="list-group list-group-flush">
      <a href="#" class="list-group-item list-group-item-action bg-light">Dashboard</a>
      <a href="#" class="list-group-item list-group-item-action bg-light">Shortcuts</a>
      <a href="#" class="list-group-item list-group-item-action bg-light">Overview</a>
      <a href="#" class="list-group-item list-group-item-action bg-light">Events</a>
      <a href="#" class="list-group-item list-group-item-action bg-light">Profile</a>
      <a href="#" class="list-group-item list-group-item-action bg-light">Status</a>
    </div>
  </div>
  <!-- /#sidebar-wrapper -->

  <!-- Page Content -->
  <div id="page-content-wrapper">

    <nav class="navbar navbar-expand-lg navbar-light bg-light border-bottom">
      <button class="btn btn-primary" id="menu-toggle">Toggle Menu</button>

      <button class="navbar-toggler" type="button" data-toggle="collapse" data-target="#navbarSupportedContent" aria-controls="navbarSupportedContent" aria-expanded="false" aria-label="Toggle navigation">
        <span class="navbar-toggler-icon"></span>
      </button>

      <div class="collapse navbar-collapse" id="navbarSupportedContent">
        <ul class="navbar-nav ml-auto mt-2 mt-lg-0">
          <li class="nav-item active">
            <a class="nav-link" href="#">Home <span class="sr-only">(current)</span></a>
          </li>
          <li class="nav-item">
            <a class="nav-link" href="#">Link</a>
          </li>
          <li class="nav-item dropdown">
            <a class="nav-link dropdown-toggle" href="#" id="navbarDropdown" role="button" data-toggle="dropdown" aria-haspopup="true" aria-expanded="false">
              Dropdown
            </a>
            <div class="dropdown-menu dropdown-menu-right" aria-labelledby="navbarDropdown">
              <a class="dropdown-item" href="#">Action</a>
              <a class="dropdown-item" href="#">Another action</a>
              <div class="dropdown-divider"></div>
              <a class="dropdown-item" href="#">Something else here</a>
            </div>
          </li>
        </ul>
      </div>
    </nav>

    <div class="container-fluid">
      <th:block th:replace = "${content}"></th:block>
    </div>
  </div>
  <!-- /#page-content-wrapper -->

</div>
<!-- /#wrapper -->

<!-- Bootstrap core JavaScript -->
<script th:src="@{/vendor/jquery/jquery.min.js}"></script>
<script th:src="@{/vendor/bootstrap/js/bootstrap.bundle.min.js}"></script>

<!-- Menu Toggle Script -->
<script>
  $("#menu-toggle").click(function(e) {
    e.preventDefault();
    $("#wrapper").toggleClass("toggled");
  });
</script>

</body>

</html>

```

  * GuestbookApplication
```
@SpringBootApplication
@EnableJpaAuditing
public class GuestbookApplication {

    public static void main(String[] args) {
        SpringApplication.run(GuestbookApplication.class, args);
    }

}
```
- 첫 화면
![image](https://user-images.githubusercontent.com/86938974/168716907-f42b5310-7b19-4df7-90a7-b6c3d767635e.png)

![image](https://user-images.githubusercontent.com/86938974/168716835-2bd70dcf-0159-4451-888c-b44902629d61.png)

  * 자동으로 처리되는 날짜/시간 설정
- 시간 처리를 위한 BaseEntity 클래스
- 엔티티 객체의 등록 시간과 최종 수정 시간 담당

![image](https://user-images.githubusercontent.com/86938974/168718748-5c2dacbd-0fd7-4041-afff-ef800842d30d.png)

```
package org.zerock.guestbook.entity;

import lombok.Getter;
import org.springframework.data.annotation.CreatedDate;
import org.springframework.data.annotation.LastModifiedDate;
import org.springframework.data.jpa.domain.support.AuditingEntityListener;

import javax.persistence.Column;
import javax.persistence.EntityListeners;
import javax.persistence.MappedSuperclass;
import java.time.LocalDateTime;

@MappedSuperclass // 테이블로 생성되지 않는다.
@EntityListeners(value = {AuditingEntityListener.class}) // 엔티티를 updating할 때 정보를 캐치한다.
@Getter
abstract class BaseEntity {

    @CreatedDate // 처음 등록된 시간
    @Column(name = "regdate" , updatable = false)
    private LocalDateTime regDate; // 작성일

    @LastModifiedDate // 최종 수정시간
    @Column(name="moddate")
    private LocalDateTime modDate; // 수정시 마다 업데이트됨

}


```
- @MappedSuperClass는 테이블로 생성되지 않는다.
- 실제 테이블은 BaseEntity 클래스를 상속한 엔티티의 클래스로 데이터베이스 테이블이 생성된다.
- JPA내부에서 엔티티 객체가 생성/변경되는 것을 감지하는 역할은 AuditingEntityListener로 이루어진다. 이를 통해서 regDate, modDate에 적절한 값이 지정된다.
- @CreateDate는 JPA에서 엔티티의 생성 시간을 처리하고 @LastModifiedDate는 최종 수정 시간을 자동으로 처리한다.

- JPA를 이용하면서 AuditingEntityListener를 활성화시키기 위해서는 프로젝트에 @EnableJpaAuditing 설정 추가
```
@SpringBootApplication
@EnableJpaAuditing
public class GuestbookApplication {

    public static void main(String[] args) {
        SpringApplication.run(GuestbookApplication.class, args);
    }

}
```

  * 엔티티 클래스와 Querydsl 설정
- Guestbook 클래스

```
package org.zerock.guestbook.entity;

import lombok.*;

import javax.persistence.*;

@Entity
@Getter
@Builder
@AllArgsConstructor
@NoArgsConstructor
@ToString
public class GuestBook extends BaseEntity {
    
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long gno;
    
    @Column(length = 100, nullable = false)
    private String title;
    
    @Column(length = 1500, nullable = false)
    private String content;
    
    @Column(length = 50, nullable = false)
    private String writer;
}

```
- 생성 확인
![image](https://user-images.githubusercontent.com/86938974/168720392-7b19a4f8-e749-4244-8746-8bf88c3a1e5f.png)

![image](https://user-images.githubusercontent.com/86938974/168720647-b100b65b-56e8-407a-a238-becfaeec5011.png)

- GuestbookRepository 인터페이스 생성 (CRUD 담당)
```
package org.zerock.guestbook.respository;

import org.springframework.data.jpa.repository.JpaRepository;
import org.zerock.guestbook.entity.Guestbook;

public interface GuestbookRepository extends JpaRepository<Guestbook, Long> {
}

```
- https://mvnrepository.com/artifact/com.querydsl/querydsl-jpa 접속
- Querydsl JPA Support 4.3.1 복사 -> build.gradle파일의 dependencies 안에 코드 추가
![image](https://user-images.githubusercontent.com/86938974/168729990-2cf544ea-8f34-441d-924e-051186dc4c87.png)


```
plugins {
    id 'org.springframework.boot' version '2.6.7'
    id 'io.spring.dependency-management' version '1.0.11.RELEASE'
    id 'java'
    id 'war'
    id 'com.ewerk.gradle.plugins.querydsl' version '1.0.10'
}

group = 'org.zerock'
version = '0.0.1-SNAPSHOT'
sourceCompatibility = '11'

configurations {
    compileOnly {
        extendsFrom annotationProcessor
    }
}

repositories {
    mavenCentral()
}

dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-data-jpa'
    implementation 'org.springframework.boot:spring-boot-starter-thymeleaf'
    implementation 'org.springframework.boot:spring-boot-starter-web'
    compileOnly 'org.projectlombok:lombok'
    developmentOnly 'org.springframework.boot:spring-boot-devtools'
    annotationProcessor 'org.projectlombok:lombok'
    providedRuntime 'org.springframework.boot:spring-boot-starter-tomcat'
    testImplementation 'org.springframework.boot:spring-boot-starter-test'
    // https://mvnrepository.com/artifact/org.mariadb.jdbc/mariadb-java-client
    implementation group: 'org.mariadb.jdbc', name: 'mariadb-java-client', version: '2.7.1'
    // https://mvnrepository.com/artifact/org.thymeleaf.extras/thymeleaf-extras-java8time
    implementation group: 'org.thymeleaf.extras', name: 'thymeleaf-extras-java8time', version: '3.0.4.RELEASE'
    // https://mvnrepository.com/artifact/com.querydsl/querydsl-jpa
    implementation group: 'com.querydsl', name: 'querydsl-jpa', version: '5.0.0'
    // https://mvnrepository.com/artifact/com.querydsl/querydsl-apt
    implementation group: 'com.querydsl', name: 'querydsl-apt', version: '5.0.0'



}

tasks.named('test') {
    useJUnitPlatform()
}

def querydslDir = "$buildDir/generated/querydsl"

querydsl {
    jpa = true
    querydslSourcesDir = querydslDir
}

sourceSets {
    main.java.srcDir querydslDir
}

configurations {
    compileOnly {
        extendsFrom annotationProcessor
    }
    querydsl.extendsFrom compileClasspath
}

compileQuerydsl {
    options.annotationProcessorPath = configurations.querydsl
}

```
- 아래 폴더 자동 생성
![image](https://user-images.githubusercontent.com/86938974/168734560-186b3deb-c4ea-4908-a8c9-7135c587e5a6.png)


 * 엔티티의 테스트

- Guestbook에 다음 코드 추가
```
public void changeTitle(String title){
        this.title = title;
    }

    public void changeContent(String content){
        this.content = content;
    }
```

- GuestbookRepositoryTests 생성
![image](https://user-images.githubusercontent.com/86938974/168736191-f80d374e-47f2-402e-9e66-018bfb5cc77c.png)

```
package org.zerock.guestbook.repository;

import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.zerock.guestbook.entity.Guestbook;
import org.zerock.guestbook.respository.GuestbookRepository;

import java.util.Optional;
import java.util.stream.IntStream;

@SpringBootTest
public class GuestbookRepositoryTests {

    @Autowired
    private GuestbookRepository guestbookRepository;

    @Test
    public void insertDummies(){

        IntStream.rangeClosed(1, 300).forEach(i->{
            Guestbook guestbook = Guestbook.builder()
                    .title("Title..."+i)
                    .content("Content..."+i)
                    .writer("user"+(i%10))
                    .build();
            System.out.println(guestbookRepository.save(guestbook));
        });
    }

    @Test
    public void updateTest(){
        Optional<Guestbook> result = guestbookRepository.findById(300L);

        if(result.isPresent()){
            Guestbook guestbook = result.get();
            guestbook.changeTitle("Change Title....");
            guestbook.changeContent("Change Content....");
            guestbookRepository.save(guestbook);
        }
    }
}

```
- 실행 결과
![image](https://user-images.githubusercontent.com/86938974/168737190-740c12ff-dd0d-4b01-871b-54ee297670a2.png)
- 변경 확인
![image](https://user-images.githubusercontent.com/86938974/168738685-6e2c3063-8fd2-4033-a39c-22c48cc8057c.png)

 * Querydsl 테스트

- /제목/내용/작성자 같이 단 하나의 항목 검색
- '제목+내용'/'내용+작성자'/'제목+작성자'와 같이 2개의 항목 검색
- 제목+내용+작성자와 같이 3개의 항목 검색
- 이런 상황을 대비해서 상황에 맞게 쿼리를 처리하는 Querydsl이 필요

```
package org.zerock.guestbook.repository;

import com.querydsl.core.BooleanBuilder;
import com.querydsl.core.types.dsl.BooleanExpression;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.data.domain.Page;
import org.springframework.data.domain.PageRequest;
import org.springframework.data.domain.Pageable;
import org.springframework.data.domain.Sort;
import org.zerock.guestbook.entity.Guestbook;
import org.zerock.guestbook.entity.QGuestbook;
import org.zerock.guestbook.respository.GuestbookRepository;


import java.util.Optional;
import java.util.stream.IntStream;

@SpringBootTest
public class GuestbookRepositoryTests {

    @Autowired
    private GuestbookRepository guestbookRepository;

    @Test
    public void insertDummies(){

        IntStream.rangeClosed(1, 300).forEach(i->{
            Guestbook guestbook = Guestbook.builder()
                    .title("Title..."+i)
                    .content("Content..."+i)
                    .writer("user"+(i%10))
                    .build();
            System.out.println(guestbookRepository.save(guestbook));
        });
    }

    @Test
    public void updateTest(){
        Optional<Guestbook> result = guestbookRepository.findById(300L);

        if(result.isPresent()){
            Guestbook guestbook = result.get();
            guestbook.changeTitle("Change Title....");
            guestbook.changeContent("Change Content....");
            guestbookRepository.save(guestbook);
        }
    }
    @Test
    public void testQuery1(){
        Pageable pageable = (Pageable) PageRequest.of(0, 10, Sort.by("gno").descending());

        QGuestbook qGuestbook = QGuestbook.guestbook; //1
        String keyword = "1";
        BooleanBuilder builder = new BooleanBuilder(); // 2
        BooleanExpression expression = qGuestbook.title.contains(keyword); 
        builder.and(expression);
        Page<Guestbook> result = guestbookRepository.findAll(builder, pageable);

        result.stream().forEach(guestbook -> {
            System.out.println(guestbook);
        });
    }
}
@Test
    public void testQuery2(){
        Pageable pageable = PageRequest.of(0, 10, Sort.by("gno").descending());
        QGuestbook qGuestbook = QGuestbook.guestbook;
        String keyword="1";
        BooleanBuilder builder = new BooleanBuilder();
        BooleanExpression exTitle = qGuestbook.title.contains(keyword);
        BooleanExpression exContent = qGuestbook.content.contains(keyword);
        BooleanExpression exAll = exTitle.or(exContent);
        builder.and(exAll);
        builder.and(qGuestbook.gno.gt(0L));
        Page<Guestbook> result = guestbookRepository.findAll(builder, pageable);
        
        result.stream().forEach(guestbook -> {
            System.out.println(guestbook);
        });
        
    }
```
- 이를 통해 페이지 처리와 동시에 검색 처리 가능
![image](https://user-images.githubusercontent.com/86938974/168746082-2092d295-44e9-488f-a18b-468ee85fd04f.png)

 * DTO 생성
- GuestbookDTO 클래스
![image](https://user-images.githubusercontent.com/86938974/168934613-97c845f4-8f7f-4df0-bf83-3816989bb37a.png)


```
package org.zerock.guestbook.dto;


import lombok.AllArgsConstructor;
import lombok.Builder;
import lombok.Data;
import lombok.NoArgsConstructor;

import java.time.LocalDateTime;

@Builder
@NoArgsConstructor
@AllArgsConstructor
@Data
public class GuestbookDTO {
    
    private Long gno;
    private String title;
    private String content;
    private String writer;
    private LocalDateTime regDate, modDate;
    
}

```
- 서비스 계층에서는 GuestbookDTO를 이용해서 필요한 내용을 전달받고, 반환하도록 처리하는데 GuestbookService 인터페이스와 GuestbookServiceImpl 클래스를 작성한다.

 * 등록과 DTO를 엔티티로 변환

- 서비스 계층에서는 파라미터를 DTO타입으로 받기 때문에 이를 JPA로 처리하기 위해서는 엔티티 타입의 객체로 변환해야하는 작업이 반드시 필요하다.

- GuestbookService 인터페이스(service 패키지 생성)
![image](https://user-images.githubusercontent.com/86938974/168937280-3f923afc-ce2f-4e80-b28f-f2b4ef997453.png)

```
package org.zerock.guestbook.service;

import org.zerock.guestbook.dto.GuestbookDTO;
import org.zerock.guestbook.entity.Guestbook;

public interface GuestbookService {
    Long register(GuestbookDTO dto);

    default Guestbook dtoToEntity(GuestbookDTO dto){
        Guestbook entity = Guestbook.builder()
                .gno(dto.getGno())
                .title(dto.getTitle())
                .content(dto.getContent())
                .writer(dto.getWriter())
                .build();
        return entity;
    }
}

```

- GuestbookServiceImpl 클래스에는 스프링에서 빈으로 처리되도록 @Service 어노테이션 추가

```
package org.zerock.guestbook.service;


import lombok.RequiredArgsConstructor;
import lombok.extern.log4j.Log4j2;
import org.springframework.stereotype.Service;
import org.zerock.guestbook.dto.GuestbookDTO;
import org.zerock.guestbook.entity.Guestbook;
import org.zerock.guestbook.respository.GuestbookRepository;


@Service
@Log4j2
@RequiredArgsConstructor // final이나 @nonNull이 붙어있는 필드에 대해 생성자를 만들어줌
public class GuestbookServiceImpl implements GuestbookService {

    private final GuestbookRepository repository; // 반드시 final로 선언, @Autowired 생략 가능
    
    @Override
    public Long register(GuestbookDTO dto){
        
        log.info("DTO-----------------------");
        log.info(dto);
        
        Guestbook entity = dtoToEntity(dto);
        
        log.info(entity);
        
        repository.save(entity);
        
        return entity.getGno();
    }
}

```
- GuestbookServiceImpl 클래스는 JPA처리를 위해서 GuestbookRepository를 주입하고 클래스 선언 시에 @RequiredArgsConstructor를 이용해서 자동으로 주입한다.
- register의 내부에서는 save()를 통해 저장하고, 저장된 후에 해당 엔티티가 가지는 gno값을 반환한다.
- DTO객체는 Entity로 변환해서 사용하므로 dtoToEntity 메서드를 만들었다.

 * 목록 처리
- PageRequestDTO 클래스
```
package org.zerock.guestbook.dto;

import lombok.AllArgsConstructor;
import lombok.Builder;
import lombok.Data;
import org.springframework.data.domain.PageRequest;
import org.springframework.data.domain.Pageable;
import org.springframework.data.domain.Sort;

@Builder
@AllArgsConstructor
@Data
public class PageRequestDTO {

    private int page;
    private int size;

    public PageRequestDTO(){
        this.page = 1;
        this.size = 10;
    }
    public Pageable getPageable(Sort sort){
        return PageRequest.of(page-1, size, sort);
    }
}
```
- PageRequestDTO는 화면에서 전달되는 page라는 파라미터와 size라는 파라미터를 수집하는 역할
- JPA를 이용하는 경우에는 페이지 번호가 0부터 시작한다는 점을 감안해서 1페이지의 경우 0이 될 수 있도록 page-1을 하는 형태로 작성
- 정렬은 나중에 다양한 상황에서 쓰기 위해 별도의 파라미터를 받는다.
 
 * 페이지 결과 처리 DTO
- Page<Entity>의 엔티티 객체들을 DTO객체로 변환해서 자료구조로 담는다.
 
- PageResultDTO 클래스

 ```
 package org.zerock.guestbook.dto;

import lombok.Data;
import org.springframework.data.domain.Page;

import java.util.List;
import java.util.function.Function;
import java.util.stream.Collectors;

@Data
public class PageResultDTO<DTO, EN> {

    private List<DTO> dtoList;

    public PageResultDTO(Page<EN> result, Function<EN, DTO> fn){
        //목록생성. dtoList에 저장
        //map()함수에서 사용할 함수를 fn으로 지정
        dtoList = result.stream().map(fn).collect(Collectors.toList());
        //map함수 사용법
        //배열or컬렉션or페이지.stream().map(화살표함수).collect(Collectors.toList())
        //스트림은 하나씩 꺼내줌, 즉 result(Page<Guestbook>)에서 하나씩 꺼내어 map(fn)의 fn람다식을 하나씩 실행한다.
        
   }
}

 ```
 * 서비스 계층에서는 목록 처리
- GuestbookService 인터페이스의 추상메소드 하나 추가
 ```
 PageResultDTO<GuestbookDTO, Guestbook> getList(PageRequestDTO);
 
 default GuestbookDTO entityToDto(Guestbook entity){
        GuestbookDTO dto = GuestbookDTO.builder()
                .gno(entity.getGno())
                .title(entity.getTitle())
                .content(entity.getContent())
                .writer(entity.getWriter())
                .regDate(entity.getRegDate())
                .modDate(entity.getModDate())
                .build();

        return dto;
    }
 ```
 - GuestbookServiceImpl에서 Override
 ```
 package org.zerock.guestbook.service;


import lombok.RequiredArgsConstructor;
import lombok.extern.log4j.Log4j2;
import org.springframework.data.domain.Page;
import org.springframework.data.domain.Pageable;
import org.springframework.data.domain.Sort;
import org.springframework.stereotype.Service;
import org.zerock.guestbook.dto.GuestbookDTO;
import org.zerock.guestbook.dto.PageRequestDTO;
import org.zerock.guestbook.dto.PageResultDTO;
import org.zerock.guestbook.entity.Guestbook;
import org.zerock.guestbook.respository.GuestbookRepository;

import java.util.function.Function;


@Service
@Log4j2
@RequiredArgsConstructor // final이나 @nonNull이 붙어있는 필드에 대해 생성자를 만들어줌
public class GuestbookServiceImpl implements GuestbookService {

    private final GuestbookRepository repository; // 반드시 final로 선언, @Autowired 생략 가능

    @Override
    public Long register(GuestbookDTO dto) {

        log.info("DTO-----------------------");
        log.info(dto);

        Guestbook entity = dtoToEntity(dto);

        log.info(entity);

        repository.save(entity);

        return entity.getGno(); // 글번호 리턴
    }

    @Override
    public PageResultDTO<GuestbookDTO, Guestbook> getList(PageRequestDTO requestDTO) {
        Pageable pageable = requestDTO.getPageable(Sort.by("gno").descending());
        Page<Guestbook> result = repository.findAll(pageable);
        //PageResultDTO생성자의 map()함수에서
        Function<Guestbook, GuestbookDTO> fn = (entity -> entityToDto(entity));

        return new PageResultDTO<>(result, fn);
    }
}

 ```
- 목록 데이터 페이지 처리 (PageResultDTO 클래스)
```
 package org.zerock.guestbook.dto;

import lombok.Data;
import org.springframework.data.domain.Page;
import org.springframework.data.domain.Pageable;

import java.util.List;
import java.util.function.Function;
import java.util.stream.Collectors;
import java.util.stream.IntStream;

@Data
public class PageResultDTO<DTO, EN> {

    private List<DTO> dtoList; //글목록을 저장하는 List
    private int totalPage; //총 페이지수
    private int page; //현재 페이지
    private int size; //페이지당 보여지는 글 수
    private int start; //1.......10에서 시작 번호
    private int end; //1.......10 에서 끝 번호
    private boolean prev; //이전블록 유무 여부
    private boolean next; //다음블록 유무 여부
    private List<Integer> pageList; //1.......10 번호 목록

    public PageResultDTO(Page<EN> result, Function<EN, DTO> fn){
        //목록생성. dtoList에 저장
        //map()함수에서 사용할 함수를 fn으로 지정
        dtoList = result.stream().map(fn).collect(Collectors.toList());
        //map함수 사용법
        //배열or컬렉션or페이지.stream().map(화살표함수).collect(Collectors.toList())
        //스트림은 하나씩 꺼내줌, 즉 result(Page<Guestbook>)에서 하나씩 꺼내어 map(fn)의 fn람다식을 하나씩 실행한다.

        totalPage = result.getTotalPages();
        makePageList(result.getPageable());
   }
   //paging에 관련된 필드들의 값을 구해서 저장
    private void makePageList(Pageable pageable){
        this.page = pageable.getPageNumber() + 1; //페이지번호, 0부터 시작하므로 1을 추가
        this.size = pageable.getPageSize(); // 페이지당 글 수

        //temp and page
        int tempEnd = (int)(Math.ceil(page/10.0))*10; // 1......10에서 10. 실제 페이지수가 tempEnd보다 작으면 실제 페이지수가 End
        start = tempEnd-9; // 끝번호(10)에서 9를 빼면 처음 번호
        prev = start>1; // 시작번호가 1보다 크면 최소 두번째 구간이므로 이전구간 존재
        end = totalPage > tempEnd ? tempEnd:totalPage; //총페이지수가 현재구간의 tempEnd보다 작으면 총페이지수가 End가 됨
        next = totalPage > tempEnd; // 총페이지수가 현재구간의 tempEnd보다 크면 다음 구간 존재

        pageList = IntStream.rangeClosed(start, end).boxed().collect(Collectors.toList()); // 1....10까지의 수를 List에 저장
    }
}
 
```
 * 컨트롤러와 화면에서의 목록 처리
- GuestbookController에 추가 
```
@RequiredArgsConstructor
public class GuestbookController {
 private final GuestbookService service;
 @GetMapping("/list")
    public void list(PageRequestDTO pageRequestDTO, Model model){
        log.info("list..........."+pageRequestDTO);
        model.addAttribute("result", service.getList(pageRequestDTO));
    }
}
```
- 스프링 MVC는 파라미터를 자동으로 수집해주는 기능이 있으므로, 화면에서 page와 size라는 파라미터를 전달하면 PageRequestDTO객체로 자동으로 수집된다. 

- list.html 목록 출력
```
<!DOCTYPE html>
<html lang="en" xmlns:th = "http://www.thymeleaf.org">

<th:block th:replace="~{/layout/basic :: setContent(~{this::content})}">

    <th:block th:fragment="content">

        <h1>GuestBook List Page</h1>

        <table class = "table table-striped">
            <thead>
            <tr>
                <th scope="col">#</th>
                <th scope="col">Title</th>
                <th scope="col">Writer</th>
                <th scope="col">Regdate</th>
            </tr>
            </thead>
            <tbody>
            <tr th:each = "dto : ${result.dtoList}">
                <th scope="row">[[${dto.gno}]]</th>
                <td>[[${dto.title}]]</td>
                <td>[[${dto.writer}]]</td>
                <td>[[${#temporals.format(dto.regDate, 'yyyy/MM/dd')}]]</td>
            </tr>
            </tbody>
        </table>
    </th:block>
</th:block>
```
- th:each를 이용해서 PageResultDTO안에 들어있는 dtoList를 반복 처리
![image](https://user-images.githubusercontent.com/86938974/169181753-cc21a4e6-f1fe-47da-b910-80fbcce2c385.png)


 * 목록 페이지 처리
- /guestbook/list 혹은 /guestbook/list?page=1의 경우 1페이지 출력
- list.html 하단 페이지 처리
```
<ul class="pagination h-100 justify-content-center align-items-center">
            <li class="page-item" th:if="${result.prev}">
                <a class="page-link" th:href="@{/guestbook/list(page=${result.start - 1})}" tabindex="-1">Previous</a>
            </li>

            <li th:class=" 'page-item' + ${result.page==page?'active':''}" th:each="page : ${result.pageList}">
                <a class="page-link" th:href="@{/guestbook/list(page=${page})}">[[${page}]]</a>
            </li>

            <li class="page-item" th:if="${result.next}">
                <a class="page-link" th:href="@{/guestbook/list(page=${result.end+1})}">Next</a>
            </li>
        </ul>
```
- 1페이지
![image](https://user-images.githubusercontent.com/86938974/169187667-4ccfb35f-28ab-4014-8b39-d6d3d9931349.png)
- Next 클릭(맨 뒤)
![image](https://user-images.githubusercontent.com/86938974/169187752-a750a1c7-85db-4ef5-b476-22a445ef856f.png)
- Previous 클릭
![image](https://user-images.githubusercontent.com/86938974/169187764-b11e1c3b-b341-4058-9788-d7e2ab7cde45.png)

* 등록 페이지와 등록 처리
      * GuestbookController 클래스
 ```
 @GetMapping("/register")
    public void register(){
        log.info("register get....");
    }

    @PostMapping("/register")
    public String registerPost(GuestbookDTO dto, RedirectAttributes redirectAttributes){
        //insert 후 등록된 글번호 저장
        Long gno = service.register(dto); // 폼에 입력된 값이 dto에 자동 수집됨
        // msg라는 이름으로 뷰에 전달
        redirectAttributes.addFlashAttribute("msg", gno);
        return "redirect:/guestbook/list"; //목록으로 이동
    }
 ```
- Spring에서는 View에 입력한 값이 Controller에 전달되면 dto에 자동수집된다.

       * register.html 생성
 ```
 <!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<th:block th:replace="~{/layout/basic :: setContent(~{this::content})}">
    <th:block th:fragment="content">
        <h1 class="mt-4">GuestBook Register Page</h1>

        <form th:action="@{/guestbook/register}" th:method="post">
            <div class="form-group">
                <label>Title</label>
                <input type = "text" class="form-control" name="title" placeholder="Enter Title">
            </div>
            <div class="form-group">
                <label>Content</label>
                <textarea class="form-control" rows="5" name="content"></textarea>
            </div>
            <div class="form-group">
                <label>Writer</label>
                <input type="text" class="form-control" name="writer" placeholder="Enter Writer">
            </div>

            <button type="submit" class="btn btn-primary">Submit</button>
        </form>
    </th:block>
</th:block>
 ```
- 글쓰기 화면
 ![image](https://user-images.githubusercontent.com/86938974/169192990-59883edb-20c8-4206-854b-dd4a128bdd05.png)
- 글 작성
![image](https://user-images.githubusercontent.com/86938974/169193767-e5f9da0c-1954-4f21-b068-f49114b4f8aa.png)
- 최상단에 올라와 있는 글 확인
![image](https://user-images.githubusercontent.com/86938974/169193810-222a58fd-a45e-44f6-ad9b-88e373391ad3.png)

* 등록 처리와 목록 페이지의 모달창(부트스트랩 사용)
- list.html에 추가
```
<h1>GuestBook List Page
            <span>
<!--                <a th:href="@{/guestbook/register}">-->
<!--                    <button type="button" class="btn btn-outline-secondary">등록하기</button>-->
<!--                </a>-->
                <a href="/guestbook/register" class="btn btn-outline-secondary">등록하기</a>
            </span>
        </h1>
 <!-- 모달창 --------------------------------->
        <div class="modal" tabindex="-1" role="dialog">
            <div class="modal-dialog" role="document">
                <div class="modal-content">

                    <!-- Modal Header -->
                    <div class="modal-header">
                        <h5 class="modal-title">알림</h5>
                        <button type="button" class="close" data-dismiss="modal" aria-label="Close">
                            <span aria-hidden="true">&times;</span>
                        </button>
                    </div>

                    <!-- Modal body -->
                    <div class="modal-body">
                        <p>처리가 완료되었습니다.</p>
                    </div>

                    <!-- Modal footer -->
                    <div class="modal-footer">
                        <button type="button" class="btn btn-secondary" data-dismiss="modal">Close</button>
                    </div>

                </div>
            </div>
        </div>

        <script th:inline="javascript">
            var msg = [[${msg}]];
            console.log(msg);

            if(msg){
                $(".modal").modal()
            }
        </script>
        <!-- 모달창 종료-->
```
 - 화면
![image](https://user-images.githubusercontent.com/86938974/169197015-918e4fa1-9e22-4b95-a31d-b6c7cec1e2e4.png)
- 등록하기 클릭시 이동
![image](https://user-images.githubusercontent.com/86938974/169197044-f6a0160c-d06a-4192-8488-4775cc6c0104.png)
- 작성
![image](https://user-images.githubusercontent.com/86938974/169203783-042cb01e-68cc-4396-8e09-64b47a9543c3.png)
- 모달창 확인
![image](https://user-images.githubusercontent.com/86938974/169203834-813be26e-ff0e-410c-ada8-8dbfd67b1bac.png)

 * 등록 페이지의 링크와 조회 페이지 링크 처리
 - list.html 수정하여 각 글의 번호에 링크 처리
```
 <tr th:each = "dto : ${result.dtoList}">
                <th scope="row">
                    <a th:href="@{/guestbook/read(gno=${dto.gno}, page=${result.page})}">
                        [[${dto.gno}]]
                    </a>
                </th>
```
![image](https://user-images.githubusercontent.com/86938974/169206299-e977291b-565b-4d11-bc57-3945a4c64585.png)
  
 * 방명록 조회 처리
- GuestbookService 인터페이스 추가
```
GuestbookDTO read(Long gno);
```
  
- GuestbookServiceImpl 클래스
```
@Override
    public GuestbookDTO read(Long gno) {
        Optional<Guestbook> result = repository.findById(gno);
        return result.isPresent()? entityToDto(result.get()):null;
    }
```
- GuestbookController 클래스 추가
```
 @GetMapping("/read")
    public void read(long gno, @ModelAttribute("requestDTO") PageRequestDTO requestDTO, Model model){
        log.info("gno:"+gno);
        GuestbookDTO dto = service.read(gno);
        model.addAttribute("dto",dto);
    }
```
- 조회화면 작성 (read.html 생성)
```
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">

<th:block block th:replace="~{/layout/basic :: setContent(~{this::content})}">

    <th:block th:fragment="content">

        <h1 class="mt-4">GuestBook Read Page</h1>

        <div class="form-group">
            <label>Gno</label>
            <input type="text" class="form-control" name="gno" th:value="${dto.gno}" readonly>
        </div>

        <div class="form-group">
            <label>Title</label>
            <input type="text" class="form-control" name="title" th:value="${dto.title}" readonly>
        </div>

        <div class="form-group">
            <label>Content</label>
            <textarea class="form-control" rows="5" name="content" readonly>[[${dto.content}]]</textarea>
        </div>

        <div class="form-group">
            <label>Writer</label>
            <input type="text" class="form-control" name="writer" th:value="${dto.writer}" readonly>
        </div>

        <div class="form-group">
            <label>RegDate</label>
            <input type="text" class="form-control" name="regDate"
                   th:value="${#temporals.format(dto.regDate, 'yyyy/MM/dd HH:mm:ss')}" readonly>
        </div>

        <div class="form-group">
            <label>ModDate</label>
            <input type="text" class="form-control" name="modDate"
                   th:value="${#temporals.format(dto.modDate, 'yyyy/MM/dd HH:mm:ss')}" readonly>
        </div>

        <a th:href="@{/guestbook/modify(gno=${dto.gno},page=${requestDTO.page})}">
            <button type="button" class="btn btn-primary">Modify</button>
        </a>
        <a th:href="@{/guestbook/list(page=${requestDTO.page})}">
            <button type="button" class="btn btn-info">List</button>
        </a>
    </th:block>

</th:block>
```
- 화면
![image](https://user-images.githubusercontent.com/86938974/169213978-b8ef5f18-4798-48a1-94c9-00eafb0c54c0.png)
 
* 방명록의 수정/삭제 처리
- GuestbookController에 추가
```
@GetMapping({"/read","/modify"})
```
- 수정 화면(Modify.html) 생성
```
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">

<th:block block th:replace="~{/layout/basic :: setContent(~{this::content})}">

    <th:block th:fragment="content">

        <h1 class="mt-4">GuestBook Modify Page</h1>

        <form action="/guestbook/modify" method="post">
            <div class="form-group">
                <label>Gno</label>
                <input type="text" class="form-control" name="gno" th:value="${dto.gno}" readonly>
            </div>

            <div class="form-group">
                <label>Title</label>
                <input type="text" class="form-control" name="title" th:value="${dto.title}">
            </div>

            <div class="form-group">
                <label>Content</label>
                <textarea class="form-control" rows="5" name="content" >[[${dto.content}]]</textarea>
            </div>

            <div class="form-group">
                <label>Writer</label>
                <input type="text" class="form-control" name="writer" th:value="${dto.writer}" readonly>
            </div>

            <div class="form-group">
                <label>RegDate</label>
                <input type="text" class="form-control" name="regDate"
                       th:value="${#temporals.format(dto.regDate, 'yyyy/MM/dd HH:mm:ss')}" readonly>
            </div>

            <div class="form-group">
                <label>ModDate</label>
                <input type="text" class="form-control" name="modDate"
                       th:value="${#temporals.format(dto.modDate, 'yyyy/MM/dd HH:mm:ss')}" readonly>
            </div>
        </form>

        <button type="button" class="btn btn-primary">Modify</button>
        <button type="button" class="btn btn-info">List</button>
        <button type="button" class="btn btn-danger">Remove</button>

    </th:block>

</th:block>
```
- 수정 화면
![image](https://user-images.githubusercontent.com/86938974/169219194-c367d5fe-a717-465c-ba7e-0372fcd6fccd.png)
