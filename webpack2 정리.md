## Webpack 2 - Installation and Config 
(https://www.youtube.com/playlist?list=PLkEZWD8wbltnRp6nRR8kv97RbpcUdNawY)

**프로젝트 시작(package.json 파일생성)**
프로젝트 디렉토리 npm 초기화
```
npm init
enter -> skip

생략(한번에)
npm init -y
```

**webpack 설치**
```
npm install --save-dev webpack 
```

(npm i -D webpack)
(i는 install 약자)
(-D, --save-dev: Package will appear in your devDependencies)

npm i -g webpack(글로벌)
npm view webpack versions
npm view webpack versions --json (버전보기)
npm i -D webpack@2.2.0 (버전으로 설치)
npm i webpack@2.1.0-beta.25 --save


**webpack 실행**
webpack {엔트리 파일 경로} {번들 파일 경로}
엔트리 파일은 웹팩이 가장 먼저 읽을 파일이며, 번들 파일은 엔트리 파일을 시작으로 의존 관계에 있는 모듈들을 하나의 파일로 만든 결과물로 브라우저에서 사용할 수 있다. 
(엔트리 파일이 여러 개일 때에는 엔트리 파일마다 번들 파일이 생성된다.)

```
webpack ./src/app.js ./dist/app.bundle.js -p
(use -p for production, minified code)
webpack -p (짧게)

webpack ./src/app.js ./dist/app.bundle.js -p --watch
(use --watch to watch file changes)
webpack --watch (짧게)
```

**webpack.config.js**
루트경로에 webpack.config.js 파일생성
```
module.exports = {
  entry: './src/app.js',
  output: {
    filename: './dist/app.bundle.js',
  }
}
```

**package.json**
```
"scripts": {
    "dev": "webpack -d --watch",
    "prod": "webpack -p"
}
```

```
npm run dev
npm run prod
```

dependencies : 프로덕션에서 사용할 패키지 의존성 정보 --save
devDependencies : 개발용 패키지 의존성 정보 --save-dev

콘솔지우기
clear

## HTML Webpack Plugin

html5 기본 템플릿(vscode key: !+tab)

**html 자동생성**
```
npm i html-webpack-plugin --save-dev
```
https://github.com/jantimon/html-webpack-plugin

**webpack.config.js**
```
var HtmlWebpackPlugin = require('html-webpack-plugin');
var webpackConfig = {
  entry: './src/app.js',
  output: {
    path: 'dist',
    filename: 'app.bundle.js'
  },
  plugins: [
    new HtmlWebpackPlugin({
        title: 'Project demo',
        // minify: {
        //     collapseWhitespace: true
        // },
        hash: true,
        //excludeChunks: ['contact'],
        template: './src/index.html'
        //template: './src/index.ejs' 
    })
  ]
  //plugins: [new HtmlWebpackPlugin()]
}
```

**index.ejs**
https://github.com/tj/ejs
```
<!DOCTYPE html>
<html>
  <head>
    <meta http-equiv="Content-type" content="text/html; charset=utf-8"/>
    <title><%= htmlWebpackPlugin.options.title %></title>
  </head>
  <body>
  </body>
</html>
```

## Style, CSS and Sass loaders
```
npm i css-loader --save-dev (css파일 로드)
npm i style-loader --save-dev (html에 브라우저에서 스타일 적용)
npm install --save-dev css-loader style-loader (한번에)
```

**webpack.config.js**
```
module: {
    rules: [{
        test: /\.css$/,
        use: [ 'style-loader', 'css-loader' ]
    }]
}
```

**sass-loader 설치**
```
npm i sass-loader node-sass --save-dev
```

**webpack.config.js**
```
module: {
    rules: [{
        test: /\.scss$/,
        use: [ 'style-loader', 'css-loader', 'sass-loader' ]
    }]
}

module: {
    rules: [{
        test: /\.scss$/,
        use: [{
            loader: "style-loader"
        }, {
            loader: "css-loader"
        }, {
            loader: "sass-loader",
            options: {
                includePaths: ["absolute/path/a", "absolute/path/b"]
            }
        }]
    }]
}
```

### scss 변환 순수 css로
```
npm i extract-text-webpack-plugin --save-dev
```

**webpack.config.js**
```
const ExtractTextPlugin = require("extract-text-webpack-plugin");

module: {
	rules: [{
	    test: /\.scss$/,
	    use: ExtractTextPlugin.extract({
	    	fallback: "style-loader",
	     	use: ['css-loader', 'sass-loader'],
	     	publicPath: ‘/dist’
	    })
	}]
},
plugins: [
    new ExtractTextPlugin({
		filename: ‘app.css’,
		disabled: false,
		allChunks: true
	})
]


const extractSass = new ExtractTextPlugin({
    filename: "[name].[contenthash].css",
    disable: process.env.NODE_ENV === "development"
});

module.exports = {
    ...
    module: {
        rules: [{
            test: /\.scss$/,
            use: extractSass.extract({
                use: [{
                    loader: "css-loader"
                }, {
                    loader: "sass-loader"
                }],
                // use style-loader in development
                fallback: "style-loader"
            })
        }]
    },
    plugins: [
        extractSass
    ]
};
```

**app.js**
```
const css = require('./app.scss');
```


## Webpack Dev Server
```
npm i webpack-dev-server -D
```

**package.json**
```
"scripts": {
	"dev": "webpack-dev-server",
	"prod": "webpack -p",
}
```

**webpack.config.js**
```
var path = require('path');

output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'app.bundle.js'
},

devServer: {
    contentBase: path.join(__dirname, "dist"),
    compress: true, //gzip
    port: 9000,
    stats: "errors-only",
    open: true
}
```


## How to install React and Babel
```
npm i -D react react-dom
npm i -D babel babel-preset-react babel-preset-es2015
```

**babel loader**
```
npm i -D babel-core babel-loader
```


**.babelrc 루트경로 파일 생성**
```
{
	“presets”: [“es2015”, “react”]
}
```

**webpack.config.js**
```
module: {
    rules: [
        {
            test: /\.js$/,
            exclude: /node_modules/,
            use: [{
                loader: 'babel-loader',
                options: {presets: ['es2015']}
            }]
        }
    ]
}
```

**app.js**
```
const css = require('./app.scss');

import React from 'react';
import ReactDOM from 'react-dom';

ReactDOM.render(
  <h1>Hello, world!</h1>,
  document.getElementById('root')
);
```



## Using Webpack and Babel In Your Project

**babel 설치**
```
npm i --save-dev babel-core babel-loader babel-preset-es2015
npm i jquery babel-polyfill --save-dev
```

locahost 서버띄우기
npm i -g webpack-dev-server(설치)
webpack-dev-server

바벨
loadersinclude: path.resolve(__dirname, "src"),


## Multiple templates options and RimRaf
**dist 폴더삭제**
rimraf 명령어를 통해 삭제 가능합니다. 먼저 rimraf 모듈을 설치합니다.
```
npm i -D rimraf
```
rimraf 명령을 통해 원하는 폴더 경로를 입력해서 삭제합니다.


"prod": "npm run clean && webpack -p",
"clean": "rimraf ./dist/*"

**package.json**
```
"scripts": {
	"dev": "webpack-dev-server",
	"prod": "npm run clean && webpack -p",
	"clean": "rimraf ./dist/*"
}
```

excludeChunks: ['contact'],
chunks: ['contact']

entry: {
    app: './src/app.js',
    contact: './src/contact.js'
},
output: {
    path: path.resolve(__dirname, 'dist'),
    filename: '[name].bundle.js'
},


## How to use pug (jade) templates with Webpack
```
npm i-D pug pug-html-loader
```

## Hot Module Replacement - CSS

**webpack.config.js**
```
const webpack = require('webpack');

rules: [{
	test: /\.scss$/,
	use: [’style-loader', 'css-loader’, 'sass-loader']
}]

devServer: {
    contentBase: path.join(__dirname, "dist"),
    compress: true,
    port: 9000,
    stats: "errors-only",
    open: true,
    hot: true
},

plugins: [
	new webpack.HotModuleReplacementPlugin(),
	new webpack.NamedModulesPlugin(),
]
```

## Production vs Development Environment

**package.json**
```
"scripts": {
    "dev": "webpack-dev-server",
    "prod": "NODE_ENV=production webpack -p"
}
```

**webpack.config.js**
```
var HtmlWebpackPlugin = require('html-webpack-plugin');
var ExtractTextPlugin = require('extract-text-webpack-plugin');
var webpack = require('webpack');
var path = require('path');


var isProd = process.env.NODE_ENV === 'production'; //true or false
var cssDev = ['style-loader', 'css-loader?sourceMap', 'sass-loader'];
var cssProd = ExtractTextPlugin.extract({
    fallback: "style-loader",
    use: ['css-loader', 'sass-loader'],
    publicPath: '/dist'
});

var cssConfig = isProd ? cssProd : cssDev;

rules: [{
    test: /\.scss$/,
    use: cssConfig
}]

plugins: [
    new ExtractTextPlugin({
        filename: 'app.css',
        disabled: !isProd,
        allChunks: true
    })
]
```

## How to load Twitter Bootstrap with Webpack 2


