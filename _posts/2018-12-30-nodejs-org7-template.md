---
layout: post
title:  "Node.js 기본 정리 7"
subtitle:   "Node.js 기본 정리 7"
categories: study
tags: node nodejs express template
---

Node.js에 대한 문법 및 내용 기본정리 7으로, 템플릿에 대해 알아본다.

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

자바스크립트 변수를 템플릿에 렌더링 할 수 있는데, res.render 호출 시 보내는 변수를  
Pug가 처리해준다. `res.render('index', { title: 'Express' });` 코드를 보자.  
render 메서드는 익스프레스가 res객체에 추가한 템플릿 렌더링을 위한 메서드로,  
위 코드는 index.pug를 HTML로 렌더링하면서 객체를 변수로 집어넣는다.  
layout.pug와 index.pug의 title부분이 모두 Express로 치환되며,  
HTML도 변수를 사용할 수 있게 된 것이다.  
두번째 인자는 따로 빼서 `res.locals.title = 'Express';`처럼 작성해도 된다.  
이 방식이 현재 라우터 뿐만아니라 다른 미들웨어에서도 locals객체에 접근가능하다.  

Pug에서 이제 변수를 사용하려면 변수명을 바로 사용해도 되고, 문장 중간에 쓸땐  
${변수명}으로 사용한다. `<button id="Express"></button>`는 `button(id=title)`,    
`<p> Hello Express </p>`는 `p Hello #{title}`처럼 쓸 수 있다.  

내부에 직접 변수를 선언하려면 빼기기호(-)를 입력한 후 자바스크립트 구문을 작성한다.  
~~~
- var node = 'NodeJS'
- var js = 'JavaScript'
p #{node}와 #{js}    // 출력은 NodeJS와 JavaScript가 출력한다.
~~~

Pug는 변수의 특수문자를 HTML 엔티티로 이스케이프한다.  
이 말은 즉 자바스크립트에 `<div>` 문자열이 있으면 HTML에서 사용했을 때  
\<를 `&lt;`로 번역하고, \>를 `&gt;`로 번역하여 해석하는 것이다.  
`p='<div>이스케이프</div>'` 형태로 쓰며, 이스케이프를 원하지 않으면 !=를 쓴다.  

HTML문과 Pug는 다른점이 많지만 반복문, 조건문을 사용할 수 있다.  

~~~
<ul>
  <li>사과</li>
  <li>바나나</li>
  <li>오렌지</li>
</ul>
위 HTML 코드를 아래와 같이 Pug로 바꿀 수 있다.
ul
  each fruit, index in ['사과', '바나나', '오렌지']
    li=(index+1)+ '번째 과일 : ' + fruit
~~~
마치 파이썬과 같은 구분을 볼 수 있다.  
조건문도 if-else문, case-when 을 이용할 수 있다.  

HTML에서는 헤더, 푸터, 네비게이션 처럼 공통부분을 전부 하나의 코드에 넣어야 했다.  
그러나 Pug는 따로 관리할 수 있어서 번거로움을 줄여준다.  
include 파일명 으로 불러와서 적용하면 된다. 만약 헤더랑 푸터를 이미 작성했다면  

~~~ 
include header    // header.pug를 불러온다
main
  h1 메인입니다
  p 다른파일을 include하세요
include footer    // footer.pug를 불러온다
~~~

extends와 block을 이용하여 레이아웃을 정할 수 있다. 이것도 레이아웃 부분을  
따로 관리할 수 있어서 include와 함께 사용한다.  
레이아웃이 될 파일에는 공통된 마크업을 넣고, 페이지마다 달라지는 부분을  
block으로 비워둔다. block abc 처럼 비워두면 레이아웃을 사용하는 block파일은  
extends로 레이아웃 파일을 지정하고, block abc 내부에 pug코드를 작성하여  
비워둔 부분을 채워넣어 사용한다. 레이아웃 파일이 큰 틀이라 생각하면 된다.  

* EJS

EJS는 HTML문법을 그대로 사용하며 추가로 자바스크립트 문법을 사용할 수 있다.  
JSP와 문법이 비슷하며, app.js파일 내 `app.set('view engine', 'ejs');` 로 바꿔준다.  
npm i ejs로 ejs패키지도 설치해주어야 한다.  

EJS에서는 `<h1>Express</h1>`를 `<h1><%=title%></h1>`처럼 사용한다.  
변수를 \<%= %\>로 감싸주는 것이다.  
코드 내부에서 변수를 선언할 수 있으며 \<% %\>로 감싸고 그 내부에 작성한다.  

또한 HTML을 이스케이프하고 싶지 않다면 \<%- %\>를 사용한다.  
위에서 사용한 반복문, 조건문들도 \<% %\>를 이용한다. 예를들어 위의 ul과 li는  

~~~
<ul>
  <% var fruits = ['사과', '바나나', '오렌지'] %>
  <% for (var i=0; i<fruits.length; i++) { %>
    <li> <%= fruits[i] %> </li>
  <% } %>
</ul>
~~~

위 코드와 같이 JSP문법와 비슷한 느낌으로 작성한다.  
include는 `<%- include(파일경로, 데이터) %>`를 이용한다.  
EJS는 layout과 block을 지원하지 않으므로 include로 중복되는 부분을 집어넣는다.  

Pug와 EJS이외에도 Nunjucks, Hogan, Dust, Twig, Vahs 등의 템플릿 엔진들이 있다.  
