---
layout: post
title:  "MongoDB 기본정리"
subtitle:   "MongoDB"
categories: study
tags: mongodb nosql
---

Mongo DB에 대한 기본 정리

# Mongo DB

### NoSQL vs SQL

NoSQL은 SQL을 사용하지 않는 Not only SQL이다.  
SQL과의 차이점은 데이터 입력이 규칙이 없어서 자유로우며, 고정된 테이블이 없다.  
테이블과 비슷한 컬렉션이라고 있으며, 몽고디비의 경우 컬렉션을 만들고 따로  
컬럼을 생성하지 않는다. 컬렉션에는 각 다큐먼트가(테이블의 row) 있는데,  
각 다큐먼트에는 서로 다른 데이터의 컬럼을 가질 수 있다.  

또한 SQL과 달리 JOIN기능이 없고, 트랜잭션을 지원하지 않는다.  
트랜잭션은 여러 쿼리가 모두 정상수행되거나 하나도 수행되지 않음을 보장하는 기능인데,  
지원하지 않아서 데이터 일관성에 문제가 생길 수 있다. (몽고디비 4부터 지원한다고 함)  

하지만 NoSQL을 사용하는 이유는 확장성과 가용성 때문이다. 데이터를 빠르게 넣으면서  
쉽게 여러 서버에 데이터를 분산하는 기능이 있기 때문이다.  
SQL의 table, row, column => collection, document, field  

여러 기업은 SQL과 NoSQL을 함께 사용하고 있다. 사용처가 알맞은 곳에 사용하면 되는 것이라,  
예를들어 예약 시스템에서 예약처리부분은 MySQL, 빅데이터/메시징/세션관리는 NoSQL을 사용한다.  

### 몽고디비 접속하기

몽고디비를 설치하고, 설치 후 C:\data\db 폴더를 생성해준다. 이후에 콘솔 창을 켜서  
mongod 명령어를 입력하면 몽고디비 서버를 실행할 수 있다.  
서버를 실행한 이후, 새로운 콘솔창에서 mongo 명령어를 입력하면 몽고디비 프롬프트에 접속되며  
누구나 몽고디비 접속할 수 있게 된다. 이제 계정을 만들어 주어야 하므로,  
use admin 명령어를 입력 후  
`db.createUser({ user: 'root', pwd: '비밀번호', roles: ['root'] })`  
명령어를 입력하여 root계정을 생성해준다. 이제 루트로 접속하기 위해 몽고디비 서버를 끈 후  
mongod --auth 명령어로 로그인이 필요한 몽고디비 서버를 켠 후에 새로운 콘솔창에서  
mongo admin -u root -p 비밀번호 명령을 통해 몽고디비에 접속해준다.  
몽고디비에는 compass라는 워크벤치와 비슷한 프로그램이다.  

### 몽고디비의 명령어 및 CRUD

use 데이터베이스명 : 데이터베이스를 하나 생성한다  
show dbs : 데이터베이스 목록 확인  
db : 현재 사용중인 데이터베이스 확인  
db.createCollection('컬렉션명') : db에 컬렉션 생성. 보통은 다큐먼트 넣을때 자동으로 생성.  
show collections : db의 컬렉션 목록 확인

실습에서는 컬렉션에 users, comments 를 생성하여 사용자와 댓글 컬렉션을 이용한다.  
* Create (생성)  

몽고디비의 컬렉션에는 아무 데이터나 넣을 수 있다는 자유로움이 있다.  
기본적으로 자바스크립트 문법을 따르고, 추가적인 자료형이 있다.  
추가적인 자료형은 Binary Data, ObjectId, Int, Long, Decimal, Timestamp, JavaScript이다.  
ObjectId는 기본 키의 역할이며, 고유한 값을 가지므로 다큐먼트 조회에 사용된다.  

`db.컬렉션명.save({ 다큐먼트 })` 형태로 다큐먼트를 생성하게 된다.  
`db.users.save({ name:'chahaun', age: 25 });` 처럼 데이터를 생성할 수 있다.  
생성한 다큐먼트의 \_id를 알기위해서는 `db.users.find({ name:'chahaun'}, {_id:1})`를  
입력하여 ObjectId 값을 얻어낸다. 출력된 값을 이용하여 다른 컬렉션과 연동할 수 있다.  
이를 통해 사용자에 대한 댓글을 추가할 땐 다음 코드를 작성한다.  
~~~
db.comments.save({ commenter: ObjectId('사용자의 Id값'), comment:'댓글입력', createdAt: new Date()});
~~~

* Read (조회)  

db.컬렉션명.find({조건}, {조회할 다큐먼트}); 명령을 통해 다큐먼트들을 조회할 수 있다.  
`db.users.find({}, {_id: 0, name: 1, married: 1});` 처럼 원하는 데이터만  
조회할 수 있다. 0은 가져오지 않도록, 1은 가져오도록 설정하는 값이다.  
조회할 때 조건을 넣어서 조회하려면 첫번째 인자에 객체를 넣는다.  위 명령어에서  
`db.users.find({age: {$gt: 30}, {_id: 0, name: 1, married: 1});`  
이렇게 age\>30인 조건을 넣어서 조회할 수 있다.  
몽고디비는 자바스크립트를 따르므로 특수연산자($gt) 등을 사용한다.  
~~~
db.users.find({$or: [{age: {$gt: 30} }, { married: false }] }, { _id: 0, name: 1, age: 1 });
~~~
위와 같이 OR연산자를 사용할 수 있으며, 정렬조회를 할때는 아래와 같이 입력한다.  
조회할 다큐먼트의 개수를 설정할 땐 limit메서드를 뒤에 붙여준다.  
몇개를 건너뛰면서 조회할 때는 뒤에 skip메서드를 붙여준다.  
~~~
db.users.find({}, {_id: 0, name: 1, married: 1}).sort({ age: -1 })
// -1은 오름차순, 1은 내림차순으로 정렬한다.
~~~

* Update (수정)  

db.컬렉션명.update({수정할 다큐먼트}, {$set: {수정할 내용} });
이때 $set은 일부 필드만 수정하고 싶을때 꼭 사용해야하며, 사용하지 않고 쓰면  
다큐먼트가 통째로 수정할 내용으로 바뀌어버린다.  
`db.users.update({name:'chahaun'}, {$set: {comment:'설명변경'} });`  

* Delete (삭제)

db.컬렉션명.remove({삭제할 다큐먼트})로 삭제하면 된다.  
`db.users.remove({name:'chahaun'})`  

몽고디비를 노드와 연동하여 서버에서 DB를 조작할 수 있도록 해야하는데, 몽구스를 이용한다.  

### Mongoose (몽구스)

MongoDB와 Node를 연동해주며, 쿼리까지 만들어준다.  
MySQL의 시퀄라이즈와 비슷하지만, 시퀄라이즈는 ORM인 반면에 몽구스는 ODM이다.  
Object Document Mapping으로 다큐먼트를 사용하기 때문이다.  

몽구스를 사용하는 이유는 몽고디비에 없는 기능들을 보완해주기 때문인데,  
몽구스에는 스키마가 있어서 몽고디비에 데이터를 넣기 전 데이터를 한번 필터링해준다.  
그래야 자유롭게 데이터를 넣는 몽고디비에 실수를 막을 수 있기 때문이다.  

또한 JOIN기능은 populate 메서드가 보완해준다. 그래서 관계가 있는 데이터를  
쉽게 가져올 수 있게 되며, ES2015 프로미스 문법과 쿼리빌더를 지원한다.  

몽구스 스키마의 자료형은 String, Number, Date, Buffer, Boolean, Mixed, Array 등  
몽고디비의 자료형과 살짝 다르다.  

express learn-mongoose --view=pug로 프로젝트 생성 후 안에서 npm i로 모듈 설치 후  
npm i mongoose 명령어로 몽구스를 설치해준다.  

노드와 몽구스를 연동하려면 주소를 사용하여 연결해야 한다.  
`mongodb://[username:password@]host[:port][/[database][?options]]` 의 형식이며,  
\[ \]로 되어있는 부분은 있어도 되고 없어도 되는 부분이다.  
따라서 간단하게 mongodb://이름:비밀번호@localhost:27017/admin 을 이용한다.  
접속부분은 schemas/index.js 파일의 mongoose.connect()부분에 작성해야 한다.  
connect('접속주소', {사용할 DB명}, {콜백함수}) 형식으로 작성한다.  
이 파일을 모듈로 만들어서 app.js에서 연결해서 노드 실행 시 mongoose.connect 부분이  
실행되도록 해주어야 한다. 작성코드는 생략한다.  

몽구스의 스키마를 정의하기 위해서 schemas/user.js 파일을 생성하여 작성한다.  
또한 댓글을 위해 comment.js도 작성하여 코드를 작성한다.  
~~~
const mongoose = require('mongoose');
const {Schema} = mongoose; // mongoose.Schema를 Schema변수에 적용한다.
const userSchema = new Schema({
  name: {
    type: String,     // 자료형은 String
    required: true,   // 필수로 데이터 작성
    unique: true,     // 고유값이어야 함
  },
  ... 나머지 다큐먼트 작성
});

module.exports = mongoose.model('User', userSchema);
// 몽구스는 컬렉션을 생성할 때, model메서드의 첫번째 인자를 참고해서 생성한다.
// 첫번째 대문자를 소문자로 하고, 복수형으로 만들어서 users 컬렉션을 생성하는데,
// 원하는 이름의 컬렉션을 생성하려면 세번째 인자로 이름을 지정해준다.
// mongoose.model('User', userSchema, 'user_table'); 처럼 지정해준다.
~~~

comment.js에서 user의 ObjectId로 사용자에 맞게 연결하기 위해서  
~~~
const commentSchema = new Schema({
  commenter: {
    type: ObjectId,     // User의 ObjectId를 사용하여 연결
    required: true,     // 필수로 데이터 작성
    ref: 'User',        // User 스키마를 사용
  },
  ... 나머지 다큐먼트 작성
});
~~~

이러한 기능은 몽구스가 JOIN과 비슷한 기능을 할 때 사용된다.  

### 몽구스 쿼리 수행하기

MVC 모델과 비슷하게 스키마,뷰,라우터가 있다.  

스키마는 schemas폴더의 index.js, user.js, comment.js가 있다.  
index.js에는 몽고디비와 노드를 연동해주는 연결코드가 있으며,  
user.js에는 user에 대한 몽구스 스키마가 있으며,  
comment.js에는 comment에 대한 몽구스 스키마가 구성되어 있다.  

뷰는 페이지 화면을 보여주는 파일인 views/mongoose.pug가 존재하고, 내부에서  
기능을 위해 public/mongoose.js가 존재한다.  
mongoose.js에서는 사용자가 이름을 작성하고 저장버튼을 누르면 AJAX형식으로  
처리되어서 리스트에 사용자가 추가되거나, 댓글을 생성 및 수정 삭제할때  
AJAX형식으로 리스트에 추가되고 동적으로 수정, 삭제를 진행할 수 있도록 한다.  

라우터(컨트롤러)는 routes폴더의 index.js, users.js, comments.js가 있다.  
index.js는 GET /로 접속했을 때 모든 사용자를 검색한 후, mongoose.pug를  
렌더링할때 (페이지를 불러올 때) users 변수를 넣어 렌더링한다. 에러처리도 한다.  
즉, 미리 DB에서 데이터를 조회한 후 템플릿 렌더링에 사용할 수 있다.  

users.js는 GET /users와 POST /users 주소로 요청이 들어올때 처리를 해준다.  
GET /users로 요청이 들어오면 사용자를 조회하는 것이며, res.json(users)로  
JSON 형식으로 조회값을 반환해준다.  
POST /users로 요청이 들어오면 사용자를 등록하는 것이며, 먼저 new User()로  
user 객체를 생성하여 모델을 만든 후 안에 다큐먼트에 넣을 내용을 적는다.  
그 후에 user.save()를 이용해 데이터를 저장하는 코드를 작성한다.  

comments.js에서는 댓글에 대한 CRUD 작업을 진행한다.  
GET /comments/:id(조회), POST /comments(생성), PATCH /comments/:id(수정),  
DELETE /comments/:id(삭제) 총 4가지가 있다.  
조회를 예로들면 id로 조회하므로  
~~~
var express = require('express');
var Comment = require('../schemas/comment');    // comment모듈을 받아온다
var router = express.Router();
router.get('/:id', function(req, res, next) {   // id값을 받아온다
  Comment.find({ commenter: req.params.id }).populate('commenter')  // 사용자 id값으로 조회 후 populate로 그 컬렉션의 다큐먼트를 불러온다
    .then((comments) => {
      console.log(comments);
      res.json(comments);   // 조회한 데이터를 JSON형식으로 반환해준다.
    })
    .catch((err) => {
      console.error(err);
      next(err);
    });
});
... 생성/수정/삭제는 생략
~~~

이렇게 스키마, 뷰, 라우터 파일들이 구성되어 있으며 마지막으로 라우터를 서버에  
연결하기 위해 app.js파일에 원래 없었던 라우터의 comments 모듈을 받아서  
`app.use('/comments', comment모듈);` 코드로 연결한 후에 express.static  
코드 위치를 logger 아래에 위치한 후 저장하고 서버를 실행한다.  

이러한 예제는 사용자를 등록하여 몽고디비에 저장하고, 사용자의 아이디를 이용하여  
댓글을 등록하고 댓글을 수정 및 삭제를 할 수 있는 예제로 구성되어있다.  
