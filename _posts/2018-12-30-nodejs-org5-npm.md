---
layout: post
title:  "Node.js 기본 정리 5"
subtitle:   "Node.js 기본 정리 5"
categories: study
tags: node nodejs package npm
---

Node.js에 대한 문법 및 내용 기본정리 5으로, 패키지매니저 및 npm을 알아본다.

# Node.js

## 패키지 매니저

### npm

npm은 Node Package Manager의 약자이며, 노드에서 자바스크립트 프로그램을 컴퓨터에서도  
실행할 수 있게 해주는데 대부분 자바스크립트 프로그램은 패키지 이름으로  
npm에 등록되어 있으므로 특정기능이 필요하면 npm에서 찾아 설치하면 된다.  
npm에는 2018년 기준 60만개에 달하는 패키지가 등록되어 있다. 대부분 오픈소스여서  
이를 이용하여 노드로 웹 개발을 할 때 많은 도움이 된다.  

npm에 업로드된 노드 모듈을 패키지라고 부른다. 패키지가 다른 패키지를 사용할 수 있는데  
이러한 관계를 의존 관계라고 부른다.  
yarn은 페이스북이 내놓은 패키지매니저로, React에서 사용한다.  

설치한 패키지의 버전을 관리하는 파일은 package.json이다. 이 파일을 우선 만들어야 하며,  
프로젝트를 시작할 폴더에서 `npm init`을 명령어로 쳐서 파일을 만들 수 있다.  
이후 정보들을 채워 넣어서 초기화 시켜준다.  

* package name : 패키지의 이름
* version : 패키지의 버전
* entry point : 자바스크립트 실행파일 진입점. 보통 마지막 module.exports파일로 지정
* test command 코드를 테스트할 때 입력할 명령어
* git repository : 코드를 저장해 둔 git 저장소 주소
* keyworkds : npm 공식 홈페이지에서 쉽게 찾도록 해주는 키워드
* license : 해당 패키지의 라이선스(ISC, MIT, BSD, Apache, GPL 등 특징이 다름)
* scripts : npm 명령어를 저장해두는 부분. 여러개 등록할 수 있다.

이후 package.json 파일이 있는 폴더에서 `npm install express`로 익스프레스를 설치한다.  
설치한 패키지는 package.json파일의 dependencies라는 새로운 속성이 추가되고,  
이 부분에 설치된 npm과 버전이 저장된다.  
추가로 node_modules 폴더도 생성되며 그 안에는 설치한 패키지들이 들어있다.  
패키지가 여러개 있는 이유는 Express가 의존하는 패키지들이다.  
이것이 패키지가 다른 패키지들에 의존하는 의존관계이다.  

node_modules 폴더가 삭제되어도 package.json에 정보가 들어있기 때문에  
npm install 명령어를 치면 다시 설치된다.  
이는 git과 같은 버전관리 프로그램에 올릴 때 package.json만 올려도 되도록 한다.  
package-lock.json파일도 생성된다. npm으로 패키지를 설치, 수정, 삭제할 때마다  
내부 의존관계를 이 파일에 저장한다.  

모듈 여러개를 동시에 설치하려면 npm install 뒤에 이어서 나열하면 된다.  
개발용 패키지를 설치할 수도 있다. 실제 배포 시에는 사용되지 않고 개발 중이때만 사용된다.  
`npm install --save-dev [패키지]` 로 설치하면 된다. (nodemon 패키지 등)  
nodemon 패키지는 소스코드가 바뀔때마다 자동으로 노드를 재실행 해준다.  

npm의 전역설치는 패키지를 현재폴더가 아닌 npm자체가 설치되어 있는 폴더에 설치한다.  
이 경로는 시스템 환경변수(PATH)에 등록되어 있으며, 이렇게 설치한 패키지는  
콘솔의 커맨드로 사용할 수 있다. --global 옵션을 사용한다.  
예를들어 rimraf 패키지는 리눅스의 rm -rf 명령어를 윈도우에서 사용할 수 있게 해준다.  

전역설치는 package.json에 기록되지 않아서 다시 설치할 때 어려움이 있는데,  
npx 명령어를 이용하면 기록이 되면서 전역설치한 효과를 얻을 수 있다.  
npm에 등록되어 있지 않고 github에 저장되어 있을 땐, npm install git허브주소로 설치.  
npm install 명령어는 npm i로 줄여쓸 수 있으며, --save-dev는 -D, --global은 -g  

### 패키지 버전 이해하기

노드나 패키지의 버전은 3자리로 구성되어있다. 이는 SemVer 방식의 넘버링을 따른다.  
SemVer은 Semantic Versioning으로 세자리 모두 의미를 가진다는 것이다.  
각각 Major.Minor.Patch 를 나타낸다. Major은 0일땐 개발중, 1부터 정식버전이다.  
이 버전을 수정할 땐 하위호환이 안될 정도일 때 변경한다.  
Minor은 하위 호환가능 업데이트일때 변경한다.  
Patch는 간단한 버그수정등의 업데이트 일때 변경한다.  

새 버전을 배포한 이후에는 그 버전의 내용을 절대 수정하면 안된다.  
버전 앞에 ^, ~, >, < 등의 특수문자가 붙어있다면 설치나 업데이트 시  
어떤 버전을 설치해야 하는지 알려준다.  
^가 붙어있다면 minor 버전까지 설치/업데이트를 한다.  
예를들어 npm i express@^1.1.1일때 1.1.1버전보다 그 이상의 1.x.x의 모든 버전을 설치한다.  
~기호를 사용하면 patch버전까지만 설치/업데이트를 한다.  
\>, \<기호같은 경우는 그 이상, 그 이하 버전을 설치한다는 말이다.  
@latest 혹은 @x는 항상 최신버전의 패키지를 설치한다.  

### 기타 npm 명령어

npm outdated : 업데이트 할 수 있는 패키지를 확인한다.  
npm update 패키지명 : 패키지를 업데이트한다.  
npm ununstall(or rm) 패키지명 : 패키지를 제거한다.  
npm search 검색어 : npm의 패키지를 검색한다. 사이트에서 검색이 편하다.  
npm info 패키지명 : 패키지의 세부정보를 파악한다.  
npm adduser : npm 로그인을 위한 명령어이다. 배포할 때 사용한다.  
이 외에도 logout, version, deprecate(경고메세지), publish(배포) 등이 있다.  

패키지를 배포할 때는 npm계정을 사용하여 배포한다.  
배포파일명은 package.json의 main부분과 일치해야 한다.  
