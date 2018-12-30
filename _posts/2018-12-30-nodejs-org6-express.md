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

익스프레스를 Express-generator로 npm전역설치를 해준다.  
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

