# [Learn and Understand NodeJS](https://www.udemy.com/course/understand-nodejs/?utm_source=adwords&utm_medium=udemyads&utm_campaign=Search_DSA_Beta_Prof_la.EN_cc.ROW-English&campaigntype=Search&portfolio=ROW-English&language=EN&product=Course&test=&audience=DSA&topic=&priority=Beta&utm_content=deal4584&utm_term=_._ag_162511579404_._ad_696197165421_._kw__._de_c_._dm__._pl__._ti_dsa-1677053911088_._li_9196883_._pd__._&matchtype=&gad_source=1&gad_campaignid=21168154305&gbraid=0AAAAADROdO0_sczDNpw8g4_LjE_XlQqVy&gclid=CjwKCAjwravBBhBjEiwAIr30VHtpqc6vF8B8LIIGH7dNjehWgG-ZhCcGS-IV-ARdvPVbzQFNNb_tvRoCMoYQAvD_BwE)

미루고 미루던 노드 공부. 자바스크립트에 대한 이해를 확실히 하기 위해선 노드JS도 잘 알아야 한다는 생각에 학습을 하게 되었다. 

## 목차
  - [V8 엔진](#1.-V8-엔진)
  - [모듈 시스템](#2.-모듈-시스템)
  - [이벤트](#3.-이벤트)

## 1. V8 엔진

맨날 말로만 듣던 V8엔진이 도대체 무엇인지 차근차근 알아보자.

### 1.1 마이크로프로세서

먼저 마이크로프로세서(CPU)를 살짝 건들고 가자

  - Node.js는 V8 엔진으로 브라우저 밖에서도 자바스크립트를 실행할 수 있다.
  - 우리가 Node.js에서 작성하는 코드는 자바스크립트지만, 실제로 V8(C++로 만들어진)이 이 코드를 받아 기계어로 변환하여 CPU에서 실행한다.
  - 마이크로프로세서(CPU)는 자신만의 명령어 집합(ISA)를 가진다.
  - 이 명령어 집합으로 작성된 코드를 머신코드, 즉 **기계어**라고 부른다.

실제 머신 코드 예시 (x86 어셈블리)
 
```text
mov eax, 1
add eax, 2
ret
```
  - 이러한 코드는 결국 이진수로 변환되어 CPU가 직접 해석하는 것이고, 모든 프로그램은 기계어로 변환되어야만 CPU가 실행할 수 있다.
  - 기계어를 고수준 언어로 변환하는 것을 컴파일러와 런타임 엔진이다.
  - 자바스크립트의 경우, V8 엔진이 담당하고 있다.

### 1.2 ECMAScript

V8에 대해 더 알아보기 전에 ECMAScript를 잠깐 짚어 가야한다. 

- ECMAScript는 쉽게 말해 자바스크립트의 공식 명세서다.([ECMA](https://262.ecma-international.org/6.0/)에서 메서드들 정의한 거보면 꽤 재밌다.)
- V8말고도 브라우저 별로 엔진이 다 다른데, 다른 엔진들이 다른 브라우저에서 동일한 동작을 할 수 있도록 하는 것이 ECMAScript
- 즉, 엔진들은 js 코드를 받아 ECMAScript 명세에 따라 파싱하고 기계어로 변환하여 실행하면서 표준을 지킨다

### 1.3 V8 조금씩 알아가기 - 정의

먼저 V8 엔진 내부 동작을 살펴보기 전에 간단하게 V8이 뭔지 보면 아래와 같은 정의들이 있다.

<img width="1000" alt="스크린샷 2025-05-19 오후 6 34 42" src="https://github.com/user-attachments/assets/0a3ba3be-a344-4a18-b967-b32d03267a9d" /><br />

- 구체적으로 V8이 C++로 어떻게 돌아가는지 V8 소스를 아주 잠깐만 살펴보자.
  
<img width="1000" alt="스크린샷 2025-05-19 오후 6 41 00" src="https://github.com/user-attachments/assets/e7c5a935-01a7-4733-835b-108ef2d412af" /><br />

- C++로 이루어진 수 많은 파일이 있고, 그중에 익숙한 ```date.cc```란 파일을 보면 무언가 열심히 계산하고 있는 코드를 볼 수 있다.
- 즉, js에서 ```new Date()```를 호출하면, 실제로는 V8의 ```date.cc``` 코드가 실행되는 것이다.

### 1.4 V8 더 알아가기 - 임베딩

복잡한 내용은 차지하고 V8은 결국 C++로 작성되어 있고, 브라우저 밖에서도 동작하는 엔진이다. 이 사실을 바탕으로 **embedding**이 가능하다
즉, C++로 작성된 어떤 프로그램이든 그 안에 V8 엔진을 내장해서 자바스크립트 코드를 실행할 수 있는 것이다.
이러한 embedding 덕분에 Node.js가 만들어졌다.

<img width="1000" alt="스크린샷 2025-05-19 오후 7 08 24" src="https://github.com/user-attachments/assets/58709743-58d5-45f6-be6b-2c431afdc0fc" /><br />

C++로 작성된 파일에 V8을 임배딩하고

<img width="1000" alt="스크린샷 2025-05-19 오후 7 09 39" src="https://github.com/user-attachments/assets/dddba86f-9cdb-47a5-98d4-74851dc881a9" /><br />

자바스크립트 코드를 입력하면, 정확하게 실행된다. 굉장하다. 

잠깐 위에서 언급했던 것처럼 V8을 임베딩하면 C++로 작성한 함수나 기능을 자바스크립트에서 키워드처럼 쓸 수 있다고 했다. 

<img width="1000" alt="스크린샷 2025-05-19 오후 7 18 05" src="https://github.com/user-attachments/assets/803052fb-9cb8-4ba8-a705-92e3553432a7" /><br />

위와 같이, ```print```처럼 js에 없는 함수를 C++에서 구현하고 V8에 키워드를 바인딩하면 V8는 js 코드를 파싱하다가 바인딩된 키워드를 만나고 C++ 함수를 실행하여 js코드로(혹은 처럼) 실행하는 것이다.

즉, 이런 호환이 가능하기 때문에 ECMAScript에 없는 파일 읽기, 쓰기, 데이터베이스 접근 등 다양한 기능을 js에서 쓸 수 있게 되었다 (바로 Node.js의 탄생. 굉장하다)

그럼 여기서 ```Node.js```가 무엇인지 정리해보자.

```Node.js```란 V8엔진이 좀 더 기계어에 가까운 C++를 등에 업고 서버/OS 프로그래밍 등을 javascript에서도 가능하게 만든 굉장한 녀석이다!!

## 2. 모듈 시스템

V8 엔진 덕분에 Node.js가 탄생할 수 있었던 배경도 알았으니 본격적으로 ```Node.js```에 대해 알아가보자. 

### 2.1 자바스크립트로 서버를 만들려면 무엇이 필요할까

서버가 되기 위해 자바스크립트에 필요한 기능을 하나씩 살펴보는 것이 곧 Node.js의 설계 철학을 알 수 있는 방법이다. 그럼 하나씩 자바스크립트가 Node.js가 되어가는 과정을 살펴보자.

#### 2.1.1 모듈화

자바스크립트가 모듈 시스템 없이 하나의 스크립트 파일에 모든 코드를 작성할 떄, Node.js는 CommonJS을 도입하여 파일 단위로 모듈화하였다.
ES6이후부터는 ES모듈도 사용가능하다. 

#### 2.1.2 파일 시스템 접근

웹 서버는 파일에 접근할 수 있어야 한다. 

하지만 ECMAScript 표준에는 파일 시스템 접근 API가 없기 때문에 Node.js는 C++로 구현된 fs 모듈을 자바스크립트에 바인딩해 제공한다.

#### 2.1.3 데이터베이스 연동

DB와의 연결, 쿼리, 트랜잭션 등 당연히 ECMAScript 표준에 없다. Node.js는 다양한 DB를 자바스크립트에서 쓸 수 있게 지원한다.

#### 2.1.4 네트워크 통신

너무나 당연하게도 ECMAScript 표준에는 네트워크 소켓이나 HTTP 서버 기능이 없다. 역시나 C++로 구현된 네트워크 라이브러리(http, net 등)를
자바스크립트에 바인딩해 제공한다. (이렇게 보니 자바스크립트에 있는 게 뭐지라는 생각이 든다)

#### 2.1.5 비동기 작업/장시간 작업 처리

Node.js는 서버 환경에 알맞게 이벤트 기반, 논블로킹 아키텍처로 지연 작업에도 다른 요청을 동시에 처리할 수 있게 한다.

#### 2.1.6 그렇다면 Node.js는 C++ 코드를 어떻게 사용하나

Node.js를 서버로써 작동할 수 있게 한 기능들을 간단하게 살펴봤다. 그럼 어떤 식으로 Node.js가 C++의 함수들을 가져와서 사용하는지 간단하게 봐보자.

[레포](https://github.com/nodejs/node)에 들어가보면 여러 디렉토리가 있고, 우리는 ```src/```와 ```lib/```만 확인해 보면된다.

```src/``` 내부엔 대부분 C++ 파일이다. Node.js가 C++의 OEM이 아닐까 의심될정도로 전부 C++다. 그렇다면 js 코드로 작성된 함수는 어디에서 오는 것인가? 

바로 ```lib/```이다. ```lib/```은 js 파일만 있다. 예를들어 ```lib/zlib.js```을 보면

```js
...
const binding = internalBinding('zlib');
...
```

이런 식으로 내부에 있는 C++로 작성된 모듈을 가져와 js로 래핑해주고 있다. 즉, Node.js는 C++의 저수준 코드를 js로 래핑해서 쓴다는 것이다. 

Node.js를 구성하는 여러 코어 중 가장 근본, 과연 각 모듈을 어떻게 불러오고 읽어내는지를 알아보자.

### 2.2 써 본 적 없는 CJS에 대하여

Node.js는 CommonJs로 파일 단위로 모듈화를 하고 있다고 했다. 

지금은 Node.js에서도 ESM을 주로 쓰지만, 과거 버전에서는 아직도 CJS도 있으니 CJS가 어떻게 작동되는지 한번쯤 알아두는 것도 좋다

그렇다면 Node.js 내부에서 CJS 기준으로 어떻게 모듈을 가져오고 내보내는지 살펴보자.

```js
// app.js
const greet  = require('./greet')
greet()

// greet.js
const greet = fuction() {
 console.log("hello world")
}

module.exports = greet
```

다른 파일에 위치한 ```greet.js```를 ```app.js```에서 호출하고 싶을 때, ```require```로 가져온다. 
```greet.js```에선 ```module.exports = greet```로 모듈을 내보낸다.
즉, ```greet.js```의 반환값이 ```module.exports```에 할당되는 것이다. 이러한 키워드들은 도대체 어디서 오는 걸까?

<img width="500" alt="스크린샷 2025-05-19 오후 9 33 34" src="https://github.com/user-attachments/assets/18263fdf-ec24-471a-adc4-e82a51f1831e" /><br />

require, module, module.exports, __filename, __dirname 등등 이러한 변수들은 Node.js가 제공하는 내부 변수들이다. 
결국 이러한 변수로 Node.js는 모듈 시스템을 만드는 것이다.

### 2.3 갑분 call by reference와 call by value

여기서 잠깐 js의 call by reference와 call by value를 봐야한다. 왜냐면 이 개념이 결국 Node.js의 모듈 시스템이기 떄문이다.

js에서 함수에 인자를 넘길 때, 항상 값이 복사되어 전달된다는 점을 떠올리고 예제 코드를 보면

1. 원시값인 경우

```js
function foo(x) {
  x = 100;
}
let a = 1;
foo(a);
console.log(a); // 1
```

원시값은 값자체가 복사되었기 때문에 함수 내부에서 변수를 변경해도 외부에 영향이 없다.

2. 객체인 경우

```js
function bar(obj) {
  obj.value = 100;
}
let b = { value: 1 };
bar(b);
console.log(b.value); // 100
```

객체인 경우 참조(혹은 주소)가 복사되어 함수에 넘어가기 때문에 함수 안에서 ```obj.value```를 변경하면 외부에도 영향을 준다.

그렇다면 왜 **항상 값이 복사되어 전달된다**는 건 무슨 의미일까?

3. 참조 자체도 사실 by value였던 거임
```js
function baz(obj) {
  obj = { value: 200 }; // obj 변수에 아예 새로운 객체의 참조를 할당한다
}
let c = { value: 1 };
baz(c);
console.log(c.value); // 1
```

이렇게 객체 내부의 프로퍼티가 아니라 객체 자체에 새로운 객체으 참조를 할당해버리면, 참조가 끊겨 바깥 변수에는 영향이 없다.

Node.js에서 모듈의 반환값이 ```module.exports```에 할당된다고 했다. 만약 객체를 exports 해버리게 되면

```js
// foo.js
module.exports = { count: 0 };

// app.js
const obj = require('./foo');
obj.count = 42;

// bar.js
const obj2 = require('./foo');
console.log(obj2.count); // 42 (같은 객체의 참조를 공유)
```

이렇게 다른 모듈에서 원본을 훼손해버리는 일이 발생한다. 
그래서 이러한 객체 참조 공유를 방지하고자 객체를 exports 하지 않고, 팩토리 패턴이나 class로 모듈을 export하거나 혹은 의도적으로 모듈을 공유하기 위해 이렇게 쓰기도 한다. (이 부분이 제일 흥미로웠다.)

### 2.4 그땐 그랬었지

ESM 이전, 모듈 춘추 전국시대에 Node.js는 **IIFE**를 사용하여, 모듈 패턴을 만들었다. 

```js
const myModule = (function() {
  const privateVar = '비공개';
  function privateFunc() { /* ... */ }
  return {
    publicMethod: function() { /* ... */ }
  };
})();

myModule.publicMethod(); // 가능
console.log(myModule.privateVar); // undefined (외부 접근 불가)
```

Node.js는 모든 모듈 파일을 내부적으로 Module Wrapper Function이란 IIFE로 래핑하면서 각 모듈 파일의 변수, 함수는 다른 파일과 완전히 분리된 스코프를 가지게 된다.

```js
(function (exports, require, module, __filename, __dirname) {
  // 실제 greet.js, app.js 등 모듈의 코드가 여기 들어감
});
```

## 2.5 require와 module.exports

그렇다면 ```require```는 어떤 방식으로 모듈을 찾아서 가져올까?

<img width="1000" alt="스크린샷 2025-05-20 오전 11 51 50" src="https://github.com/user-attachments/assets/84ee89e2-e715-4101-b295-ab92d5117e08" /><br />

```require```를 타고 들어가면 Node.js 내부에 있는 위치함을 확인할 수 있다. ```prototype```으로 ```Module```에 연결되어 있고, 실제 반환하는 것은 ```Module._load```라는 녀석이다. 이 함수를 한번 가보자

<img width="1000" alt="스크린샷 2025-05-20 오전 11 55 07" src="https://github.com/user-attachments/assets/7e9b403c-c35f-4e48-98f1-23d9e900cda7" /><br />

여기서, 흥미로운 점은 가져온 모듈을 캐싱한다는 것이다! ```require```로 한번 모듈을 불러오면 그 뒤로 캐싱된 모듈을 사용한다. 이는 [2.2.1](#2.2.1)의 모듈 참조 공유의 원인이기도 하다. 

<img width="1000" alt="스크린샷 2025-05-20 오후 12 00 11" src="https://github.com/user-attachments/assets/3162240b-da59-43f5-a055-0f37e16991ed" /><br />

이 부분이 모듈을 불러오는(실제로는 모듈 객체를 만드는) 부분이다. (무언가 모듈을 찾고 불러오는 하는 과정이 RSC에서 클라이언트 컴포넌트 참조 만드는 것과 비슷하다 생각했다)

<img width="1000" alt="스크린샷 2025-05-20 오후 12 02 22" src="https://github.com/user-attachments/assets/9f046aa8-907d-4093-ab69-92659ec20ece" /><br />

빈 객체에 파일 내용을 채울 준비를 한다. 

<img width="1000" alt="스크린샷 2025-05-20 오후 12 14 31" src="https://github.com/user-attachments/assets/f2641680-c2df-4e12-9343-822e2437b796" /><br />

그리고 js파일의 내용을 읽어 오고 

<img width="1000" alt="스크린샷 2025-05-20 오후 12 16 06" src="https://github.com/user-attachments/assets/975cbd44-2390-4c5f-b94e-445740ad97de" /><br />

그 파일을 V8 엔진으로 넘길 준비를 한다. ```module._compile```을 살펴보면

<img width="1000" alt="스크린샷 2025-05-20 오후 12 18 12" src="https://github.com/user-attachments/assets/b11f1a51-f2ae-4faa-84bd-91f392864f98" /><br />

드디어 ```wrapper```를 만나게 된다. 이제 파일은 래핑되었고, 무언가를 주입받을 준비가 끝났다. (무언가 모듈을 찾고 불러오는 하는 과정이 RSC에서 클라이언트 컴포넌트 참조 만드는 것과 비슷하다 생각했다)

<img width="1000" alt="스크린샷 2025-05-20 오후 12 20 18" src="https://github.com/user-attachments/assets/a1d343ca-c359-43b9-85d1-aaab718bcbd9" /><br />

[2.2](#2.2)에서 언급했던 ```exports, require, module, __filename, __dirname``` 키워드들이 주입된다.

<img width="1000" alt="스크린샷 2025-05-20 오후 12 34 45" src="https://github.com/user-attachments/assets/aa0b11d6-eaa2-48ca-9e5a-ee042f7997fd" /><br />

이모든 과정이 끝나면 ```require```로 가져온 모듈은 ```module.exports```로 반환된다.

정리하자면, ```require```에 넘긴 ```'./greet'```를 **전체 파일 경로로 변환**하고 Node.js 내부에서 **모듈 객체와 exports 객채를 생성**한다. 그리고 해당 파일을 읽어와 **exports, require 등이 단긴 래퍼 함수로 감싸서 V8로 실행**하는 것이다. 
 
## 3. 이벤트

이벤트란 무엇인가? 내게 이벤트는 브라우저 이벤트뿐이었지만, Node.js는 다르다. 

Node.js에는 두 종류의 이벤트가 있다. 시스템 이벤트와 커스텀 이벤트다. 시스템 이벤트는 OS에서 발생하는 이벤트고, 커스텀 이벤트는 js에서 사용자에 의해 트리거되는 이벤트다. 

그럼 두 이벤트를 알아보자. 

### 3.1 Event Emitter

```EventEmitter```는 여러 모듈에 이벤트를 발행하고, 여러 리스너가 구독하는 형태의 객체다. 

```js
const events = {
  greet: [fn1, fn2],
  something: [fn3]
};
```

쉽게 말해 키, 배열 쌍으로 가지고 있다가, emit이 되면 배열을 순회하면서 함수을 실행하는 것이다. 

아래는 ```EventEmitter``` 구현한 간단한 예시다.

```js
// emitter.js

function Emitter() {
  this.events = {};
}

// 리스너 등록함
Emitter.prototype.on = function(type, listener) {
  this.events[type] = this.events[type] || [];
  this.events[type].push(listener);
};

// 이벤트 발생시킴
Emitter.prototype.emit = function(type) {
  if (this.events[type]) {
    this.events[type].forEach(function(listener) {
      listener();
    });
  }
};

module.exports = Emitter;

// app.js
const Emitter = require('./emitter');
const emitter = new Emitter();

emitter.on('greet', function() {
  console.log('hello');
});

emitter.on('greet', function() {
  console.log('good bye');
});

emitter.emit('greet'); // 리스너에 등록한 이벤트 이름을 넘김
// hello
// good bye
```

여기서 눈치챘겠지만, ```EventEmitter```는 ```prototype```을 사용하고 있다.

실무에서 거의 보지 못 했던, 자바스크립트 서적 마지막 장에 위치해서 손이 가지 않은 ```prototype```이다.

 ```EventEmitter```를 비롯한 많은 Node.js 코어 모듈들은 내부적으로 ```prototype```을 바탕으로 설계되었다. 추후 사용하게 될 fs, http 등등의 기능들도 ```prototype```으로  ```EventEmitter```을 상속하여 사용한다. 

그때는 js에 클래스가 없어서 프로토타입으로 설계했겠지만(js의 class도 내부적으로는 프로토타입을 사용하지만), 프로토타입 덕분에 쉽게 상속 구조를 변경할 수 있어(런타임에 프로토타입 체인을 탐색하기 때문에) 가볍고 확장성이 높은 js만의 장점을 잘 살린 것 같다. (이 부분은 좀 더 공부해봐야할듯)

Node.js 소스로 돌아가서 ```/util.js```에 가보자

<img width="1000" alt="스크린샷 2025-05-20 오후 4 19 29" src="https://github.com/user-attachments/assets/2ac3aa6c-3005-4ae3-a78f-c99205d1ce73" /><br />

```inherits```라는 내장함수가 있다. 이름부터 뭔가 상속과 관련되어 보인다. ```inherits```는 두 개의 인자를 받는다 ```ctor```은 타겟(자식) 생성자고, ```superCtor```은 상속받을 부모 생성자다. 

<img width="947" alt="스크린샷 2025-05-20 오후 4 42 46" src="https://github.com/user-attachments/assets/fcbda07e-8daa-4b2c-91c1-adafefb9ec9e" /><br />

```ctor```의 인스턴스의 프로토타입 체인에  ```superCtor```의 ```prototype```을 연결한다. 

만약 ```new Child```을 하게 되면 아래와 같이 체인을 타면서 부모의 기능을 사용하게 된다. 

```
new Child() → Child.prototype → Parent.prototype → Object.prototype
```

그렇다면 한 번 ```inherits```을 사용하여 ```EventEmitter```를 상속받아보자.

<img width="750" alt="스크린샷 2025-05-20 오후 4 50 12" src="https://github.com/user-attachments/assets/5d51cd0e-fec2-45de-bf39-ef0a3d7726fb" /><br />

커스텀 이벤트인 ```Greetr```에 ```EventEmitter```를 상속받고, 

<img width="750" alt="스크린샷 2025-05-20 오후 7 06 44" src="https://github.com/user-attachments/assets/34f8d31c-d259-4a44-87d1-305ca8201e64" /><br />

추가적으로 새로운 메서드인 ```greet```을 정의했다. 프로토타입으로 ```EventEmitter```을 연결했기 때문에 ```emit```을 사용할 수 있다.

<img width="750" alt="스크린샷 2025-05-20 오후 7 13 14" src="https://github.com/user-attachments/assets/742ff95c-0f5d-414e-ac0a-f7839aef4e90" /><br />

이제 인스턴스를 하나 만들고 ```on```으로 이벤트를 등록하고 ```greet```을 실행해보면

<img width="750" alt="스크린샷 2025-05-20 오후 7 14 15" src="https://github.com/user-attachments/assets/859133ff-46af-41a8-952f-6105a1b3b761" /><br />

```on```에 등록한 로그가 잘 나온다. ```greeter.greet()```는 자신의 prototype에서 찾고, ```greeter.emit()```, ```greeter.on()```은 프로토타입 체인을 따라 ```EventEmitter.prototype```에서 찾는 것이다. 

### 3.2 call

프론트엔드를 하다보면 프로토타입도 그렇고, this도 그렇고, call, apply 이론만 배웠지 써본 적 없었는데, 여기 다 있었구나. Node.js 최고다.

```call```, ```apply```가 Event Emitter 상속에 있어 큰 관여를 하고 있다. 그것이 무엇인지 알아보자.

<img width="750" alt="스크린샷 2025-05-20 오후 7 49 04" src="https://github.com/user-attachments/assets/dccb36e2-75fd-48f3-91c5-33548d78db90" /><br />

아까 예제를 다시 보자. 여기서 한 가지 빠진 게 있다. 

위 예제는 Child 인스턴스에서는 Parent의 prototype에 정의된 속성만 접근할 수 있다. 

즉, Parent에서 생성자 함수 안에서 ```this```에 직접 붙이는 속성은 프로토타입 상속이 되지 않는다. 

가령, 

```js
function EventEmitter() {
  this._events = Object.create(null);
  // ... 
}
```

이렇게 ```EventEmitter```에서 this로 정의된 속성은 사용하지 못 하는 것이다. 이걸 해결하기 위해 ```cal```을 사용한다. (다른 언어에서 super()와도 비슷하다)

<img width="750" alt="스크린샷 2025-05-20 오후 7 54 15" src="https://github.com/user-attachments/assets/f76db5eb-1991-4226-aefe-3ed56dec8286" /><br />

```call```로 부모의 this를 바인딩해줘야 한다. 이제 위 ```EventEmitter```를 Class로 변경해보면 

<img width="750" alt="스크린샷 2025-05-20 오후 8 23 44" src="https://github.com/user-attachments/assets/5b3e5185-c908-4e2f-8c48-f199ee238f5e" /><br />

ES6 이후 Class가 나왔지만 여전히 js가 프로토타입 기반인 것은 바뀌지 않았기 때문에 프로토타입을 이해하는 것은 중요하다. 

## 4. 동기와 비동기


