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

여기서 res.write는 클라이언트에게 보낼 데이터이다. 어떤 종류든 상관없다. 여러번도 가능.  
res.end는 응답을 종료하는 메서드로, 인자를 보내고 응답을 종료한다.  
일단 res.write로 HTML코드를 작성하는 것은 비효율적이므로 html파일은 따로 사용하자.  
위 코드를 아래와 같이 수정한다.  

* server1.html   

~~~
<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8" />
  <title>Hello Node</title>
</head>
<body>
  <h1>Hello Node</h1>
  <p>Hello Server</p>
</body>
</html>
~~~

* server1.js  
~~~
const http = require('http');
const fs = require('fs');

const server = http.createServer((req, res) => {
  fs.readFile('./server1.html', (err, data) => {
    if (err) throw err;
    res.end(data);
  });
});
server.listen(8080);
server.on('listening', () => {
  console.log('서버 대기중');
});
server.on('error', (err) => {
  console.error(err);
});
~~~

이렇게 해서 js파일에서 html을 불러와 서버페이지를 구성하게 되었다.  
지금은 요청이 오는 모두에게 같은 응답을 보내지만, 다음에는 누구인지에 따라 다른 응답을 해보자.  

### 쿠키와 세션

우리가 서버페이지에 접속하여 로그인할때 내부적으로는 쿠키와 세션을 사용하고 있다.  
서버가 요청에 대한 응답을 할때 쿠키를 같이 보내주며, 웹 브라우저는 쿠키를 저장해두었다가  
요청할 때마다 쿠키를 동봉해서 보내준다. 데이터는 단순한 '키-값' 쌍으로 구성되어 있으며,  
서버는 요청에 들어있는 쿠키를 읽어서 사용자가 누구인지 파악한다.  

즉, 서버는 미리 클라이언트에게 요청자를 추정할 만한 정보를 쿠키로 만들어서 보내고,  
그 다음부터는 클라이언트로부터 쿠키를 받아 요청자를 파악한다.  
쿠키는 요청과 응답의 헤더에 저장된다.  

~~~
const http = require('http');
const parseCookies = (cookie = '') =>
  cookie
    .split(';')   //문자열을 ; 구분자로 분할
    .map(v => v.split('=')) // 각 나누어진 문자열들을 '=' 으로 구분해 분할
                            // 키는 k, 값은 vs에 저장. 
    .map(([k, ...vs]) => [k, vs.join('=')]) //
    .reduce( (acc, [k, v]) => {   // acc와 [k, v]는 다음 콜백함수 처리한다.
      acc[k.trim()] = decodeURIComponent(v);
      return acc;
    }, {} );  // {} 이부분은 초기값 설정부분
    
http.createServer((req, res) => {
  const cookies = parseCookies(req.headers.cookie); //헤더에 담긴 쿠키값을 인자로 넘김.
  console.log(req.url, cookies);
  res.writeHead(200, { 'Set-Cookie': 'mycookie=test' });  //응답코드와 헤더내용 입력
  res.end('Hello Cookie');
}).listen(8080, () => {
    console.log('서버 대기중...');
  });
~~~

위 코드는 parseCookies라는 문자열 형식을 쿠키 객체형태로 바꾸도록 한다.  
쿠키는 name=chahaun;year=1995같은 문자열로 되어있어서, 이를 파싱하여  
{name:'chahaun', year:'1995'}처럼 바꾸는 것이다.  
createServer에서 writeHead의 Set-Cookie는 다음과 같은 값의 쿠키를 저장하라는 의미이다.  
응답을 받은 브라우저는 mycookie=test라는 쿠키를 저장하며, 쿠키를 파싱한다.  

이 쿠키와 세션 개념을 이용하여 로그인을 구현하는 코드를 작성해 볼 수 있다.  

