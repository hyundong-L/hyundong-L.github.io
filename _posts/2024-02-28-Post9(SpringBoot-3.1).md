---
layout: post
title: "3.1 - 게시판 만들고 새 글 작성하기"
author: "Hyundong Lee"
categories: Spring_Boot
tags: [Github, Blog, Develop]
data: 2024-02-28 01:10:01 +0900
---

## 1. 폼 데이터
<hr/>

* HTML 요소인 `<form>` 태그에 실려 전송되는 데이터
	* ex) 인터넷 게시판에서 글을 쓰고 등록을 눌렀을 때, 이메일을 작성하고 전송을 눌렀을 때... 등등
	<br/><br/>
	* `<form>` 태그는 테이터를 웹에서 서버로 전송할 때 사용한다.
		* 택배와 같이 `<form>` 태그도 어디로, 어떻게 보낼지 적어서 데이터와 함께 보낸다.
<br/><br/>

## 2. DTO(Data Transfer Object)
<hr/>

* 이렇게 `<form>` 태그에 실어서 보낸 데이터를 서버에서 컨트롤러가 객체에 받아서 담는데, 데이터를 담는 객체를 DTO라고 한다.

* DTO에 담긴 데이터는 최종적으로 데이터베이스에 저장된다.
<br/><br/>

## 3. 정리
<hr/>

1. 웹 페이지에서 무언가를 입력 후 버튼을 누른다.
2. 버튼을 눌렀을 때, 웹 페이지의 `<form>` 태그가 입력 받은 정보와 부가적인 정보(어디로, 어떻게 보낼지)를 서버로 전송
3. 컨트롤러가 정보를 받을 때 DTO에 받는다.
4. DTO는 받은 정보를 데이터베이스에 저장
<br/><br/>

## 4. 폼 데이터를 DTO로 받기
<hr/>

#### 1. 입력 폼 만들기
1. main -> resources -> templates 우클릭, New -> Directory, 이름은 articles

2. articles 우클릭, New -> File, 파일명은 new.mustache로 뷰 페이지 만들기

3. 아래와 같이 코드 작성하기

```html
//new.mustache

{ {>layouts/header}}  
  
<form action=""> 
    <input type="text">  
    <textarea></textarea>
    <button type="submit">Submit</button>  
</form>  
  
{ {>layouts/footer}}
```
* form 태그는 입력 폼(양식)을 만드는 것
* input 태그는 입력 창을 만드는 것, text 형식을 넘기겠다는 의미
* textarea 태그는 여러 줄을 입력할 창을 만드는 것.
* button 태그는 버튼을 만드는 것, 태그 사이에 들어가는 글이 버튼의 글자가 되고, submit 타입은 눌렀을 때 제출된다는 의미
<br/><br/>

#### 2. 컨트롤러 만들기
1. main -> java -> com.example.~ -> controller에서 우클릭, New -> Java Class, 이름은 ArticleController

2. 아래 코드 작성하기

    ```java
    //ArticleController.java

    package com.example.Chapter_3_9.controller;  
    
    import org.springframework.stereotype.Controller;  
    import org.springframework.web.bind.annotation.GetMapping;  
    
    @Controller  
    public class ArticleController {  
        @GetMapping("/articles/new")  
        public String newArticle() {  
            return "articles/new";  
        }  
    }
    ```
    * 뷰 파일을 template의 articles 디렉토리 안에 만들었기 때문에 경로까지 반환해준다.
<br/><br/>

3. ~Application 파일을 실행시키고, http://localhost:8080/articles/new 로 접속해보자.
<br/><br/>

#### 3. 뷰 파일에 css 적용하기
* 화면이 별로 예쁘지 않으니 new.mustache 파일에 아래와 같이 css를 적용해보자

```html
//new.mustache

{ {>layouts/header}}  
  
<form class="container">  
    <div class="mb-3">  
        <label class="form-label">Title</label>  
        <input type="text" class="form-control">  
    </div>    
    <div class="mb-3">  
        <label class="form-label">Content</label>  
        <textarea class="form-control" rows="3"></textarea>  
    </div>    
    <button type="submit" class="btn btn-primary">Submit</button>  
</form>  
  
{ {>layouts/footer}}
```
* ~~코드를 다 작성하면 망치 모양 아이콘을 클릭하면 된다는데 보이지 않아서 포기합니다.~~
* 자바 코드를 변경하면 다시 실행시켜야 하지만, mustache 코드는 빌드만 다시 하면 된다.
<br/><br/>

1. 망치 모양 아이콘은 빌드를 다시 하는 것이며, 위 메뉴에 Build -> Build Project를 하면 된다.
	* 다시 실행시켜도 상관은 없다.

2. 다시 사이트를 새로고침 해보면 변경된 것을 확인할 수 있다.
<br/><br/>

#### 막간 - 빌드란?
* 프로그래밍을 하고 실행을 시킨다면 3가지 과정을 거쳐서 실행된다
	1. 코드를 컴파일 -> 바이트 코드로 변환
	2. 컴파일 한 파일을 실행 파일로 패키징
	3. 실행 파일을 실행
<br/><br/>
* 이러한 과정들을 빌드라고 한다.
<br/><br/>

#### 4. 폼 데이터 전송하기
* 위에서 만든 웹에서 내용을 적고 버튼을 누르면 아무 일도 일어나지 않는다.
	* `<form>` 태그에 필요한 부가적인 정보(어디로, 어떻게 보낼지)를 작성하지 않았기 때문이다.

* 아래 코드와 같이 뷰 파일에 부가적인 정보를 추가해주자.

```html
//new.mustache

...
<form class="container" action="/articles/create" method="post">
...
```
* action은 어디로, method는 어떻게에 해당한다.
<br/><br/>

#### 5. 폼 데이터 받기
* 이제 전송을 했으니 Controller에서 데이터를 받게 만들어보자

* ArticleController 파일을 열어 아래와 같이 추가해주자

```java
//ArticleController.java

package com.example.Chapter_3_9.controller;  
  
import org.springframework.stereotype.Controller;  
import org.springframework.web.bind.annotation.GetMapping;  
import org.springframework.web.bind.annotation.PostMapping;  
  
@Controller  
public class ArticleController {  
    ...
  
    @PostMapping("/articles/create")  
    public String createArticle() {  
        return "";  
    }  
}
```
* 이번에는 `@PostMapping`를 사용하였는데, 뷰 페이지에서 Post 방식으로 정보를 보냈으니 이 방식으로 받는 것이다.
	* 주소는 `<form>`태그의 action에 작성한 주소로 지정해주면 된다.
<br/><br/>

#### 막간 - GET, POST 방식 차이
* GET
	* 클라이언트에서 서버로 정보를 요청하기 위해 사용
		* ex) 게시판의 게시물 조회
* POST
	* 클라이언트에서 서버로 무언가를 생성하거나 업데이트 하기 위해 데이터를 보낼 떄 사용
		* ex) 게시판의 게시글을 작성
<br/><br/>

#### 6. DTO 받기
1. com.example.~ 우클릭, New -> Package, 이름은 dto

2. dto 우클릭, New -> Java Class, 이름은 ArticleForm
	* 이 파일이 데이터를 받아 올 DTO가 된다.

3. 아래와 같이 코드를 작성하자

```java
//ArticleForm.java

package com.example.Chapter_3_9.dto;  
  
import lombok.*;  
  
@AllArgsConstructor  
@ToString  
public class ArticleForm {  
    private String title;   //제목을 받을 필드  
    private String content; //내용을 받을 필드  
}
```
* 뷰에서 보낸 것이 2개(제목, 내용)였으니까 받을 때도 2개를 만들어 받아야 한다.
* 코드에서 붙인 3개의 어노테이션은 자동으로 만들어주는 어노테이션이며, 프로젝트를 만들 때 Lombok을 추가해야 사용할 수 있는 것이다.
	* `@AllArgsConstructor`
		* 모든 필드 값을 파라미터로 받는 생성자를 만들어준다.
	* `@toString`
		* 객체가 갖고 있는 값을 문자열로 반환해주는 메서드를 만들어준다.
			* 데이터를 제대로 받았는지 확인하기 위해 사용
<br/><br/>

#### 7. 폼 데이터를 DTO에 담기
* ArticleController 파일을 열고, 아래와 같이 추가하기

```java
//ArticleController.java

package com.example.Chapter_3_9.controller;  
  
import com.example.Chapter_3_9.dto.ArticleForm;  
import org.springframework.stereotype.Controller;  
import org.springframework.web.bind.annotation.GetMapping;  
import org.springframework.web.bind.annotation.PostMapping;  
  
@Controller  
public class ArticleController {  
    ...
  
    @PostMapping("/articles/create")  
    public String createArticle(ArticleForm form) {  
        System.out.println(form.toString());    //데이터가 잘 받아졌는지 확인용  
        return "";  
    }  
}
```
<br/>

#### 8. 입력 폼과 DTO 필드 연결하기
* DTO에 값이 들어가려면 뷰의 입력 폼에 DTO에서 선언한 필드의 이름을 작성해야 한다.
<br/><br/>

1. new.mustache를 열어 아래와 같이 추가한다.

    ```html
    //new.mustache

    ...
    <input type="text" class="form-control" name="title">  
    ...
    <textarea class="form-control" rows="3" name="content"></textarea>  
    ...
    ```

2. 서버를 재시작하여 http://localhost:8080/articles/new 로 접속하여 내용을 채운 뒤 버튼을 누르자

3. 다시 인텔리제이로 돌아와서 하단의 실행창에 마지막 줄에 보면 작성한 내용이 출력된 것을 확인할 수 있다.
<br/><br/>

## 5. DTO를 데이터베이스에 저장하기
<hr/>

#### 데이터베이스(DB)
* 데이터를 저장하고 관리하는 창고
* 엑셀 파일과 같이 행과 열로 구성된 테이블이라는 곳에 저장해 관리
* SQL이라는 언어를 사용
<br/><br/>
* 다양한 DB 프로그램이 존재하지만, 프로젝트를 만들 때 추가한 H2 DB를 사용할 것이다.
* JPA라는 자바로 DB에 명령을 내리는 도구를 사용할 것이다.
<br/><br/>

#### Entity(엔티티), Repository(리포지토리)
* Entity(엔티티)
	* 자바 객체를 DB가 이해할 수 있게 만든 것이며, 이를 기반으로 테이블이 만들어진다.
<br/><br/>
* Repository(리포지토리)
	* 엔티티가 DB의 테이블에 저장, 관리되게 하는 인터페이스
<br/><br/>

#### DTO를 DB에 저장하기 위한 순서
1. DTO를 엔티티로 변환
2. 리포지토리를 이용해 엔티티를 DB에 저장
<br/><br/>

#### 1. DTO를 엔티티로 변환하기
1. 엔티티 만들기
	1. com.example.~ 우클릭, New -> Package, 이름은 entity
	2. entity 우클릭, New -> Java Class, 이름은 Article로 만들고 아래 코드와 같이 작성

    ```java
    //Article.java

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
    public class Article {  
        @Id  
        @GeneratedValue    
        private Long id;  
        
        @Column  
        private String title;  
        
        @Column  
        private String content;  
    }
    ```
    * `@Entity`
        * 이 클래스가 엔티티임을 선언, 어노테이션이 붙은 클래스를 기반으로 DB에 테이블이 생성된다.
    * `@NoArgsConstructor`
        * 매개변수가 없는 기본 생성자를 자동으로 생성해준다.
    * `@Id`
        * 엔티티의 대표값을 지정해준다 -> 제목과 내용이 같은 것이 있어도 ID 값으로 구분할 수 있다.
            * 주민등록번호와 같은 역할 -> 중복되면 안된다.
    * `@GeneratedValue`
        * 위에서 말한 엔티티의 대표값을 자동으로 생성해준다.
    * `@Column`
        * DB 테이블의 각 열과 연결되게 선언하는 것
<br/><br/>

2. DTO를 엔티티로 변환하는 메서드 만들기
	1. ArticleForm 파일을 열어, 아래 코드와 같이 추가

    ```java
    //ArticleForm.java

    package com.example.Chapter_3_9.dto;  
    
    import com.example.Chapter_3_9.entity.Article;  
    import lombok.*;  
    
    @AllArgsConstructor  
    @ToString  
    public class ArticleForm {  
        ...
        
        public Article toEntity() {  
            return new Article(null, title, content);  
        }  
    }
    ```
    * 반환값은 Article의 생성자 입력 양식에 맞게 작성하면 되는데, 우리는 모든 필드의 값을 받는 생성자로 만들었고, 아직 ArticleForm에는 id 값을 추가하지 않았으니 null로 작성하면 된다.
<br/><br/>

3. ArticleController에 아래 코드 추가하기 - DTO를 엔티티로 변환

    ```java
    //ArticleController.java

    package com.example.Chapter_3_9.controller;  
    
    import com.example.Chapter_3_9.dto.ArticleForm;  
    import com.example.Chapter_3_9.entity.Article;  
    import org.springframework.stereotype.Controller;  
    import org.springframework.web.bind.annotation.GetMapping;  
    import org.springframework.web.bind.annotation.PostMapping;  
    
    @Controller  
    public class ArticleController {  
        @GetMapping("/articles/new")  
        public String newArticle() {  
            return "articles/new";  
        }  
    
        @PostMapping("/articles/create")  
        public String createArticle(ArticleForm form) {  
    //        System.out.println(form.toString());    //데이터가 잘 받아졌는지 확인용  
    
            //1. DTO -> Entity  
            Article article = form.toEntity();  --> 추가
            
            return "";  
        }  
    }
    ```
<br/>

#### 막간 - Entity에 기본 생성자가 필요한 이유
* 앞에서 JPA는 자바로 DB에 명령을 내리는 도구라고 했다. 엔티티 객체를 만들어 값을 넣는 일을 JPA가 하는데, 이때 Reflection API라는 것을 이용한다.
	* 이때 Reflection API로 객체를 생성하려면 기본 생성자가 필요한 것이다.
<br/><br/>

#### 2. 리포지토리로 엔티티를 DB에 저장하기
1. 리포지토리 만들기
	1. com.example.~ 우클릭, New -> Package, 이름은 repository
	2. repository 우클릭, New -> Java Class -> Interface, 이름은 ArticleRepository
	3. 아래 코드 추가하기

    ```java
    //ArticleRepository.java

    package com.example.Chapter_3_9.repository;  
    
    import com.example.Chapter_3_9.entity.Article;  
    import org.springframework.data.repository.CrudRepository;  
    
    public interface ArticleRepository extends CrudRepository<Article, Long> {  
    }
    ```
    * CrudRepository 인터페이스는 JPA에서 제공하며, 엔티티 관리(생성, 조회, 수정, 삭제)를 할 수 있게 만들어준다.
        * <> 안에는 엔티티 클래스 타입과 대표값 타입을 넣어주면 되고, 이렇게 작성하면 별도의 정의 없이 기능을 그대로 사용할 수 있다.
<br/><br/>

2. ArticleController에 코드 추가하기 - 리포지토리로 DB에 저장하기

    ```java
    //ArticleController.java

    package com.example.Chapter_3_9.controller;  
    
    import com.example.Chapter_3_9.dto.ArticleForm;  
    import com.example.Chapter_3_9.entity.Article;  
    import com.example.Chapter_3_9.repository.ArticleRepository;  
    import org.springframework.stereotype.Controller;  
    import org.springframework.web.bind.annotation.GetMapping;  
    import org.springframework.web.bind.annotation.PostMapping;  
    
    @Controller  
    public class ArticleController {  
        @Autowired
        ArticleRepository articleRepository;  
        
        ...
    
        @PostMapping("/articles/create")  
        public String createArticle(ArticleForm form) {  
            ...
    
            //2. Repository -> DB  
            Article saved = articleRepository.save(article);  
            
            return "";  
        }  
    }
    ```
    * `@Autowired`는 스프링부트가 만든 객체를 주입해준다. 아래 의존성 주입 설명을 참고하기.
    * 리포지토리가 엔티티를 저장하고, saved라는 엔티티 변수에 반환하는 코드
<br/><br/>

#### 3. 데이터 저장 확인하기
* 순서
    1. DTO가 엔티티로 잘 변환되었는지 확인
    2. article이 DB에 잘 저장되었는지 확인
<br/><br/>

* 아래와 같이 각 역할을 하는 코드 밑에 출력 코드를 작성해보자.

```java
//ArticleController.java

...
  
@Controller  
public class ArticleController {  
    ...
  
    @PostMapping("/articles/create")  
    public String createArticle(ArticleForm form) {  
		System.out.println(form.toString());    //데이터가 잘 받아졌는지 확인용
		
        //1. DTO -> Entity  
        Article article = form.toEntity();  
        System.out.println(article.toString());  --> 추가
  
        //2. Repository -> DB  
        Article saved = articleRepository.save(article);  
        System.out.println(saved.toString());  --> 추가
  
        return "";  
    }  
}
```

1. 서버를 시작하고, http://localhost:8080/articles/new 로 접속 후, 내용을 입력하고 버튼을 클릭
2. 인텔리제이로 돌아와서 하단 실행 창을 확인하면 아래와 같은 결과들이 나와야 한다.
	1. ArticleForm(title=1111, content=1111) - DTO
	2. Article(id=null, title=1111, content=1111) - Entity
	3. Article(id=1, title=1111, content=1111) - Repository
<br/><br/>

## 6. 의존성 주입(Dependency Injection)
<hr/>

* 원래 자바에서는 구현 객체를 new 키워드로 만들어야 했다.
* 하지만 스프링에서는 컨테이너에 자동으로 객체를 먼저 생성해두고, `@Autowired` 어노테이션을 붙이면 미리 생성했던 객체를 가져와서 연결해준다.

* 장점
	- 코드 재사용을 높인다.
	* 객체 간의 관계를 동적으로 주입하여 유연성을 확보하고, 결합도를 낮출 수 있음