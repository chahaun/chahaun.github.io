---
layout: post
title:  "Node.js 기본 정리 3"
subtitle:   "Node.js 기본 정리 3"
categories: study
tags: node nodejs http
---

Node.js에 대한 문법 및 내용 기본정리 3으로, http모듈로 웹 서버를 만든다

# Node.js

## http모듈로 웹 서버 만들기

### 요청과 응답

클라이언트에서 서버로 요청(request)를 보내고, 서버에서는 요청안 내용을 읽고 처리한 뒤  
클라이언트에게 응답(response)를 보내는 것이 요청과 응답이다.  

노드서버를 위해서 http모듈을 사용한다.  
이후 `http.createServer((req, res) => { //응답 });` 으로 요청에 대한 콜백함수가 실행한다.  
req에는 요청에 관한 정보들, res에는 응답에 관한 정보들이 담겨있다.  
이 응답부분에 res.write와 res.end로 응답 서버페이지에 출력을 띄울 수 있다.  
이후 createServer 메서드 뒤에 listen 메서드를 붙여서 접속을 대기한다.  
포트번호를 이용하여 대기하며, 연결되면 실행할 콜백함수를 넣어준다.  
실행할 콜백함수가 여러개(정상처리, 에러처리 등)이면 on메서드로 따로 빼준다.  
간단한 노드 서버 코드는 다음과 같다.  
~~~
const http = require('http');
const server = http.createServer((req, res) => {
  res.write('<h1>Hello Node</h1>');
  res.end('<p>Hello Server</p>');
});
server.listen(8080);
server.on('listening', () => {
  console.log('서버 대기중');
});
server.on('error', (err) => {
  console.error(err);
});
~~~
