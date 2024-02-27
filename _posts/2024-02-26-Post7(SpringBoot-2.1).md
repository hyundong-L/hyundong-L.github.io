---
layout: post
title: "2.1 - MVC 패턴 이해와 실습"
author: "Hyundong Lee"
categories: Spring_Boot
tags: [Github, Blog, Develop]
---

## 1. 뷰 템플릿
<hr/>

* 화면을 담당하는 기술, 웹 페이지를 하나의 틀로 만들고, 변수를 삽입해 서로 다른 페이지를 보여준다
	* ex) 로그인 했을 때, 닉네임을 보여주는 것

* 스프링 부트 프로젝트를 만들 때, 추가한 Mustache가 뷰 템플릿을 만드는 도구이다.
<br/><br/>

## 2. MVC 패턴
<hr/>

* Model
	* 데이터를 관리하는 역할

* View
	* 뷰 템플릿을 간단히 부르는 이름

* Controller
	* 클라이언트의 요청을 서버에서 처리하는 역할
<br/><br/>

* 이렇게 세 가지로 역할을 나누는 기법을 MVC 패턴이라고 한다.
<br/><br/>

* MVC 패턴을 그림으로 표현
    ![Image Alt MVC_pattern](/assets/img/for_post/MVC_img.jpeg)
<br/><br/>

## 3. MVC를 활용해 뷰 만들기
<hr/>

1. 스프링 부트 실습 - 1에서 만든 프로젝트를 열자
<br/><br/>
2. src -> main -> resources -> templates에서 우클릭, New -> File 선택, greetings.mustache로 파일 생성
	* 앞의 실습에서는 static에 html 파일을 만들었지만, 뷰는 templates에 만든다.
<br/><br/>
3. 만약 파일을 생성하고, `Plugins supporting *.mustache ~`라고 나오면 Install ~을 클릭, 창이 뜨면 Ok를 눌러 설치하면 된다.
	* 만약 뜨지 않거나, 실수로 지워버렸다면 Settings -> plugins -> Marketplace -> mustache 검색 후 설치하기
<br/><br/>
4. 이제 만든 mustache 파일을 열어 `! + Tab` or `doc를 입력 후 Tab`을 하면 기본 형식이 자동으로 작성된다.
<br/><br/>
5. 아래 코드와 같이 `<body></body>` 부분에 내용을 추가하자

```html
<!doctype html>  
<html lang="en">  
<head>  
    <meta charset="UTF-8">  
    <meta name="viewport"  
          content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">  
    <meta http-equiv="X-UA-Compatible" content="ie=edge">  
    <title>Document</title>  
</head>  
<body>  
    <h1>안녕하세요!</h1>  
</body>  
</html>
```

* 만든 뷰 페이지를 웹에서 보려면 컨트롤러와 모델을 만들어야 한다.
<br/><br/>

## 4. 컨트롤러 만들기
<hr/>

1. src -> main -> java에 있는 패키지(폴더)를 우클릭, New -> Package 클릭, 기본 패키지명 뒤에 `controller` 추가
	* ex) 기본 패키지 -> `com.example.firstproject.controller`

2. 만든 controller 패키지 우클릭, New -> Java Class 클릭, FirstController로 클래스 만들기

3. 아래와 같이 작성하기

    ```java
    package com.example.FirstProject.controller;  
    
    import org.springframework.stereotype.Controller;  
    import org.springframework.web.bind.annotation.GetMapping;  
    
    @Controller  
    public class FirstController {  
        @GetMapping("/hi")  
        public String niceToMeetyou() {  
            return "greetins";  
        }  
    }
    ```

4. 이제 ~Application 파일을 실행시키고, http://localhost:8080/hi 로 접속하면 페이지가 잘 뜨는 것을 확인할 수 있다.
	* 이때 웹 페이지에 글자가 아닌 물음표로 나온다면 application.properties 파일에 아래 명령 추가하기
		* `server.servlet.encoding.force-response=true`
<br/><br/>

#### 코드 설명
* 이때 `@Controller`와 같은 `@`가 붙는 것을 어노테이션(Annotation)이라고 부르며, 컴파일과 실행 과정에서 코드를 어떻게 처리할 지 스프링 부트에게 알려주는 역할이다.
* `@GetMapping("/hi")`은 http://localhost:8080/hi 로 접속하면 해당 메서드를 실행하라는 뜻이다.
	* 스프링은 어노테이션이 중요합니다~
<br/><br/>

* `return "greetins";`은 서버가 알아서 greetings.mustache 파일을 찾아서 웹 브라우저로 보낸다는 것이다.
	* return 할 값은 보여줄 뷰 파일의 이름만 적으면 된다.
<br/><br/>

#### import 문제 발생 시 해결방법
* import는 IntelliJ에서 자동으로 해주는데, 만약 되지 않는다면 아래 방법대로 하자.
	1. Settings -> Editor -> General -> Auto Import를 선택 후 Insert imports on paste를 Always로 변경한다
		* Always로 변경하면 코드 붙여 넣기 시 자동으로 import 해준다.
	2. 그 후 바로 밑의 2개 체크 박스를 체크한다.
		* Add unambiguous imports on the fly
			* 코드 변경 시 필요한 패키지 자동 삽입
		* Optimize imports on the fly
			* 코드 변경 시 불필요한 패키지 자동으로 삭제

#### 주의
* 코드를 수정했다면 반드시 다시 실행시켜야 변경사항이 적용됩니다~
<br/><br/>

#### 정리

![Image Alt Controller_Process](/assets/img/for_post/Controller_process.jpeg)
<br/><br/>

## 5. Model 추가하기
<hr/>

* 앞에서 만든 뷰에는 안녕하세요를 넣었는데, 대신 다른 말을 넣고 싶다면 변수를 사용하여 내용이 변경되게 할 수 있다.
* mustache 문법으로 변수는 아래와 같이 사용하면 된다.
    * \{\{ 파일명 \}\}
<br/><br/>

1. greetings.mustache로 가서 안녕하세요 부분을 아래와 같이 변경해보자.
    * \<h1\>\{\{ username \}\}님, 안녕하세요~\</h1\>

2. Model에 변수를 만들고, 저장해야 하므로 FirstController에 가서 아래와 같이 추가하자

    ```java
    package com.example.FirstProject.controller;  
    
    import org.springframework.stereotype.Controller;  
    import org.springframework.ui.Model;  
    import org.springframework.web.bind.annotation.GetMapping;  
    
    @Controller  
    public class FirstController {  
        @GetMapping("/hi")  
        public String niceToMeetyou(Model model) {  //추가
            model.addAttribute("username","Lee");   //추가
            return "greetings";  
        }  
    }
    ```

3. 저장 후 ~Application을 실행 후 http://localhost:8080/hi 로 접속하면 내가 넣은 값과 작성한 글이 출력된다.

4. model의 값을 바꿔 다시 실행하면 값이 바뀌어 출력된다.
<br/><br/>

#### 코드 설명
* 이때 addAttribute로 model에 변수를 등록하는 것이고, 앞에는 변수 이름, 뒤에는 변수에 넣을 값을 작성하면 된다.
	* `mode.addAttribute("변수명", "넣을 값")`
<br/><br/>

## 6. 정리
<hr/>

#### MVC의 역할
* 위에서 말한 것들이 이해가 잘 되지 않는다면 식당의 주문 처리 과정으로 예시를 들 수 있다.
	* 손님이 주문을 하면 웨이터가 주문을 받고, 주방장이 요리를 하고, 식재료 담당자가 재료를 준비한다.
	* 똑같이 클라이언트가 요청을 하면, 컨트롤러가 요청을 받고, 뷰가 최종 페이지를 만들고, 모델이 페이지에 쓰일 데이터를 뷰에 전달한다.
<br/><br/>

#### /hi 페이지의 실행 흐름
1. 클라이언트로부터 "/hi"라는 요청을 받으면 `niceToMeetYou()` 메서드를 실행
	
2. 뷰에서 사용할 변수를 등록하기 위해 모델 객체를 매개변수로 받아온다.
	
3. 모델에서 사용할 변수 등록, 이 변수 내용에 따라 서로 다른 페이지가 나온다.
	
4. 이제 결과로 greetings.mustache 파일을 반환한다
	* 이때 메서드 return 부분에는 파일 이름만 작성하면 서버에서 알아서 찾는다.
		* ex) greetings
<br/><br/>

## 7. 연습 문제
<hr/>

* "localhost:8080/bye"라는 주소로 접속했을 때, "~님 안녕히가세요~" 라는 문구를 출력하게 해보자
	* 메서드 이름은 seeYouNext()로 하고, FirstController에 추가하자.
	* 뷰 이름은 goodbye.mustache로 하자.
	* 변수 이름은 nickname으로 하고, 값은 원하는 것으로 넣자.
<br/><Br/>

#### 답

* goodbye.mustache

```html
<!doctype html>  
<html lang="en">  
<head>  
    <meta charset="UTF-8">  
    <meta name="viewport"  
        content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">  
    <meta http-equiv="X-UA-Compatible" content="ie=edge">  
    <title>Document</title>  
</head>  
<body>  
    <h1> { { nickname }}님, 안녕하가세요~</h1>  
</body>  
</html>
```
<br/>

* FirstController.java

```java
package com.example.FirstProject.controller;  
  
import org.springframework.stereotype.Controller;  
import org.springframework.ui.Model;  
import org.springframework.web.bind.annotation.GetMapping;  
  
@Controller  
public class FirstController {  
    ...
  
    @GetMapping("/bye")  
    public String seeYouNext(Model model) {  
        model.addAttribute("nickname", "Lee");  
        return "goodbye";  
    }  
}
```