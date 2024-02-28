---
layout: post
title: "5.1 - 게시글 읽기"
author: "Hyundong Lee"
categories: Spring_Boot
tags: [Github, Blog, Develop]
date: 2024-02-28 20:00:36 +0900
---

## 1. 데이터 조회 과정
<hr/>

* 웹 페이지에서 게시글을 등록하면 서버를 통해 DB에 저장되는데, 이렇게 저장한 데이터를 출력하려면 아래의 과정을 거친다.
	1. 사용자가 데이터를 조회해 달라고 웹에서 URL 요청을 보낸다.
	2. 서버의 컨트롤러가 요청을 받아서 해당 URL에서 받으려는 데이터 정보를 리포지토리에 전달
	3. 리포지토리는 정보를 갖고 DB에 데이터 조회 요청
	4. DB는 데이터를 찾아 엔티티로 반환
	5. 반환된 엔티티는 모델을 통해 뷰로 전달
	6. 최종적으로 결과 뷰 페이지가 완성되어 화면에 출력
<br/><br/>

![Img Alt Data inquiry](/assets/img/for_post/Data%20inquiry%20process.jpeg)
<br/><br/>

## 2. 단일 데이터 조회
<hr/>

* DB에 저장한 데이터를 웹에서 보려면 해당 출력 페이지에 접속해야 하고, 그러려면 URL 요청이 필요하다.
	* ex)
		* id가 1인 게시글 -> /articles/1
		* id가 2인 게시글 -> /articles/2
<br/><br/>

#### 1. URL 요청 받기
* 먼저 요청을 받을 컨트롤러를 만들어야 한다.
<br/><br/>

1. ArticleController를 열어 아래 코드를 추가하자.

    ```java
    package com.example.Chapter_3_9.controller;  
    
    import com.example.Chapter_3_9.dto.ArticleForm;  
    import com.example.Chapter_3_9.entity.Article;  
    import com.example.Chapter_3_9.repository.ArticleRepository;  
    import lombok.extern.slf4j.Slf4j;  
    import org.springframework.beans.factory.annotation.Autowired;  
    import org.springframework.stereotype.Controller;  
    import org.springframework.web.bind.annotation.GetMapping;  
    import org.springframework.web.bind.annotation.PathVariable;  
    import org.springframework.web.bind.annotation.PostMapping;  
    
    @Controller  
    @Slf4j  
    public class ArticleController {  
        ...
    
        @GetMapping("/articles/{id}")  
        public String show(@PathVariable Long id) {  
            log.info("id = " + id);  
            return "";   
        }  
    }
    ```
    * URL 요청을 받기 위해 `@GetMapping`을 사용하며, id 값에 따라 URL이 바뀌어야 하니까 id 값을 변수로 사용해야 한다.
        * 컨트롤러에서는 변수로 사용하려면 중괄호 하나만 사용하면 된다.
    * show 메서드의 매개변수에 `@PathVariable`은 URL 요청으로 들어온 전달값을 매개변수로 받겠다는 것.
    * 로깅을 사용해 id 값이 제대로 받아졌는지 확인
<br/><br/>

2. 서버를 시작하고, <http://localhost:8080/articles/1000> 으로 접속하면 실행창에 id 값이 잘 전달된 것을 확인할 수 있다.
<br/><br/>

#### 2. 데이터 조회 후 출력
###### 순서
1. id를 조회, DB에서 데이터 가져오기
2. 가져온 데이터를 모델에 등록
3. 조회한 데이터를 사용자에게 보여주기 위한 뷰를 만들고 반환
<br/><br/>

###### 1. id를 조회하고 데이터 가져오기
* 3장에서 만든 articleRepository를 사용하여 DB에서 데이터를 가져오겠다.
	* DB에서 데이터를 가져오려면 리포지토리가 꼭 필요하다.
* ArticleController를 열어 아래처럼 코드를 추가하자

```java
package com.example.Chapter_3_9.controller;  
  
import com.example.Chapter_3_9.dto.ArticleForm;  
import com.example.Chapter_3_9.entity.Article;  
import com.example.Chapter_3_9.repository.ArticleRepository;  
import lombok.extern.slf4j.Slf4j;  
import org.springframework.beans.factory.annotation.Autowired;  
import org.springframework.stereotype.Controller;  
import org.springframework.web.bind.annotation.GetMapping;  
import org.springframework.web.bind.annotation.PathVariable;  
import org.springframework.web.bind.annotation.PostMapping;  
  
@Controller  
@Slf4j  
public class ArticleController {  
    ...
  
    @GetMapping("/articles/{id}")  
    public String show(@PathVariable Long id) {  
        log.info("id = " + id);  
  
        Article articleEntity = articleRepository.findById(id).orElse(null);  
          
        return "";  
    }  
}
```
* `.findById`는 id 값을 이용하여 데이터를 찾는 메서드
* `.orElse(null)`은 만약 값이 없다면 null을 반환한다는 의미이다.
<br/><br/>

###### 2. 모델에 데이터 등록
1. Model 객체 받아오기
2. 모델에 데이터 등록 - `addAttribute()` 사용, "article"이라는 이름으로 등록

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
  
    @GetMapping("/articles/{id}")  
    public String show(@PathVariable Long id, Model model) {  // --> 추가
        log.info("id = " + id);  
  
        ...
  
        //2. 모델에 데이터 등록  
        model.addAttribute("article", articleEntity);  // --> 추가
  
        return "";  
    }  
}
```
<br/><br/>

#### 3. 뷰 반환
###### 1. 뷰 만들기
1. 데이터를 보여줄 뷰 페이지를 작성할 것이며, templates/articles에 show.mustache 만들기

2. 헤더와 푸터 작성

3. 부트스트랩(<https://getbootstrap.com>)으로 접속 
	1. 버전 5.02를 선택
	2. table을 검색, 가장 위에 있는 Tables 선택 후 가장 먼저 나오는 코드 복사
	4. 헤더와 푸터 사이 붙여넣기

4. 붙여넣은 표를 우리가 사용할 형태로 수정
	1. 제목 행의 이름 수정
	2. 필요 없는 코드 삭제
	3. 모델에 저장한 데이터를 받을 수 있게 수정
<br/><br/>

* show.mustache

```html
{ {>layouts/header}}  
  
<table class="table">  
    <thead>    
    <tr>        
    <th scope="col">Id</th>  //4-1
        <th scope="col">Title</th>  //4-1
        <th scope="col">Content</th>  //4-1
    </tr>    
    </thead>        
    
    <tbody>  
    { {#article}}  //4-3
    <tr>  
        <th>{ {id}}</th>  //4-3  
        <td>{ {title}}</td>   //4-3
        <td>{ {content}}</td>   //4-3
    </tr>    
    { {/article}}  //4-3
    </tbody>  
</table>  
  
{ {>layouts/footer}}
```
* 모델에 등록한 데이터는 뷰에서 이중 중괄호(\{\{\}\})로 출력하며, \{\{#article\}\} ~ \{\{/article\}\}처럼 `#`으로 열고, `/`으로 닫는다
	* 가져온 데이터를 사용할 때 범위를 지정하는 것이다.
* \{\{id\}\}, \{\{title\}\}, \{\{content\}\}는 article에 담긴 정보들을 가져오는 것이다.
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
  
@Controller  
@Slf4j  
public class ArticleController {  
    ...
  
    @GetMapping("/articles/{id}")  
    public String show(@PathVariable Long id, Model model) {  
        ...
  
        return "articles/show";  
    }  
}
```
<br/><br/>

#### 4. 실행해보기
1. 서버를 시작
2. 우리가 사용하는 H2 DB는 데이터가 계속 날아간다고 했으므로, <http://localhost:8080/articles/new> 로 접속 후 데이터를 추가
3. 실행창을 보면 id 값과 정보가 추가되었는지 나오므로 확인하여 "http://localhost:8080/articles/id_값" 으로 접속해보자