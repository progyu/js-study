# Class



## 자바스크립트 Class의 탄생 배경

**자바스크립트는 프로토타입 기반 언어라서 '상속' 개념이 존재하지 않는다.** 이는 클래스 기반의 다른 언어에 익숙한 많은 개발자들을 혼란스럽게 했고, 따라서 클래스와 비슷하게 동작하게끔 흉내 내는 여러 기법들이 탄생했다. 클래스에 대한 이러한 니즈에 따라 **결국 ES6에는 클래스 문법이 추가됐다. 다만, ES6의클래스에서도 일정 부분 프로토타입을 활용**하기 때문에 ES5 체제 하에서 클래스를 흉내내기 위한 구현 방식을 학습하는 것은 여전히 큰 의미가 있다. 



## 클래스와 인스턴스의 개념 이해

영어사전에서 class는 '계급, 집단, 집합' 등으로 번역한다. 프로그래밍 언어적으로도 이와 동일한 개념에서 접근하면 된다.

음식, 과일은 모두 집단, 즉 클래스이다.

음식이나 과일은 **어떤 사물들의 공통 속성을 모아 정의한 것일 뿐 직접 만질 수도 볼 수도 없는 추상적인 개념**이다.

**하위 개념은 상위 개념을 포함하면서 더 구체적인 개념이 추가**된다. 물론 하위 클래스가 아무리 구체화 되더라도 이들은 결국 추상적인 개념일 뿐이다.

한편 과일 클래스에 속하는 감귤, 자몽 등은 실제로 먹을 수 있고 만질 수 있는 실존하는 개체이다. 이처럼 **어떤 클래스의 속성을 지니는 실존하는 개체를 일컬어 인스턴스**라고 한다.

영한사전에는 인스턴스를 '사례'라고 번역하고 있다. 풀어쓰면 '어떤 조건에 부합하는 구체적인 예시'가 된다. 여기서의 조건이 곧 클래스를 의미한다고 보면, 어떤 클래스에 속한 개체는 그 클래스의 조건을 모두 만족하므로 **그 클래스의 구체적인 예시, 즉 인스턴스가 될 것**이다. 

**현실 세계**에서는 개체들이 이미 존재하는 상태에서 이들을 구분짓기 위해 클래스를 도입한다. **반면, 프로그래밍 언어상**에서는 접근 방식이 정반대이다. 컴퓨터는 위와 같은 구분법을 알지 못하므로 **사용자가 직접 여러가지 클래스를 정의**해야 하며, 클래스를 바탕으로 인스턴스를 만들 때 비로소 어떤 개체가 클래스의 속성을 지니게 된다. 또한 **한 인스턴스는 하나의 클래스만을 바탕으로 만들어진다.** 어떤 인스턴스가 다양한 클래스에 속할 수는 있지만 이 클래스들은 모두 인스턴스 입장에서는 '직계존속'이다. 다중상속을 지원하는 언어이든 그렇지 않은 언어이든 **결국 인스턴스를 생성할 때 호출할 수 있는 클래슨느 오직 하나뿐일 수밖에 없기 때문**이다.



## 자바스크립트의 클래스

프로토타입을 일반적인 의미에서의 클래스 관점에서 접근해보면 비슷하게 해석할 수 있는 요소들이 있다.

생성자 함수 Array를 new 연산자와 함께 호출하면 인스턴스가 생성된다. 이때 Array를 일종의 클래스라고 하면, **Array의 prototype 객체 내부 요소들이 인스턴스에 상속된다고 볼 수 있다.** **엄밀히는 상속이 아닌 프로토타입 체이닝에 의한 참조지만** 결과적으로 동일하게 동작한다. 한편 Array 내부 프로퍼티들 중 prototype 프로퍼티를 제외한 나머지는 인스턴스에 상속되지 않는다.

**인스턴스에 상속되는지(인스턴스가 참조하는지) 여부에 따라 스태틱 멤버와 인스턴스 멤버로 나뉜다.** 이 분류는 다른 언어의 클래스 구성요소에 대한 정의를 차용한 것으로서 클래스 입장에서 사용 대상에 따라 구분한 것이다. **그런데 여느 클래스 기반 언어와 달리 자바스크립트에서는 인스턴스에서도 직접 메서드를 정의할 수 있기 때문**에 인스턴스 메서드라는 명칭은 프로토타입에 정의한 메서드를 지칭하는 것인지 인스턴스에 정의한 메서드를 지칭하는 것인지에 대해 혼란을 야기한다. 따라서, 이 명칭 대신 자바스크립트의 특성을 살려 **프로토타입 메서드**라는 용어로 많이 사용되고 있다.



```javascript
// 스태틱 메서드, 프로토타입 메서드
var Rectangle = function (width, height) { // 생성자
  this.width = width;
  this.height = height;
};

Rectangle.prototype.getArea = function () { // 프로토타입 메서드
  return this.width * this.height;
};

Rectangle.isRectangle = function (instance) { // 스태틱 메서드
  return instance instanceof Rectangle && 
    instance.width > 0 && instance.height > 0;
};

var rect1 = new Rectangle(3, 4)
console.log(rect1.getArea());
console.log(rect1.isRectangle(rect1));
console.log(Rectangle.isRectangle(rect1));
```

**인스턴스에서 직접 호출할 수 있는 메서드를 프로토타입 메서드라 하고, 인스턴스에서 직접 접근할 수 없는 메서드를 스태틱 메서드라고 한다. 스태틱 메서드는 생성자 함수를 this로 해야만 호출할 수 있다.**

**프로그래밍 언어에서 클래스는 사용하기에 따라 추상적일 수도 있고 구체적인 개체가 될 수도 있다**고 하는데 구체적인 인스턴스가 사용할 메서드를 정의한 '틀'의 역할을 담당하는 클래스는추상적인 개념이지만, **클래스 자체를 this로 해서 직접 접근해야만 하는 스태틱 메서드를 호출할 때의 클래스는 그 자체가 하나의 개체로서 취급**된다.



## 클래스 상속

클래스 상속은 객체지향에서 가장 중요한 요소 중 하나이다.

아래 다중 프로토타입 체인에 대한 예시가 클래스 상속의 핵심을 잘 표현한다.

```javascript
var Grade = function () {
  var args = Array.prototype.slice.call(arguments);
  for (var i = 0; i < args.length; i++) {
    this[i] = args[i];
  }
  this.length = args.length;
};

Grade.prototype = [];
var g = new Grade(100, 80);
```

자바스크립트에서 클래스 상속을 구현했다는 것은 결국 프로토타입 체이닝을 잘 연결한 것이다. 다만, 기본적으로는 그렇다는 것이고, 세부적으로 완벽하게 superclass와 subclass의 구현이 이뤄진 것은 아니다. **위 예제에는 몇가지 큰 문제가 있다. length 프로퍼티가 configurable(삭제 가능)하다는 점과 Grade.prototype에 빈 배열을 참조시켰다는 점**이 그렇다.



**length 프로퍼티가 삭제 가능한 문제**

위 예제 코드에 이어서 아래 코드를 실행

```javascript
g.push(90);
console.log(g); // Grade [100, 80, 90, length: 3]

delete g.length;
g.push(70);
console.log(g); // Grade [70, 80, 90, length: 1]
```

내장객체인 배열 인스턴스의 length 프로퍼티는 configurable 속성이 false라서 삭제가 불가능하지만, Grade 클래스의 인스턴스는 배열 메서드를 상속하지만 기본적으로는 일반 객체의 성질을 그대로 지니므로 삭제가 가능하다. 한편 push 했을 때 0번째 인덱스에 70이 들어가고 length가 다시 1이 될 수 있었던 까닭은 무엇일까? 바로 g.`__proto__`, 즉 Grade.prototype이 빈 배열을 가리키고 있기 때문이다.

이처럼 **클래스의 있는 값이 인스턴스의 동작에 영향을 줘서는 안된다.** 이런 영향을 줄 수 있다는 사실 자체가 이미 클래스의 추상성을 해치는 것이기 때문이다. 인스턴스와의 관계에서는 구체적인 데이터를 지니지 않고,  오직 인스턴스가 사용할 메서드만을 지니는 추상적인 '틀'로서만 작용하게끔 작성하지 않는다면 예기치 않은 오류가 발생할 가능성을 안고 가야 하는 것이다.



```javascript
// Rectangle을 상속하는 Square 클래스
var Rectangle = function (width, height) {
  this.width = width
  this.height = height
}

Rectangle.prototype.getArea = function () {
  return this.width * this.height
}

var rect = new Rectangle(3, 4)
console.log(rect.getArea())

var Square = function (width) {
  Rectangle.call(this, width, width)
}

Square.prototype = new Rectangle()

var sq = new Square(5) // Square의 프로토타입 객체에 Rectangle의 인스턴스 부여
console.log(sq)
```

위 예제는 기본적인 메서드 상속은 가능하지만 다음과 같은 두가지 문제점을 지닌다.

**Square.prototype에 width, height 같은 값이 존재하는 문제**

- Square.prototype.width(또는 height)에 값을 부여하고 sq.width(또는 height) 값을 지워버린다면 프로토타입 체이닝에 의해 엉뚱한 결과가 나올 것이다.

**constructor가 여전히 Rectangle을 바라보고 있는 문제**

- Sq.constructor로 접근하면 프로토타입 체이닝을 따라 sq.`__proto__.__proto__`. 즉 Rectangle.prototype에서 constructor를 찾게 되며, 이는 Rectangle을 가리키고 있는다.

이처럼 **하위 클래스로 삼을 생성자 함수의 prototype에 상위 클래스의 인스턴스를 부여하는 것**만으로도 기본적인 메서드 상속은 가능하지만 **다양한 문제가 발생할 여지가 있어 구조적으로 안전성이 떨어진다.**



## 클래스가 구체적인 데이터를 지니지 않게 하는 방법



#### 클래스 상속 및 추상화 방법(1) - 인스턴스 생성 후 프로퍼티 제거

 ```javascript
var Rectangle = function (width, height) {
  this.width = width
  this.height = height
}

Rectangle.prototype.getArea = function () {
  return this.width * this.height
}

// 인스턴스 생성 후 프로퍼티 제거
var extendClass1 = function (SuperClass, SubClass, subMethods) {
  SubClass.prototype = new SuperClass()
  for (var prop in SubClass.prototype) {
    if (SubClass.prototype.hasOwnProperty(prop)) {
      delete SubClass.prototype[prop]
    }
  }
  
  if (subMethods) {
    for (var method in subMethods) {
      SubClass.prototype[method] = subMethods[method]
    }
  }
  
  Object.freeze(SubClass.prototype)
  return SubClass
}

var Square = extendClass1(Rectangle, function (width) {
  Rectangle.call(this, width, width)
})

var sq = new Square(5) // 불필요한 인스턴스 프로퍼티가 제거 되었다.
console.log(sq)
 ```



#### 클래스 상속 및 추상화 방법(2)  - 빈 함수를 활용

 더글라스 크락포드가 제시해서 대중적으로 널리 알려진 방법
 SubClass의 prototype에 직접 SuperClass의 인스턴스를 할당하는 대신 아무런 프로퍼티를 생성하지 않는 빈 생성자 함수(Bridge)를 하나 더 만들어서 그 prototype이 SuperClass의 prototype을 바라보게끔 한 다음, SubClass의 prototype에는 Bridge의 인스턴스를 할당하게 하는 것. 빈 함수에 다리 역할을 부여 하는 것이다.

```javascript
var Rectangle = function (width, height) {
  this.width = width
  this.height = height
}

Rectangle.prototype.getArea = function () {
  return this.width * this.height
}

var extendClass2 = (function () {
  var Bridge = function () {}
  return function (SuperClass, SubClass, subMethods) {
    Bridge.prototype = SuperClass.prototype
    SubClass.prototype = new Bridge()
    if (subMethods) {
      for (var method in subMethods) {
        SubClass.prototype[method] = subMethods[method]
      }
    }
    Object.freeze(SubClass.prototype)
    return SubClass
  }
})()

var Square = extendClass2(Rectangle, function (width) {
  Rectangle.call(this, width, width)
})

var sq = new Square(5)

console.log(sq)
```



#### 클래스 상속 및 추상화 방법(3)  - Object.create 활용

ES5에서 도입된 Object.create를 이용한 방법. 이 방법은 SubClass의 `__proto__`가 SuperClass의 prototype을 바라보되, SuperClass의 인스턴스가 되지는 않으므로 앞서 소개한 두 방법보다 간단하면서도 안전하다.

```javascript
var Rectangle = function (width, height) {
  this.width = width
  this.height = height
}

Rectangle.prototype.getArea = function () {
  return this.width * this.height
}

var extendClass3 = function (SuperClass, SubClass, subMethods) {
  SubClass.prototype = Object.create(SuperClass.prototype)
  if (subMethods) {
    for (var method in subMethods) {
      SubClass.prototype[method] = subMethods[method]
    }
  }
  Object.freeze(SubClass.prototype)
  return SubClass
}

var Square = extendClass3(Rectangle, function (width) {
  Rectangle.call(this, width, width)
})

const sq = new Square(5)
console.log('instance', sq)
```

 **클래스 상속 및 추상화를 프로토타입으로 구현하는 기본적인 접근 방법은 위 세 가지 아이디어를 크게 벗어나지 않는다. 결국 SubClass.prototype의 `__proto__`가 SuperClass.prototype을 참조하고, SubClass.prototype에는 불필요한 인스턴스 프로퍼티가 남아있지 않으면 된다.**



## constructor 복구하기

위 세 가지 방법으로 기본적인 상속 구현에는 성공했지만 SubClass 인스턴스의 constructor는 여전히 SuperClass를 가리키는 상태이다. 엄밀히는 SubClass의 인스턴스, SubClass.prototype에도 constructor가 없는 상태이다.  이 문제를 해결하기 위해서는 SubClass.prototype.constructor가 원래의 SubClass를 바라보도록 해주면 된다.

```javascript
SubClass.prototype.constructor = SubClass
```



## 상위 클래스에서의 접근 수단 제공

하위 클래스의 메서드에서 상위 클래스의 메서드 실행 결과를 바탕으로 추가적인 작업을 수행하고 싶을 때가 있다. 이럴 때 매번 SuperClass.prototype.method.apply(this, arguments)로 해결하는 것은 상당히 번거롭고 가독성이 떨어지는 방식이다. **하위 클래스에서 상위 클래서의 프로토타입 메서드에 접근하기 위한 별도의 수단**이 있다면 편리할 것이다. **이런 별도의 수단으로  다른 객체지향 언어들의 클래스 문법 중 하나인 'super' 가 있다.**

```javascript
var extendClass = function (SuperClass, SubClass, subMethods) {
  SubClass.prototype = Object.create(SuperClass.prototype)
  SubClass.prototype.constructor = SubClass
  SubClass.prototype.super = function (propName) {
    var self = this
    if (!propName) {
      return function () {
        SuperClass.apply(self, arguments)
      }
    }
    var prop = SuperClass.prototype[propName]
    if (typeof prop !== 'function') return prop
    return function () {
      return prop.apply(self, arguments)
    }
  }

  if (subMethods) {
    for (var method in subMethods) {
      SubClass.prototype[method] = subMethods[method]
    }
  }
  Object.freeze(SubClass.prototype)
  return SubClass
}

var Rectangle = function (width, height) {
  this.width = width
  this.height = height
}

Rectangle.prototype.getArea = function () {
  return this.width * this.height
}

var Square = extendClass(
  Rectangle,
  function (width) {
    this.super()(width, width)
  },
  {
    getArea: function () {
      console.log('size is :', this.super('getArea')())
    }
  }
)

var sq = new Square(10)
sq.getArea() // SubClass의 메서드 실행
console.log(sq.super('getArea')()) // SuperClass의 메서드 실행
```

위 예시 코드의 super 사용법은 다음과 같다. SuperClass의 생성자 함수에 접근하고자 할 때는 this.super(), SuperClass의 프로토타입 메서드에 접근하고자 할 때는 this.super(propName)과 같이 사용하면 된다.



## ES6의 클래스 및 클래스 상속



#### ES5와 ES6의 클래스 문법 비교

```javascript
var ES5 = function (name) {
  this.name = name
}

ES5.staticMethod = function () {
  return this.name + ' staticMethod'
}

ES5.prototype.method = function () {
  return this.name + ' prototypeMethod'
}

var es5Instance = new ES5('es5')
console.log(ES5.staticMethod())
console.log(es5Instance.method())

var ES6 = class {
  constructor (name) {
    this.name = name
  }

  static staticMethod () {
    return this.name + ' staticMethod'
  }

  method () {
    return this.name + ' prototypeMethod'
  }
}

var es6Instance = new ES6('es6')
console.log(ES6.staticMethod())
console.log(es6Instance.method())
```



#### ES6의 클래스 상속

```javascript
var Rectangle = class {
  constructor (width, height) {
    this.width = width
    this.height = height
  }

  getArea () {
    console.log('this!!!', this)
    return this.width * this.height
  }
}

var Square = class extends Rectangle {
  constructor (width) {
    // constructor 내부에서 super 키워드를 함수처럼 사용할 수 있는데
    // 이 함수는 SuperClass의 Constructor를 실행한다.
    super(width, width)
  }

  getArea () {
    // constructor를 제외한 다른 메서드에서는 super 키워드를 마치 객체처럼
    // 사용할 수 있고, 이때 객체는 SuperClass.prototype을 바라보는데, 호출한
    // 메서드의 this는 'super'가 아닌 원래의 this를 그대로 따른다.
    console.log('size is :', super.getArea())
  }
}

var sq = new Square(10)
console.log(sq.getArea())
```



## 정리

클래스는 어떤 사물의 공통 속성을 모아 정의한 추상적인 개념이고, 인스턴스는 클래스의 속성을 지니는 구체적인 사례이다. 상위 클래스의 조건을 충족하면서 더욱 구체적인 조건이 추가된 것을 하위 클래스라고 한다.

클래스의 prototype 내부에 정의된 메서드를 프로토타입 메서드라고 하며, 이들은 인스턴스가 마치 자신의 것처럼 호출할 수 있다. 한편 클래스(생성자 함수)에 직접 정의한 메서드를 스태틱 메서드라고 하며, 이들은 인스턴스가 직접 호출할 수 없고, 클래스(생성자 함수)에 의해서만 호출할 수 있다.

클래스 상속을 흉내내기 위한 방법은 세 가지가 있다. 

1. SubClass.prototype에 SuperClass의 인스턴스를 할당한 다음 프로퍼티를 모두 삭제하는 방법
2. 빈 함수(Bridge)를 활용하는 방법
3. Object.create를 이용하는 방법

위 세 방법 모두 constructor 프로퍼티가 원래의 생성자 함수를 바라보도록 조정해야 한다.



## 질문

1.  아래 코드의 length는 어떤 프로토타입의 프로퍼티를 참조한 것일까?

```javascript
const arr = [1,2];

arr.length
```

2. 아래 코드의 콘솔 실행 결과는?

```javascript
const arrayLike = { 0: 1, 1: 2, 2: 3, length: 3 };

arrayLike.__proto__ = [];

delete arrayLike.length;

arrayLike.push(4)

console.log(arrayLike); // ???
```

3. 아래 코드의 콘솔 실행 결과는?

```javascript
var ES5 = function (name) {
  this.name = name
}

ES5.staticMethod = function () {
  console.log('staticMethod this', this)
  return this.name + ' staticMethod'
}

ES5.prototype.method = function () {
  console.log('prototypeMethod this', this)
  return this.name + ' prototypeMethod'
}

var es5Instance = new ES5('es5')
console.log(ES5.staticMethod())
console.log(es5Instance.method())
```

