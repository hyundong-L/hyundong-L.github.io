---
layout: post
title: "6 - 게시판 내 페이지 이동"
author: "Hyundong Lee"
categories: Spring_Boot
tags: [Github, Blog, Develop]
date: 2024-02-29 00:47:05 +0900
---

## 1. 링크와 리다이렉트
<hr/>

#### 링크
* 미리 정해 놓은 요청을 간편히 전송하는 기능, 보통 페이지 이동을 위해 사용
	* 보통 `<a>` 혹은 `<form>` 태그로 작성하며, 클라이언트가 링크를 통해 이동할 페이지를 요청하면 서버는 결과 페이지를 응답으로 보낸다.
<br/><br/>

#### 리다이렉트
* 클라이언트가 보낸 요청을 처리한 후, 계속해서 처리할 다음 요청 주소를 서버에서 재지시 하는 것
	* ex) a로 업무를 처리하기 위해 전화했는데, 업무 일부만 처리하고 나머지 담당은 b라서 b로 전화해보라고 요청하는 것
<br/><br/>

* 리다이렉트를 사용해서 분리된 기능을 하나의 연속적인 흐름으로 연결할 수 있다.
* 리다이렉트를 받은 클라이언트는 해당 주소로 다시 요청을 보내고, 서버는 결과를 응답하게 된다.
<br/><br/>

#### 구현할 기능
* 웹 게시판에서 처음에 목록 페이지가 뜨며, 제목을 클릭하면 글의 상세 페이지로 이동, 글쓰기 버튼을 클릭하면 입력 페이지로 이동해야 한다.
* 상세 페이지에 있을 때는 목록 페이지로 이동 가능해야 하며, 입력 페이지에서는 글을 작성하지 않고 목록 페이지로 이동할 수 있어야 한다.
* 새 글을 작성 후 전송 버튼을 눌러 상세 페이지를 볼 수 있게 해야 한다.
<br/><br/>

* 위 내용들을 그림으로 나타내면 아래와 같이 되어야 하며, 이런 것들은 링크와 리다이렉트라는 기능으로 구현한다.

![Img Alt Notice Board](/assets/img/for_post/Move%20page%20within%20notice%20board.jpeg){: width="90%"}
<br/><br/>

## 2. 페이지 연결하기
<hr/>

* 먼저 우리가 앞에서 만든 게시판을 접속해보면 아래와 같은 문제점들이 존재한다.
	* 새 글을 작성하기 위한 링크가 없다.
		* 직접 /articles/new 주소로 접속해야 한다.

	* 글을 작성하는 페이지에 목록으로 돌아가는 뒤로가기가 없다.
<br/><br/>

#### 새 글 작성 링크 만들기
1. articles/index.mustache 파일을 열고, 아래 코드를 추가하자

    ```html
    ...
    </table>  
    
    <a href="/articles/new">New Article</a>  
    
    { {>layouts/footer}}
    ```

2. 서버를 실행하여 <http://localhost:8080/articles> 로 접속 후 링크를 클릭했을 때 제대로 이동하는지 확인하자.
<br/><br/>

#### 입력 페이지 -> 목록 페이지
1. articles/new.mustache 파일을 열고, 아래 코드를 추가하자.

    ```html
        ...
        <button type="submit" class="btn btn-primary">Submit</button>  
        <a href="/articles">Back</a>  
    </form>  
    
    { {>layouts/footer}}
    ```

2. 서버 실행 후 <http://localhost:8080/articles/new> 로 접속해 링크가 제대로 동작하는지 확인해보자.
<br/><br/>

#### 입력 페이지 -> 상세 페이지
* 입력 페이지에서 내용을 등록하면 제대로 등록되었는지 확인할 수 있게, 상세 페이지가 나오게 만들기 위해 리다이렉트를 사용할 것이다.
<br/><br/>

* 입력 페이지에서 데이터를 전송하면 ArticleController에서 `createArticle()` 메서드에서 데이터를 받았다.
* 현재는 해당 메서드에 return 값이 없으며, 여기에 리다이렉트를 정의하는 것이다.
	* `return "redirect:URL_주소` 형식으로 정의
		* ex) `return "redirect:/articles`라고 하면 목록 페이지로 재요청하라는 것이다.
<br/><br/>

* 우리는 상세 페이지로 이동하도록 해야하기 때문에, id 값에 따라 재요청을 하게 만들어야 하고, + 연산자를 사용해 데이터를 저장한 saved 객체의 id 값을 주소에 붙이게 만들 것이다.
<br/><br/>

1. saved 객체에서 값을 가져오려면 getter 메서드를 정의해야 하며, Lombok을 사용하여 자동으로 생성되게 만들 것이다.

    ```java
    package com.example.Chapter_3_9.entity;  
    
    import jakarta.persistence.Column;  
    import jakarta.persistence.Entity;  
    import jakarta.persistence.GeneratedValue;  
    import jakarta.persistence.Id;  
    import lombok.AllArgsConstructor;  
    import lombok.Getter;  
    import lombok.NoArgsConstructor;  
    import lombok.ToString;  
    
    @Entity  
    @AllArgsConstructor  
    @NoArgsConstructor  
    @ToString  
    @Getter  // --> 추가
    public class Article {  
        ...
    }
    ```
<br/>

2. ArticleController에 리다이렉트 값을 추가해야 한다.

    ```java
    package com.example.Chapter_3_9.controller;  
    
    import com.example.Chapter_3_9.dto.ArticleForm;  
    import com.example.Chapter_3_9.entity.Article;  
    import com.example.Chapter_3_9.repository.ArticleRepository;  
    import lombok.extern.slf4j.Slf4j;  
    import org.springframework.beans.factory.annotation.Autowired;  
    import org.springframework.stereotype.Controller;  
    import org.springframework.ui.Model;  
    import org.springframework.web.bind.annotation.GetMapping;  
    import org.springframework.web.bind.annotation.PathVariable;  
    import org.springframework.web.bind.annotation.PostMapping;  
    
    import java.util.List;  
    
    @Controller  
    @Slf4j  
    public class ArticleController {  
        ...
    
        @PostMapping("/articles/create")  
        public String createArticle(ArticleForm form) {  
            System.out.println(form.toString());    //데이터가 잘 받아졌는지 확인용  
    
            //1. DTO -> Entity  
            Article article = form.toEntity();  
    //        System.out.println(article.toString());  
            log.info(article.toString());  
    
            //2. Repository -> DB  
            Article saved = articleRepository.save(article);  
    //        System.out.println(saved);  
            log.info(saved.toString());  
    
            return "redirect:/articles/" + saved.getId();  // --> 추가
        }  
    
        ...
    }
    ```
    * `getId()`는 saved 객체의 id 값을 가져오는 메서드
<br/><br/>

3. 서버를 실행하여 <http://localhost:8080/articles/new> 로 접속하여 글을 추가했을 때 상세 페이지로 이동하는지 확인하자.
<br/><br/>

#### 상세 페이지 -> 목록 페이지
1. 상세 페이지를 보여주는 show.mustache 파일을 열어 아래 코드를 추가하자

    ```html
    ...
    </table>  
    
    <a href="/articles">Go to Article List</a>  
    
    { {>layouts/footer}}
    ```

2. 서버를 실행하고 <http://localhost:8080/articles/2> 로 이동 후 링크가 제대로 동작하는지 확인하자.
	* 꼭 2가 아니어도 상관 없다.
<br/><br/>

#### 목록 페이지 -> 상세 페이지
1. 목록 페이지의 뷰 파일인 index.mustache를 열어 아래 코드를 추가하자.

    ```html
    ...
    <th>{ {id}}</th>  
    <td><a href="/articles/{ {id}}">{ {title}}</a></td>  
    <td>{ {content}}</td>
    ...
    ```
    * id가 1이라면 1번의 상세 페이지로, 2라면 2번의 상세 페이지로 이동하는 코드
<br/><br/>

2. 서버를 실행하고, 데이터가 없다면 추가, 있다면 /articles로 접속 후 제목을 클릭했을 때, 상세 페이지로 이동하는지 확인
<br/><br/>

## 3. 연습 문제
<hr/>

* 3~5장에서 만든 회원 가입 페이지에서 아래와 같이 페이지 이동이 가능하게 만들기
	* 이메일, 비밀번호를 입력 후 제출 버튼을 클릭하면 입력 정보를 보여주는 상세 페이지로 이동
	* 상세 페이지에서 Go to member list를 클릭하면 목록 페이지로 이동
	* 목록 페이지에서 회원가입을 눌렀을 때 회원 가입 페이지로 이동
<br/><br/>

#### 답
* Member.java

```java
package com.example.Chapter_3_9.entity;  
  
import jakarta.persistence.Column;  
import jakarta.persistence.Entity;  
import jakarta.persistence.GeneratedValue;  
import jakarta.persistence.Id;  
import lombok.AllArgsConstructor;  
import lombok.Getter;  
import lombok.NoArgsConstructor;  
import lombok.ToString;  
  
@AllArgsConstructor  
@NoArgsConstructor  
@ToString  
@Getter  
@Entity  
public class Member {  
    ...
}
```
<br/>

* MemberController.java

```java
package com.example.Chapter_3_9.controller;  
  
import com.example.Chapter_3_9.dto.MemberForm;  
import com.example.Chapter_3_9.entity.Member;  
import com.example.Chapter_3_9.repository.MemberRepository;  
import lombok.extern.slf4j.Slf4j;  
import org.springframework.beans.factory.annotation.Autowired;  
import org.springframework.stereotype.Controller;  
import org.springframework.ui.Model;  
import org.springframework.web.bind.annotation.GetMapping;  
import org.springframework.web.bind.annotation.PathVariable;  
import org.springframework.web.bind.annotation.PostMapping;  
  
import java.util.List;  
  
@Controller  
@Slf4j  
public class MemberController {  
    ...
  
    @PostMapping("/join")  
    public String memberRegister(MemberForm memberForm) {  
        //DTO 확인용  
        System.out.println(memberForm.toString());  
  
        //1. DTO -> Entity  
        Member member = memberForm.toEntity();  
//        System.out.println(member.toString());  
        log.info(member.toString());  
  
        //2. Repository -> DB  
        Member saved = memberRepository.save(member);  
//        System.out.println(saved);  
        log.info(saved.toString());  
  
        return "redirect:/members/" + saved.getId();  
    }  
  
   ...
}
```
<br/>

* show.mustache

```html
...
</table>  
  
<a href="/members">Go to member list</a>  
  
{ {>layouts/footer}}
```
<br/>

* index.mustache

```html
...
</table>  
  
<a href="/signup">회원가입</a>  
  
{ {>layouts/footer}}
```