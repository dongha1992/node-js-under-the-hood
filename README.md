# [Learn and Understand NodeJS](https://www.udemy.com/course/understand-nodejs/?utm_source=adwords&utm_medium=udemyads&utm_campaign=Search_DSA_Beta_Prof_la.EN_cc.ROW-English&campaigntype=Search&portfolio=ROW-English&language=EN&product=Course&test=&audience=DSA&topic=&priority=Beta&utm_content=deal4584&utm_term=_._ag_162511579404_._ad_696197165421_._kw__._de_c_._dm__._pl__._ti_dsa-1677053911088_._li_9196883_._pd__._&matchtype=&gad_source=1&gad_campaignid=21168154305&gbraid=0AAAAADROdO0_sczDNpw8g4_LjE_XlQqVy&gclid=CjwKCAjwravBBhBjEiwAIr30VHtpqc6vF8B8LIIGH7dNjehWgG-ZhCcGS-IV-ARdvPVbzQFNNb_tvRoCMoYQAvD_BwE)

미루고 미루던 노드 공부. 자바스크립트에 대한 이해를 확실히 하기 위해선 노드JS도 잘 알아야 한다는 생각에 학습을 하게 되었다. 

## 목차
  - [V8 엔진](#V8-엔진)
  - [작성중](#Node) 

## V8 엔진

맨날 말로만 듣던 V8엔진이 도대체 무엇인지 차근차근 알아보자.

### 마이크로프로세서

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

### ECMAScript

V8에 대해 더 알아보기 전에 ECMAScript를 잠깐 짚어 가야한다. 

- ECMAScript는 쉽게 말해 자바스크립트의 공식 명세서다.([ECMA](https://262.ecma-international.org/6.0/)에서 메서드들 정의한 거보면 꽤 재밌다.)
- V8말고도 브라우저 별로 엔진이 다 다른데, 다른 엔진들이 다른 브라우저에서 동일한 동작을 할 수 있도록 하는 것이 ECMAScript
- 즉, 엔진들은 js 코드를 받아 ECMAScript 명세에 따라 파싱하고 기계어로 변환하여 실행하면서 표준을 지킨다

### V8 조금씩 알아가기 - 정의

먼저 V8 엔진 내부 동작을 살펴보기 전에 간단하게 V8이 뭔지 보면 아래와 같은 정의들이 있다.

<img width="1000" alt="스크린샷 2025-05-19 오후 6 34 42" src="https://github.com/user-attachments/assets/0a3ba3be-a344-4a18-b967-b32d03267a9d" />

- 구체적으로 V8이 C++로 어떻게 돌아가는지 V8 소스를 아주 잠깐만 살펴보자.
  
<img width="1000" alt="스크린샷 2025-05-19 오후 6 41 00" src="https://github.com/user-attachments/assets/e7c5a935-01a7-4733-835b-108ef2d412af" />

- C++로 이루어진 수 많은 파일이 있고, 그중에 익숙한 ```date.cc```란 파일을 보면 무언가 열심히 계산하고 있는 코드를 볼 수 있다.
- 즉, js에서 ```new Date()```를 호출하면, 실제로는 V8의 ```date.cc``` 코드가 실행되는 것이다.

### V8 더 알아가기 - 임베딩

복잡한 내용은 차지하고 V8은 결국 C++로 작성되어 있고, 브라우저 밖에서도 동작하는 엔진이다. 이 사실을 바탕으로 **embedding**이 가능하다
즉, C++로 작성된 어떤 프로그램이든 그 안에 V8 엔진을 내장해서 자바스크립트 코드를 실행할 수 있는 것이다.
이러한 embedding 덕분에 Node.js가 만들어졌다.

<img width="1000" alt="스크린샷 2025-05-19 오후 7 08 24" src="https://github.com/user-attachments/assets/58709743-58d5-45f6-be6b-2c431afdc0fc" />

C++로 작성된 파일에 V8을 임배딩하고

<img width="1000" alt="스크린샷 2025-05-19 오후 7 09 39" src="https://github.com/user-attachments/assets/dddba86f-9cdb-47a5-98d4-74851dc881a9" />

자바스크립트 코드를 입력하면, 정확하게 실행된다. 굉장하다. 

잠깐 위에서 언급했던 것처럼 V8을 임베딩하면 C++로 작성한 함수나 기능을 자바스크립트에서 키워드처럼 쓸 수 있다고 했다. 

<img width="1000" alt="스크린샷 2025-05-19 오후 7 18 05" src="https://github.com/user-attachments/assets/803052fb-9cb8-4ba8-a705-92e3553432a7" />

위와 같이, ```print```처럼 js에 없는 함수를 C++에서 구현하고 V8에 키워드를 바인딩하면 V8는 js 코드를 파싱하다가 바인딩된 키워드를 만나고 C++ 함수를 실행하여 js코드로(혹은 처럼) 실행하는 것이다.

즉, 이런 호환이 가능하기 때문에 ECMAScript에 없는 파일 읽기, 쓰기, 데이터베이스 접근 등 다양한 기능을 js에서 쓸 수 있게 되었다 (바로 Node.js의 탄생. 굉장하다)

그럼 여기서 ```Node.js```가 무엇인지 정리해보자.

```Node.js```란 V8엔진이 좀 더 기계어에 가까운 C++를 등에 업고 서버/OS 프로그래밍 등을 javascript에서도 가능하게 만든 굉장한 녀석이다!!


## 노드

V8 엔진 덕분에 Node.js가 탄생할 수 있었던 배경도 알았으니 본격적으로 ```Node.js```에 대해 알아가보자. 










