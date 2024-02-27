---
layout: post
title: "3.2 - 게시판 만들고 새 글 작성하기"
author: "Hyundong Lee"
categories: Spring_Boot
tags: [Github, Blog, Develop]
date: 2024-02-28 01:20:10 +0900
---

## 1. DB 데이터 조회하기
<hr/>

* DB에 테이블이라는 틀에 데이터가 맞춰 저장된다.
	* ex) Article 테이블에는 id, title, content가 한 행씩 생성되어 저장된다.
	* 이때 한 행을 레코드라고 한다.
<br/><br/>

#### SQL
* 생성(테이블에 저장) - INSERT
	* `INSERT INTO 테이블명(속성1, 속성2, 속성3 ...) VALUES(값1, 값2, 값3 ...);`

* 조회 - SELECT
	* `SELECT 속성명 FROM 테이블명;`

* 수정 - UPDATE

* 삭제 - DELETE
<br/><br/>
* 이제 SQL 연습을 위해 DB에 접속해보자
<br/><br/>

#### H2 DB 접속하기
1. H2 DB에 웹으로 접속하기 위한 기본 설정
	1. main -> resources -> application.properties로 가서 아래 명령어 추가
		* `spring.h2.console.enabled=true`

2. 서버를 시작 후 http://localhost:8080/h2-console 로 접속

3. 인텔리제이의 하단 실행창에서 `ctrl + f`를 누른 후 `jdbc:h2`를 검색했을 때 2번째로 나오는 따옴표 안의 내용을 모두 복사
	* ex) jdbc:h2:mem:22d86939-4e63-4199-92a1-a40a835ec88a
	* 컴퓨터마다 다르고, 서버를 시작할 때마다 변경된다.

4. 웹으로 돌아와서 JDBC URL에 기존 내용을 지우고 붙여넣기

5. Connect 버튼을 눌러 접속
<br/><br/>

#### 데이터 조회하기
1. 왼쪽에 ARTICLE 클릭, 나오는 코드는 모든 테이블을 조회하는 명령이다.

2. Run을 눌러 실행하면 빈 테이블이 출력된다.
	* H2 DB는 입출력을 메모리에서 돌리기 때문에 서버를 재시작하면 저장된 내용이 다 날아간다.

3. 웹 브라우저에 새 탭을 추가한 후, http://localhost:8080/articles/new 로 접속, 내용을 입력, 전송하고 다시 DB 창으로 돌아오자.

4. 다시 Run을 누르면 데이터가 잘 저장된 것을 확인할 수 있다.
<br/><br/>

#### 데이터 직접 삽입하기
1. DB 창에서 작성된 명령문을 지우고, 아래 코드를 작성하자.
	* `INSERT INTO article(id, title, content) VALUES(3, '3333', '3333');`

2. Run을 누르면 아래 Update Count: 1이라고 뜨면 잘 저장된 것이다.

3. 다시 `SELECT * FROM articles;`를 작성하여 실행하면, 데이터가 잘 들어간 것을 확인할 수 있다.
<br/><br/>

## 2. 연습 문제
<hr/>

* 뷰 페이지가 아래와 같이 회원 가입 페이지일 때, 페이지를 처리하기 위한 코드를 작성해보자.
<br/><br/>

* 뷰 -> members / new.mustache
	* "/signup"으로 접속했을 때 나타나게 해야한다.
* 컨트롤러 -> controller / MemberController.java
* DTO -> dto / MemberForm.java
* Entity -> entity / Member.java
* Repository -> repository / MemberRepository.java
<br/><br/>

* 뷰 페이지

```html
{ {>layouts/header}}  
  
<form class="container" action="/join" method="post">  
    <div class="mb-3">  
        <label class="form-label">Email</label>  
        <input type="email" class="form-control" name="email">  
    </div>    
    
    <div class="mb-3">  
        <label class="form-label">Password</label>  
        <input type="password" class="form-control" name="password">  
    </div>    
    
    <button type="submit" class="btn btn-primary">Submit</button>  
    <a href="/members">Back</a>  
</form>  
  
{ {>layouts/footer}}
```
<br/>

#### 답
* MemberController.java

```java
package com.example.Chapter_3_9.controller;  
  
import com.example.Chapter_3_9.dto.MemberForm;  
import com.example.Chapter_3_9.entity.Member;  
import com.example.Chapter_3_9.repository.MemberRepository;  
import org.springframework.beans.factory.annotation.Autowired;  
import org.springframework.stereotype.Controller;  
import org.springframework.web.bind.annotation.GetMapping;  
import org.springframework.web.bind.annotation.PostMapping;  
  
@Controller  
public class MemberController {  
    @Autowired  
    MemberRepository memberRepository;  
  
    @GetMapping("/signup")  
    public String signUp() {  
        return "members/new";  
    }  
  
    @PostMapping("/join")  
    public String memberRegister(MemberForm memberForm) {  
        //DTO 확인용  
        System.out.println(memberForm.toString());  
  
        //1. DTO -> Entity  
        Member member = memberForm.toEntity();  
        System.out.println(member.toString());  
  
        //2. Repository -> DB  
        Member saved = memberRepository.save(member);  
        System.out.println(saved);  
  
        return "";  
    }  
}
```
<br/>

* MemberForm.java

```java
package com.example.Chapter_3_9.dto;  
  
import com.example.Chapter_3_9.entity.Member;  
import lombok.AllArgsConstructor;  
import lombok.ToString;  
  
@AllArgsConstructor  
@ToString  
public class MemberForm {  
    private String email;  
    private String password;  
  
    public Member toEntity() {  
        return new Member(null, email, password);  
    }  
}
```

* Member.java

```java
package com.example.Chapter_3_9.entity;  
  
import jakarta.persistence.Column;  
import jakarta.persistence.Entity;  
import jakarta.persistence.GeneratedValue;  
import jakarta.persistence.Id;  
import lombok.AllArgsConstructor;  
import lombok.NoArgsConstructor;  
import lombok.ToString;  
  
@Entity  
@AllArgsConstructor  
@NoArgsConstructor  
@ToString  
public class Member {  
    @Id  
    @GeneratedValue    
    private Long id;  
  
    @Column  
    private String email;  
  
    @Column  
    private String password;  
}
```
<br/>

* MemberRepository.java

```java
package com.example.Chapter_3_9.repository;  
  
import com.example.Chapter_3_9.entity.Member;  
import org.springframework.data.repository.CrudRepository;  
  
public interface MemberRepository extends CrudRepository<Member, Long> {  
}
```