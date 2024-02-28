---
layout: post
title: "5.2 - 게시글 읽기"
author: "Hyundong Lee"
categories: Spring_Boot
tags: [Github, Blog, Develop]
date: 2024-02-28 20:22:41 +0900
---

## 1. 데이터 목록 조회
<hr/>

* 단일 데이터와 다른 점은 엔티티의 묶음인 리스트로 반환된다는 것이다.
<br/><br/>

#### 1. URL 요청 받기
* /articles라는 URL 요청이 들어왔을 때 처리하게 만들 것이다.

1. ArticleController를 열어 아래 코드를 추가하자

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
  
@Controller  
@Slf4j  
public class ArticleController {  
    ...
  
    @GetMapping("/articles")  
    public String index() {  
        return "";  
    }  
}
```
<br/>

#### 2. 데이터 조회 후 출력
* 순서
	1. DB에서 모든 Article 데이터 가져오기
	2. 가져온 Article 묶음을 모델에 등록
	3. 사용자에게 보여 줄 뷰 설정
<br/><br/>

###### 1. 모든 데이터 가져오기
* 단일 데이터 출력에서도 말했지만 데이터를 DB에서 가져오려면 리포지토리가 필요하다.
* ArticleController를 열어 아래와 같이 작성하자

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
  
    @GetMapping("/articles")  
    public String index() {  
        List<Article> articleEntityList = articleRepository.findAll();  
          
        return "";  
    }  
}
```
* 위와 같이 작성하면 반환하는 타입과 작성한 타입이 달라서 오류가 발생한다.
* 다양한 해결 방법이 있지만, 우리는 `.findAll()`의 반환 타입을 변경할 것이다. 아래와 같이 해보자.
	1. ArticleRepository를 열어 중괄호 안에서 우클릭 -> Generate -> Override Methods 클릭
	2. `findAll():Iterable<T>` 선택
	3. 맨 앞의 `Iterable`을 `ArrayList`로 변경
<br/><br/>

###### 2. 모델에 데이터 등록
1. model 객체 받아오기
2. 모델에 데이터 묶음 등록 - "articleList"라는 이름으로 등록

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
  
    @GetMapping("/articles")  
    public String index(Model model) {  
        //1. 데이터 가져오기  
        List<Article> articleEntityList = articleRepository.findAll();  
  
        //2. 모델에 데이터 등록  
        model.addAttribute("articleList", articleEntityList);  
  
        return "";  
    }  
}
```
<br/>

#### 막간 - 타입 불일치 문제 해결
* 모든 데이터를 DB로부터 가져올 때 반환 타입과 작성한 타입이 달랐다.
* 우리는 반환 타입을 변경해줬지만, 다른 방법들도 알아보자
<br/><br/>

* 메서드가 반환하는 타입을 사용자가 작성한 타입으로 형변환(캐스팅)하기
	* `List<Article> articleEntityList = (List<Article>) articleRepository.findAll();`
* 사용자가 작성한 타입을 반환 타입으로 변경하기
	* `Iterable<Article> articleEntityList = articleRepository.findAll();`
<br/><br/>

#### 3. 뷰 반환
###### 1. 뷰 만들기
1. templates/articles에 index.mustache 파일 만들기
2. 단일 데이터 조회에서 작성한 show 뷰 파일을 복사, 붙여넣기
3. \{\{#article\}\}, \{\{/article\}\}의 article을 articleList로 변경

```html
{ {>layouts/header}}  
  
<table class="table">  
    <thead>    
    <tr>        
    <th scope="col">Id</th>  
        <th scope="col">Title</th>  
        <th scope="col">Content</th>  
    </tr>    
    </thead>  
    <tbody>    
    { {#articleList}}  
        <tr>  
            <th>{ {id}}</th>  
            <td>{ {title}}</td>  
            <td>{ {content}}</td>  
        </tr>    
    { {/articleList}}  
    </tbody>  
</table>  
  
{ {>layouts/footer}}
```
* mustache에서 사용한 변수가 리스트(데이터 묶음)일 때, 내부 코드가 반복된다.
<br/><br/>

###### 2. 컨트롤러에 뷰를 반환하게 만들기

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
  
    @GetMapping("/articles")  
    public String index(Model model) {  
        //1. 데이터 가져오기  
        List<Article> articleEntityList = articleRepository.findAll();  
  
        //2. 모델에 데이터 등록  
        model.addAttribute("articleList", articleEntityList);  
  
        return "articles/index";  
    }  
}
```
<br/>

#### 4. 실행해보기
1. 서버를 시작
2. <http://localhost:8080/articles/new> 로 접속 후 데이터를 추가
3. 실행창에 정보가 잘 추가되었는지 나오므로 확인하고, <http://localhost:8080/articles> 으로 접속해보자
<br/><br/>

## 2. 연습 문제
<hr/>

* 3장에서 만든 회원 가입 페이지에 회원 조회 기능을 넣어보자
<br/><br/>

* 특정 회원 조회 URL 요청
	* /members/id 값
* 모든 회원 조회 URL 요청
	* /members
* 뷰 페이지는 위에서 만든 게시글 뷰를 수정해서 사용하자
<br/><br/>

#### 답
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
  
    @GetMapping("/members/{id}")  
    public String show(@PathVariable Long id, Model model) {  
        //1. id 값으로 DB에서 데이터 받아오기  
        Member memberEntity = memberRepository.findById(id).orElse(null);  
  
        //2. 모델에 데이터 등록  
        model.addAttribute("member", memberEntity);  
  
        return "members/show";  
    }  
  
    @GetMapping("/members")  
    public String index(Model model) {  
	    //1. 데이터 가져오기
        List<Member> memberEntityList = memberRepository.findAll();  
		
		//2. 모델에 데이터 등록
        model.addAttribute("memberList", memberEntityList);  
  
        return "members/index";  
    }  
}
```
<br/>

* MemberRepositoy.java

```java
package com.example.Chapter_3_9.repository;  
  
import com.example.Chapter_3_9.entity.Member;  
import org.springframework.data.repository.CrudRepository;  
  
import java.util.ArrayList;  
  
public interface MemberRepository extends CrudRepository<Member, Long> {  
    @Override  
    ArrayList<Member> findAll();  
}
```
<br/>

* show.mustache

```html
{ {>layouts/header}}  
  
<table class="table">  
    <thead>    
    <tr>        
    <th scope="col">Id</th>  
        <th scope="col">Email</th>  
        <th scope="col">Password</th>  
    </tr>    
    </thead>  
    <tbody>    
    { {#member}}  
        <tr>  
            <th>{ {id}}</th>  
            <td>{ {email}}</td>  
            <td>{ {password}}</td>  
        </tr>    
    { {/member}}  
    </tbody>  
</table>  
  
{ {>layouts/footer}}
```
<br/>

* index.mustache

```html
{ {>layouts/header}}  
  
<table class="table">  
    <thead>    
    <tr>        
    <th scope="col">Id</th>  
        <th scope="col">Email</th>  
        <th scope="col">Password</th>  
    </tr>    
    </thead>  
    <tbody>    
    { {#memberList}}  
        <tr>  
            <th>{ {id}}</th>  
            <td>{ {email}}</td>  
            <td>{ {password}}</td>  
        </tr>    
    { {/memberList}}  
    </tbody>  
</table>  
  
{ {>layouts/footer}}
```