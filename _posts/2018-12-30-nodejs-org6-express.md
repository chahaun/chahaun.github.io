---
layout: post
title:  "Node.js 기본 정리 6"
subtitle:   "Node.js 기본 정리 6"
categories: study
tags: node nodejs express
---

Node.js에 대한 문법 및 내용 기본정리 6으로, 익스프레스 구조를 알아본다.

# Node.js

## 익스프레스 웹 서버 만들기

### Express

npm에는 서버 제작 시 불편함을 해소하고, 편의 기능을 추가한 웹 서버 프레임워크가 있다.  
익스프레스는 http모듈의 요청과 응답 객체에 추가 기능들을 부여했다.  
기존 메서드들도 계속 사용하면서 편리한 메서드들을 추가하여 기능을 보완하였다.  
또한 코드를 분리하기 쉽게 만들어 유지보수에도 용이하고, if문으로 구별하지 않아도 된다.  

Express-generator은 프레임워크에 필요한 package.json 및 폴더구조를 자동으로 잡아준다.  
app.js+views+routes+public+bin(www)정도를 잡아주며 models은 따로 만들자.  

익스프레스를 Express-generator로 npm전역설치를 해준다.(npm i -g Ex\~)  
기본적으로 PUG(Jade)를 템플릿 엔진으로 설치한다. EJS를 사용할 수도 있다.  
`express 프로젝트명 --view=pug` 명령으로 프로젝트를 생성해 준다.  
생성한 폴더에서 npm i로 npm모듈들을 설치해주면 서버관련 폴더를 초기화해준다.  

app.js파일은 핵심적인 서버역할을 해주고, bin/www파일은 서버실행 스크립트이다.  
public폴더는 외부에서 접근 가능한 파일들을 모아둔 곳이며, img, css, js파일 등이 있다.  
routes폴더는 주소별 라우터들을 모아둔 곳이고, views 폴더는 템플릿 파일이 있다.  
앞으로 서버의 로직은 모두 routes 폴더에 작성하고, 화면은 views에 작성한다.  
DB부분은 models 부분에 작성하며, 이는 MVC 패턴과 비슷한 구조이다.  
익스프레스의 실행은 npm start로 실행할 수 있으며 지정한 포트번호로 접속해본다.  

### Express의 구조

익스프레스는 코드가 여러개의 파일로 분산되어 있고, 각자 역할이 나누어져있다.  
bin/www파일은 http모듈에 express모듈을 연결하고, 포트를 지정하는 부분이다.  
이 파일은 확장자가 없고, 매직넘버(#!)로 시작하는 파일이다.  
app, debug, http 모듈을 사용하여 구성되어있고, app.js 모듈을 보면 수많은  
use 메서드가 사용된다. 이들은 여러 미들웨어를 구성해주는데, 미들웨어는 요청과  
응답의 중간에 위치하여 요청과 응답을 조작하여 기능을 추가하는 등의 역할을 한다.  

app.js의 미들웨어는 요청이 들어오면 logger('dev')부터 시작하여 json, cookieParser,  
라우터, 404처리, 에러 핸들러를 거쳐서 응답을 진행한다.  
이때 미들웨어 안에서는 next()를 호출해야 다음 미들웨어로 넘어간다.  
app.js에 app.use()를 이용하여 커스텀 미들웨어도 만들어 볼 수 있다.  

next() 함수에 인자를 넣을 때 route를 넣으면 특수한 기능을 한다.  
그 외 다른 값을 넣으면 바로 에러 핸들러로 이동한다. 넣어준 값을 에러내용으로 보는것이다.  
404 에러가 나면 createError(404)를 next의 인자로 넣어 에러핸들러에서 처리를 시작한다.  
받은 인자는 err매개변수에 연결되며 에러를 받아 처리해준다.  
하나의 app.use에는 여러 미들웨어(function)을 장착할 수 있으며, 순서대로 실행된다.  

morgan 미들웨어는 요청에 대한 정보를 콘솔에 기록(log)해준다.  
logger('dev')가 대표적으로, 인자를 short, common등으로 주면 나오는 로그가 다르다.  
dev인 경우 GET / 200 51.267ms - 1539라고 나오면 HTTP요청으로 GET을 이용하고,  
주소는 / 이고, 상태코드는 200, 응답속도와 응답바이트를 기록해준다.  
파일이나 데이터베이스에 로그를 남기려면 이보단 winston 모듈을 사용한다.  

body-parser 미들웨어는 보통 폼 데이터나, AJAX요청 데이터를 처리해준다.  
익스프레스 4.16.0버전부터는 일부기능이 express에 내장되어서 사용된다.  
body-parser가 필요한 경우는 Raw(버퍼), Text형식의 본문을 해석할 때 사용된다.  

POST와 PUT 요청의 본문을 전달받으려면 req.on('data'), req.on('end')로 스트림을  
사용했었는데, body-parser는 패키지가 내부적으로 본문을 해석해 req.body에 추가해준다.  
JSON형식으로 { name:'chahaun', birth:'1995' }를 본문으로 보내면 그대로 body에 들어간다.  
urlencoded 형식으로 name=chahaun&birth=1995를 보내면 body에 위와 같은 객체로 들어간다.  

cookie-parser는 요청에 동봉된 쿠키를 해석해주며 parseCookies와 비슷하다.  
해석된 쿠키들은 req.cookies 객체에 들어간다. name=chahaun을 쿠키를 보냈다면  
req.cookies에는 { name:'chahaun' }가 들어가는 것이다.  
첫번째 인자로 문자열을 주면, 제공한 문자열로 서명된 쿠키가 되어 보안성을 강화한다.  

static미들웨어는 정적파일을 제공해주며, express.static(인자)로 사용한다.  
인자에는 정적 파일들이 담겨있는 폴더를 지정한다.  
`express.static(path.join(__dirname, 'public'))` 처럼 구성하면, public 폴더에  
담겨있는 파일로 `http://localhost:3000/파일명` 처럼 접근할 수 있다.  

실제 서버의 폴더 경로에는 public이 들어있지만, 요청 주소에는 public을 기술하지 않는다.  
또한 정적 파일들을 알아서 제공해주므로 fs.readFile로 파일을 직접 읽지 않아도 된다.  
그리고 정적 파일을 제공할 주소를 직접 지정할 수도 있다. public 폴더에 abc.jpg가 있고,  
`app.use('/img', express.static(path.join(__dirname, 'public')));` 로 지정하면  
`http://localhost:3000/img/abc.jpg` 처럼 접근할 수 있다.  

이렇게 static 미들웨어가 구성되며 morgan 미들웨어 다음에 배치하는 것이 좋다.  
자체적으로 정적 파일 라우터 기능을 수행하므로 서비스에 맞는 위치에 배치하자.  

express-session 미들웨어는 세션 관리용 미들웨어로, 로그인 등 세션을 구현할 때 좋다.  
express-generator로 설치가 안되므로 npm i로 직접 설치해야 한다.  
설치 후 app.js에 express-session을 연결하여 cookie-parse 뒤에 놓아 사용한다.  
인자로는 resave, saveUninitialized, secret, cookie, store가 있으며  
req 객체 안에 req.session 객체를 만들고 이 객체에 값을 대입하거나 삭제해서  
세션을 변경할 수 있다. 세션 삭제 및 아이디 확인도 가능하다.  

connect-flash는 일회성 메세지들을 웹 브라우저에 나타낼 때 사용한다.

### Router 객체로 라우팅 분리하기

이전에 라우터를 만들 때 요청메서드와 주소별로 if문 분기처리를 해서 코드가 복잡했다.  
익스프레스는 라우팅을 깔끔하게 관리할 수 있다. 다음 app.js의 일부를 보자.  

~~~
...
var indexRouter = require('./routes/index');
var userRouter = require('./routes/users');
...
app.use('/', indexRouter);        // 주소가 '/' 로 시작하면 routes/index.js 호출
app.use('/users', userRouter);   // '/users' 로 시작하면 routes/users.js 호출
...
~~~

app.use로 연결되어 있는걸 보면 라우터도 일종의 미들웨어로 볼 수 있다.  
다른 미들웨어와 다르게 앞에 주소가 붙어있다. 그래서 이 주소에 해당하는 요청이  
왔을 때만 미들웨어가 동작하게 할 수 있다.  
use 대신 get, post, put, patch, delete같은 HTTP 메서드를 사용할 수 있다.  
그러면 get을 썼다면 get 메서드의 특정 주소에 요청일때만 실행되는 식이다.  
use일때는 HTTP 메서드는 상관없다.  

라우터 파일들은 위와 같이 routes폴더에 존재하며, index, users 라우터가 있다.  
/routes/index.js 파일은 다음과 같다.  

~~~
var express = require('express');
var router = express.Router();  // 라우터객체는 이와같이 만든다

router.get('/', function(req, res, next) {   // '/' 주소로 GET 요청을 한다
  res.render('index', {title: 'Express' });  // 클라이언트에게 응답을 데이터를 동봉해서 보낸다
});
module.exports = router;    // 라우터를 모듈로 만든다
~~~

router에도 use, get, post, put, patch, delete 메서드를 붙일 수 있다.  
또한 router 하나에 미들웨어 여러 개를 장착할 수 있다.  
라우터에서는 반드시 요청에 대한 응답을 보내거나 에러핸들러로 넘겨야 한다.  
아니면 브라우저가 응답을 계속 기다리기 때문이다.  

next함수에는 라우터에서만 동작하는 특수기능이 있다. next('route')처럼 사용하며,  
라우터에 연결된 나머지 미들웨어들을 건너뛰고 싶을 때 사용한다.  
router.get(주소, 콜백함수); 가 있을 때, 콜백함수에 next('route')코드를 만나면  
아래 코드는 실행되지 않고 주소와 일치하는 다음 라우터로 넘어가 실행한다.  

라우터 주소에는 특수한 패턴을 사용할 수 있다. 여러 패턴 중 다음 패턴이 자주쓰인다.  

~~~
router.get('/users/:id', function(req, res) {
  console.log(req.params, req.query);
});
~~~

주소에 :id를 넣음으로써 req.params.id를 조회하여 값을 얻어낸다.  
/users/123 처럼 주소가 들어오면 req.params는 {id:'123'}으로 되어있다.  
주소에 쿼리스트링도 잡아낼 수 있는데, req.query 객체 안에 키-값이 들어있다.  
/users/123?name=chahaun 주소 요청이 들어오면 req.params와 req.query객체는  
{ id:'123' } { name='chahaun' } 으로 들어있다.  
이렇게 사용할 수 있으며, 일반 라우터보다 뒤에 위치해야 방해가 되지 않는다.  

에러가 발생하지 않았으면 클라이언트에게 응답을 보내주어야 한다.  
send, sendFile, json, redirect, render 메서드 등이 있다.  
send : 버퍼 데이터나 문자열, html코드, json 데이터 등을 전송한다.  
기본적으로 200 상태코드를 응답하지만, res.status(404).send('Not Found')처럼 조작가능  
sendFile : 파일을 응답으로 보내준다.  
json : JSON 데이터를 보내준다.  
redirect : 응답을 다른 라우터로 보내준다. (ex 메인 페이지로 보냄)  
render : res.render('템플릿 파일경로', { 변수 }); 처럼 
템플릿 엔진을 렌더링 할 때 사용한다. views 폴더 안 pug확장자를 가진 파일이 템플릿엔진이다.  

라우터가 요청을 처리하지 못할 때는 404 HTTP 상태코드를 보내주어야 하므로  
다음 미들웨어에서 새로운 에러를 만들고 에러의 상태코드를 404로 설정한 뒤  
에러처리 미들웨어로 넘겨버린다.
