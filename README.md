# Webpack 발표자료 정리

## Javascript 모듈의 필요성
### 현재 Javascript의 문제점
* 글로벌(전역) 스코프가 쉽게 오염
* 동일한 이름의 변수, 함수 사용 (중첩 문제)
* 올바른 의존성 순서
* 웹페이지가 커질수록 script 태그 수 의 증가


자바스크립트를 어느정도 다뤄본 사람이라면 자바스크립트의 스코프 관리가 지저분하다는 것을 알 수 있습니다.

변수나 함수들이 global scope에 저장 되어
변수를 일일이 관리 해야하고 
다른 스크립트에서 실수로 중복 선언이 되면
서로 충돌하는 예기치 않은 문제점들이 빈번하게 발생하기 때문이다.

각 파일이 어떤 파일들에 의존하고 있는지와 어떤 파일을 필요하지 않은지를 포함해서 파일들이 올바른 순서로 로드되도록 항상 신경을 써야 합니다.

다수의 script 태그는 브라우저가 서버로부터 코드를 가져오기 위해서 
최소한 태그의 수 만큼 호출을 해야한다는 뜻이므로 성능에 부정적인 영향을 끼칩니다.


### 이런 문제점들을 해결하기 위한 방법
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

### 모듈 시스템의 도입
<img src="img/es6.png" alt="" width="400">

코드베이스가 커지면 필연적으로 코드를 쪼개는 행위(코드의 모듈화)가 필요

* CommonJS
* AMD 
* UMD
* ES6 Modules

JavasScript가 브라우저 언어를 넘어 범용적으로 사용하기 위해 필요한 기술이 바로 모듈화였고, 
이를 논의 하기 위해 자발적으로 만들어진 그룹이 CommonJS 워킹 그룹이다.

### CommonJS
```js
var lib = require( "package/lib" );

function foo(){
    lib.log( "hello world!" );
}

module.exports = foo;
```
개발자들에 의해 AMD와 CommonJS 두가지 방법으로 모듈 관리 환경이 발전하게 되었는데, 
AMD 방식은 RequireJS가 많이 사용되고 있고 
CommonJS는 Browserify가 인기가 많았다. 
CommonJS는 NodeJS에서 사용하고 있는 방식이다.

명세에서 정의하는 전역변수는 module.exports와 require 객체가 있다.
exports로 모듈을 정의하고, 
require를 이용하여 모듈을 동기적으로 사용한다.
필요한 파일이 모두 로컬 디스크에 있어 바로 불러 쓸 수 있는 상황, 
즉 서버사이드에서는 CommonJS 명세가 AMD 방식보다 간결하다.

### AMD (Asynchronous Module Definition)
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
AMD에서는 모듈은 js로 분리해야한다. 
AMD 명세는 define() 함수(클로저를 이용한 모듈 패턴)를 이용해 모듈을 구현하므로 전역변수 문제가 없다. 대표적으로 requirejs 가 있다.
AMD 명세에서 정의하는 전역변수는 define과 require 객체가 있다.

define으로 모듈을 정의(의존성도 명시)하고, 
require를 이용(의존성도 명시)하여 모듈을 비동기적으로 사용한다.

필요한 파일을 네트워크를 통해 내려받아야 하는 브라우저와 같은 환경에서는 
AMD가 CommonJS보다 더 유연한 방법을 제공

### UMD (Universal Module Definition)
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
IIFE + AMD + CommonJS 를 모두 지원하는 모듈
AMD, CommonJS 순으로 지원 여부를 확인하고, 둘 다 지원하지 않을 경우, IIFE 방식을 사용한다.


### ES6 Modules
```js
import { lib } from 'package/lib';

export function foo(){
    return lib.log( "hello world!" );
}
```


이러한 모듈화의 움직임은 결국 언어자체의 스펙을 바꾸는데까지 영향을 끼치게 되었다.
ES6 스펙부터 언어자체적으로 모듈개념을 도입했다. 
export와 import 키워드가 생겨남으로써 언어만으로도 처리할 수 있게 된 것이다. 

ES6에 들어오면서 import와 export라는 새로운 구문을 통해서 
스크립트간의 관계를 규정하고 상호간에 서로의 스크립트를 읽어들여올수 있게 되면서 
타 프로그래밍 언어와 같이 스크립트들을 모듈화 함으로써 관리를 보다 손쉽게 할수 있게 되었다. 

즉, 프로그램을 가능한 최대한으로 작은 유닛으로 모듈화 하여서 언제 어디서든 필요할때마다 재사용할수 있게 만드는 것이다.


- [ES6 - Module](http://caniuse.com/#feat=es6-module)

안타깝게도 ES6가 2015년에 나온지 2년이 지난 지금에도 브라우저(크롬, 파이어폭스, 기타 등등) 내에서 import나 export는 아직 구현되지 않은 기능이다. 

ES6를 온전히 사용하기 위해서는 bundler(이하 번들러) 혹은 preprocessor(이하 프리프로세서)등을 사용해서 안정적인 자바스크립트로 컴파일 해주어야 한다. 

Webpack이라는 번들러를 사용해서 ES6를 컴파일 한다.

export 키워드에 의해 모듈을 정의
import 키워드에 의해 모듈을 사용





- JS 모듈 관리를 위한 코딩 표준을 정의하려는 노력이 있었음(CommonJS와 AMD)
- CommonJS
    - JS의 활용성을 높이려는 자발적 워킹그룹
    - JS를 범용 프로그래밍 언어로 만드는 것이 목적
    - 2009년 1월 Kevin Dangoor가 SSJS(서버-사이드 자바스크립트)에 대한 아이디어를 제시하고 사람들을 모으기 시작
    - JS 모듈 관리에 관한 코딩 표준을 제시함
- AMD(Asynchronouse Module Definition)
	- 브라우저에서의 JS 모듈 활용성을 높일 목적으로 CommonJS에서 파생됨
	- AMD는 CommonJS와 interoperable
	- jQuery를 비롯 다수의 오픈소스 솔루션이 AMD를 지지
		- jQuery의 경우, 1.7부터 AMD 모듈 등록 기능을 지원하기 시작
	- CommonJS와 마찬가지로 JS 모듈 관리에 관한 코딩 표준을 제시함
- RequierJS
	- AMD 표준을 준수하는 가장 인기 있는 JS Loader
	- 기존 JS 로딩의 문제(script 태그를 이용 JS 파일을 로딩할 경우...)
		- 로딩이 시퀀셜하게 진행
		- JS 파일간 dependency가 없을 경우 시퀀셜하게 로딩하는 것은 시간 낭비
		- JS 파일간 dependency가 있을 경우 개발자가 script 태그 배열 순서를 신경 써야 함
	- RequireJS를 비롯한 asynchronous loader의 역할
		- JS를 비롯한 모든 리소스를 가능한 병렬로 로딩
		- JS 파일간 dependency를 JS Loader가 지능적으로 관리






## Webpack 소개


### 모듈 번들러란?
* 대부분의 프로그래밍 언어에서는 코드를 여러 개의 파일에 나누고 이 파일들에 담겨있는 기능들을 사용
* 하지만 브라우저에서는 임포트를 사용할 수 없으며, 모듈 번들러가 기능을 대신 지원
* 비동기적으로 모듈들을 로딩하여 로딩이 완료되면 실행되도록 하거나 파일들을 하나의 파일로 묶어서 HTML에서 script 태그로 로딩될 수 있도록 만들어 줍니다.

<img src="https://cdn.filepicker.io/api/file/QIuZVivBTFWIu8LN9i3E" alt="">
그림 3. 모듈 번들러 웹팩. 개발을 하다보면, 개발 외적으로 생각보다 노력을 할애해야할 경우가 많다. 

단적인 예로 디펜던시 관리인데, 웹팩은 여러가지 디펜던시들을 효율적인 방법으로 통합하고 짜집기 하여 하나의 번들 파일을 생산해낸다. 
이 번들파일을 index.html에 등록하면 모든 것이 완성.


### Webpack이란게 왜 있는것일까? 
Webpack을 한 단어로 표현하자면 코드 Bundler이다. 
코드를 가져 와서 변환하고 Bundle 한 다음 새로운 버전의 코드를 반환


### Webpack이 어떤 문제점을 해결해줄까? 
SASS 또는 LESS와 같은 CSS 전처리기를 사용한 적이 있다면 SASS / Less 코드를 일반 CSS로 변환해야 한다는 것은 알고 있을 것이다. 
ES6, TypeScript나 다른 자바 스크립트 언어를 사용 해본 적이 있다면 변환 단계가 있다는 것을 알고 있을 것이다. 
Webpack이 정말 좋은 점은 다양한 종류의 코드를 목표로하는 것으로 변환 시킬 수 있다는 것이다. 
코드를 작성하여 해당 변경 사항을 실시간으로 출력 할 수도 있다.

### 웹팩의 장점은 
ES6를 컴파일하여 준다는 것을 넘어서 여러가지 디펜던시들을 하나의 번들로 묶어준다는 것이다. 
심지어는 CSS와 HTML마저 자바스크립트 내부에서 로드할수 있게해준다. 
이때까지 index.html에 각 플러그인 마다 필요한 자바스크립트와 CSS파일들을 일일이 써넣어주어야 하는 것이 상당히 귀찮은 작업이었지만, 

웹팩을 이용하면 직관적이고 효율적으로 디펜던시들을 관리할수 있게해준다.
            

### webpack을 선호하는지는 몇 가지 이유
* 비교적 최신이어서 이전의 번들러에서 발생하던 문제점과 단점을 피할 수 있습니다.
* 쉽게 시작할 수 있습니다. 그냥 평범한 자바스크립트 파일이므로 별도 형식의 환경설정 파일이 필요 없습니다.
* 플러그인 시스템을 통해서 훨씬 많은 것을 할 수 있으며 강력한 기능들을 사용할 수 있으므로 webpack 하나로 끝낼 수 있습니다.

### 클라이언트 사이드에서 ES6 및 import 기능 사용하기
* 클라이언트 사이드에서 단순히 ES6 문법을 사용하려면 babel 을 사용하면 됩니다. 
단, 이걸 한다고 해서 import 기능 까지 호환 되지는 않죠.

클라이언트 사이드에서도 import 기능을 사용 하려면 필요한것은 바로 Module Bundler 입니다.
Module Bundler 는 브라우저단에서도 CommonJS 스타일을 사용 할 수 있게 해주는 도구입니다.

이는 대표적으로 Browserify와 Webpack이 있는데요,
여러 로더를 지원하고 자체적으로 최적화가 이미 되어있는 webpack 을 사용

### Webpack 설정파일 (webpack.config.js)
* entry: ./src/js/main.js 파일을 가장 처음으로 읽습니다. 그리고 그 파일에서부터 import 된 파일들을 계속해서 읽어가면서 연결시켜줍니다.
* output: 읽은 파일을 모두 합쳐서 /dist/js/bundle.js 에 저장합니다.
* module: 읽은 파일들을 babel-loader 를 통하여 ES6 스크립트를 컴파일해줍니다.
* plugins: UglifyJsPlugin 을 사용하여 컴파일한 스크립트를 minify 합니다.

webpack이 잘 되는지 확인하려면 우선.. 
js 코드에서도 ES6 및 import 를 사용하는 예제를 만들어봐야겠죠?

```js
//------ sample.js ------
    class Sample {
        constructor(name) {
            this.name = name;
        }

        say() {
            console.log("HI, I AM ", this.name);
        }
    }

    export default Sample;

//------ app.js ------
    import Sample from './sample';

    let sample = new Sample('velopert');
    sample.say();
```                 


ES6와 웹팩의 조합을 이용한 뒤로, 어느순간 웹 프론트엔드 개발이 오로지 자바스크립트 개발인 것처럼 되어버렷다. 예전에는 프론트엔드라하면 자바스크립트의 사용을 최소화하고 HTML과 CSS의 극대화한 것과 같이 느껴졌지만, 이제는 자바스크립트 애플리케이션 개발중에 시각적인 효과(UI)를 위해 덤으로 HTML과 CSS를 얹혀놓는 느낌이 되어버렸다. 또한 내가 만들어놓은 모듈들은 ES6의 클래스로 저장을 시켜놓아서 Angular.js이건, React이건 어느 프레임워크에서도 동작이 되는 서비스모듈로 만들수 있게되었다. 재사용성을 넘어서 범용성의 극대화가 되었다.



webpack(뿐만 아니라 모든 CLI 팩키지에 해당되는)을 npm을 통해서 전역으로 설치할 수도 있지만 이렇게 하면 프로젝트별로 서로 다른 버전을 사용할 수가 없으며 프로젝트의 의존성에 포함될 수 없게됩니다. 
따라서 CLI 팩키는 가능한 로컬에 설치해서 상대 경로를 사용하거나 npm 스크립트로 팩키지를 실행하는 것이 좋습니다. 이미 글로벌로 설치된 CLI 팩키지가 있다면 삭제하고 다시 로컬로 설치하기를 권합니다.
