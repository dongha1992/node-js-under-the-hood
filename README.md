# [NodeJS Internals and Architecture](https://www.udemy.com/course/understand-nodejs/?utm_source=adwords&utm_medium=udemyads&utm_campaign=Search_DSA_Beta_Prof_la.EN_cc.ROW-English&campaigntype=Search&portfolio=ROW-English&language=EN&product=Course&test=&audience=DSA&topic=&priority=Beta&utm_content=deal4584&utm_term=_._ag_162511579404_._ad_696197165421_._kw__._de_c_._dm__._pl__._ti_dsa-1677053911088_._li_9196883_._pd__._&matchtype=&gad_source=1&gad_campaignid=21168154305&gbraid=0AAAAADROdO0_sczDNpw8g4_LjE_XlQqVy&gclid=CjwKCAjwravBBhBjEiwAIr30VHtpqc6vF8B8LIIGH7dNjehWgG-ZhCcGS-IV-ARdvPVbzQFNNb_tvRoCMoYQAvD_BwE](https://www.udemy.com/course/nodejs-internals-and-architecture/?couponCode=ND-MAY2025-L))

미루고 미루던 노드 공부. 자바스크립트에 대한 이해를 확실히 하기 위해선 노드JS도 잘 알아야 한다는 생각에 학습을 하게 되었다. 

## 목차
- [V8 엔진](#1v8-엔진)
  - [인터프리터 언어](#11-인터프리터-언어)
    - [마이크로프로세서(CPU)](#111-마이크로프로세서cpu)
  - [ECMAScript](#12-ecmascript)
  - [V8 조금씩 알아가기 - 정의](#13-v8-조금씩-알아가기---정의)
  - [JIT(Just-In-Time)](#14-jitjust-in-time)
  - [메모리 구조](#15-메모리-구조)
  - [임베딩](#16-임베딩)
- [모듈 시스템](#2모듈-시스템)
  - [자바스크립트로 서버를 만들려면 무엇이 필요할까](#21-자바스크립트로-서버를-만들려면-무엇이-필요할까)
    - [모듈화](#211-모듈화)
    - [파일 시스템 접근](#212-파일-시스템-접근)
    - [데이터베이스 연동](#213-데이터베이스-연동)
    - [네트워크 통신](#214-네트워크-통신)
    - [비동기 작업/장시간 작업 처리](#215-비동기-작업장시간-작업-처리)
    - [그렇다면 Node.js는 C++ 코드를 어떻게 사용하나](#216-그렇다면-nodejs는-c-코드를-어떻게-사용하나)
  - [써 본 적 없는 CJS에 대하여](#22-써-본-적-없는-cjs에-대하여)
  - [갑분 call by reference와 call by value](#23-갑분-call-by-reference와-call-by-value)
  - [그땐 그랬었지](#24-그땐-그랬었지)
  - [require와 module.exports](#25-require와-moduleexports)
- [이벤트 시스템](#3이벤트-시스템)
  - [Event Emitter](#31-event-emitter)
    - [Prototype](#311-prototype)
    - [call](#312-call)
  - [System Event](#32-system-event)
    - [Event Loop](#321-event-loop)
    - [process.nextTick()](#322-processnexttick)
    - [promise는 어떻게 작동하는가](#323-promise는-어떻게-작동하는가)
    - [그래서 async과 await은 어떻게 작동하느냐](#324-그래서-async과-await은-어떻게-작동하느냐)
    - [libuv](#325-libuv)
    - [file](#326-file)
    - [stream](#327-stream)
    - [pipe](#328-pipe)
- [네트워크](#4네트워크)
  - [HTTP](#41-http)
  - [DNS](#42-DNS)


## 1.V8 엔진

맨날 말로만 듣던 V8엔진이 도대체 무엇인지 차근차근 알아보자.

### 1.1 인터프리터 언어

Node.js는 자바스크립트의 런타임 환경이다. 자바스크립트는 인터프리터 언어다. 그럼 인터프리터 언어은 무엇인가

인터프리터 언어는 별도의 컴파일 과정 없이 소스 코드가 곧 런타임에 의해 실제로 실행된다. 런타임 자체는 C나 C++ 등으로 미리 컴파일되어 각 OS와 CPU 아키텍처에 맞는 바이너리(실행 파일)로 제공된다. (가령, node.js 공홈에서 다운로드 받은 파일)

런타임은 소스 코드(가령 ```node app.js```)을 입력값으로 받는다. 그러면 OS는 ```node```를 실행하여 내부적으로 소스코드를 파싱하는데, 한 줄씩 해석하면 바이트 코드를 변환시킨다. 그리고 해석한 결과를 CPU가 이해할 수 있는 머신코드로 변환한다. 

런타임 자체는 C나 C++ 등으로 미리 컴파일 된다고 하였다. 그렇다면 컴파일은 무엇인가

컴파일은 쉽게 말해 사람이 작성한 고급 언어(C, C++ 등)을 컴퓨터가 이해할 수 있는 저수준 언어(기계어)로 변환하는 것을 의미한다.

그래서 컴파일러는 컴파일 과정을 수행하는 프로그램으로 컴파일러의 결과물은 기계어로 변환한 실행 파일(.exe) 혹은 중간 코드 파일(.dll)이다. 

즉, 컴파일된 실행 파일을 실행하는 과정이 런타임이다. 


#### 1.1.1 마이크로프로세서(CPU)

그럼 마이크로프로세서(CPU)는 무슨 역할을 하는가

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

### 1.4 JIT(Just-In-Time)

당연하게도 인터프리터 언어는 런타임이 소스코드를 계속 해석해야 하므로 상대적으로 느릴 수 밖에 없다.

그래서 V8에서 등장한 것이 JIT(Just-In-Time) 컴파일이다. JIT은 자주 실행되는 코드를 런타임 도중에 머신코드로 즉시 컴파일하여 성능을 극대화한 것인데, 컴파일된 코드는 힙 메모리에 저장되었다가 CPU가 직접 실행한다. 

### 1.5 메모리 구조

V8의 메모리는 스택과 힙으로 나눠져있다. 스택엔 원시값이나 함수 호출 등 고정 크기 데이터가 적재되고, 힙엔 객체나 배열 같은 가변 크기 데이터가 적재된다.

```js
let x = 5;
let y = {"a" : 10, "b" : 20}
```
은 아래와 같이 메모리 저장된다. 

<img width="500" alt="스크린샷 2025-05-21 오후 8 35 40" src="https://github.com/user-attachments/assets/94405379-c565-422a-bfb5-bcfe043a0305" /><br />

만약 ```y.a```에 접근한다고 했을 때, 메모리에서는 히든 클래스 배열에서 키의 인덱스 위치를 찾고, 해당 인덱스로 객체에서 실제 값을 가져온다. 선형 탐색인 것이다.

그 외에도 인라인 캐싱이나 해싱을 사용하여 최적화한다. 

### 1.6 임베딩

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

## 2.모듈 시스템

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

CJS를 기반으로 하는 ```require```는 동기적으로 동작한다. require가 호출되면 모듈 파일을 디스크에서 읽고, 파싱하고 실행하여 Initial phase (이벤트 루프 시작 전)에 모듈을 모두 로딩한다.

(반대로 import는 Promise 기반으로 비동기적으로 모듈을 로딩한다.)

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

여기서, 흥미로운 점은 가져온 모듈을 캐싱한다는 것이다! ```require```로 한번 모듈을 불러오면 그 뒤로 캐싱된 모듈을 사용한다. 이는 [2.3](#2-3)의 모듈 참조 공유의 원인이기도 하다. 

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

[2.2](#2-2)에서 언급했던 ```exports, require, module, __filename, __dirname``` 키워드들이 주입된다.

<img width="1000" alt="스크린샷 2025-05-20 오후 12 34 45" src="https://github.com/user-attachments/assets/aa0b11d6-eaa2-48ca-9e5a-ee042f7997fd" /><br />

이모든 과정이 끝나면 ```require```로 가져온 모듈은 ```module.exports```로 반환된다.

정리하자면, ```require```에 넘긴 ```'./greet'```를 **전체 파일 경로로 변환**하고 Node.js 내부에서 **모듈 객체와 exports 객채를 생성**한다. 그리고 해당 파일을 읽어와 **exports, require 등이 단긴 래퍼 함수로 감싸서 V8로 실행**하는 것이다. 

 
## 3.이벤트 시스템

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

#### 3.1.1 Prototype

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

#### 3.1.2 call

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

### 3.2 System Event

자, 이제 Node.js의 두 번째 이벤트, System Event다. 먼저 이벤트 루프, 그리고 파일 I/O 등을 살펴보자.

#### 3.2.1 Event Loop

이벤트 루프는 Node.js에서 가장 중요한 개념이다. 이벤트 루프는 여러 단계를 거치는데 각 단계는 자신만의 queue를 가지고 있다. queue가 모두 실행되어야 다음 단계로 넘어간다.

그럼 각 단계별로 자세히 알아보자.
  
    1. Initial Phase

Initial Phase에서는 Node.js가 실행되고 이벤트 루프가 돌기 전, 스크립트 파일을 처음부터 끝까지 읽으며 코드를 실행한다.

이 과정에서 변수를 선언하거나 함수를 정의하는 등 동기 코드는 실행되고 비동기 코드는 콜백을 등록만 한다. 

Initial Phase가 끝나고 이벤트 루프가 돌기 때문에 서버 성능을 위해서는 Initial Phase를 짧게 가져가는 것이 좋다. 

<img width="750" alt="스크린샷 2025-05-21 오후 9 20 54" src="https://github.com/user-attachments/assets/216d4195-06d7-4d9e-b40a-78ac065fa2bd" /><br />

오래 걸리는 동기 작업이 있다면, 이벤트 루프는 그만큼 늦게 시작한다. 

    2. Timer phase
    
Timer phase에선 setTimeout, setInterval의 Initial Phase에서 등록된 콜백이 실행된다. 대기 중인 타이머가 있으면 콜백을 실행하고, 없으면 다음 페이즈로 이동한다.

타이머를 구현하는 방법에는 세 가지가 있다. 5초 타이머를 콜백에 등록했다고 가정하면

먼저 while을 돌면서 현재 시간을 체크하며 5초가 지났는지 확인하는 것이다. 이러한 방법은 당연히 CPU 낭비에, 다른 작업들이 모두 블로킹되기 때문에 비효율적이다.

두 번째로 OS의 sleep을 사용하는 것이다. 프로세스 자체를 sleep 상태로 두고, 커널에 5초 뒤에 꺠워달라고 요청하는 것이다. 이 방법은 CPU 자원을 아낄 수 있지만, 프로세스가 멈추기 때문에 Node.js와는 맞지 않는다.

마지막으로 하트웨어 타이머(인터럽트)를 활용하는 방법이다. 실제 하드웨어 타이머가 정확한 시점에 인터럽트를 발생시켜 프로세스를 깨우는 것이다.

Node.js 2번과 3번 방법을 조합하는데, 간단히 설명하자면 ```libuv```를 통해 OS 커널의 타이머 기능을 활용한다. 타이머가 등록되면 별도의 ```libuv``` 스레드에서 언제 콜백을 실행해야 하는지 메타데이터를 저장한다.

T0라는 특정 시각에 여러 타이머가 동시에 등록됐다고 가정하자.

<img width="1208" alt="스크린샷 2025-05-21 오후 10 04 50" src="https://github.com/user-attachments/assets/e99758c7-de10-4206-a8f2-343f5f1032c4" /><br />

```libuv```는 각 타이머의 종료 시점(현재 시각 + 대기 시간)으로 관리하여 가장 짧은 대기 시간만큼 sleep을 한다. sleep이 끝나면 종료 시점이 지난 타이머 콜백을 모두 실행한다. T0+100ms까지 sleep 하고 콜백 실행 그리고 다음 짧은 대기 시간전까지 다시 sleep ..  이런 식이다. 

그렇기 때문에 OS 스케줄링, 다른 페이즈의 지연 등등의 이유로 실행 시점은 지정한 시간보다 늦을 수 있다. 

    3. Pending Callbacks Phase

Timer phase가 끝나면 Pending Callbacks Phase이 시작된다. 

Pending Callbacks Phase는 이름 그대로 I/O 작업 중 일부 콜백을 다음 루프에 미뤄서 실행하는 역할을 한다.

중요도가 낮은 콜백을 의도적으로 지연시켜서 더 중요한 작업(타이머 등)을 먼저 처리하도록 한다. 대표적으로 TCP 연결 오류나 네트워크 에러가 해당된다.

이러한 페이즈가 별도로 분리된 이유는, 리얼 월드에선 네트워크 에러가 빈번한데 대부분 이러한 네트워크 에러는 재시도로 해결할 수 있기 때문에 에러가 났다고 바로 처리하지 않고 루프를 한 번 더 돌리고 다음 페이즈에서 처리하는 것이 다른 작업들을 지연하지 않고 처리할 수 있다. 

    4. Idle/Prepare Phase

Idle/Prepare Phase은 Node.js와 libuv가 내부적으로만 사용하는 영역이다. (개발자는 이 단계를 체감할 수 없다)

다음 페이즈에 있을 실제 I/O 작업을 준비하는 단계이다. (예를 들어, 서버 소켓을 열고, 새로운 연결을 감지할 준비)

    5. Poll Phase

Poll Phase는  I/O 작업이 실제로 처리되는 단계다. 이 단계에서는 두 가지 일이 발생하는데 I/O 이벤트를 감지하고(polling), I/O 콜백을 실행한다.

I/O 이벤트를 감지 즉, 데이터가 있는지 계속 커널에 물어보고, 데이터가 있으면 콜백을 실행한다. 네트워크 소켓 lisening, 파일 읽기 등이 이 단계에 이루어진다.

만약, 실행할 콜백(Initial Phase에서 등록했던)이 있는 경우 큐에 쌓인 콜백을 즉시 실행하고 큐가 다 비았으면 다음 phase로 넘어간다. 

하지만 poll queue가 비었는데 만료될 타이머가 있다면 타이머가 만료될 때까지 ```epoll_wait```을 블로킹한다.

가령, setTimeout이 100ms 후 예정되어 있으면, 최대 100ms까지만 대기하는 것이다. 타이머가 만료되면 timers phase로 돌아가서 타이머 콜백 실행한다.

만약 poll queue가 비어 있고, 타이머도 없는 경우라면 poll phase는 이벤트가 도착할 때까지 ```epoll_wait```으로 블로킹한다. (잠자기 모드처럼)

(동기 I/O의 경우 poll phase와 무관하게 전체 이벤트 루프가 멈춘다)

    6. Check Phase

Check Phase는 개발자가 제어할 수 있는 유일한 페이즈다. setImmediate로 등록된 콜백이 이 단계에서 실행된다.

만약, 긴 콜백이 있다면 여러 개의 작은 콜백(setImmediate을 사용하여)으로 쪼개서, 이벤트 루프를 블로킹하지 않고, 각 페이즈마다 코드를 조금씩 실행할 수 있다.

해당 페이즈에서 한 가지 살펴볼 것은 file이 있는 경우와 없는 경우에서의 실행 순서다.

먼저 파일이 있을 경우다. 

<img width="750" alt="스크린샷 2025-05-22 오후 12 52 23" src="https://github.com/user-attachments/assets/5fc5ddc6-5358-49fb-b4ae-141a1352c552" /><br />

코드의 흐름을 살펴보면

Initial phase에 파일을 open, setImmediate을 등록한다. 그리고 Poll phase에 파일 읽기 I/O 요청과 콜백 대기가 이루어진다.

이제 check phase에 도달하면 setImmediate 콜백이 실행 되는데 파일 읽기 콜백은 아직 준비 안 되어서 다음 루프 때 poll phase에서 파일 읽기 콜백 준비하여 실행한다.

<img width="750" alt="스크린샷 2025-05-22 오후 12 56 44" src="https://github.com/user-attachments/assets/ab5e66c9-13c8-41bb-8050-2f4e0b01f55e" /><br />

그리고 다음은 파일이 없을 경우다. 코드는 동일하고 파일만 존재하지 않는다.

코드의 흐름은 Initial phase에서 파일 open을 시도하지만 실패한다. Poll phase에서 에러 콜백 즉시 준비하고 실행한다. 그리고나서 check phase에서 setImmediate 콜백 실행된다.

<img width="750" alt="스크린샷 2025-05-22 오후 12 57 55" src="https://github.com/user-attachments/assets/858de6bf-ab6d-4da7-a06c-a986ea79bcb3" /><br />

```js
fs.readFile(f, () => {
  setTimeout(() => {
    console.log("timeout")
  }, 0)

  setImmediate(() =>
  console.log("setImmidiate called")
      )
    }
)

// setImmidiate called
// timeout
```

쉽게 정리하자면, Poll phase에서 fs.readFile의 콜백 실행되면서 콜백 안에서 setTimeout(0)과 setImmediate를 등록한다.

setImmediate는 바로 다음 check phase에서 실행되고, setTimeout(0)은 다음 이벤트 루프의 Timers phase에서 실행되는 것이다. 

만약, fs.readFile의 콜백 밖에서 root 레벨에서 실행하면 어떨까?

```js
  setTimeout(() => {
    console.log("timeout")
  }, 0)

  setImmediate(() =>
  console.log("setImmidiate called")
  )

// ??
// ??
```

이렇게 되면 순서를 예측하지 못한다. 이유인즉슨 Node.js가 스크립트를 시작할 때, 어떤 phase가 먼저 도달하는지는 시스템 상황이나 현재 이벤트 루프 상태 등에 따라 달라지기 때문이다. (실제로 계속 돌려보면 랜덤으로 순서가 바뀐다. 신기...)

    7. Close Callbacks Phase

이제 마지막 페이즈다. 이 단계에서는 소켓, 파일, 서버 등 리소스가 닫힐 때 발생하는 ```close``` 이벤트의 콜백이 실행된다.

이렇게 이벤트루프의 모든 페이즈를 살펴봤다. 그림으로 보면 아래와 같다. 

<img width="1000" alt="스크린샷 2025-05-22 오후 2 06 53" src="https://github.com/user-attachments/assets/fca77011-4824-4fdf-91ae-16e77bd77437" /><br />

이제, 각 페이즈가 끝날때마다 등장하는 ```process.nextTick()```에 대해 알아보자.

#### 3.2.2 process.nextTick()

```process.nextTick()```는 각 페이즈(타이머, poll, check 등) 사이마다 등록된 모든 nextTick 콜백을 즉시 실행한다. 이벤트루프의 일부라기보다 **마이크로태스크 큐**로 분리된다.

<img width="750" alt="스크린샷 2025-05-22 오후 1 44 53" src="https://github.com/user-attachments/assets/68edfbcc-dbd3-4b8e-ab9d-4a4b30e132dc" /><br />

<img width="750" alt="스크린샷 2025-05-22 오후 2 09 37" src="https://github.com/user-attachments/assets/8c474881-e477-4bf7-b9d5-c5178ce5001d" /><br />

위 예시처럼 타이머 phase에서 콜백 실행 중 process.nextTick을 등록하면, 다음 phase인 timer phase로 넘어가기 전 nextTick 큐에 쌓인 모든 콜백을 실행한다.
즉, nextTick은 가장 빠른 비동기 콜백이며, 지금 당장 처리해야 하는 우선순위 작업에 적합하다. 

#### 3.2.3 promise는 어떻게 작동하는가

Promise는 콜백을 쉽게 사용하기 위한 문법적 설탕이란 말을 많이 들었을 것이다. 조금 어렵게 설명하자면, Promise는 콜백과 큐 등 기존 비동기 구조 위에 더 읽기 쉬운만들어진 추상화 계층이라고 할 수 있다.

자세한 내용에 앞서 예제를 한 번 보고 가자.

<img width="750" alt="스크린샷 2025-05-22 오후 2 26 38" src="https://github.com/user-attachments/assets/0c9223bd-895b-4aeb-8f03-14f9a8fb4f84" /><br />

```promiseWork```는 4초 뒤에 리졸브 되는 setTimeout 콜백을 **즉시** 프로미스로 반환한다. 

<img width="750" alt="스크린샷 2025-05-22 오후 2 27 19" src="https://github.com/user-attachments/assets/f809b437-2645-4e4c-80cf-2fdd4298c32b" /><br />

반환된 프로미스는 리졸브되고, 

<img width="750" alt="스크린샷 2025-05-22 오후 2 27 59" src="https://github.com/user-attachments/assets/6bca8522-7d1a-471f-8daf-6b7e5bbb7356" /><br />

Initial phase 단계가 끝나면 setTimeout이 timer 단계에서 실행된다. 전체 코드는 아래와 같다.

<img width="750" alt="스크린샷 2025-05-22 오후 2 28 38" src="https://github.com/user-attachments/assets/ed1a8845-7617-4f25-be91-71eba7691321" /><br />

한번 흐름을 생각해보자. 

Initial phase가 시작되면서 모든 console.log와 promiseWork를 실행, 실행하고, setTimeout을 콜백에 등록할 것이다. 

```promiseWork```가 실행되면, Promise 객체가 생성되는데 이때 Promise의 콜백이 즉시 실행되면서 setTimeout을 등록하게 된다. setTimeout은 4초 뒤 resolve를 호출할 것이다. 

Initial phase가 끝나면 Timer phase가 시작되고 등록된 setTimeout의 콜백이 실행된다. 

그리고 setTimeout가 4초 뒤 resolve를 호출하면 이제서야 ```promised.then```에서 콜백으로 받은 로그가 실행될 것이다. 그럼 실행결과를 보자

<img width="750" alt="스크린샷 2025-05-22 오후 2 59 00" src="https://github.com/user-attachments/assets/bc97f7b6-a561-481b-8d77-8089c0d1b288" /><br />

```---END Initial phase---```가 끝나고 이벤트루프에 진입한다. 

<img width="750" alt="스크린샷 2025-05-22 오후 3 06 47" src="https://github.com/user-attachments/assets/d3aa5283-8bc1-42a8-900e-ef8c66f10641" /><br />

콜백에 등록된 setTimeout가 실행되었다.

<img width="750" alt="스크린샷 2025-05-22 오후 3 07 56" src="https://github.com/user-attachments/assets/654a9e4b-4122-404e-97ad-56903e9c5f05" /><br />

4초가 지나고 resolve가 되면서 then으로 물린 콜백이 실행된다. (어우 헷갈려)

여기서 흥미로운 점은 Promise가 resolve나 reject 되었을 때, then이나 catch에 등록된 콜백이 **마이크로태스크 큐**에서 실행된다는 것이다. 

<img width="750" alt="스크린샷 2025-05-22 오후 3 20 35" src="https://github.com/user-attachments/assets/320186cb-2673-4f6a-805d-c8b3ed4c25aa" /><br />

즉, ```nextTick```으로 콜백을 받아 실행하는 것과 동일하다.

다시 정리하면 현재 동기 코드로 작동하는 코드들 혹은 phase가 끝난 뒤, 다음 phase로 넘어가기 전에 Promise의 then이나 catch 콜백이 반드시 먼저 실행되는 것이다!!!

#### 3.2.4 그래서 async과 await은 어떻게 작동하느냐

async 함수 내에서 await를 만나면 해당 Promise가 resolve될 때까지 함수 실행을 동기적으로 멈춘 것 같아 보이지만, 실제로는 await **이후** 코드는 Promise의 then처럼 콜백으로 분리되어 실행된다.

아주 흥미로운 사실을 발견했다. **await 이후 코드는 Promise의 then처럼 콜백으로 분리된다**고 했다. 확인해보자 

<img width="750" alt="스크린샷 2025-05-22 오후 3 35 26" src="https://github.com/user-attachments/assets/6d60a739-4388-42d5-b5c9-eb24408fd1df" /><br />

```then``` 콜백으로 받던 것을 ```async```,```await```으로 수정했다. 그리고 실행해보면 

<img width="750" alt="스크린샷 2025-05-22 오후 3 39 16" src="https://github.com/user-attachments/assets/8f81babc-1842-4d24-b6be-07bcfcab13d8" /><br />

실제로 await **이후** 코드는 then의 콜백처럼 resolve이후 같이 실행된다!! (진짜 신기함)

여기서 await, async 형제가 비동기 코드를 동기처럼 보이게 해준다는 이유가 있다.

await를 만나면 Promise가 resolve될 때까지 함수 실행이 일시 중단되지만 이벤트 루프는 멈추지 않고 다른 작업을 계속 처리한다. 그래서 비동기이지만 동기처럼 보이는 것!

Promise가 resolve되면 **마이크로태스크 큐**, 즉 nextTick에서 실행된다. nextTick은 각 페이즈가 끝나면 실행된다. 혹시 이 과정이 헷갈리다면 간소하게 마지막 예제를 보면 된다.

<img width="750" alt="스크린샷 2025-05-22 오후 3 54 42" src="https://github.com/user-attachments/assets/2b0fbf05-3ddc-47b5-9bee-9b29886bde08" /><br />

```promiseWork```에서 setTimeout을 제거했다. 

<img width="750" alt="스크린샷 2025-05-22 오후 3 56 50" src="https://github.com/user-attachments/assets/4cc767de-47a3-479f-8045-a0c7a336e339" /><br />

async 함수인 ```run```

<img width="750" alt="스크린샷 2025-05-22 오후 3 55 51" src="https://github.com/user-attachments/assets/a5275f6e-e12e-4c54-9aba-fa020fc9f8db" /><br />

그리고 0초 뒤에 실행되는 setTimeout이 있고 ```run```이 실행된다. 실행 순서를 확인해보면

<img width="750" alt="스크린샷 2025-05-22 오후 3 58 09" src="https://github.com/user-attachments/assets/0ad87a7c-9cda-466c-a8d5-5a7140b41276" /><br />

Initial phase -> nextTick() -> Timer phase 이기 때문이다!!

#### 3.2.5 libuv 

libuv은 이벤트 루프와 더불어 Node.js의 핵심이다. 

<img width="1000" alt="스크린샷 2025-05-20 오후 8 55 33" src="https://github.com/user-attachments/assets/62b6921b-38cd-47d7-8e86-75150c3cf238" /><br />

libuv는 Node.js에 내장된 C 라이브러리로 Node.js에서 이벤트 루프를 구현하여 비동기를 담당한다.

위 그림에서 libuv는 OS에 요청을 보내고, 이벤트가 완료되면 Queue에 등록한다. 이때, libuv는 이벤트 루프를 통해 완료된 이벤트가 있는지 확인한다.

완료된 이벤트가 있으면 콜백으로 V8에 전달한다. V8은 동기이므로 스택에 쌓인 이벤트를 하나씩 실행한다. 이것이 libuv의 일이다. (브라우저의 비동기와도 동일한 거 같다)

#### 3.2.6 file

드디어 파일 시스템을 해볼 차례다. 

```greet.txt```라는 텍스트 파일을 하나 만들고, ```app.js``에서 파일을 읽어보자.

<img width="750" alt="스크린샷 2025-05-20 오후 10 28 57" src="https://github.com/user-attachments/assets/9687eb94-03c3-40e1-9958-6255747a9a1f" />
<br />

여태까지 학습했던 것을 잠시 복습해보면, ```fs```은 C++로 구현된 모듈이다. 임베딩된 V8이 js 코드로 래핑해서 Node.js에서 모듈처럼 사용할 수 있다.

```__dirname```은 Node.js가 모듈 시스템을 구현할 때, 즉시 실행 함수 패턴으로 전역 변수로 주입하는 변수다. 

<img width="750" alt="스크린샷 2025-05-20 오후 10 36 59" src="https://github.com/user-attachments/assets/abd3eae6-66aa-49d1-b1fe-48b13f9522c1" />
<br />

이제 ```greet```을 찍어보면 ```greet.txt```의 바이너리 데이터가 Buffer 객체로 나오고 있다. (버퍼는 V8의 힙에 저장된다)

아직 인코딩을 지정하지 않아서인데, ```utf-8```로 지정해주면 

<img width="750" alt="스크린샷 2025-05-20 오후 10 45 20" src="https://github.com/user-attachments/assets/d9f3a55b-7b0a-4b04-8922-a865a9a66974" />
<br />

문자열로 잘 나온다. ```readFileSync```는 메서드명에서 알 수 있듯 동기로 작동한다. 그럼 비동기적으로 작동하는 친구를 알아보자. 

<img width="750" alt="스크린샷 2025-05-20 오후 11 01 28" src="https://github.com/user-attachments/assets/346e59cb-f2f9-42d3-b947-927e2b1971b0" /><br />

 ```readFile```는 비동기적으로 작동한다. 
 
즉, 파일 읽기 요청을 OS에 전달하면 libuv가 이벤트 루프에서 비동기로 처리한다. 파일 읽기가 끝나면, 콜백이 호출되어 결과를 전달하는 식이다.

파일 I/O는 외부 하드웨어와의 통신이기 때문에 네트워크 I/O보다 느리고 비용이 크다. 

OS는 기본적으로 파일 I/O에 대해 블로킹 방식으로 처리하기 때문에 NodeJs에선 libuv의 스레드풀을 활용하여 워커 스레드에서 파일 I/O를 실행한다. 
 

#### 3.2.7 stream

RS에서도 보았던 ```stream```이다. ```stream```은 데이터를 청크내서 순차적으로 처리하는 흐름이다. 버퍼가 찼다 -> 스트림 -> 다시 버퍼를 채운다 이렇게 반복한다.

<img width="1000" alt="스크린샷 2025-05-21 오전 11 41 48" src="https://github.com/user-attachments/assets/2eccee49-a59d-494c-ae2a-684312984dd9" /><br />

내부적으로 스트림을 보면, ```EventEmitter```를 상속하는 것을 알 수 있다. 그리고 스트림이 조금 독특한 점이 있는데, 바로 **추상 클래스**라는 것

스트림은 직접 인스턴스화하지 않고, 상속받아 하위 클래스에서 read, write 등 구체적 동작을 구현한다. 

이유인즉슨, 스트림은 어떤 동작이라기보다, 개념이기 때문이다. 그렇기 때문에 추상 클래스로 공통 동작만 정의하는 것이다. 

그럼 간단한 예제를 살펴보자. 

<img width="1000" alt="스크린샷 2025-05-21 오후 12 06 10" src="https://github.com/user-attachments/assets/465fdd48-4ffb-4ecc-933e-c73b3bf93fa7" /><br />

장문의 텍스트를 ```greet.txt```에 담아 스트림했다. 

<img width="750" alt="스크린샷 2025-05-21 오후 12 08 28" src="https://github.com/user-attachments/assets/faa3871e-3162-443e-9d2e-029e2ea7874a" /><br />

텍스트의 ```length```는 대략 18,000개 정도 되고

<img width="750" alt="스크린샷 2025-05-21 오후 12 10 14" src="https://github.com/user-attachments/assets/31274831-4994-40f4-9f0f-6579016ac28c" /><br />

크기는 19KB다. 앞서 말했듯 버퍼가 차면 스트림을하고 다시 버퍼를 채우는 식으로 데이터를 흘러 보낸다. 그러면 스트림에서 버퍼의 용량을 제한해보자. 

<img width="750" alt="스크린샷 2025-05-21 오후 12 14 47" src="https://github.com/user-attachments/assets/a0f83ade-79cd-4ca0-869f-bcdf501c9807" /><br />

```highWaterMark```로 버퍼의 크기를 제한할 수 있다. 버퍼의 크기를 1KB로 제한해보자. (기본 버퍼의 크기는 64KB)

<img width="750" alt="스크린샷 2025-05-21 오후 12 16 30" src="https://github.com/user-attachments/assets/5cc69a1e-f800-428e-8e19-c854784ad0d0" /><br />

제한한 크기만큼 잘라서 오는 것을 확인할 수 있다. 


#### 3.2.8 pipe

이제 ```pipe```를 알아볼 차례다. pipe는 두 스트림, Readable과 Writable을 연결하는 메서드다.

```pipe()```를 호출하면 Readable의 각 청크가 자동으로 Writable의 write()로 전달된다. 

<img width="1000" alt="스크린샷 2025-05-21 오후 12 33 21" src="https://github.com/user-attachments/assets/016761c8-c188-40f8-8f2e-8267c898b039" /><br />

```Readable.protoype.pipe``` 내부를 보면 더 정확하게 알 수 있는데, ```pipe```는 결국 ```EventEmitter```를 상속하고 있고, ```src``` 즉, 지금 Readable하고 있는 파일에서 ```dest``` 즉, 데이터를 보내고 싶은 쪽으로 write를 하면서 데이터를 전송한다. 


<img width="1000" alt="스크린샷 2025-05-21 오후 12 40 46" src="https://github.com/user-attachments/assets/d1d3e27e-bbcb-4405-b3a0-5250c13f6f33" /><br />

바꿔말해 위 과정을 

<img width="1000" alt="스크린샷 2025-05-21 오후 12 41 23" src="https://github.com/user-attachments/assets/5f6ea55b-d7ef-40d8-9272-bc9d248d8ee3" /><br />

이렇게 pipe로 대신하는 것이다. pipe는 또한 ```writable```를 반화하기 때문에 체이닝으로 연속 작업을 할 수 있다(함수형 프로그래밍의 pipe처럼)

<img width="1000" alt="스크린샷 2025-05-21 오후 1 24 37" src="https://github.com/user-attachments/assets/3f00fd92-e0a9-4a9d-8ca9-fbd2ee4750cb" /><br />

보통은 promise와 결합하여 사용한다. 


## 4.네트워크

렛츠고 네트워크! NodeJs의 네트워크에 들어가기 전에 네트워크 관련 간단한 개념을 짚어가보자.

<img width="750" alt="스크린샷 2025-06-01 오후 11 09 27" src="https://github.com/user-attachments/assets/e42d7330-ce89-41ad-9e45-0d922e9d6d37" /><br />

먼저, 클라이언트에서 서버에 연결을 시도하면 소켓 객체(노트북 앞에 빨간점)가 생성된다. 그럼 서버는 리스닝 소켓(서버쪽 초록점)을 열고 클라이언트의 연결 요청을 accept하면 새로운 연결 소켓(서버쪽 흰점)이 생성되어 각 연결을 고유하게 식별한다.

이로써 클라이언트-서버가 연결된 것이다. 연결된 소켓을 통해서만 읽기/쓰기가 가능하며, 리스닝 소켓에서는 데이터 입출력이 불가능하다.

<img width="750" alt="스크린샷 2025-06-02 오전 11 22 03" src="https://github.com/user-attachments/assets/0cde407f-8cde-4e08-9331-9a33827eda8d" /><br />

클라-서버에서 데이터를 주고 받게 되면, 먼저 epoll로 해당 소켓이 읽기/쓰기가 가능한지 확인 후 실제 read/write 시스템 콜을 진행한다.

여기서 **epoll**은 리눅스에서 사용하는 비동기 I/O에 쓰이는 개념이다. 간단하게 말하면 소켓의 변화를 감지하여 알리는 역할을 수행한다.

아무튼, NodeJs는 내부적으로 epoll을 통해 소켓의 상태를 감지하고, 데이터가 도착하면 on('data') 이벤트를 자동으로 발생시켜 콜백을 실행한다.

그럼 이제 클라에서 보낸 'hello'란 데이터는 커널에서 복사되어 Node.js로 전달된다.

그럼 예제와 함께 데이터 송수신 과정을 살펴보자.

<img width="750" alt="스크린샷 2025-06-02 오전 11 40 04" src="https://github.com/user-attachments/assets/a1bd5065-bc18-4b3e-a03c-ad64f2cbfc21" /><br />

net을 사용하여 connect, write, close 흐름을 구축했다.

<img width="750" alt="스크린샷 2025-06-02 오전 11 49 18" src="https://github.com/user-attachments/assets/2ef0f833-c022-4a1f-a639-2ccd7a93ef44" /><br />

<img width="750" alt="스크린샷 2025-06-02 오전 11 49 53" src="https://github.com/user-attachments/assets/07164413-e1f9-4759-99b5-b620e28d5517" /><br />

여기서 ```93.184.215.14```는 https://www.example.com 의 IP다. 93.184.215.14에 80포트로 연결을 시도하고 ```client.connect```로 connection이 생성되면 epoll은 I/O가 준비될 때까지 대기한다. 

<img width="652" alt="스크린샷 2025-06-02 오전 11 55 27" src="https://github.com/user-attachments/assets/b3f7fac7-68e3-4204-8d7c-14c20aca418c" /><br />

그리고 ```client.on("data", ...)``` 에서 NodeJs가 콜백을 실행하게 되는 것이다. 

자, 이제 서버 케넥션를 추가하면

<img width="750" alt="스크린샷 2025-06-02 오후 12 23 54" src="https://github.com/user-attachments/assets/ed8a2add-6f7e-4621-a3a9-7a661acf8295" /><br />

첫 그림 예제에서 봤듯이, ```listen(8080)```로 리스닝 소켓을 생성하고, ```connection```으로 새로운 서버쪽 연결 소켓을 만든다. 

### 4.1 HTTP

HTTP는 정말 신기한 거 같다. 현대 사회로 이끈 게 HTTP 덕분이 아닐까 생각이 들정도로.. 

아무튼, HTTP는 원래 텍스트 기반으로 설계되어 사람이 직접 읽고 쓰기 쉬웠지만, 이후 HTTP/2, HTTP/3에서는 성능을 위해 바이너리 포맷으로 변화했다.

HTTP/2, HTTP/3는 데이터를 바이너리로 변환해 전송하며, 응답은 압축되어 전송되기도 한다.

HTTP의 본질은 결국 요청-응답 구조다. TCP를 생각해보자. TCP는 그냥 양방향 바이트 스트림이다. 순서도 없고 왔다갔다할 뿐이다.

그런데, HTTP는 요청이 있을 때만 응답을 준다. 

요청의 시작과 끝은 어디고 응답의 시작과 끝은 어디인가! 이러한 것들을 정교하게 컨트롤 하는 HTTP가 신비로울뿐..

<img width="1000" alt="스크린샷 2025-06-02 오후 2 52 13" src="https://github.com/user-attachments/assets/cf0ec5af-2974-4385-b4d3-dc18817a079c" /><br />

기본적으로 HTTP 1.1에서 한 커넥션에서 한 번에 하나의 요청만 처리된다. 다음 요청은 응답이 끝난 뒤에 보낼 수 있다.

크롬은 도메인당 여러 커넥션(기본 6개)을 열어 동시성을 확보하기도 한다.

그럼 NodeJs에선 HTTP를 어떻게 다룰까

<img width="500" alt="스크린샷 2025-06-02 오후 2 59 46" src="https://github.com/user-attachments/assets/b7cc0b9f-d735-4a2c-ba4d-fda67d3ff83c" /><br />

NodeJs에선 에이전트로 커넥션 풀을 관리한다. 커넥션 풀이 가득 차면 요청이 큐에 쌓여 대기하게 된다. 

### 4.2 DNS

네트워크에서 빠질 수 없는 DNS 차례다. DNS는 간단하게 말해 도메인을 IP로 매핑해주는 역할을 한다. 

DNS는 단일 거대한 데이터베이스가 아니라, 루트 서버 → TLD 서버(.com) → 권한 네임서버로 이어지는 계층적인 구조이다. 

예를들어 www.google.com 접속 시 DNS 동작 과정을 나열하면, 

<img width="1000" alt="스크린샷 2025-06-03 오후 4 50 31" src="https://github.com/user-attachments/assets/048f435d-f7f6-452f-ac79-da784598fdfb" /><br />

1. 사용자가 브라우저에 도메인 입력하면 DNS 리졸버에게 요청한다.

2. 캐시가 있다면 해당 도메인의 IP를 반환하고, 캐시가 없다면 루트 서버에 요청하게 된다.

<img width="1000" alt="스크린샷 2025-06-03 오후 4 53 56" src="https://github.com/user-attachments/assets/bd7c93fc-5cde-4033-a212-ad7688dfb798" /><br />

3. 루트 서버는 DNS 계층에서 최상위 계층으로, TLD(Top-Level Domain) 서버의 주소를 제공한다. 즉, 루트 서버는 ".com"과 같은 TLD 서버의 IP를 알려주는 역할을 하는 것이다.

4. 루트 서버는 ".com" TLD 서버의 IP 목록을 반환하고, DNS 리졸버가 "google.com"의 권한 있는 네임서버 주소(AWS와 같은)를 요청한다.

<img width="1000" alt="스크린샷 2025-06-03 오후 4 57 19" src="https://github.com/user-attachments/assets/e18cfb6d-207a-49a4-9fd5-89d1ea3db724" /><br />

5. 권한 네임서버가 IP 주소를 반환하면 리졸버가 캐시에 저장 후 사용자에게 전달한다. 

자, 그렇다면 Node에서는 어떻게 DNS를 처리할까?

<img width="750" alt="스크린샷 2025-06-03 오후 5 14 25" src="https://github.com/user-attachments/assets/549c3eee-4460-4cc2-8010-36f5a7a0bba5" /><br />

NodeJs는 내부적으로 libuv의 스레드풀을 사용해 DNS 질의를 처리한다.

그럼 여기서 궁금한 점이 생기는데, DNS는 분명 UDP(53번 포트) 기반의 네트워크 I/O인데, 왜 NodeJs에서는 스레드풀을 사용할까?

사실 NodeJs에서 DNS 처리는 상당히 복잡한 작업이다.

파일 I/O에서 hosts 파일 조회도 해봐야 하고, 네트워크 I/O에서 실제 DNS 서버와의 UDP 통신도 해야한다. (OS 레벨의 DNS 캐시 조회도 있다)

그래서 NodeJs는 DNS 조회를 위해 ```getaddrinfo()```라는 시스템 콜을 사용하는데, ```getaddrinfo()```는 네트워크 DNS 전에 로컬 hosts 파일을 먼저 확인한다.

```js
const req = http.request('https://example.com', { method: "GET" });
```

가령 이러한 요청을 발생했다고 하자, 그렇다면 NodeJs는 아래와 같이 처리한다.

1. 먼저 메인 스레드에선 HTTP 요청 시작한다.
2. 그리고 libuv 스레드풀에서 getaddrinfo() 호출하여 hosts 파일 조회 및 DNS 서버 질의를 수행한다.
3. 마지막으로 메인 스레드에서 DNS 결과 받아서 TCP 연결 시작한다.

### 4.3 TCP






 
