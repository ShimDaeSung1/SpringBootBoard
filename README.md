# SpringBootBoard

* 기능 방명록 조회/수정/삭제, 페이징 처리
  * 방명록 조회/수정/삭제
  * 페이징 처리
  * 댓글 수정/삭제 기능
  
* 연관관계와 관계형 데이터베이스 설계
 * 한 명의 회원은 여러 개의 게시글을 작성할 수 있다.
 * 하나의 게시글은 한 명의 작성자만을 표시한다.
 * 하나의 게시글은 여러 개의 댓글을 가질 수 있다.
 * 하나의 댓글은 하나의 게시글에 속한다. 
![erd](https://user-images.githubusercontent.com/86938974/170183988-5b76079e-1e49-4857-8faa-e0fa6fc4af87.png)

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
    // https://mvnrepository.com/artifact/com.querydsl/querydsl-jpa
    implementation group: 'com.querydsl', name: 'querydsl-jpa', version: '5.0.0'
    // https://mvnrepository.com/artifact/com.querydsl/querydsl-apt
    implementation group: 'com.querydsl', name: 'querydsl-apt', version: '5.0.0'
}

```

* BaseEntity 클래스
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

* @ManyToOne 어노테이션

      * Board의 writer는 Member테이블을 참조한다.

```
@ManyToOne(fetch = FetchType.LAZY)
    private Member writer; //연관관계 지정, FK 생성
```

      * Reply의 board는 Board테이블 참조
```
@ManyToOne
    private Board board;
```
- 두 개 이상의 엔티티 간의 연관관계를 맺고 나면 쿼리를 실행하는 데이터베이스 입장에서는 엔티티 클래스들이 실제 데이터베이스상에서는 두 개 혹은 두 개 이상의 테이블로 생성되기 때문에 조인이 필요하다고 여긴다. @ManyToOne의 경우에는 FK쪽의 엔티티를 가져올 때, PK 쪽의 엔티티도 같이 가져온다.
- 특정한 엔티티를 조회할 때 연관관계를 가진 모든 엔티티를 같이 로딩하는 것을 'Eager loading' 이라고 한다. 한 번에 연관된 모든 엔티티를 가져온다는 장점이 있지만, 여러 연관관계를 맺고 있거나 연관관계가 복잡할수록 조인으로 인한 성능 저하를 피할 수 없다.


* Repository 인터페이스 추가 
      * MemberRepository, BoardRepository, ReplyRepository
      * MemberRepositoryTests 테스트 코드를 통해 Member 테이블에 더미데이터 추가
```
package org.zerock.guestbook.repository;

import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.zerock.guestbook.entity.Member;
import org.zerock.guestbook.respository.MemberRepository;


import java.util.stream.IntStream;

@SpringBootTest
public class MemberRepositoryTests {

    @Autowired
    private MemberRepository memberRepository;

    @Test
    public void insertMembers(){
        IntStream.rangeClosed(1, 100).forEach(i->{

            Member member = Member.builder()
                    .email("user"+i+"@aaa.com")
                    .password("1111")
                    .name("USER"+i)
                    .build();

            memberRepository.save(member);
        });
    }
}

```
 ![image](https://user-images.githubusercontent.com/86938974/170186052-e47bb1dd-6f1d-40af-af0d-c5ee5ec0cdb3.png)

* JPQL과 Left(Outer) join
      * Board 엔티티 클래스 내부에는 Member 엔티티 클래스를 변수로 선언하고, 연관관계를 맺으므로 Board의 writer 변수를 이용해서 조인을 처리한다.
      * Board와 Reply테이블을 조인해서 특정 게시물과 해당 게시물에 속한 댓글들을 조회한다.
      * BoardRepository 

```
package org.zerock.guestbook.respository;

import org.springframework.data.domain.Page;
import org.springframework.data.domain.Pageable;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.data.jpa.repository.Query;
import org.springframework.data.repository.query.Param;
import org.zerock.guestbook.entity.Board;
import org.zerock.guestbook.respository.search.SearchBoardRepository;


import java.util.List;

public interface BoardRepository extends JpaRepository<Board, Long>, SearchBoardRepository {
    @Query("select b, w from Board b left join b.writer w where b.bno =:bno")
//    @Query(value = "select b.*, m.name " +
//            " from board b left join member m" +
//            " on b.writer_email=m.email " +
//            " where b.bno =:bno",nativeQuery = true)
    Object getBoardWithWriter(@Param("bno") Long bno);

    @Query("SELECT b, r from Board b LEFT JOIN Reply r ON r.board = b where b.bno = :bno")
//    @Query("SELECT b, r FROM Reply r LEFT JOIN r.board b WHERE b.bno = :bno")
//    @Query(value = "SELECT b.bno, r.* " +
//            " FROM Board b LEFT JOIN Reply r " +
//            " ON r.board_bno = b.bno " +
//            " WHERE b.bno = :bno",nativeQuery = true)
    List<Object[]> getBoardWithReply(@Param("bno") Long bno);

    @Query(value = "SELECT b, w, count(r)"+
            " FROM Board b" +
            " LEFT JOIN b.writer w"+
            " LEFT JOIN Reply r ON r.board = b" +
            " GROUP BY b",
            countQuery = "SELECT count(b) FROM Board b"
    )
//    @Query(value ="SELECT b, w, count(r) " +
//            " FROM Reply r " +
//            " LEFT JOIN r.board b " +
//            " LEFT JOIN b.writer w  " +
//            " GROUP BY b",
//            countQuery ="SELECT count(b) FROM Board b")
//    @Query(value ="SELECT b.*, m.name, count(r.rno) " +
//            " FROM board b  LEFT JOIN member m on b.writer_email=m.email" +
//            " LEFT JOIN reply r ON r.board_bno = b.bno " +
//            " GROUP BY b.bno",
//            countQuery ="SELECT count(*) FROM Board b",nativeQuery = true)
    Page<Object[]> getBoardWithReplyCount(Pageable pageable);

    @Query("SELECT b, w, count(r)" +
            " FROM Board b LEFT JOIN b.writer w "+
            " LEFT OUTER JOIN Reply r ON r.board = b"+
            " WHERE b.bno = :bno")
    Object getBoardByBno(@Param("bno") Long bno);


}

```
* DTO 계층과 서비스 계층
      * BoardDTO, BoardService, BoardServiceImpl 클래스 작성
```
package org.zerock.guestbook.dto;

import lombok.*;

import java.time.LocalDateTime;

@Data
@ToString
@Builder
@AllArgsConstructor
@NoArgsConstructor
public class BoardDTO {
    private Long bno;
    private String title;
    private String content;
    private String writerEmail; //작성자의 이메일(id)
    private String writerName; //작성자의 이름
    private LocalDateTime regDate;
    private LocalDateTime modDate;
    private int replyCount; // 해당 게시글의 댓글 수
}

```

```
package org.zerock.guestbook.service;

import org.zerock.guestbook.dto.BoardDTO;
import org.zerock.guestbook.dto.PageRequestDTO;
import org.zerock.guestbook.dto.PageResultDTO;
import org.zerock.guestbook.entity.Board;
import org.zerock.guestbook.entity.Member;

import static org.zerock.guestbook.entity.QMember.member;

public interface BoardService {

    Long register(BoardDTO dto);

    PageResultDTO<BoardDTO, Object[]> getList(PageRequestDTO pageRequestDTO);

    BoardDTO get(Long bno);

    void modify(BoardDTO boardDTO);
    void removeWithReplies(Long bno); //삭제 기능
    default Board dtoToEntity(BoardDTO dto){

        Member member = Member.builder().email(dto.getWriterEmail()).build();
        Board board = Board.builder()
                .bno(dto.getBno())
                .title(dto.getTitle())
                .content(dto.getContent())
                .writer(member)
                .build();
        return board;
    }

    default BoardDTO entityToDTO(Board board , Member member, Long replyCount ){

        BoardDTO boardDTO = BoardDTO.builder()
                .bno(board.getBno())
                .title(board.getTitle())
                .content(board.getContent())
                .regDate(board.getRegDate())
                .modDate(board.getModDate())
                .writerEmail(member.getEmail())
                .writerName(member.getName())
                .replyCount(replyCount.intValue()) // long으로 나오므로 int로 처리하도록
                .build();
        return boardDTO;
    }
}

```

```
package org.zerock.guestbook.service;

import lombok.RequiredArgsConstructor;
import lombok.extern.log4j.Log4j2;
import org.springframework.data.domain.Page;
import org.springframework.data.domain.Sort;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;
import org.zerock.guestbook.dto.BoardDTO;
import org.zerock.guestbook.dto.PageRequestDTO;
import org.zerock.guestbook.dto.PageResultDTO;
import org.zerock.guestbook.entity.Board;
import org.zerock.guestbook.entity.Member;
import org.zerock.guestbook.respository.BoardRepository;
import org.zerock.guestbook.respository.ReplyRepository;

import java.util.function.Function;

@Service
@RequiredArgsConstructor
@Log4j2
public class BoardServiceImpl implements BoardService {

    private final BoardRepository repository; // 자동 주입 final
    private final ReplyRepository replyRepository;

    @Override
    public Long register(BoardDTO dto) {
        log.info(dto);
        Board board = dtoToEntity(dto);
        repository.save(board);
        return board.getBno();
    }

    @Override
    public PageResultDTO<BoardDTO, Object[]> getList(PageRequestDTO pageRequestDTO) {
        log.info(pageRequestDTO);

        Function<Object[], BoardDTO> fn = (en -> entityToDTO((Board)en[0], (Member)en[1], (Long)en[2]));

//        Page<Object[]> result = repository.getBoardWithReplyCount(
//                pageRequestDTO.getPageable(Sort.by("bno").descending()));
        Page<Object[]> result = repository.searchPage(
                pageRequestDTO.getType(),
                pageRequestDTO.getKeyword(),
                pageRequestDTO.getPageable(Sort.by("bno").descending())
        );
        return new PageResultDTO<>(result, fn);


    }

    @Override
    public BoardDTO get(Long bno) {
        Object result = repository.getBoardByBno(bno);
        Object[] arr = (Object[])result;
        return entityToDTO((Board)arr[0], (Member)arr[1], (Long)arr[2]);
    }

    @Override
    public void modify(BoardDTO boardDTO) {

        Board board = repository.getOne(boardDTO.getBno());

            board.changeTitle(boardDTO.getTitle());
            board.changeContent(boardDTO.getContent());

            repository.save(board);
    }

    @Transactional
    @Override
    public void removeWithReplies(Long bno) {
        //댓글 부터 삭제
        replyRepository.deleteByBno(bno);

        repository.deleteById(bno);
    }


}

```

- 게시물 등록은 BoardDTO 타입을 파라미터로 전달 받고, 생성된 게시물의 번호를 반환하도록 한다.
- 게시물 목록 처리 : Object[]을 DTO로 변환 => Object[]의 내용은 board와 member, 댓글의 수는 Long 타입으로 나오게 되므로, 이를 파라미터로 전달 받아서 BoardDTO를 구성하도록 작성
- 게시물 조회 처리 : 파라미터로 게시물의 번호(bno)를 파라미터로 받아서 처리하도록 BoardService와 BoardServiceImpl클래스에 get()메서드를 추가해서 작성. BoardRepository의 Board 엔터티와 Member엔터티, 댓글의 수를 가져오는 getBoardByBno()를 이용해서 처리한다.


* 게시물 삭제 처리
      * 게시물을 FK로 참조하고 있는 reply테이블 역시 삭제해준다.
      * 해당 게시물의 모든 댓글을 삭제한 후에 해당 게시물을 삭제 하므로 두 작업이 하나의 트랜잭션으로 처리되어야 한다.
      * 
- ReplyRepository 인터페이스 (JPQL을 사용해서 update, delete를 진행하기 위해서는 @Modifying 어노테이션 추가)
```
public interface ReplyRepository extends JpaRepository<Reply, Long> {

    @Modifying
    @Query("delete from Reply r where r.board.bno = :bno")
    void deleteByBno(Long bno);
}
```
- BoardService 인터페이스
```
void removeWithReplies(Long bno); //삭제 기능
```

- BoardServiceImpl

```
@Transactional
    @Override
    public void removeWithReplies(Long bno) {
        //댓글 부터 삭제
        replyRepository.deleteByBno(bno);

        repository.deleteById(bno);
    }
```
- Transactional을 걸어주면 연속된 작업이 모두 성공하면 모두 commit, 하나라도 실패하면 모두 rollback 해준다.

* 컨트롤러와 화면 처리 (타임리프 사용)
      * BoardController 클래스
```
package org.zerock.guestbook.controller;

import lombok.RequiredArgsConstructor;
import lombok.extern.log4j.Log4j2;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.ModelAttribute;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.servlet.mvc.support.RedirectAttributes;
import org.zerock.guestbook.dto.BoardDTO;
import org.zerock.guestbook.dto.GuestbookDTO;
import org.zerock.guestbook.dto.PageRequestDTO;
import org.zerock.guestbook.service.BoardService;

@Controller
@RequestMapping("/board")
@Log4j2
@RequiredArgsConstructor
public class BoardController {
    private final BoardService boardService;

    @GetMapping("/list")
    public void list(PageRequestDTO pageRequestDTO, Model model){
        log.info("list......"+pageRequestDTO);
        model.addAttribute("result", boardService.getList(pageRequestDTO));
    }

    @GetMapping("/register")
    public void register(){
        log.info("register get....");
    }

    @PostMapping("/register")
    public String registerPost(BoardDTO dto, RedirectAttributes redirectAttributes){
        Long bno = boardService.register(dto);
        redirectAttributes.addFlashAttribute("msg", bno);
        return "redirect:/board/list";

    }

    @GetMapping({"/read", "/modify"})
    public void read(@ModelAttribute("requestDTO") PageRequestDTO pageRequestDTO, Long bno, Model model){
        BoardDTO boardDTO = boardService.get(bno);
        model.addAttribute("dto", boardDTO);
    }

    @PostMapping("/remove")
    public String remove(long bno, RedirectAttributes redirectAttributes){
        boardService.removeWithReplies(bno);
        redirectAttributes.addFlashAttribute("msg", bno);
        return "redirect:/board/list";
    }

    @PostMapping("/modify")
    public String modify(BoardDTO dto,
                         @ModelAttribute("requestDTO") PageRequestDTO requestDTO,
                         RedirectAttributes redirectAttributes){
        boardService.modify(dto);

        redirectAttributes.addAttribute("page", requestDTO.getPage());
        redirectAttributes.addAttribute("type", requestDTO.getType());
        redirectAttributes.addAttribute("keyword", requestDTO.getKeyword());
        redirectAttributes.addAttribute("bno", dto.getBno());
        return "redirect:/board/read";
    }

}

```
* 레이아웃 (basic.html , layout.html)
      * basic.html
```
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">

<th:block th:fragment="setContent(content)">

  <head>

    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
    <meta name="description" content="">
    <meta name="author" content="">

    <title>Simple Sidebar - Start Bootstrap Template</title>

    <!--    &lt;!&ndash; Bootstrap core CSS &ndash;&gt;-->
    <!--    <link th:href="@{/vendor/bootstrap/css/bootstrap.min.css}" rel="stylesheet">-->
    <!--    &lt;!&ndash; Custom styles for this template &ndash;&gt;-->
    <!--    <link th:href="@{/css/simple-sidebar.css}" rel="stylesheet">-->
    <!--    &lt;!&ndash; Bootstrap core JavaScript &ndash;&gt;-->
    <!--    <script th:src="@{/vendor/jquery/jquery.min.js}"></script>-->
    <!--    <script th:src="@{/vendor/bootstrap/js/bootstrap.bundle.min.js}"></script>-->

    <!-- Bootstrap core CSS -->
    <link href="/vendor/bootstrap/css/bootstrap.min.css" rel="stylesheet">
    <!-- Custom styles for this template -->
    <link href="/css/simple-sidebar.css" rel="stylesheet">
    <!-- Bootstrap core JavaScript -->
    <script src="/vendor/jquery/jquery.min.js"></script>
    <script src="/vendor/bootstrap/js/bootstrap.bundle.min.js"></script>

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
        <th:block th:replace="${content}"></th:block>
      </div>
    </div>
    <!-- /#page-content-wrapper -->

  </div>
  <!-- /#wrapper -->



  <!-- Menu Toggle Script -->
  <script>
    $("#menu-toggle").click(function(e) {
      e.preventDefault();
      $("#wrapper").toggleClass("toggled");
    });
  </script>

  </body>
</th:block>
</html>

```

      * layout.html
```
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<th:block th:fragment="setContent(content)">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        * {
            margin:0;
            padding: 0;
        }
        .header{
            width:100vw;
            height:20vh;
            background-color: aqua;
        }
        .content{
            width:100vw;
            height:70vh;
            background-color: lightgray;
        }
        .footer {
            width: 100vw;
            height: 10vh;
            background-color: green;
        }
    </style>
</head>
<body>
    <div class="header">
        <h1>HEADER</h1>
    </div>
    <div class = "content">

        <th:block th:replace="${content}"></th:block>

    </div>
    <div class="footer">
        <h1>FOOTER</h1>
    </div>
</body>
</th:block>
</html>
```

      * list.html, register.html, read.html, modify.html은 thymeleaf를 사용했다.
```
<th:block th:replace="~{/layout/basic :: setContent(~{this::content})}">

<th:block th:fragment="content">
.
.
.

```
* SearchBoardRepositoryImpl
```
package org.zerock.guestbook.respository.search;

import com.querydsl.core.BooleanBuilder;
import com.querydsl.core.Tuple;
import com.querydsl.core.types.Order;
import com.querydsl.core.types.OrderSpecifier;
import com.querydsl.core.types.dsl.BooleanExpression;
import com.querydsl.core.types.dsl.PathBuilder;
import com.querydsl.jpa.JPQLQuery;
import lombok.extern.log4j.Log4j2;
import org.springframework.data.domain.Page;
import org.springframework.data.domain.PageImpl;
import org.springframework.data.domain.Pageable;
import org.springframework.data.domain.Sort;
import org.springframework.data.jpa.repository.support.QuerydslRepositorySupport;
import org.zerock.guestbook.entity.Board;
import org.zerock.guestbook.entity.QBoard;
import org.zerock.guestbook.entity.QMember;
import org.zerock.guestbook.entity.QReply;


import java.util.List;
import java.util.stream.Collectors;

@Log4j2
public class SearchBoardRepositoryImpl extends QuerydslRepositorySupport implements SearchBoardRepository {

    public SearchBoardRepositoryImpl() {
        super(Board.class);
    }

    @Override
    public Board search1() {

        log.info("search1........................");

        QBoard board = QBoard.board;
        QReply reply = QReply.reply;
        QMember member = QMember.member;

        JPQLQuery<Board> jpqlQuery = from(board);
        jpqlQuery.leftJoin(member).on(board.writer.eq(member));
        jpqlQuery.leftJoin(reply).on(reply.board.eq(board));

        JPQLQuery<Tuple> tuple = jpqlQuery.select(board, member.email, reply.count());
        tuple.groupBy(board);

        log.info("---------------------------");
        log.info(tuple);
        log.info("---------------------------");

        List<Tuple> result = tuple.fetch();

        log.info(result);

        return null;
    }

    @Override
    public Page<Object[]> searchPage(String type, String keyword, Pageable pageable) {

        log.info("searchPage.............................");

        QBoard board = QBoard.board;
        QReply reply = QReply.reply;
        QMember member = QMember.member;

        JPQLQuery<Board> jpqlQuery = from(board);
        jpqlQuery.leftJoin(member).on(board.writer.eq(member));
        jpqlQuery.leftJoin(reply).on(reply.board.eq(board));

        //SELECT b, w, count(r) FROM Board b
        //LEFT JOIN b.writer w LEFT JOIN Reply r ON r.board = b
        JPQLQuery<Tuple> tuple = jpqlQuery.select(board, member, reply.count());

        BooleanBuilder booleanBuilder = new BooleanBuilder();
        BooleanExpression expression = board.bno.gt(0L);

        booleanBuilder.and(expression);

        if(type != null){
            String[] typeArr = type.split("");
            //검색 조건을 작성하기
            BooleanBuilder conditionBuilder = new BooleanBuilder();

            for (String t:typeArr) {
                switch (t){
                    case "t":
                        conditionBuilder.or(board.title.contains(keyword));
                        break;
                    case "w":
                        conditionBuilder.or(member.email.contains(keyword));
                        break;
                    case "c":
                        conditionBuilder.or(board.content.contains(keyword));
                        break;
                }
            }
            booleanBuilder.and(conditionBuilder);
        }

        tuple.where(booleanBuilder);

        //order by
        Sort sort = pageable.getSort();

        //tuple.orderBy(board.bno.desc());

        sort.stream().forEach(order -> {
            Order direction = order.isAscending()? Order.ASC: Order.DESC;
            String prop = order.getProperty();

            PathBuilder orderByExpression = new PathBuilder(Board.class, "board");
            tuple.orderBy(new OrderSpecifier(direction, orderByExpression.get(prop)));

        });
        tuple.groupBy(board);

        //page 처리
        tuple.offset(pageable.getOffset());
        tuple.limit(pageable.getPageSize());

        List<Tuple> result = tuple.fetch();

        log.info(result);

        long count = tuple.fetchCount();

        log.info("COUNT: " +count);

        return new PageImpl<Object[]>(
                result.stream().map(t -> t.toArray()).collect(Collectors.toList()),
                pageable,
                count);
    }
}
```
- Tuple 객체 사용, JPQLQuery의 leftjoin, join을 이용해서 Board, Member, Reply처리, Groupby사용
- 파라미터로 전달되는 type 값은 '제목(t), 내용(c), 작성자(w)'를 하나 혹은 조합으로 'tcw'와 같은 형태이다.
- 파라미터에 따라서 검색 조건을 추가할 수 있도록 BooleanBuilder와 BooleanExpression 추가

* 목록 화면에서의 검색 처리
      * list.html
```
 <form action="/board/list" method="get" id="searchForm">
            <div class="input-group">
                <input type="hidden" name="page" value="1">
                <div class="input-group-prepend">
                    <select class="custom-select" name="type">
                        <option th:selected="${pageRequestDTO.type == null}">-------</option>
                        <option value ="t" th:selected="${pageRequestDTO.type == 't'}">제목</option>
                        <option value="c" th:selected="${pageRequestDTO.type=='c'}">내용</option>
                        <option value="w" th:selected="${pageRequestDTO.type == 'w'}">작성자</option>
                        <option value="tc" th:selected="${pageRequestDTO.type =='tc'}">제목 + 내용</option>
                        <option value="tcw" th:selected="${pageRequestDTO.type == 'tcw'}">제목 + 내용 + 작성자</option>
                    </select>
                </div>
                <input class="form-control" name="keyword" th:value="${pageRequestDTO.keyword}">
                <div class="input-group-append" id="button-addon4">
                    <button class="btn btn-outline-secondary btn-search" type="button">Search</button>
                    <button class="btn btn-outline-secondary btn-clear" type="button">Clear</button>
                </div>
            </div>
        </form>
```

* JSON과 Ajax로 댓글 처리
      * 게시물이 로딩된 이후에 화면에서 댓글의 숫자를 클릭하면 해당 게시물에 속한 댓글을 Ajax로 가져옴
      * 특정한 버튼을 클릭하면 새로운 댓글을 입력할 수 있는 모달창 보여주고 Ajax의 Post방식으로 댓글을 전송한다.
- Reply 클래스
```
package org.zerock.guestbook.entity;

import lombok.*;

import javax.persistence.*;

@Entity
@Builder
@AllArgsConstructor
@NoArgsConstructor
@Getter
@ToString(exclude = "board")
public class Reply extends BaseEntity{
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long rno;
    private String text;
    private String replyer;

    @ManyToOne(fetch = FetchType.LAZY)
    private Board board;
}

```
- ReplyRepository 인터페이스
```
// 게시물로 댓글 목록 가져오기
    List<Reply> getRepliesByBoardOrderByRno(Board board);
```

- ReplyDTO

```
package org.zerock.guestbook.dto;

import lombok.AllArgsConstructor;
import lombok.Builder;
import lombok.Data;
import lombok.NoArgsConstructor;

import java.time.LocalDateTime;

@Builder
@AllArgsConstructor
@NoArgsConstructor
@Data
public class ReplyDTO {
    
    private Long rno;
    private String text;
    private String replyer;
    private Long bno; // 게시글번호
    private LocalDateTime regDate, modDate;
}

```
- ReplyDTO를 Reply 엔티티로 처리하거나 반대의 경우에 대한 처리는 ReplyService인터페이스, ReplyServiceImpl 클래스 작성 처리
- ReplyService : 
- 댓글 등록(register)
- 특정 게시물의 댓글 리스트 가져오기(getList)
- 댓글을 수정(modify)하고 삭제(remove)
- Reply를 ReplyDTO를 변환하는 entityToDTO()
- ReplyDTO를 Reply로 변환하면 dtoToEntity()

      * ReplyService

```
package org.zerock.guestbook.service;

import org.zerock.guestbook.dto.ReplyDTO;
import org.zerock.guestbook.entity.Board;
import org.zerock.guestbook.entity.Reply;

import java.util.List;

public interface ReplyService {
    
    Long register(ReplyDTO replyDTO);
    
    List<ReplyDTO> getList(Long bno);
    
    void modify(ReplyDTO replyDTO);
    
    void remove(Long rno);
    
    default Reply dtoToEntity(ReplyDTO replyDTO){
        Board board = Board.builder().bno(replyDTO.getBno()).build();
        
        Reply reply = Reply.builder()
                .rno(replyDTO.getRno())
                .text(replyDTO.getText())
                .replyer(replyDTO.getReplyer())
                .board(board)
                .build();
        
        return reply;
    }
    
    default ReplyDTO entityToDTO(Reply reply){
        ReplyDTO dto = ReplyDTO.builder()
                .rno(reply.getRno())
                .text(reply.getText())
                .replyer(reply.getReplyer())
                .regDate(reply.getRegDate())
                .modDate(reply.getModDate())
                .build();
        return dto;
    }
}

```

      * ReplyServiceImpl
```
package org.zerock.guestbook.service;

import lombok.RequiredArgsConstructor;
import org.springframework.stereotype.Service;
import org.zerock.guestbook.dto.ReplyDTO;
import org.zerock.guestbook.entity.Board;
import org.zerock.guestbook.entity.Reply;
import org.zerock.guestbook.respository.ReplyRepository;

import java.util.List;
import java.util.stream.Collectors;

@Service
@RequiredArgsConstructor
public class ReplyServiceImpl implements ReplyService{

    private final ReplyRepository replyRepository;

    @Override
    public Long register(ReplyDTO replyDTO) {
        Reply reply = dtoToEntity(replyDTO);

        replyRepository.save(reply);
        return reply.getRno();
    }

    @Override
    public List<ReplyDTO> getList(Long bno) {
        List<Reply> result = replyRepository
                .getRepliesByBoardOrderByRno(Board.builder().bno(bno).build());
        return result.stream().map(reply -> entityToDTO(reply)).collect(Collectors.toList());
    }

    @Override
    public void modify(ReplyDTO replyDTO) {
        Reply reply = dtoToEntity(replyDTO);
        replyRepository.save(reply);
    }

    @Override
    public void remove(Long rno) {
        replyRepository.deleteById(rno);
    }
}

```
- DB에 수정, 삽입시 Entity여야 하므로 dtoToEntity 사용해서 DTO를 Entity로 바꿔주고
- controller로 내보내서 출력하기 위해 사용해야하면 EntityToDTO를 사용해서 html에서 thymeleaf로 사용할 수 있도록 DTO로 바꾼다.

* @RestController
      * ReplyController 클래스
```
package org.zerock.guestbook.controller;

import lombok.RequiredArgsConstructor;
import lombok.extern.log4j.Log4j2;
import org.springframework.http.HttpStatus;
import org.springframework.http.MediaType;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;
import org.zerock.guestbook.dto.ReplyDTO;
import org.zerock.guestbook.service.ReplyService;

import java.util.List;

@RestController
@RequestMapping("/replies")
@Log4j2
@RequiredArgsConstructor
public class ReplyController {

    private final ReplyService replyService;

    @GetMapping(value = "/board/{bno}", produces = MediaType.APPLICATION_JSON_VALUE)
    public ResponseEntity<List<ReplyDTO>> getListByBoard(@PathVariable("bno") Long bno){
        return new ResponseEntity<>(replyService.getList(bno), HttpStatus.OK);
    }
}

```
- @RestController의 경우 모든 메서드의 리턴 타입은 기본으로 JSON사용
- 메서드의 반환 타입은 ResponseEntity라는 객체를 이용하는데 이를 이용하면 HTTP의 상태 코드 등을 같이 전달할 수 있다.
- @PathVariable을 통해 /replies/board/100과 같이 특정 게시물 번호로 조회할 때 100이라는 데이터를 변수로 처리할 수 있다.

* 조회 화면에서 처리
      * read.html 일부
```
        <div>
            <div class="mt-4">
                <h5><span class="badge badge-secondary replyCount">Reply Count [[${dto.replyCount}]]</span> </h5>
            </div>
            <div class="list-group replyList">

            </div>
        </div>
        
        <script th:inline="javascript">
            $(document).ready(function(){

                var bno = [[${dto.bno}]];
                var listGroup = $(".replyList");

                $(".replyCount").click(function(){
                    $.getJSON('/replies/board'+bno, function(arr){
                        console.log(arr);
                    })//end getJson
                })//end click

                //댓글이 추가될 영역
                var listGroup = $(".replyList");

                function formatTime(str){
                    var date = new Date(str);

                    return date.getFullYear() + '/' +
                        (date.getMonth() + 1) + '/' +
                        date.getDate() + ' ' +
                        date.getHours() + ':' +
                        date.getMinutes();
                }

                function loadJsonData(){
                    $.getJSON('/replies/board/'+bno, function(arr){
                        var str = "";

                        $('.replyCount').html(" Reply Count " + arr.length);

                        $.each(arr, function(idx, reply){
                            console.log(reply);
                            str += '    <div class="card-body" data-rno="'+reply.rno+'"><b>'+reply.rno +'</b>';
                            str += '    <h5 class="card-title">'+reply.text+'</h5>';
                            str += '    <h6 class="card-subtitle mb-2 text-muted">'+reply.replyer+'</h6>';
                            str += '    <p class="card-text">'+ formatTime(reply.regDate) +'</p>';
                            str += '    </div>';
                        })
                        listGroup.html(str);
                    });
                }
                $(".replyCount").click(function(){
                    loadJsonData();
                })



            });
        </script>
```



















