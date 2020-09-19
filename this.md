# this

다른 대부분의 객체 지향언어에서 this는 클래스로 생성한 인스턴스 객체를 의미한다. 클래스에서만 사용할 수 있기 때문에 혼란의 여지가 많지 않다. 그러나 자바스크립트에서의 this는 어디서든 사용할 수 있다.

함수와 객체(메서드)의 구분이 느슨한 자바스크립트에서 this는 실질적으로 이 둘을 구분하는 거의 유일한 기능이다.



## 1. 상황에 따라 달라지는 this

자바스크립트에서 this는 기본적으로 실행 컨텍스트가 생성될 때 함께 결정된다. 실행 컨텍스트는 함수를 호출할 때 생성되므로, 바꿔 말하면 **this는 함수를 호출할 때 결정된다**고 할 수 있다. 함수를 어떤 방식으로 호출하느냐에따라 달라지는 것이다.



### 전역 공간에서의 this

전역 공간에서 this는 전역 객체를 가리킨다. 개념상 전역 컨텍스트를 생성하는 주체가 바로 전역 객체이기 때문이다. 전역 객체는 자바스크립트 런타임 환경에 따라 다른 이름과 정보를 가지고 있다. 브라우저 환경에서는 window, Node.js 환경에서는 global이다.



참고

전역 변수를 선언하면 자바스크립트 엔진은 이를 전역객체의 프로퍼티로도 할당한다. 변수이면서 객체의 프로퍼티이기도 한 셈이다. 

```javascript
var a = 1;
console.log(a); // 1
console.log(window.a); // 1
console.log(this.a); // 1

// Q.let, const 키워드로 선언했을 때는 어떻게 될까?
```

**자바스크립트의 모든 변수는 실은 특정 객체의 프로퍼티로서 동작한다.** 특정 객체란 바로 실행 컨텍스트의 LexicalEnvironment이다. 

**전역 변수를 선언하면 자바스크립트 엔진은 이를 전역객체의 프로퍼티로 할당한다.**

변수 a를 직접 호출할 때도 1이 나오는 까닭은? => 변수 a에 접근하고자 하면 스코프 체인에서 a를 검색하다가 가장 마지막에 도달하는 전역 스코프의 L.E, 즉 전역객체에서 해당 프로퍼티 a를 발견해서 그 값을 반환하기 때문이다.



### 메서드로 호출할 때 그 메서드 내부에서의 this

#### 함수 vs 메서드

- **함수와 메서드를 구분하는 유일한 차이는 독립성**에 있다. 함수는 그 자체로 독립적인 기능을 수행하는 반면, 메서드는 자신을 호출한 대상 객체에 관한 동작을 수행해야 한다.
- 메서드를 '**객체의 프로퍼티에 할당된 함수**'로 이해하는 것은 **반은 맞고 반은 틀린 말**이다. 어떤 함수를 객체의 프로퍼티에 할당한다고 해서 그 자체로서 무조건 메서드가 되는 것이 아니라 **객체의 메서드로서 호출할 경우에만 메서드로 동작하고, 그렇지 않으면 함수로 동작한다.**

```javascript
const func = function (x) {
    console.log(this, x);
};
func(1); // window {...}

const obj = {
    method: func
};

obj.method(2) // {method: f} 2
```

위 코드 예제를 보면 원래의 익명함수는 그대로인데 이를 변수에 담아 호출한 경우와 obj 객체의 프로퍼티에 할당해서 호출한 경우에 this가 달라지는 것을 알 수 있다. 

함수로서 호출과 메서드로서 호출을 구분하는 가장 쉬운 방법은 함수 앞에 `.`이 있는지 여부만으로 간단하게 구분할 수 있다.



#### 메서드 내부에서의 this

this에는 호출한 주체에 대한 정보가 담긴다. 

```javascript
const obj = {
    methodA: function () { console.log(this); },
    inner: {
        methodB: function () { console.log(this); }
    }
};

obj.methodA(); //
obj.inner.methodB(); //
```



### 함수로서 호출할 때 그 함수 내부에서의 this



#### 함수 내부에서의 this

**어떤 함수를 함수로써 호출할 경우에는 this가 지정되지 않는다**. 함수로서 호출하는 것은 호출 주체를 명시하지 않고 개발자가 직접 코드에 관여해서 실행할 것이기 때문에 호출 주체를 알 수 없다. 실행 컨텍스트를 활성화할 당시에 this가 지정되지 않은 경우 this는 전역 객체를 바라본다. 따라서 **함수에서의 this는 전역 객체를 가리킨다.** 더글라스 크락포는 이를 명백히 설계의 오류라고 지적한다.



#### 메서드의 내부함수에서의 this

```javascript
const obj1 = {
    outer: function () {
        console.log(this); // 1
        const innerFunc = function () {
            console.log(this); // 2, 3
        }
        innerFunc();
        
        const obj2 = {
            innerMethod: innerFunc
        };
        obj2.innerMethod();
    }
};
obj1.outer();
```

this 바인딩에 관해서는 함수를 실행하는 당시의 주변환경은 중요하지 않고, 오직 해당 함수를 호출하는 구문 앞에 점 또는 대괄호 표기가 있는지 없는지가 관건이다.



#### 메서드 내부 함수에서의 this를 우회하는 방법

##### 변수 활용

```javascript
const obj = {
    outer: function () {
        console.log(this); // 1
        const innerFunc1 = function () {
            console.log(this); // 2
        }
        innerFunc1();
        
        const self = this;
        const innerFunc2 = function () {
            console.log(self); // 2
        }
        innerFunc2();
    }
};
obj.outer();
```



##### this를 바인딩하지 않는 함수

**화살표 함수는 실행 컨텍스트를 생성할 때 this 바인딩 과정 자체가 빠지게 되어, 상위 스코프의  this를 그대로 활용할 수 있다.**

```javascript
const obj = {
    outer: function () {
        console.log(this); // 1
        const innerFunc1 = function () {
            console.log(this); // 2
        }
        innerFunc1();
    }
}
       
obj.outer();
```



### 콜백 함수 호출 시 그 함수 내부에서의 this

콜백 함수도 함수이기 때문에 기본적으로 this가 전역 객체를 참조하지만, 제어권을 받은 함수에서 콜백 함수에 별도로 this가 될 대상을 지정한 경우에는 그 대상을 참조하게 된다.

```javascript
setTimeOut(function () { console.log(this) }, 3000); // 전역 객체 참조

document.body.querySelector('#a')
	.addEventListener('click', function (e) {
    console.log(this, e);
});
```

addEventListener 메서드는 콜백 함수를 호출할 때 자신의 this를 상속하도록 정의되어 있다.

콜백 함수의 제어권을 가지는 함수가  콜백 함수에서의 this를 무엇으로 할지를 결정하며, 정의하지 않은 경우에는 함수와 마찬가지로 전역객체를 바라본다.



### 생성자 함수 내부에서의 this

생성자 함수는 어떤 공통된 성질을 지니는 객체들을 생성하는 데 사용하는 함수. 객체지향 언어에서는 생성자를 클래스, 클래스를 통해 만든 객체를 인스턴스라고 한다. 

생성자는 구체적인 인스턴스를 만들기 위한 일종의 틀이다.

**자바스크립트는 함수에 생성자로서의 역할을 함께 부여**했다. **new 명령어와 함께 함수를 호출**하면 해당 함수가 생성자로 동작한다. 어떤 함수가 **생성자로서 호출된 경우 내부에서의 this는 곧 새로 만들 구체적인 인스턴스 자신이 된다.**

생성자 함수를 호출하면 우선 생성자의 prototype 프로퍼티를 참조하는 `__proto__` 라는 프로퍼티가 있는 객체(인스턴스)를 만들고 미리 준비되니 공통 속성 및 개성을 해당 객체(this)에 부여한다.

```javascript
const Cat = function (name, age) {
	this.bark = '야옹';
    this.name = name;
    this.age = age;
};

const choco = new Cat('초코', 7);
const nabi = new Cat('나비', 5);
console.log(choco, nabi);
```





## 2. 명시적으로 this를 바인딩하는 방법



### call 메서드

```javascript
Function.prototype.call(thisArg, [, art1[, arg2[, ...]]])
```

call 메서드는 메서드의 호출 주체인 함수를 즉시 실행하도록 하는 명령이다. call 메서드의 첫번째 인자를 this로 바인딩하고, 이후의 인자들을 호출할 함수의 매개변수로 한다.



```javascript
const obj = {
    a: 1,
    method: function(x, y) {
        console.log(this.a, x, y);
    }
};

obj.method(2,3);
obj.method.call({a: 4}, 5, 6);
```



### apply 메서드

```javascript
Function.prototype.call(thisArg[, argsArray]])
```

apply 메서드는 두번째 인자를 배열로 받아 그 배열의 요소들을 호출할 함수의 매개변수로 진정한다는 차이만 제외하면 call 메서드와 기능적으로 완전히 동일하다.

```javascript
const obj = {
    a: 1,
    method: function(x, y) {
        console.log(this.a, x, y);
    }
};

obj.method(2,3);
obj.method.call({a: 4}, [5, 6]);
```



### call / apply 메서드의 활용



#### 유사배열객체(array-like-object)에 배열 메서드를 활용

```javascript
const obj = {
    0: 'a',
  	1: 'b'.
    2: 'c',
    length: 3
};

Array.prototype.push.call(obj, 'd');
console.log(obj);

const arr = Array.prototype.slice.call(obj);
console.log(arr);
```

객체에는 배열 메서드를 직접 적용할 수 없다. 그러나 키가 0 또는 양의 정수인 프로퍼티가 존재하고 length  프로퍼티의 값이 0 또는 양의 정수인 객체, 즉 유사배열객체는 call, apply 메서드를 이용해 배열 메서드를 차용할 수 있다. 함수 내부에서 접근할 수 있는 arguments  객체, querySelectorAll, getElementsByClassName 등의 Node 선택자로 선택한 결과인 NodeList 등이 유사배열객체에 포함된다.



```javascript
function a () {
    const argv = Array.prototype.slice.call(arguments);
    argv.forEach(function (arg) {
        console.log(arg);
    })
}

a(1,2,3);

document.body.innerHTML = '<div>a</div><div>b</div><div>c</div>';
const nodeList = document.querySelectorAll('div');
const nodeArr = Array.prototype.slice.call(nodeList);
nodeArr.forEach(function(node) {
    console.log(node);
});
```



배열처럼 인덱스와 length 프로퍼티를 지니는 문자열에도 배열 메서드를 적용할 수 있다. 단, 문자열의 경우 length 프로퍼티가 읽기 전용이기 때문에 원본 **문자열에 변경을 가하는 메서드(push, pop, shift, unshift, splice 등)는 에러를 던지면, concat 처럼 대상이 반드시 배열이어야 하는 경우에는 에러는 나지 않지만 제대로 된 결과를 얻을 수 없다.** 



call / apply를 이용해서 형변환하는 것은 this를 원하는 값으로 지정해서 호출한다는 본래의 메서드의 의도와는 동떨어진 활용법이라 할 수 있다. slice 메서드는 오직 배열 형태로 복사하기위해 차용됐을 뿐이니, 어떤 의도인지 파악하기 어렵다. 이에 **ES6에서는 유사배열객체 또는 순회 가능한 모든 종류의 데이터 타입을 배열로 전환하는 Array.from 메서드를 새로 도입했다.**



```javascript
const obj = {
    0: 'a',
  	1: 'b'.
    2: 'c',
    length: 3
};

const arr = Array.from(obj);
console.log(arr);
```



#### 생성자 내부에서 다른 생성자를 호출

생성자 내부에 다른 생성자와 공통된 내용이 있을 경우 call 또는 apply를 이용해 다른 생성자를 호출하면 간단하게 반복을 줄일 수 있다.

 ```javascript
function Person (name, gender) {
    this.name = name;
    this.gender = gender
}

function Student(name, gender, school) {
    Person.call(this, name, gender);
    this.school = school;
}

const by = new Student('보영', 'female', '단국대');
 ```



#### 여러 인수를 묶어 하나의 인수로 전달하고 싶을 때 - apply 활용

```javascript
const numbers = [10, 20, 3, 16, 45];
const max = Math.max.apply(null, numbers);
```

ES6에 spread operator를 이용하면 apply를 적용하는 것보다 더욱 간편하게 작성 가능하다.

```javascript
const numbers = [10, 20, 3, 16, 45];
const max = Math.max(...numbers);
```





### bind 메서드

```javascript
Function.prototype.bind(thisArg[,arg1[, arg2[, ...]]])
```

**call과 비슷하지만 즉시 호출하지 않고 넘겨받은 this 및 인수들을 바탕으로 새로운 함수를 반환하기만 하는 메서드 이다.**  bind 메서드는 함수에 this를 미리 적용하는 것과 부분 적용 함수를 구현하는 두 가지 목적을 모두 지닌다.

```javascript
const func = function (a,b,c,d) {
    console.log(this, a, b, c ,d);
};
func(1,2,3,4);

const bindFunc1 = func.bind({ x: 1 });
bindFunc1(5,6,7,8);

const bindFunc2 = func.bind({ x: 1 }, 4, 5);
bindFunc2(6,7);
bindFunc2(8,9);
```

bindFunc2는 this지정과 함께 부분 적용 함수를 구현한 것이다.

bind 메서드를 적용해서 새로 만든 함수는 name 프로퍼티에 동사 bind의 수동태인  'bound'라는 접두어가 붙는 특징이 있다. 따라서, 기존의 call이나 apply 보다 코드를 추적하기에 더욱 수월해진 면이 있다.



#### 상위 컨텍스트의 this를 내부함수나 콜백 함수에 전달하기

```javascript
const obj = {
    outer: function () {
        console.log(this);
        const innerFunc = function () {
            console.log(this);
        }.bind(this);
        innerFunc();
    }
};
obj.outer();
```

콜백 함수를 인자로 받는 함수나 메서드 중에서 기본적으로 콜백 함수 내에서의 this에 관여하는 함수 또는 메서드에 대해서도 bind 메서드를 이용하면 this 값을 변경할 수 있다.

```javascript
const obj = {
    logThis: function () {
        console.log(this);
    },
    logThisLater1: function () {
        setTimeout(this.logThis, 500);
    },
    logThisLater2: function () {
        setTimeout(this.logThis.bind(this), 500);
    }
};

obj.logThisLater1();
obj.logThisLater2();
```



### 화살표 함수의 예외사항

ES6에 새롭게 도입된 화살표 함수는 **실행 컨텍스트 생성 시 this를 바인딩하는 과정이 제외**됐다. 즉, 이 함수 내부에는 **this가 아예 없으며**, 접근하고자 하면 **스코프 체인상 가장 가까운 this**에 접근하게 된다.

```javascript
const obj = {
    outer: function () {
        console.log(this);
        const innerFunc = () => {
            console.log(this);
        };
        innerFunc();
    }
};
obj.outer();
```



### 별도의 인자로 this를 받는 경우(콜백 함수 내에서의 this)

콜백 함수를 인자로 받는 메서드 중 일부는 추가로 this를 지정할 객체(thisArg)를 인자로 지정할 수 있는 경우가 있다. 이러한 메서드의 thisArg 값을 지정하면 콜백 함수 내부에서 this값을 원하는 대로 변경할 수 있다. 이런 형태는 배열 메서드, Set, Map 등의 메서드에 많이 포진돼 있다.



```javascript
// ES5
var report = {
    sum: 0,
    count: 0,
    add: function () {
        var args = Array.prototype.slice.call(arguments);
        // 콜백 함수 내부에서의 this는 forEach 함수의 두 번째 인자로 전달해준 this가 바인딩 된다.
        // 콜백 함수 내부에서의 this는 add 메서드에서의 this가 전달된 상태이므로 add 메서드의 this(report)를 그대로 가리키고 있다.
        args.forEach(function (entry) {
            this.sum += entry;
            ++this.count;
        }, this);
    },
    average: function () {
        return this.sum / this.count;
    }
};

report.add(60, 85, 95);
console.log(report.sum, report.count, report.average());


// ES6
const report = {
    sum: 0,
    count: 0,
    add () {
        const args = [...arguments];
        args.forEach(entry => {
            this.sum += entry;
            ++this.count;
        });
    },
    average () {
        return this.sum / this.count;
    }
}

report.add(60, 85, 95);
console.log(report.sum, report.count, report.average());


class Report {
    constructor(sum, count) {
        this.sum = sum;
        this.count = count;
    }
    
    add () {
        const args = [...arguments];
        args.forEach(entry => {
            this.sum += entry;
            ++this.count;
        });
    }
        
    average () {
        return this.sum / this.count;
    }
}

const report = new Report(0,0);
report.add(60, 85, 95);
console.log(report.sum, report.count, report.average());
```

