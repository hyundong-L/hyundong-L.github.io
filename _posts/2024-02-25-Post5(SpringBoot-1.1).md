---
layout: post
title: "1.1 - 스프링 부트 시작하기"
author: "Hyundong Lee"
categories: Spring_Boot
tags: [Github, Blog, Develop]
---

## 1. 스프링 부트 프로젝트 만들기
<hr/>

1. <https://start.spring.io> 로 접속
<br/><br/>

2. 아래 설정대로 설정하기
	1. Project -> Gradle - Groovy
    
	2. Language -> Java
    
    3. Spring Boot -> 3.2.3
    
    4. Project Metadata
		* Group -> 원하는 이름으로 바꿔도 되고 그대로 둬도 된다.

		* Artifact / Name -> 하나만 바꿔도 같이 바뀌고, 이것이 프로젝트 이름이 되니 원하는 것으로 바꾸기

		* Packageing -> Jar
		
        * Java -> 17

        * Dependencies -> 스프링 프로젝트에 필요한 여러 도구를 가져온다
			* Spring Web (웹 도구)

			* Mustache (화면을 만들기 위해 필요한 도구)
			
			* Spring Data JPA (데이터베이스를 편리하게 다룰 수 있게 하는 도구)
			
			* H2 Database (데이터베이스)
			
			* Lombok (반복되는 메서드들을 어노테이션을 사용하면 자동으로 작성해주는 도구)
				* ex) 생성자, getter, setter 등
<br/><br/>

3. 설정이 끝나면 Generate를 클릭하여 zip 파일 다운 받기
<br/><br/>

4. 압축을 풀어 IntelliJ에서 폴더를 열기
<br/><br/>

## 2. 프로젝트 구성 요소
<hr/>

![Image Alt element](/assets/img/for_post/spring_project_element.JPG)
* main
	* java
		* 자바 코드가 저장된다.
		 
		* 자신이 작성한 프로젝트 이름 + Application 파일을 실행해야 스프링부트 프로젝트가 실행된다.
	
	* resources
		* static
			* 이미지 같은 정적 요소가 저장된다
		 
		* templates
			* 뷰 파일(화면을 나타내는 파일)이 들어간다.
 <br/><br/>
* test
	* 테스트 코드가 들어간다.
<br/><br/>

## 3. 웹 사이트에 Hello World 출력해보기
<hr/>

1. main -> resources -> static에서 우클릭 -> new -> HTML File -> `hello.htm`로 파일 만들기
	* 여기서는 html 페이지가 동적으로 변경되지 않아 static에 넣지만 뒤에서는 뷰 파일(화면을 나타내는 파일)을 templates에 넣는다.
    <br/><br/>

2. hello.html 파일에 아래 코드 작성
    ```html
    <!DOCTYPE html>  
    <html lang="en">  
    <head>  
        <meta charset="UTF-8">  
        <title>Title</title>  
    </head>  
    <body>  
        <h1>Hello World~</h1>  
    </body>  
    </html>
    ```
<br/>

3. ~Application 파일을 우클릭, 메뉴 중간 쯤에 Run '~...' 클릭
	* 또는 오른쪽 상단에 시작 버튼(벌레 모양 왼쪽) 클릭
    * 아래와 같이 뜨면 제대로 실행된 것이다.
    ![Image Alt console](/assets/img/for_post/spring_run_console.JPG)
<br/><br/>

4. 웹사이트를 열고 <http://localhost:8080/hello.html> 로 접속해보기