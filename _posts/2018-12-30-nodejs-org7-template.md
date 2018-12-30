---
layout: post
title:  "Node.js 기본 정리 7"
subtitle:   "Node.js 기본 정리 7"
categories: study
tags: node nodejs express template
---

Node.js에 대한 문법 및 내용 기본정리 6으로, 익스프레스 구조를 알아본다.

# Node.js

## 익스프레스 웹 서버 만들기

### 템플릿 엔진 사용하기

HTML은 정적인 언어로 사용자가 기능을 직접 추가할 수 없다.  
또한 1000개 정도의 데이터를 표현하려면 자바스크립트는 반복문을 쓰는 반면에  
HTML은 일일이 직접 코딩해야한다. 템플릿 엔진은 자바스크립트를 사용해서  
HTML을 렌더링 할 수 있게 해준다.  
대표적인 템플릿 엔진으로 Pug와 EJS가 있다.  

* Pug (Jade)  

먼저 app.js에 다음 부분이 있어야 한다.  

~~~
app.set('views', path.join(__dirname, 'views'));
app.set('views engine', 'pug');
~~~

views는 템플릿 파일들이 위치한 폴더를 지정한다. res.render 메서드가 이 폴더를  
기준으로 템플릿 엔진을 찾아서 렌더링한다. 즉 /views/파일명.pug를 렌더링한다.  
views engine은 어떤 종류의 템플릿 엔진을 사용할 지 나타낸다.  

Pug문법은 기존 HTML과 다르게 화살괄호와 닫는 태그가 없다. 스페이스나 탭으로만  
관계들을 정의한다. 파이썬처럼 동일한 종류의 들여쓰기를 적용하면 된다.  

~~~
doctype html
html
  head
    title=title
    link(rel='stylesheet', href='/stylesheets/style.css')
~~~

코드가 위와 같으며, 태그의 속성도 태그명 뒤에 소괄호로 묶어준다.  
속성 중 `<div id="login"></div>` 같은 아이디와 클래스가 있는 경우는  
id는 #으로, class는 .(점)으로 표현할 수 있다. 즉 다음과 같이  
`#login` 으로 간단하게 나타낼 수 있다. div 태그는 생략할 수 있기 때문이다.  
`<p class="hidden full"></p>` 는 `p.hidden.full`처럼 나타낼 수 있다. 

HTML 텍스트는 태그 또는 속성 뒤에 한 칸을 띄고 입력한다.  
`<button type="submit">전송</button>`은 `button(type='submit') 전송` 으로 사용.  
태그 안에 br처럼 또 다른 태그를 넣을 수 있고, |(파이프)를 이용해 여러줄을 입력한다.  
~~~
p
  | Hello World!
  | chahaun
  br
  | 태그를 중간에 넣습니다
~~~
위 코드는 `<p>Hello World! chahaun <br/> 태그를 중간에 넣습니다</p>`와 같다.  

style이나 script태그로 코드를 작성하려면 태그 뒤에 .을 붙여서 사용한다.
내부에 작성하는 코드는 원래대로 사용하면 된다.  

* Pug의 변수

