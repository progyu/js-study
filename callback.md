callback



## 1. 콜백함수란?

콜백함수는 다른 함수의 인자로 넘겨주는 함수이다. 콜백 함수를 넘겨받은 코드는 이 콜백함수를 필요에 따라 적절한 시점에 실행할 것이다. **콜백함수는 제어권과 관련이 깊다.**  

callback은 '부르다', '호출하다'는 의미인 call과  '뒤돌아오다', '되돌다' 는 의미인 back의 합성어로 '되돌아 호출해달라'는 명령이다. 어떤 함수 x를 호출하면서 특정 조건일 때 함수 Y를 실행해서 나에게 알려달라는 요청을 함께 보내는 것이다. 이 요청을 받은 함수 x는 해당 조건이 갖춰줬는지 여부를 스스로 판단하고 Y를 직접 호출한다.



## 2. 제어권

```javascript
let count = 0;

const cbfunc = function () {
    console.log(count)
    if(++count > 4) clearTimeout(timer)
}

const timer = setInterval(cbfunc, 300);
```

setInterval이라는 다른 코드에 첫번째 인자로서 cbfunc 함수를 넘겨주자 제어권을 넘겨받은 setInterval이 스스로의 판단에 따라 적절한 시점에 이 익명함수를 실행했다. 이처럼 콜백함수의 제어권을 넘겨받은 코드는 콜백 함수 호출 시점에 대한 제어권을 가진다.



### 인자

```javascript
const newArr = [10, 20, 30].map(function (currentValue, index) {
    console.log(currentValue, index);
    return currentValue + 5;
});

console.log(newArr);

// map 메서드 구조
Array.prototype.map(callback[, thisArg])
callback: function(currentValue,index, arrary)


const newArr2 = [10, 20, 30].map(function (index, currentValue) {
    console.log(index, currentValue);
    return currentValue + 5;
});

console.log(newArr2);
```

콜백 함수를 호출하는 주체가 사용자가 아닌  map 메서드 이므로 map 메서드가 콜백 함수를 호출할 때 인자에 어떤 값들을 어떤 순서로 넘길 것인지가 전적으로 map 메서드에게 달린 것이다. 이처럼 **콜백 함수의 제어권을 넘겨받은 코드는 콜백 함수를 호출할 때 인자에 어떤 값들을 순서대로 넘길 것인지에 대한 제어권을 가진다** 



###  this

콜백 함수도 함수이기 때문에 기본적으로 this가 전역객체를 참조하지만, 제어권을 넘겨받을 코드에서 콜백 함수에 별도로 this가 될 대상을 지정한 경우에는 그 대상을 참조하게 된다.

```javascript
Array.prototype.map = function(callback, thisArg) {
    let mappedArr = [];
   	console.log('this', this)
    for (let i = 0; i < this.length; i++) {
        const mappedValue = callback.call(thisArg || window, this[i], i, this);
        mappedArr[i] = mappedValue;
    }
    return mappedArr;
}
```

map에 콜백 함수 내부에서의 this는 따로 지정하지 않은 경우 전역 객체를 참조한다.



### 콜백함수는 함수다

 **콜백 함수로 어떤 객체의 메서드를 전달하더라도 그 메서드는 메서드가 아닌 함수로서 호출된다.**

어떤 함수의 인자에 객체의 메서드를 전달하더라도 이는 결국 메서드가 아닌 함수일 뿐이다.

```javascript
const obj = {
    vals: [1,2,3],
    logValues: function(v, i) {
        console.log(this, v, i)
    }
}

obj.logValues(1,2)
[4,5,6].forEach(obj.logValues)




const obj = {
    vals: [1,2,3],
    logValues: function(v, i) {
        console.log(this,this[i], v, i)
    }
}

[4,5,6].forEach(obj.logValues, obj.vals) // ??
```



### 콜백 함수 내부의 this에 다른 값 바인딩 하기

```javascript
const obj1 = {
    name: 'obj1',
    func: function () {
        console.log(obj1.name);
    }
}

setTimeout(obj1.func, 1000); // ?? 해당 결과가 나오는 이유는?




// 스코프 체인을 타고 전역에 있는 obj1을 찾는다.s
```



```javascript
const obj1 = {
    name: 'obj1',
    func: function () {
        self = this;
        return function () {
            console.log(self.name)
        }
    }
}

// 클로저
const callback = obj1.func();
setTimeout(callback, 1000);


const obj2 = {
    name: 'obj2',
    func: obj1.func
}

const callback2 = obj2.func()
setTimeout(callback2, 1500)

const obj3 = { name: 'obj3' }
const callback3 = obj1.func.call(obj3)
setTimeout(callback3, 2000)

// 결과는???



// obj3이 3 번 찍힌다. setTimeout이 비동기로 동작하기 때문에 동기 코드가 모두 실행된 후 callback이 호출된다.
// this는 호출 시점에 바인딩 된다. 호출 시점에 this는 모두 obj3로 바인딩 되어 있고 따라서 obj3만 3번 찍히는 결과가 나온다. 
```



```javascript
// bind 메서드 활용
const obj1 = {
    name: 'obj1',
    func: function () {
        console.log(this.name);
    }
}

const callback = obj1.func.bind(obj1)
setTimeout(callback, 1000)
```



### 콜백 지옥과 비동기 제어

콜백 지옥은 콜백 함수를 익명 함수로 전달하는 과정이 반복되어 코드의 들여쓰기 수준이 감당하기 힘들 정도로 깊어지는 현상이다. 주로 이벤트 처리나 서버 통신과 같이 비동기적인 작업을 수행하기 위해 이런 형태가 자주 등장하는데, 가독성도 떨어지고, 코드를 수정하기도 어렵다.

cpu의 계산에 의해 즉시 처리가 가능한 대부분의 코드는 동기적인 코드이다. 계산식이 복잡해서 cpu가 계산하는데 시간이 많이 필요한 경우라 하더라도 이는 동기적인 코드이다. 반면 사용자의 요청에 의해 특정 시간이 경과되기 전까지 보류한다거나 (setTimeout), 사용자의직접적인 개입이 있을 때 비로소 어떤 함수를 실행하도록 대기한다거나(addEventListener), 웹 브라우저 자체가 아닌 별도의 대상에 무언가를 요청하고 그에 대한 응답이 왔을 때 비로소 어떤 함수를 실행하도록 대기하는 등(XMLHttpRequest), 별도의 요청, 실행 대기, 보류 등과 관련된 코드는 비동기적인 코드이다.



 ```javascript
setTimeout(function(name) {
    let coffeList = name;
    setTimeout(function(name) {
        coffeList += ', ' + name
        setTimeout(function(name) {
            coffeList += ', ' + name
            console.log('coffeList', coffeList)
        }, 300, '카페라떼')
    }, 300, '아메리카노')
}, 300, '에스프레소')
 ```

위 코드는 들여쓰기 수준이 과도하고 값이 전달되는 순서가 아래에서 위로 향하고 있어 어색하게 느껴지는 단점이 있다. 단점을 해결하는 방법으로 콜백 함수를 모두 기명 함수로 전환하는 방법이 있다.





```javascript
let coffeList = '';

const addEspresso = function(name) {
    coffeList = name;
    setTimeout(addAmericano, 1000, '아메리카노')
}

const addAmericano = function(name) {
    coffeList += ', ' + name;
    setTimeout(addCaffeLatte, 1000, '카페라떼')
}

const addCaffeLatte = function(name) {
    coffeList += ', ' + name;
    console.log('coffeList', coffeList)
}

setTimeout(addEspresso, 1000, '에스프레소')
```

위에서부터 아래로 순서대로 읽어내려가는데 어려움이 없다. 변수를 최상단으로 끌어올림으로써 외부에 노출하게 됐지만 즉시 실행 함수등으로 감싸면 해결될 문제이다. 그렇지만 일회성 함수를 전부 변수에 할당하는 것이 마뜩지 않을 수 있고 코드명을 일일이 따라다녀야 하므로 오히려 헷갈릴 소지가 있기도 하다. 지난 십수 년간 자바스크립트 진영은 비동기적인 일련의 작업을 동기적으로, 혹은 동기적인 것처럼 보이게끔 처리해주는 장치를 마련하고자 끊임없이 노력해 왔다. ES6에서는 Promise, Generator 들이 도입됐고, ES2017에서는 async, await가 도입됐다.



```javascript
new Promise(resolve => {
    setTimeout(name => 
        resolve(name),
               1000, '에스프레소')
})
.then(prevName => 
    new Promise(resolve => {
        setTimeout(name =>
           resolve(prevName + ', ' + name)
                   , 1000, '아메리카노')
    })
)
.then(prevName =>
     new Promise(resolve => {
    	 setTimeout(name =>
           resolve(prevName + ', ' + name)
                   , 1000, '카페라떼')
	 })
)
.then(console.log)
```

ES6의 promise를 이용한 방식. new 연산자와 함께 호출한 Promise의 인자로 넘겨주는 콜백 함수는 호출할 때 바로 실행되지만 그 내부에 **resolve 또는 reject 함수를 호출할 수  있는 구문이 있을 경우 둘 중 하나가 실행되기 전까지는 다음(then) 또는 오류 구문(catch)로 넘어가지 않는다. 따라서 비동기 작업이 완료될 때 비로소 resolve 또는 reject를 호출하는 방법으로 비동기 작업의 동기적 표현이 가능하다.**



```javascript
const addCoffee = function (name) {
    return function (prevName) {
        return new Promise(function(resolve) {
            setTimeout(function() {
                let newName = prevName ? (prevName + ', ' + name) : name
                console.log('newName', newName)
                resolve(newName)
            }, 1000)
        })
    }
} 


// 클로저 등장, 외부함수가 return문을 만나 종료 되었음에도 불구하고 name, prevName인자를 내부함수에서 참조할 수 있다.
addCoffee('에스프레소')() // promise를 return, newName이 resolve 된다.
	.then(addCoffee('아메리카노')) // 이전에 resolve된 newName 어떻게 prevName이 되는 것일까????????????
	.then(addCoffee('카페라떼'))
	.then(console.log)

// promsie 단축 표기법
addCoffee('에스프레소')()
	.then(newName => addCoffee('아메리카노')(newName))
	.then(newName => addCoffee('아메리카노')(newName))
	.then(console.log)
```



```javascript
// Generator

const addCoffee = function (prevName, name) {
    setTimeout(function () {
        coffeeMaker.next(prevName ? prevName + ', ' + name : name);
    }, 500)
}

const coffeeGenerator = function* () {
    const espresso = yield addCoffee('', '에스프레소');
    console.log(espresso)
    const americano = yield addCoffee(espresso, '아메리카노');
    console.log(americano)
    const caffeLatte = yield addCoffee(americano, '카페라떼');
    console.log(caffeLatte)
}

const coffeeMaker = coffeeGenerator();
coffeeMaker.next();
```

Generator 함수를 실행하면 Iterator가 반환된다. Iterator는 next라는 메서드를 가지고 있다. 이 next 메서드를 호출하면 Generator 함수 내부에서 가장 먼저 등장하는 yield에서 함수의 실행을 멈춘다. 비동기 작업이 완료되는 시점마다 next 메서드를 호출해주면 Generator 함수 내부의 소스가 위에서부터 아래로 순차적으로 진행된다.



```javascript
// Promise + Async/await
const addCoffee = function (name) {
    return new Promise(function(resolve) {
        setTimeout(function () {
            resolve(name)
        }, 500)
    })
}

const coffeeMaker = async function () {
    let coffeeList = '';
    const _addCoffee = async function (name) {
        coffeeList += (coffeeList ? ',' : '') + await addCoffee(name)
    }
    
    await _addCoffee('에스프레소')
    await _addCoffee('아메리카노')
    await _addCoffee('카페라떼')
    
    console.log(coffeeList);
}

coffeeMaker();
```

ES2017에서는 가독성이 뛰어나면서도 작성법이 간단한 새로운 기능이 추가됐는데,  바로 async / await이다. 비동기 작업을 수행하고자 하는 함수 앞에 async를 표기하고, 함수 내부에서 실질적인 비동기 작업이 필요한 위치마다 await를 표기하는 것만으로 뒤의 내용을 promise로 자동 전환하고, 해당 내용이 resolve된 이후에야 다음으로 진행한다. **즉, promise의 then과 흡사한 효과를 얻을 수 있다.**



```javascript
await Promise.resolve(5) // ? (1)

const promise = Promise.resolve('김개똥');

async function getName () {
    console.log(await promise);
    return '홍길동';
}

getName() // ? (2)
```



## 정리

- 콜백 함수는 다른 코드에 인자로 넘겨줌으로써 그 제어권도 함께 위임한 함수이다.
- 제어권을 넘겨받은 코드는 다음과 같은 제어권을 가진다.
  - 콜백 함수를 호출하는 시점을 스스로 판단해서 **실행**한다.
  - 콜백 함수를 호출할 때 **인자로 넘겨줄 값들 및 그 순서**가 정해져 있다. 이 순서를 따르지 않고 코드를 작성하면 엉뚱한 결과를 얻게 된다.
  - 콜백 함수의 **this가 무엇을 바라보도록 할지**가 정해져 있는 경우도 있다. 정하지 않은 경우에는 전역 객체를 바라본다. 사용자 임의로 this를 바꾸고 싶을 경우 bind 메서드를 활용한다.
- 어떤 함수에 인자로 메서드를 전달하더라도 이는 결국 함수로서 실행된다.
- 비동기 제어를 위해 콜백 함수를 사용하다 보면 콜백 지옥에 빠지기 쉽다. 최근 ECMAScript에는 Promise, Generator, async/await 등 콜백 지옥에서 벗어날 수 있는 훌륭한 방법들이 속속 등장하고 있다.



