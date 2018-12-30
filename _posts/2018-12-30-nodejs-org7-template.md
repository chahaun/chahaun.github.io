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

