---
layout: post
title: "2.2 - MVC 패턴 이해와 실습"
author: "Hyundong Lee"
categories: Spring_Boot
tags: [Github, Blog, Develop]
---

## 1. 뷰에 레이아웃 적용하기
<hr/>

#### 레이아웃
* 화면에 요소를 배치하는 일

* 헤더-푸터 레이아웃을 적용할 것이다. 가장 기본이 되며, 샌드위치 같은 구조이다.
	* 헤더
		* 사이트 안내를 위한 정보(내비게이션)
	* 콘텐트
		* 사용자가 보는 내용
	* 푸터
		* 사이트 정보
<br/><br/>

#### "/hi" 페이지에 적용
1. 부트스트랩 (https://getbootstrap.com) 으로 접속
	* 웹 페이지를 쉽게 만들 수 있게, 레이아웃, 버튼 등의 디자인을 미리 만들어 놓은 사이트
<br/><br/>

2. 오른쪽 위에 버전을 5.0.2로 바꾸고 오른쪽 목차에 Starter template을 클릭하여 코드를 복사
<br/><br/>

3. greetings.mustache 파일 내용을 다 지우고 복사한 내용으로 바꾸고, 주석은 헷갈리니까 다 지우자.
<br/><br/>

4. `<h1></h1>`부분을 지우고 아래와 같이 아까 작성한 코드로 바꾸자 -> 콘텐트
	* `<h1>{ {username}}님, 안녕하세요~</h1>`
<br/><br/>

5. 헤더에 내비게이션 바를 추가하기 위해 다시 부트스트랩으로 가서 버전을 5.0.2로 바꾸고, 왼쪽 위 검색 창에 navbar를 입력하고 나오는 것을 선택
<br/><br/>	

6. 내리다 보면 코드가 나오는데 복사하여 아까 작성한 `<h1></h1>` 태그 위에 붙여넣자
<br/><br/>

7. 이제 푸터에 사이트 정보를 추가할 것이고, 아래 코드를 `<script></script>` 위에 작성하자

    ```html
    <div class="mb-5 container-fluid">  
        <hr>
        <p>CloudStudying : <a href="#">Privacy</a> : <a href="#">Terms</a></p>
    </div>
    ```
    * 이때 a 태그에 '#'은 클릭했을 때 페이지 전환이 되지 않게 하는 것이다.
<br/><br/>

8. 콘텐트 부분의 간격을 주기 위해서 아래 코드와 같이 작성해보자

    ```html
    <div class="bg-dark text-white p-5">  
        <h1>Hello, world!</h1>  
    </div>
    ```

9. 다 작성했으면 ~Application 파일을 실행시키고, http://localhost:8080/hi 로 접속해보자
<br/><br/>

* 전체 코드

```html
<!doctype html>
<html lang="en">
<head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">

    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.0.2/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-EVSTQN3/azprG1Anm3QDgpJLIm9Nao0Yz1ztcQTwFspd3yD65VohhpuuCOmLASjC" crossorigin="anonymous">

    <title>Hello, world!</title>
</head>
<body>
<!--Navigation-->
    <nav class="navbar navbar-expand-lg navbar-light bg-light">
        <div class="container-fluid">
            <a class="navbar-brand" href="#">Navbar</a>
            <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarSupportedContent" aria-controls="navbarSupportedContent" aria-expanded="false" aria-label="Toggle navigation">
                <span class="navbar-toggler-icon"></span>
            </button>
            <div class="collapse navbar-collapse" id="navbarSupportedContent">
                <ul class="navbar-nav me-auto mb-2 mb-lg-0">
                    <li class="nav-item">
                        <a class="nav-link active" aria-current="page" href="#">Home</a>
                    </li>
                    <li class="nav-item">
                        <a class="nav-link" href="#">Link</a>
                    </li>
                    <li class="nav-item dropdown">
                        <a class="nav-link dropdown-toggle" href="#" id="navbarDropdown" role="button" data-bs-toggle="dropdown" aria-expanded="false">
                            Dropdown
                        </a>
                        <ul class="dropdown-menu" aria-labelledby="navbarDropdown">
                            <li><a class="dropdown-item" href="#">Action</a></li>
                            <li><a class="dropdown-item" href="#">Another action</a></li>
                            <li><hr class="dropdown-divider"></li>
                            <li><a class="dropdown-item" href="#">Something else here</a></li>
                        </ul>
                    </li>
                    <li class="nav-item">
                        <a class="nav-link disabled" href="#" tabindex="-1" aria-disabled="true">Disabled</a>
                    </li>
                </ul>
                <form class="d-flex">
                    <input class="form-control me-2" type="search" placeholder="Search" aria-label="Search">
                    <button class="btn btn-outline-success" type="submit">Search</button>
                </form>
            </div>
        </div>
    </nav>

<!--Content-->
    <div class="bg-dark text-white p-5">
        <h1>Hello, world!</h1>
    </div>

<!--Footer-->
    <div class="mb-5 container-fluid">
        <hr>
        <p>CloudStudying : <a href="#">Privacy</a> : <a href="#">Terms</a></p>
    </div>
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.0.2/dist/js/bootstrap.bundle.min.js" integrity="sha384-MrcW6ZMFYlzcLA8Nl+NtUVF0sA7MsXsP1UyJoMp4YLEuNSfAP+JcXn/tWtIaxVXM" crossorigin="anonymous"></script>
</body>
</html>
```
<br/>

## 2. 레이아웃 템플릿
<hr/>

* "/bye" 페이지도 "/hi" 페이지처럼 변경할 것인데, 이때 똑같이 작성해도 되지만 너무 비효율적이므로 "/hi" 페이지를 템플릿화해서 사용할 것이다.
	* 이때 템플릿화는 코드를 하나의 틀로 만들어 변수화한다는 말인데, 아래 예시를 보자.
<br/><br/>

#### 템플릿 파일 만들기
1. templates 디렉토리(폴더)에서 우클릭, New -> Directory 클릭, 이름은 layouts으로 만들자.
	
2. 만든 디렉토리에서 우클릭, New -> File, header.mustache 파일과 footer.mustache 파일을 만들자.
	
3. greetings.mustache 파일에서 `<!doctype html> ~ </nav>`(헤더)까지 자른 후, header.mustache에 가져오자
	
4. 이제 greetings.mustache 파일에는 상단 헤더 부분이 없으니까 템플릿으로 대체해야 한다.
	* \{\{ >파일명 \}\}으로 작성 -> \{\{ >layouts/header \}\}
	
5. greetings.mustache 파일에서 `<div class="mb-5 container-fluid"> ~ </html>`까지 잘라서 footer.mustache에 가져오자.
	
6. greetings.mustache 파일에 푸터 부분에는 헤더와 같은 방식으로 \{\{ >layouts/footer \}\}를 작성하면 된다.
<br/><br/>

* header.mustache

```html
<!doctype html>
<html lang="en">
<head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">

    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.0.2/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-EVSTQN3/azprG1Anm3QDgpJLIm9Nao0Yz1ztcQTwFspd3yD65VohhpuuCOmLASjC" crossorigin="anonymous">

    <title>Hello, world!</title>
</head>
<body>
<!--Navigation-->
<nav class="navbar navbar-expand-lg navbar-light bg-light">
    <div class="container-fluid">
        <a class="navbar-brand" href="#">Navbar</a>
        <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarSupportedContent" aria-controls="navbarSupportedContent" aria-expanded="false" aria-label="Toggle navigation">
            <span class="navbar-toggler-icon"></span>
        </button>
        <div class="collapse navbar-collapse" id="navbarSupportedContent">
            <ul class="navbar-nav me-auto mb-2 mb-lg-0">
                <li class="nav-item">
                    <a class="nav-link active" aria-current="page" href="#">Home</a>
                </li>
                <li class="nav-item">
                    <a class="nav-link" href="#">Link</a>
                </li>
                <li class="nav-item dropdown">
                    <a class="nav-link dropdown-toggle" href="#" id="navbarDropdown" role="button" data-bs-toggle="dropdown" aria-expanded="false">
                        Dropdown
                    </a>
                    <ul class="dropdown-menu" aria-labelledby="navbarDropdown">
                        <li><a class="dropdown-item" href="#">Action</a></li>
                        <li><a class="dropdown-item" href="#">Another action</a></li>
                        <li><hr class="dropdown-divider"></li>
                        <li><a class="dropdown-item" href="#">Something else here</a></li>
                    </ul>
                </li>
                <li class="nav-item">
                    <a class="nav-link disabled" href="#" tabindex="-1" aria-disabled="true">Disabled</a>
                </li>
            </ul>
            <form class="d-flex">
                <input class="form-control me-2" type="search" placeholder="Search" aria-label="Search">
                <button class="btn btn-outline-success" type="submit">Search</button>
            </form>
        </div>
    </div>
</nav>
```
<br/>

* footer.mustache

```html
//footer.mustache

<!--Footer-->
<div class="mb-5 container-fluid">
    <hr>
    <p>CloudStudying : <a href="#">Privacy</a> : <a href="#">Terms</a></p>
</div>
<script src="https://cdn.jsdelivr.net/npm/bootstrap@5.0.2/dist/js/bootstrap.bundle.min.js" integrity="sha384-MrcW6ZMFYlzcLA8Nl+NtUVF0sA7MsXsP1UyJoMp4YLEuNSfAP+JcXn/tWtIaxVXM" crossorigin="anonymous"></script>
</body>
</html>
```
<br/>

* greetings.mustache

```html
{ {>layouts/header}}  
  
<!--Content-->  
<div class="bg-dark text-white p-5">  
    <h1>{{username}}님, 안녕하세요~</h1>  
</div>  
  
{ {>layouts/footer}}
```
<br/>

## 3. "/bye" 페이지 변경
<hr/>

* "/bye" 페이지도 "/hi" 페이지처럼 변경하고, 콘텐트 부분에 디자인을 넣어주자

```html
//goodbye.mustache

{ {>layouts/header}}  
  
<div class="bg-dark text-white p-5">  
    <h1>{{nickname}}님, 안녕히가세요~</h1>  
</div>  
  
{ {>layouts/footer}}
```
<br/>

## 4. 연습 문제
<hr/>

* 랜덤하게 단어가 선택되어 출력되게 만들어보자
	* "/random-quote"로 접속했을 때 출력되게 하자.
	* 함수 이름은 randomQuote, FirstController에 추가하자.
	* 뷰 이름은 quote.mustache
	* 변수 이름은 randWord
    <br/><br/>
	* 랜덤 함수 사용법
	    * `int 변수명 = (int) (Math.random() * 단어를 담은 배열.length);`
<br/><br/>

#### 답
* quote.mustache

```html
{ {>layouts/header}}  
	  
<div class="bg-dark text-white p-5">  
    { {randWord}}  
</div>  
	  
{ {>layouts/footer}}
```
	
* FirstController.java

```java
package com.example.FirstProject.controller;  
  
import org.springframework.stereotype.Controller;  
import org.springframework.ui.Model;  
import org.springframework.web.bind.annotation.GetMapping;  
	  
@Controller  
public class FirstController {  
    ...
	  
    @GetMapping("random-quote")  
    public String randomQuote(Model model) {  
        String[] word = {"안녕", "잘가", "반가워", "조심히 가", "재밌다"};  
	  
        int randInt = (int) (Math.random() * word.length);  
        model.addAttribute("randWord", word[randInt]);  
  
        return "quote";  
    }  
}
```