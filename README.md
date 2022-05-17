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

