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

express learn-mongoose --view=pug로 프로젝트 생성 후 안에서 npm i로 모듈 설치 후  
npm i mongoose 명령어로 몽구스를 설치해준다.  

노드와 몽구스를 연동하려면 주소를 사용하여 연결해야 한다.  
`mongodb://[username:password@]host[:port][/[database][?options]]` 의 형식이며,  
\[\]로 되어있는 부분은 있어도 되고 없어도 되는 부분이다.  
따라서 간단하게 mongodb://이름:비밀번호@localhost:27017/admin 을 이용한다.  
접속부분은 schemas/index.js 파일의 mongoose.connect()부분에 작성해야 한다.  
connect('접속주소', {사용할 DB명}, {콜백함수}) 형식으로 작성한다.  

