# 실행 컨텍스트

**실행할 코드에 제공할 환경 정보들을 모아놓은 객체**로, 자바스크립트의 **동적 언어**로서의 성격을 가장 잘 파악할 수 있는 개념이다. 자바스크립트는 어떤 실행 컨텍스트가 활성화되는 시점에 선언된 변수들을 위로 끌어올리고**(호이스팅**), **외부 환경 정보를 구성**하고, **this 값을 설정**하는 등의 동작을 수행한다.



## 1. 실행 컨텍스트란?

**스택**

스택은 출입구가 하나뿐인 깊은 우물 같은 데이터 구조 (LIFO)

**큐**

양쪽이 모두 열려 있는 파이프 (FIFO)

 

동일한 환경에 있는 **코드들을 실행할 때 필요한 환경 정보들을 모아 컨텍스트를 구성**하고, 이를 **콜스택**에 쌓아올렸다가, 가장 위에 쌓아있는 컨텍스트와 관련 있는 코드를 실행하는 식으로 **전체 코드의 환경과 순서를 보장**한다. 동일한 환경. 즉, 하나의 실행 컨텍스트를 구성할 수 있는 방법으로는 전역공간, eval(), 함수 등이 있다. 우리가 흔히 **컨텍스트를 구성하는 방법은 함수를 실행하는 것**이다.

최상단 공간은 별도의 실행 명령 없이도 브라우저에서 자동으로 실행하므로 자바스크립트 파일이 열리는 순간 전역 컨텍스트가 활성화된다.

한 실행 컨텍스트가 콜 스택의 맨 위에 쌓이는 순간이 곧 현재 실행할 코드에 관여하게 되는 시점이다. 어떤 실행컨텍스트가 활성화 될 때 자바스크립트 엔진은 코드 실행에 필요한 환경 정보들을 수집해서 실행 컨텍스트 객체에 저장한다.



- VariableEnvironment: 현재 컨텍스트 내의 식별자들에 대한 정보 + 외부 환경 정보, 선언 시점의 Lexical Environment의 스냅샷으로, 변경 사항은 반영되지 않음.
- LexicalEnvironment: 처음에는 Variable Environment와 같지만 변경 사항이 실시간으로 반영됨.
- ThisBinding: this 식별자가 바라봐야 할 대상 객체



## 2. VariableEnvironment

실행 컨텍스트를 생성할 때 VariableEnvironment의 정보를 먼저 담은 다음, 이를 그대로 복사해서 LexicalEnvironment를 만들고, 이후에는 LexicalEnvironment를 주로 활용하게 된다.



## 3. LexicalEnvironment

컨텍스트를 구성하는 환경 정보들을 사전에서 접하는 느낌으로 모아놓은 것.



### environmentRecord와 호이스팅

environmentRecord에는 현재 컨텍스트와 관련된 코드의 **식별자 정보들이 저장**된다. 

변수 정보를 수집하는 과정은 코드 실행되기 전에 완료된다. 즉, **코드가 실행되기 전임에도 불구하고 자바스크립트 엔진은 이미 해당 환경에 속한 코드의 변수명들을 모두 알고 있다.**



<img src="https://every-body.s3.ap-northeast-2.amazonaws.com/ialreadyknow.jpg" alt="이미 다 알고 있었단 말이다" style="width: 500px; height: 300px;"/>

=> 마치 식별자들을 최상단으로 끌어올려 놓은 다음 코드를 실행하는 것처럼 동작한다.

​	=> **호이스팅!!** 

​		=> 호이스팅은 변수 정보를 수집하는 과정을 더욱 이해하기 쉬운 방법으로 대체한 **가상의 개념**이다.

​	

#### 호이스팅 규칙

environmentRecord에는 매개변수의 이름, 함수 선언, 변수명 등이 담긴다.

```javascript
// 매개변수와 변수에 대한 호이스팅

function a (x) {
    console.log(x); // (1)
    var x;
    console.log(x); // (2)
    var x = 2;
    console.log(x); // (3)
}

a(1)

// 인자를 함수 내부의 다른 코드보다 먼저 선언 및 할당이 이뤄진 것으로 간주할 수 있다.
function a (x) {
    var x = 1;
    console.log(x); // (1)
    var x; 
    console.log(x); // (2)
    var x = 2;
    console.log(x); // (3)
}

a(1)


function a (x) {
    var x;
    var x;
    var x;
    
    x = 1;
    console.log(x); // (1) 
    console.log(x); // (2)
    x = 2;
    console.log(x); // (3)
}

a(1)
```



```javascript
// 함수 선언의 호이스팅

function a () {
    console.log(b);
    var b = 'bbb';
    console.log(b);
    function b () {}
    console.log(b);
}
a();

// 변수는 선언부와 할당부를 나누어 선언부만 끌어올리는 반면 함수 선언은 함수 전체를 끌어올린다.
function a () {
    var b;
    function b () {} 
    
    console.log(b);
    b = 'bbb';
    console.log(b);
    console.log(b);
}
a();
```



#### 함수 선언문과 함수 표현식

함수 선언문: function 정의부만 존재하고 별도의 할당 명령이 없다.

함수 표현식: 정의한 function을 별도의 변수에 할당하는 것.

함수 표현식은 함수명이 없어도 된다. => 익명 함수 표현식

```javascript
const b = function b () {} // 익명 함수 표현식. 변수명 b가 곧 함수명.
b();
```



**함수 선언문은 선언과 할당, 즉 전체를 호이스팅한 반면 함수 표현식은 변수 선언부만 호이스팅 한다. 함수 표현식은 함수를 다른 변수에 값으로써 할당한 것이다.**



### 스코프, 스코프 체인, outerEnvironmentReference

**스코프란 식별자에 대한 유효범위이다.**

ES5까지는 전역공간을 제외하며 오직 함수에 의해서만 스코프가 생성되었다. ES6 이후로는 블록에 의해서도 스코프 경계가 발생하게 되었다. (let, const, class, strict mode에서의 함수 선언)

**식별자의 유효범위를 안에서부터 바깥으로 차례로 검색해나가는 것을 스코프 체인이라고 한다. 그리고 이를 가능하게 하는 것이 LexicalEnvironment의 두번째 수집 자료인 outerEnvironmentReference이다.** 



#### 스코프 체인

outerEnvironmentReference는 현재 호출된 함수가 **선언될 당시(과거시점)**의 LexicalEnvironment를 참조한다.

'선언하다' 라는 행위가 실제로 일어나는 시점은 실행 컨텍스트가 활성화된 상태일 때이다**. 선언하는 행위도 하나의 코드이며, 모든 코드는 실행 컨텍스트가 활성화 상태일 때 실행되기 때문이다.**





## 질문

1. [ ? ] 은 변수 정보를 수집하는 과정을 더욱 이해하기 쉬운 방법으로 대체한 **가상의 개념**이다.
2. 함수 선언문과 함수 표현식의 호이스팅 시 차이?
3. 스코프 체이닝이 가능한 이유는 실행 컨텍스트의 어떤 부분 덕분인가?









## 답

1. 호이스팅
2. 함수 선언문은 선언과 할당, 즉 전체를 호이스팅한 반면 함수 표현식은 변수 선언부만 호이스팅 한다.
3. LexicalEnvironment의 두번째 수집 자료인 outerEnvironmentReference

