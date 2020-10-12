## prototype



자바스크립트는 프로토타입 기반 언어이다. 클래스 기반 언어에서는 '상속'을 사용하지만 프로토타입 기반 언어에서는 어떤 객체를 원형(prototype)으로 삼고 이를 복제(참조)함으로써 상속과 비슷한 효과를 얻는다.



## 1. 프로토타입의 개념 이해



### 1-1. constructor, prototype, instance

```javascript
var instance = new Constructor();
```

- 어떤 생성자 함수(Constructor)를 new 연산자와 함께 호출하면
- Constructor에서 정의된 내용을 바탕으로 새로운 인스턴스가 생성된다.
- 이 때 instance에서는 `__proto__`라는 프로퍼티가 자동으로 부여되는데,
- 이 프로퍼티는 Constructor의 prototype이라는 프로퍼티를 참조한다.



 prototype이라는 프로퍼티와 `__proto__` 라는 프로퍼티가 새로 등장했는데, 이 **둘의 관계가 프로토타입 개념의 핵심이다.**  prototype은 객체이다. 이를 **참조하는** `__proto__` 역시 당연히 객체이다. **prototype 객체 내부에는 인스턴스가 사용할 메서드를 저장**한다. 인스턴스에서도 숨겨진 프로퍼티인 `__proto__`를 통해 이 메서드들에 접근할 수 있게 된다.



```javascript
var Person = function (name) {
    this._name = name;
};

Person.prototype.getName = function () {
    return this._name;
};
```

```javascript
var suzi = new Person('Suzi');
suzi.__proto__.getName(); // undefined
```

```javascript
Person.prototype === suzi.__proto__ // true
```



Person의 인스턴스는  `__proto__` 프로퍼티를 통해 getName을 호출할 수 있다. 왜냐하면 instance의 `__proto__`가 Constructor의 prototype 프로퍼티를 참조하므로 결국 둘은 같은 객체를 바라보기 때문이다.

**메서드 호출의 결과로 왜 undefined가 반환되었을까? this에 바인딩 된 대상이 잘못 지정되었기 때문이다.** 어떤 함수를 메서드로 호출할 때는 메서드 명 바로 앞의 객체가 this가 된다. 위 코드에서  suzi.`__proto__`.getName()에서 getName 함수 내부에서의 this는 suzi가 아니라 suzi.`__proto__`가 된다. **이 객체 내부에는 name 프로퍼티가 없으므로** 찾고자 하는 식별자가 정의돼 있지 않을 때는 Error 대신 undefined를 반환하다. 라는 규약에 의해 undefined가 반환된 것이다. 



**this를 인스턴스로 할 수 있다면** 본래 생각하던 올바른 결과를 얻을 수 있다. 그 방법은 `__proto__` 없이 **인스턴스에서 곧바로 메서드를 사용**하는 것이다.

```javascript
var suzi = new Person('Suzi')
suzi.getName() // Suzi
```

`__proto__` 없이 어떻게 prototype 메서드에 접근할 수 있는걸까??  그 이유는 바로 `__proto__`가 생략 가능한 프로퍼티이기 때문이다.

**결과적으로 suzi.`__proto__`에 있는 메서드인 getName을 실행하지만 this는 suzi를 바라보게 할 수 있게 된 것이다.**

프로토타입의 핵심을 간단한 문장으로 설명하면 다음과 같다. **"new 연산자로 Constructor를 호출하면 instance가 만들어지는데, 이 instance의 생략 가능한 프로퍼티인 `__proto__`는 Constructor의 prototype을 참조한다."**

프로토타입의 개념을 좀 더 상세히 설명하면 다음과 같다. 자바스크립트는 함수에 자동으로 객체인 prototype 프로퍼티를 생성해 놓는데,  해당 함수를 생성자 함수로 사용할 경우, 즉 new 연산자와 함께 호출할 경우, 그로부터 생성된 인스턴스에는 `__proto__` 가 자동으로 생성되며, 이 프로퍼티는 생성자 함수의 prototype 프로퍼티를 참조한다. `__proto__` 프로퍼티는 생략 가능하도록 구현돼 있기 때문에 생성자 함수의 prototype에 어떤 메서드나 프로퍼티가 있다면 인스턴스에서도 마치 자신의 것처럼 해당 메서드나 프로퍼티에 접근할 수 있다.



### 1-2. constructor 프로퍼티

**생성자 함수의 프로퍼티인 prototype 객체 내부에는 constructor라는 프로퍼티**가 있다. 인스턴스의 `__proto__` 객체 내부에도 마찬가지이다. **이 프로퍼티는 원래의 생성자 함수(자기 자신)을 참조한다.** 이는 **인스턴스로부터 그 원형이 무엇인지를 알 수 있는 수단**으로서 유용하다.

```javascript
var arr = [1,2];
Array.prototype.constructor === Array // true
arr.__proto__.constructor === Array   // true
arr.constructor === Array             // true

var arr2 = new arr.constructor(3,4)
console.log(arr2)  				 	  // [3,4]
```

인스턴스의 `__proto__`가 생성자 함수의 prototype 프로퍼티를 참조하며 `__proto__`가 생략 가능하기 때문에 인스턴스에서 직접 constructor에 접근할 수 있는 수단이 생긴 것이다.

constructor는 읽기 전용 속성이 부여된 예외적인 경우를 제외하고는 값을 바꿀 수 있다.

```javascript
var NewConstructor = function () {
	console.log('this is new constructor!')
};
var dataTypes = [
    1,
    'test',
    true,
    {},
    [],
    function () {},
    /test/,
    new Number(),
    new String(),
    new Boolean(),
    new Object(),
    new String(),
    new Array(),
    new Function(),
    new RegExp(),
    new Date(),
    new Error()
]


dataTypes.forEach(function(d) {
    d.constructor = NewConstructor
    console.log(d.constructor.name, '&', d instanceof NewConstructor)
})
```

위 코드의 모든 데이터가 d instanceof NewConstructor 명령에 대해 false를 반환한다. 이는 **constructor를 변경하더라도 참조하는 대상이 변경될 뿐 이미 만들어진 인스턴스의 원형이 바뀐다거나 데이터 타입이 변하는 것은 아님을 알 수 있다.**

어떤 인스턴스로부터 생성자 정보를 알아내는 유일한 수단인 constructor가 항상 안전하지는 않지만 오히려 그렇기 때문에 클래스 상속을 흉내 내는 등이 가능해진 측면도 있다.



1. 다음 각 줄은 모두 동일한 대상을 가리킨다.

```javascript
[Constructor]
[instance].__proto__.Constructor
[instance].Constructor
Object.getPrototypeOf([instance]).Constructor
[Constructor].prototype.Constructor
```



2. 다음 각 줄은 모두 동일한 객체(prototype)에 접근할 수 있다.

```javascript
[Constructor].prototype
[instance].__proto__
[instance]
Object.getPrototypeOf([instance])
```





## 2. 프로토타입 체인



### 1-1. 메서드 오버라이드

인스턴스가 prototype에 정의된 프로퍼티 또는 메서드와 동일한 이름의 프로퍼티 또는 메서드를 가지고 있는 경우

```javascript
var Person = function (name) {
    this.name = name
}

Person.prototype.getName = function () {
    return this.name
}

var iu = new Person('지금')
iu.getName = function () {
    return '바로 ' + this.name
}

console.log(iu.getName())
```

 위 코드에서 일어난 현상을 메서드 오버라이드라고 한다. 자바스크립트 엔진이 getName 이라는 메서드를 찾는 방식은 **가장 가까운 대상인 자신의 프로퍼티를 검색하고, 없으면 그 다음으로 가까운 대상인 `__proto__`를 검색하는 순서로 진행된다.**

메서드 오버라이딩이 이뤄져 있는 상황에서 prototype에 있는 메서드에 접근하려면 어떻게 해야할까?

```javascript
iu.__proto__.getName().call(iu)
```



### 1-2. 프로토타입 체인

모든 객체의 `__proto__` 에는 Object.prototype이 연결된다.

`__proto__`는 생략이 가능하기 때문에 아래 코드와 같이 배열에서도 Object.prototype 내부의 메서드도 자신의 것처럼 실행할 수 있다.

```javascript
var arr = [1,2]
arr(.__proto__).push(3)
arr(.__proto__)(.__proto__).hasOwnProperty(2)
```

어떤 데이터 `__proto__`  프로퍼티 내부에 다시 `__proto__` 프로퍼티가 연쇄적으로 이어진 것을 프로토타입 체인이라 하고, 이 체인을 따라가며 검색하는 것을 프로토타입 체이닝이라고 한다. 

중요도가 낮은 내용이지만 각 생성자 함수는 모두 함수이기 때문에 Function 생성자 함수의 prototype과 연결된다.

```javascript
[instance].constructor.constructor // ƒ Function() {}
```



### 1-3. 객체 전용 메서드의 예외사항

어떤 생성자 함수이든 prototype은 반드시 객체이기 때문에 **Object.prototype이 언제나 프로토 타입의 최상단**에 존재하게 된다. 따라서 객체에서만 사용할 메서드는 다른 여느 데이터 타입처럼 프로토타입 객체 안에 정의할 수 없다. **객체에서만 사용할 메서드를 Object.prototype 내부에 정의한다면 다른 데이터 타입도 해당 메서드를 사용할 수 있게 되기 때문이다.**

이 같은 이유로 객체만을 대상으로 동작하는**객체 전용 메서드들은 부득이 Object.prototype이 아닌 Object에 스태틱 메서드로 부여할 수밖에 없었다.** 또한 생성자 함수인 Object와 인스턴스인 객체 리터럴 사이에서는 this를 통한 연결이 불가능하기 때문에 여느 전용 메서드처럼 '메서드명 앞의 대상이 곧 this'가 되는 방식 대신 **this의 사용을 포기하고 대상 인스턴스를 인자로 직접 주입해야 하는 방식으로 구현돼 있다.** 만약  객체 전용 메서드에서도 다른 데이터 타입과 마찬가지의 규칙을 적용할 수 있었다면, **예를 들어 Object.freeze(instance)의 경우instance.freeze()처럼 표현할 수 있을 것이다.**

반대로 같은 이유에서 Object.prototype에는 어떤 데이터 타입에서도 활용할 수 있는 범용적인 메서드들만 있다.

toString, hasOwnProperty, valueOf, isPrototypeOf 등은 모든 변수가 마치 자신의 메서드인 것처럼 호출할 수 있다.

> 참고
>
> 앞서 '프로토타입 체인상 가장 마지막에는 언제나 Object.prototype이 있다' 고 했지만 예외적으로 Object.create를 이용하면 Object.prototype의 메서드에 접근할 수 없는 경우가 있다. Object.create(null)은 `__proto__`가 없는 객체를 생성한다.
>
> ```javascript
> var _proto = Object.create(null)
> _proto.getValue = function (key) {
>     return this[key]
> }
> var obj = Object.create(_proto)
> obj.a = 1
> console.log(obj.getValue('a'))
> console.dir(obj)
> ```
>
> 이 방식으로 만든 객체는 일반적인 데이터에서 반드시 존재하던 내장 메서드 및 프로퍼티들이 제거됨으로써 기본 기능에 제약이 생긴 대신, 객체 자체의 무게가 가벼워짐으로써 성능상 이점을 가진다.





### 1-4. 다중 프로토타입 체인

자바스크립트의 기본 내장 데이터 타입들은 모두 프로토타입 체인이 1단계 (객체) 이거나 2단계 (나머지)로 끝나는 경우가 있지만 사용자가 새롭게 만드는 경우에는 그 이상도 얼마든지 가능하다. 대각선의 `__proto__`를 연결해나가기만 하면 무한대로 체인 관계를 이어나갈 수 있다. 이 방법으로부터 다른 언어의 클래스와 비슷하게 동작하는 구조를 만들 수 있다.

대각선의 `__proto__`를 연결하는 방법은 `__proto__`가 가리키는 대상, 즉 생성자 함수의 prototype이 연결하고자하는 상위 생성자 함수의 인스턴스를 바라보게끔 해주면 된다.

```javascript
var Grade = function () {
    var args = Array.prototype.slice.call(arguments)
    for (var i = 0; i < args.length; i++) {
        this[i] = args[i]
    }
    this.length = args.length
}

var g = new Grade(100, 80)
```

변수 g는 Grade의 인스턴스를 바라본다. Grade의 인스턴스의 정체는 배열의 형태를 지니지만, 배열의 메서드를 사용할 수 없는 유사배열객체이다. 인스턴스에서 배열 메서드를 직접 쓸 수 있게끔 하려면 어떻게 해야할까? 그렇게 하기 위해서는 g.`__proto__`, 즉, Grade.prototype이 배열의 인스턴스를 바라보게 하면된다.

```javascript
Grade.prototype = [];
```



## 질문

1. 모든 객체의 최상단 프로토타입은 Object.prototype 인가? (O, X)
2. 객체 전용 메서드들은 부득이 Object.prototype이 아닌 Object에 스태틱 메서드로 부여할 수밖에 없는데 그 이유는 무엇때문인가?
3. 예외적으로 Object.prototype의 메서드에 접근할 수 없게 객체를 생성하는 방법은 어떤 것이며, 그 방법에 장 단점은 무엇인가?

