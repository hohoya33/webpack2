### webpack 발표자료 정리

# Javascript 모듈의 필요성

## Javascript의 문제점
* 글로벌(전역) 스코프가 쉽게 오염
* 동일한 이름을 가진 변수 사용
* 올바른 의존성 순서
* 웹페이지가 커질수록 script 태그 수 의 증가

자바스크립트를 어느정도 다뤄본 사람이라면 자바스크립트의 스코프 관리가 지저분하다는 것을 알 수 있습니다.

변수나 함수들이 global scope에 저장 되어 변수를 일일이 관리 해야하고 
다른 스크립트에서 실수로 중복 선언이 되면 서로 충돌하는 예기치 않은 문제점들이 빈번하게 발생. 
이로 인해 코드의 유지보수가 힘들어지고, 명확하지 않는 변수들이 생겨나게 됩니다.

각 파일이 어떤 파일들에 의존하고 있는지와 어떤 파일을 필요하지 않은지를 포함해서 파일들이 올바른 순서로 로드되도록 항상 신경을 써야 합니다.

웹 브라우저가 서버로부터 코드를 가져오기 위해 스크립트 태그 수 만큼 호출.
다수의 script 태그는 성능에 부정적인 영향을 줄 수 있습니다.


## Javascript의 전역 문제 해결 방법
즉시실행 함수 블럭에서 선언된 변수는 전역 스코프를 오염시키지 않습니다.
즉시 실행 함수 표현식(IIFE)은 선언되었을 때 바로 실행되는 익명 함수이다.

```js
//IIFE (Immediately Invoked Founction Expression)
(function() { 
    /* code here */ 
})();

//'App' 같은 하나의 전역객체 밑에 네임스페이스를 갖습니다. 
var App = App || {}; 
App.Models = {}; 
App.Models.Note = function() {};
```

## Javascript 모듈화 및 의존성 관리

코드베이스가 커지면 유지보수가 쉽도록 코드를 나누어 관리하는 모듈 시스템이 필요. (코드 모듈화)

2000년대 후반  자바스크립트가 브라우저 언어를 넘어 범용적으로 사용하기 위해 필요한 기술이 바로 모듈화였고, 이를 논의 하기 위해 자발적으로 만들어진 그룹이 CommonJS 워킹 그룹.

이 모듈 시스템은 Node.js에 도입되어 서버 사이드에서 큰 성공을 거두게 됩니다. 
그러나 이 방식은 모든 모듈 명세가 로컬 디스크에 있어야 했기 때문에 
브라우저 환경에서는 모든 모듈을 불러올 때까지 아무것도 할 수 없게되는 단점이 있었고, 그러한 비동기 모듈 로드 문제를 해결하고자 AMD 그룹이 탄생하게 됩니다.


- CommonJS
    - JS의 활용성을 높이려는 자발적 워킹그룹
    - JS를 범용 프로그래밍 언어로 만드는 것이 목적
    - JS 모듈 관리에 관한 코딩 표준을 제시함

## CommonJS
```js
var lib = require( "package/lib" );

function foo() {
    lib.log( "hello world!" );
}

module.exports = foo;
```

CommonJS에서 정의하는 전역변수는 module.exports와 require 객체가 있습니다.
exports로 모듈을 정의하고, require를 이용하여 모듈을 동기적으로 사용 할 수 있습니다.

필요한 파일이 모두 로컬 디스크에 있어 바로 불러 쓸 수 있는 상황, 
서버사이드에서는 AMD 방식보다 간결하게 사용.


- AMD (Asynchronouse Module Definition)
	- 웹 브라우저에서 JS 모듈 활용성을 높일 목적으로 CommonJS에서 파생됨
    - 가장 대표적으로 RequierJS
	- jQuery를 비롯 다수의 오픈소스 솔루션이 AMD를 지지
		- jQuery의 경우, 1.7부터 AMD 모듈 등록 기능을 지원하기 시작
	- CommonJS와 마찬가지로 JS 모듈 관리에 관한 코딩 표준을 제시함


## AMD 비동기 모듈 정의 (Asynchronous Module Definition)

```js
define(["package/lib"], function (lib) {
    function foo() {
        lib.log( "hello world!" );
    }
    return {
        foobar: foo
    }
});

require(["package/myModule"], function(myModule) {
    myModule.foobar();
});
```

AMD에서 정의하는 전역변수는 define와 require 객체가 있습니다.
define으로 모듈을 정의하고, 
require를 이용하여 모듈을 비동기적으로 사용합니다.

필요한 파일을 네트워크를 통해 내려받아야 하는 브라우저와 같은 환경에서는 
AMD가 CommonJS보다 더 유연한 방법을 제공 합니다.

AMD 명세는 define() 함수(클로저를 이용한 모듈 패턴)를 이용해 모듈을 구현하므로 전역변수 문제가 없다.

## UMD (Universal Module Definition)
AMD + CommonJS + IIFE 를 모두 지원하는 모듈
AMD, CommonJS 순으로 지원 여부를 확인하고, 둘 다 지원하지 않을 경우, IIFE 방식을 사용.

```js
(function (root, factory) {
    if (typeof define === 'function' && define.amd) {
        //AMD
        define(['jquery'], factory);
    } else if (typeof exports === 'object') {
        //Node, CommonJS-like
        module.exports = factory(require('jquery'));
    } else {
        //Browser globals (root is window)
        root.returnExports = factory(root.jQuery);
    }
}(this, function ($) {
    //methods
    function myFunc(){};
    //exposed public method
    return myFunc;
}));
```

## 모듈 시스템의 도입
<img src="img/es6.png" alt="" width="400">

이러한 모듈화의 움직임은 결국 언어자체의 스펙을 바꾸는데까지 영향을 끼치게 되었습니다.
ES6 스펙부터 언어자체적으로 모듈개념을 도입했습니다.
export와 import 키워드가 생겨남으로써 언어만으로도 처리할 수 있게 된 것이다. 

### ES6 Modules
```js
import lib from 'package/lib';

export function foo() {
    return lib.log( "hello world!" );
}
```

export로 모듈 선언 / import로 모듈 사용

import와 export라는 새로운 구문을 통해서 스크립트간의 관계를 규정하고 
상호간에 스크립트를 읽어들여올수 있게 되면서 
타 프로그래밍 언어와 같이 스크립트들을 모듈화 함으로써 
관리를 보다 손쉽게 할 수 있게 되었다. 

프로그램을 가능한 최대한으로 작은 유닛으로 모듈화 하여서 
언제 어디서든 필요할때마다 재사용할수 있게 만드는 것이다.


안타깝게도 ES6가 2015년에 나온지 2년이 지난 지금에도 브라우저(크롬, 파이어폭스, 기타 등등) 내에서 import나 export는 아직 구현되지 않은 기능이다. 
- [ES6 - Module 브라우저 지원 상황](http://caniuse.com/#feat=es6-module)


# webpack 소개

## webpack, 모듈 번들러란?
<img src="img/cover-1.jpg" width="400">

웹 서비스를 개발할 때 자바스크립트로 작성하는 코드의 양이 많아지면 유지보수가 쉽도록 코드를 모듈로 나누어 관리하는 모듈 시스템이 필요.

아직 브라우저 에서 import나 export가 구현되지 않음. (모듈 번들러가 기능을 대신 지원)

webpack은 이런 한계를 극복하기 위한 도구 중 하나로 자바스크립트 모듈화 도구.

<img src="https://cdn.filepicker.io/api/file/QIuZVivBTFWIu8LN9i3E" alt="">
그림 1. 모듈 번들러 웹팩

webpack은 여러가지 디펜던시(의존성)들을 효율적인 방법으로 통합하여 하나의 번들 파일로 생성.
이렇게 번들링된 파일을 HTML에서 로드.

### webpack 주요기능? 
* CSS 프리프로세서(SASS/LESS), ES6, TypeScript 양한 종류의 코드를 변환
* HTML, CSS, 이미지마저 JavaScript 파일 내부에서 로드 가능
* 직관적이고 효율적인 의존성 관리
* 플러그인을 통해 편리한 기능들을 사용 (Common chunk, Uglify, HMR..)
* 빌드 시스템으로써도 손색이 없기에 React의 공식 빌드 시스템으로 채택


# webpack으로 웹 개발환경 만들기 (설치 가이드)

## Node.js 설치
webpack을 사용하기 위해선 Node.js가 필수로 설치 되어 있어야 합니다.
아래 사이트를 방문하여 OS에 맞는 버전으로 설치합니다.
- Node.js 소개 - [http://d2.naver.com/helloworld/4994500](http://d2.naver.com/helloworld/4994500)
- Node.js 다운로드 - [https://nodejs.org/ko/](https://nodejs.org/ko/)

## 디렉터리 구조
```bash
[webpack2-demo]
├── dist               # output 디렉토리, 프로덕션 환경 배포 파일
├── node_modules       # npm package들이 설치된 디렉토리
├── src
│   ├── app.js
│   └── app.css
├── index.html
├── package.json       # 프로젝트 구성 정보
└── webpack.config.js  # 웹팩 설정 파일
```

## 프로젝트 초기화
커맨드를 실행 후, 프로젝트 폴더로 이동해서 Node.js 프로젝트를 생성합니다.

```bash
$ npm init (enter skip)
```
package.json 파일이 생성되었습니다.

잠깐 살펴보는 리눅스, npm 명령어
```bash
$ mkdir [folderName] # 디렉토리 생성
$ touch [fileName]   # 파일 생성
$ npm init -y        # 입력 생략
$ npm i jquery       # i  === install
$ npm i -S jquery    # -S === --save
$ npm i -D jquery    # -D === --save-dev
$ npm un -D jquery   # un === uninstall

# Node.js Path API
# 현재 파일 경로
__filename;  # D:\workspace\diagram\main.js
# 현재 디렉토리
__dirname;   # D:\workspace\diagram
# 경로 연결
path.join(__dirname, '/test') # /home/dirname/test
# 상대적인 경로로 연결
path.resolve('/foo/bar', './baz') # /foo/bar/baz
```

## webpack 설치
webpack을 시작하기 위해서 전역 또는 로컬(프로젝트내)에 설치해야합니다.

```bash
$ npm install webpack --save-dev
or
$ npm install webpack -g
```

webpack을 전역으로 설치할 수도 있지만 이렇게 하면 프로젝트별로 서로 다른 버전을 사용할 수가 없고,
프로젝트 의존성에 포함될 수 없게됩니다.<br>
webpack CLI 팩키지는 가능한 로컬에 설치해서 상대 경로를 사용하거나 npm 스크립트로 팩키지를 실행하는 것이 좋습니다.

## 빌드된 코드를 로드할 html 파일
```html
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
        <title>webpack2</title>
    </head>
    <body>
        <h1>Hello Webpack</h1>
        <script type="text/javascript" src="dist/app.bundle.js"></script>
    </body>
</html>
```

## 번들 파일 생성 (app.bundle.js)
다음과 같이 명령어를 실행하여 번들 파일을 생성 할 수 있습니다.
```js
//------ app.js ------
console.log('Hello webpack');
```     
```bash
# webpack {엔트리 파일 경로} {번들 파일 경로}
$ webpack ./src/app.js ./dist/app.bundle.js
```


## ES6 module 사용해 보기

```js
//------ hello.js ------
export default function hello() {
    console.log('Hello webpack');
}   
```

```
//------ app.js ------
import hello from './hello';
hello();
//console.log('Hello webpack');
```


## watch 모드
소스코드가 변경될 때마다 자동으로 감지해서 다시 번드링 해주는 기능.<br>개발중에는 주로 watch 모드를 이용.

```bash
# 엔트리 파일 변경시 자동 리빌드
$ webpack ./src/app.js ./dist/app.bundle.js --watch
or
$ webpack ./src/app.js ./dist/app.bundle.js -w
```

## use -p for production, minified code
```bash
# minified code
$ webpack ./src/app.js ./dist/app.bundle.js -p
```


## webpack의 기본적인 개념

* Entry: 웹팩이 파일을 읽어들이기 시작하는 부분을 설정.
* Output: 결과물이 어떻게 나올지 설정.
* Module: 웹팩을 통해 번들링을 진행할 때 처리해야 하는 태스크들을 실행.
웹팩은 다양한 형식의 확장자들(.css, .html, .scss, .jpg.. 등)을 모듈로 취급 함께 빌드 할 수 있도록 도와줍니다. babel과 같이 트랜스파일러를 사용해서 ECMA2015(ES6) 문법을 ES5 문법으로 바꿔주는 경우에도 사용할 수 있다. 
* Plugins: 확장기능.
다양한 플러그인을 통해 효과적으로 번들링할 수 있습니다. 
코드를 난독화(Uglify)하여 압축할 수 있고, 
공통된 코드(Common chunk)를 분리할 수 있고, 
코드를 저장 할때마다 자동으로 리로딩(HotModuleReplacement)할 수 있습니다. 

## webpack 설정 파일
```bash
#webpack.config.js 생성
$ touch webpack.config.js
```

```js
//------ webpack.config.js ------ 
module.exports = {
    entry: './src/app.js',
    output: {
        filename: './dist/app.bundle.js'
    }
}
```

```js
//------ package.json ------
"scripts": {
    "dev": "webpack -d --watch",
    "prod": "webpack -p"
}
```
```bash
$ npm run dev # 디벨로프 모드
or
$ npm run prod # 프로덕션 모드
```

### Multiple files, bundled together
app.bundle.js 배열 순서대로 하나의 파일로 생성 됩니다.
```js
const path = require('path');

module.exports = {
  context: path.resolve(__dirname, './src'),
  entry: {
    app: ['./home.js', './events.js', './vendor.js'],
  },
  output: {
    path: path.resolve(__dirname, './dist'),
    filename: '[name].bundle.js',
  },
};
```

### Multiple files, multiple outputs
여러 개의 JS 파일을 번들로 묶어 분리할 수 있습니다. 
```js
const path = require('path');

module.exports = {
  context: path.resolve(__dirname, './src'),
  entry: {
    home: './home.js',
    events: './events.js',
    contact: './contact.js',
  },
  output: {
    path: path.resolve(__dirname, './dist'),
    filename: '[name].bundle.js',
  },
};
```
home.bundle.js, 
events.bundle.js,
contact.bundle.js
3개 번들파일로 제공 

## HTML webpack Plugin
[html-webpack-plugin](https://github.com/jantimon/html-webpack-plugin) 이용해서 index.html 자동으로 만들기

* 번들링된 JS, CSS 파일들을 html에 자동으로 추가
* hash : JS, CSS파일에 해시값 추가, 파일 캐시 방지
* minify : html코드 압축
* template : [ejs템플릿](https://github.com/mde/ejs), 커스터마이징 가능

```bash
$ npm i html-webpack-plugin --save-dev
```

```js
//------ webpack.config.js ------
const HtmlwebpackPlugin = require('html-webpack-plugin');
const path = require('path');

module.exports = {
    // ...
    plugins: [
        new HtmlwebpackPlugin({
            title: 'Project Demo',
            minify: {
                collapseWhitespace: true
            },
            hash: true,
            template: './src/index.html'
        })
    ]
}
```

```html
//----- index.html -----
<!DOCTYPE html>
<html>
    <head>
        <meta http-equiv="Content-type" content="text/html; charset=utf-8"/>
        <title><%= htmlWebpackPlugin.options.title %></title>
    </head>
    <body>
        <div id="root"></div>
    </body>
</html>
```


## Style, CSS and Sass loaders
* css-loader: css파일을 자바스크립트에 포함
* style-loader: html에 브라우저에서 스타일 적용, head부분에 style태그 생성

```bash
$ npm i css-loader style-loader --save-dev
```

**webpack.config.js**
```js
module: {
    rules: [{
        test: /\.css$/,
        use: [ 'style-loader', 'css-loader' ]
    }]
}
```

* rules(loaders): 값으로 배열을 받으며, 어떤 파일에 어떤 로더를 적용할지 등을 설정
* test: 정규식조건(css, js)에 부합하는 파일들을 지정한 로더로 컴파일
* exclude: exclude 값으로 적힌 정규식에 해당되는 파일들은 로더의 영향을 받지 않습니다.

**sass-loader 설치**
```bash
npm i sass-loader node-sass --save-dev
```

**webpack.config.js**
```js
module: {
    rules: [{
        test: /\.scss$/,
        //test: /\.(css|scss)$/,
        use: [ 'style-loader', 'css-loader', 'sass-loader' ]
    }]
}
```

css?sourceMap,-minimize,sass?sourceMap,outputStyle=expanded

(s)css 파일을 압축시키지 않으면서 소스맵 사용

## Extract Text Plugin
SCSS 컴파일, 순수 CSS 파일 생성. style 태그 대신 css파일로 만들고 싶은 경우 사용
```bash
npm i extract-text-webpack-plugin --save-dev
```

**webpack.config.js**
```js
const ExtractTextPlugin = require("extract-text-webpack-plugin");

module: {
	rules: [{
	    test: /\.scss$/,
	    use: ExtractTextPlugin.extract({
	    	fallback: 'style-loader',
	     	use: ['css-loader', 'sass-loader'],
	     	publicPath: '/dist'
	    })
	}]
},
plugins: [
    new ExtractTextPlugin({
		filename: 'app.css',
		disabled: false,
		allChunks: true
	})
]
```

```js
//------ app.js ------
const css = require('./app.css');
or
import css from './app.css'; //ES6
```


### Setting up React and Babel
IE환경에서도 ES2015(ES6)를 사용하기 위해 babel 같은 트랜스파일러가 필요.

```bash
$ npm i react react-dom --save-dev
$ npm i babel-cli babel-core babel-loader babel-preset-env babel-preset-react --save-dev

# .babelrc 파일생성
$ touch .babelrc
```

```js
//------ .babelrc ------
{
    "presets": ["env", "react"]
}
```

```js
//------ webpack.config.js ------
module: {
    rules: [
        {
            test: /\.js$/,
            exclude: /node_modules/,
            use: {
                loader: 'babel-loader',
                options: {
                    presets: ['env', 'react']  // ES2015, React를 이용해서 빌드
                }
            }
        }
    ]
}
```

```js
//------ app.js ------
import css from './app.css';
import React from 'react';
import ReactDOM from 'react-dom';

ReactDOM.render(
  <h1>Hello, world!</h1>,
  document.getElementById('root')
);
```


## webpack 개발 서버
로컬 개발을 위한 [webpack dev server](https://webpack.js.org/configuration/dev-server/#components/sidebar/sidebar.jsx)  를 옵션으로 제공

```bash
$ npm i webpack-dev-server --save-dev
```

```js
//------ webpack.config.js ------
module.exports = {
    devServer: {
        contentBase: path.join(__dirname, 'dist'),
        compress: true,
        port: 8080,
        stats: 'errors-only',
        open: true,
        historyApiFallback: true
    },
}
```

```js
//------ package.json ------
"scripts": {
    "dev": "webpack-dev-server",
    "prod": "webpack -p"
}
```

```bash
$ npm run dev
```

## HMR (Hot Module Replacement) 사용하기
webpack-dev-server 를 사용함으로써 우리는 hot module replacement 설정할 수 있습니다. 

HMR은 코드를 수정하거나 저장할 때마다 브라우저에서 새로고침 없이 변경 사항이 즉시 반영(CSS 수정 시 유용), 업데이트 실패 시 새로고침을 수행

* inline: 전체 페이지에 대한 실시간 리로딩(Live Reloading) 옵션
* hot: 컴포넌트가 수정 될 경우 그 수정된 부분만 리로드 해주는 부분 모듈 리로딩(Hot Module Reloading) 옵션

```bash
# 전체 페이지를 로딩 
$ webpack-dev-server --inline
# 부분 로딩
$ webpack-dev-server --hot
```

```js
//------ webpack.config.js ------
const webpack = require('webpack');

module.exports = {
    devServer: {
        contentBase: path.join(__dirname, 'dist'),
        compress: true,
        port: 8080,
        stats: "errors-only",
        hot: true,
        open: true
    },
    plugins: [
        new webpack.HotModuleReplacementPlugin()
    ]
}
```

### devtool - 소스맵 (Source Map)
소스맵은 번들 파일 내의 코드를 원래 소스 파일로 연결함으로써 브라우저 개발자도구에서 쉽게 디버깅할 수 있습니다.

* 개발용: 빌드 시간, 로그, 디버깅이 중요.<br>
cheap-module-eval-source-map, inline-source-map, webpack-dev-server 명령어를 사용
* 배포용: 용량을 우선적으로 선택.<br>
'cheap-module-source-map' - webpack 명령어를 사용<br>
'source-map' - webpack -p 명령어(코드압축)
```js
//------ webpack.config.js ------
module.exports = {
    devtool: 'source-map', //js 소스맵하고만 관련, (s)css의 소스맵과는 무관
     module: {
        rules: [
            {
                test: /\.scss$/,
                use: ExtractTextPlugin.extract({
                    fallback: 'style-loader',
                    use: ['css-loader?sourceMap', 'sass-loader?sourceMap'], //css 소스맵
                    publicPath: '/dist'
                })
            }
        ]
    },
}
```

### 개발 및 배포 빌드 구분

```js
//------ package.json ------
"scripts": {
	"dev": "webpack-dev-server",
	"prod": "NODE_ENV=production webpack -p"
    //window환경
    //"prod": "set NODE_ENV=production&& webpack -p"
}
```

```js
//------ webpack.config.js ------
const isProd = process.env.NODE_ENV === 'production'; //true or false
const cssDev = ['style-loader', 'css-loader', 'sass-loader'];
const cssProd = ExtractTextPlugin.extract({
    fallback: 'style-loader',
    use: ['css-loader?sourceMap','sass-loader?sourceMap'],
    publicPath: '/dist'
});
const cssConfig = isProd ? cssProd : cssDev;

module.exports = {
    module: {
        rules: [
            {
                test: /\.scss$/,
                use: cssConfig
            }
        ],
    },
    devtool: isProd ? 'source-map' : 'cheap-module-eval-source-map',
    plugins: [
        new ExtractTextPlugin({
            filename: 'app.css',
            disable: !isProd,
            allChunks: true
        })
    ]
}
```

### RimRaf
rimraf 명령을 통해 dist 폴더 경로를 입력해 삭제합니다.

```bash
$ npm i rimraf --save-dev
```

```js
//------ package.json ------
 "scripts": {
	"dev": "webpack-dev-server",
	"prod": "npm run clean && NODE_ENV=production webpack",
	"clean": "rimraf ./dist/*"
}
```


### File loader와 URL loader (기타 파일 번들링)
웹팩은 css, image, font 등을 하나의 모듈로 인지하고 번들링 파일로 추출.
css background url(...) 이미지를 가져 올 수 없음.

* file-loader: jpg, png, gif, svg..등 파일을 처리
* url-loader: 작은 이미지나 글꼴 파일은 복사하지 않고 문자열 형태로 변환 ([Data URI Scheme](https://en.wikipedia.org/wiki/Data_URI_scheme))


```bash
$ npm i file-loader --save-dev
$ npm i url-loader --save-dev

# 이미지 최적화
$ npm i image-webpack-loader --save-dev
```

```js
//------ webpack.config.js ------
module: {
    rules: [
        { 
            test: /\.(jpe?g|png|gif|svg)$/i, 
            use: [
                    'file-loader?name=images/[name].[ext]',
                    'image-webpack-loader' 
            ]
            //use: ['file-loader?name=img/[name].[ext]&publicPath=assets/foo/&outputPath=app/images/']
        },
        {
            test: /\.(woff2?|svg)$/,
            use: [
                'url-loader?limit=10000' //10Kb 미만인 font, svg 파일을 url-loader로 처리하도록 변경
            ]
        }
    ],
}
```

리액트 이미지  불러오는 방법
<img src={require('이미지경로')} />


### CommonsChunkPlugin
공통으로 사용되는 모듈을 빼내어서 공통(common) 번들에 추가하고, 공통 번들이 존재하지 않으면 새로운 번들을 만들어 줍니다. 
```js
//------ webpack.config.js ------
const webpack = require('webpack');

module.exports = {
  plugins: [
    new webpack.optimize.CommonsChunkPlugin({
        name: 'commons', //the commons chunk name
        filename: 'commons.js', //the filename of the commons chunk
        minChunks: function (module) {
            // this assumes your vendor imports exist in the node_modules directory
            return module.context && module.context.indexOf('node_modules') !== -1;
        }
        //chunks: ['pageA', 'pageB'], //Only use these entries
    }),
  ]
}
```
### ProvidePlugin
모듈을 자동으로 로드. 모듈에서 임의의 변수로 식별될 때마다 모듈이 자동으로 로드되며, 이 모듈은 로드된 모듈의 내보내기 모듈로 채워진다.
```js
//------ webpack.config.js ------
const webpack = require('webpack');

module.exports = {
    plugins: [
        new webpack.ProvidePlugin({
            $: 'jquery',
            jquery: 'jquery'
        })
    ]
}
```


### UglifyJS Plugin
minify를 통해 소스 용량을 줄여주고, uglify를 통해 난독화 및 console.log를 제거해 주는 기능

```js
//------ webpack.config.js ------
const webpack = require('webpack');

module.exports = {
  plugins: [
    new webpack.optimize.UglifyJsPlugin({
        sourceMap: true,
        compressor: {
            warnings: false
        }
    }),
  ]
}
```

### PurifyCSS Plugin
메인 폴더에있는 html파일을 체크해서 사용되고 있는 필요한 css 최적화

```bash
npm i purifycss-webpack purify-css --save-dev
```
```js
//------ webpack.config.js ------
const path = require('path');
const glob = require('glob');
const PurifyCSSPlugin = require('purifycss-webpack');

module.exports = {
  plugins: [
    new PurifyCSSPlugin({
      // Give paths to parse for rules. These should be absolute!
      paths: glob.sync(path.join(__dirname, 'main/*.html')),
    })
  ]
}
```