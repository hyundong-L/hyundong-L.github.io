---
layout: post
title: "4 - Lombok 사용하기"
author: "Hyundong Lee"
categories: Spring_Boot
tags: [Github, Blog, Develop]
date: 2024-02-28 02:00:30 +0900
---

## 1. Lombok이란?
<hr/>

* 코드를 간소화해 주는 라이브러리이며, 매번 getter, setter, 생성자, toString 메서드 등을 만드는 일은 번거로우며, 반복적인 작업을 없애기 위해 만들어지게 되었다.
* 우리는 맨 처음 실습부터 롬복을 사용하여 생성자, toString 메서드를 자동으로 생성해 사용하였고, 설명도 코드 밑에 했기 때문에 넘어가도록 하겠다.
<br/><br/>

## 2. Logging
<hr/>

* 이제까지 실습에서는 데이터를 `println`을 사용하여 확인하였지만, 실제 서버 개발에서 `println`은 사용하면 안된다.
	* 이유는 기록에 남지 않고, 서버 성능에도 악영향을 주기 때문이다.
<br/><br/>

* 그래서 로깅 기능을 사용하는 것이다.
	* 서버에서 일어나는 일을 모두 기록할 수 있으며, 나중에도 그동안 찍힌 로그를 확인할 수 있다.
	* 로깅을 사용하면 시간 정보까지 출력된다.
<br/><br/>

* 로깅을 사용하기 위해서는 `@Slf4j` 어노테이션을 사용해야 한다.
<br/><br/>

#### ArticleController의 println을 로깅으로 바꾸기(리팩터링하기)

```java
package com.example.Chapter_3_9.controller;  
  
import com.example.Chapter_3_9.dto.ArticleForm;  
import com.example.Chapter_3_9.entity.Article;  
import com.example.Chapter_3_9.repository.ArticleRepository;  
import lombok.extern.slf4j.Slf4j;  
import org.springframework.beans.factory.annotation.Autowired;  
import org.springframework.stereotype.Controller;  
import org.springframework.web.bind.annotation.GetMapping;  
import org.springframework.web.bind.annotation.PostMapping;  
  
@Controller  
@Slf4j  
public class ArticleController {  
    @Autowired  
    ArticleRepository articleRepository;  
  
    @GetMapping("/articles/new")  
    public String newArticle() {  
        return "articles/new";  
    }  
  
    @PostMapping("/articles/create")  
    public String createArticle(ArticleForm form) {  
        System.out.println(form.toString());    //데이터가 잘 받아졌는지 확인용  
  
        //1. DTO -> Entity  
        Article article = form.toEntity();  
//        System.out.println(article.toString());  
        log.info(article.toString());  // --> 추가
  
        //2. Repository -> DB  
        Article saved = articleRepository.save(article);  
//        System.out.println(saved);  
        log.info(saved.toString());  // --> 추가
  
        return "";  
    }  
}
```
* 위와 같이 코드를 변경하고, 실행시켜 값을 입력한 후 인텔리제이 실행창을 확인하면 로그가 찍혀있는 것을 확인할 수 있다.
<br/><br/>

## 3. 리팩터링
<hr/>

* 위에서 로깅으로 코드를 변경한 것과 같이 코드 기능에는 변함이 없지만, 구조나 성능을 개선하는 작업을 말한다.
<br/><br/>

* 장점
	* 코드의 길이가 짧아진다.
	* 코드의 가독성이 좋아진다.
<br/><br/>

## 4. 연습 문제
<hr/>

* 3 - 게시판 만들고 새 글 작성하기의 연습 문제에서 만든 MemberController.java를 롬복을 사용하여 리팩터링하기
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
import org.springframework.web.bind.annotation.GetMapping;  
import org.springframework.web.bind.annotation.PostMapping;  
  
@Controller  
@Slf4j  
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
//        System.out.println(member.toString());  
        log.info(member.toString());  
  
        //2. Repository -> DB  
        Member saved = memberRepository.save(member);  
//        System.out.println(saved);  
        log.info(saved.toString());  
          
        return "";  
    }  
}
```