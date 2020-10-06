# 클로저



## 1. 클로저의 의미와 이해

클로저는 여러 함수형 프로그래밍 언어에서 등장하는 보편적인 특성이다. 자바스크립트 고유의 개념이 아니라서 ECMAScript 명세에서도 클로저의 정의를 다루지 않고 있고, 다양한 문헌에서 제각각 클로저를 다르게 정의 또는 설명하고 있다.



### 다양한 문헌에서의 클로저 정의

- **함수를 선언할 때 만들어지는 유효범위가 사라진 후에도 호출할 수 있는 함수** - 존 레식, (자바스크립트 닌자 비급)
- **이미 생명 주기상 끝난 외부 함수의 변수를 참조하는 함수** - 송형주, 고현준, (인사이드 자바스크립트)
- **자신이 생성될 때의 스코프에서 알 수 있었던 변수들 중 언젠가 자신이 실행될 때 사용할 변수들만을 기억하며 유지시키는 함수** - 유인동(함수형 자바스크립트 프로그래밍)



MDN에서는 클로저에 대해 "**클로저는 함수와 그 함수가 선언될 당시의 lexical environment의 상호관계에 따른 현상**" 이라고 소개한다. '선언될 당시의 lexical environment'는 실행 컨텍스트의 구성 요소 중 하나인 outerEnvironmentReference에 해당한다.  LexicalEnviroment의 environmentRecord와 outerEnvironmentReference에 의해 변수의 유효범위인 스코프가 결정되고 스코프 체인이 가능해진다고 했었다. **어떤 컨텍스트 A에서 선언한 내부함수 B의 실행 컨텍스트가 활성화된 시점에는 B의 outerEnvironmentReference가 참조하는 대상인 A의 LexicalEnvironment에도 접근이 가능하다.** A에서는 B에서선언한 변수에 접근할 수 없지만 B에서는 A에서 선언한 변수에 접근이 가능하다. 

여기서 '상호관계'의 의미를 파악할 수 있다. 내부함수 B가 A의 LexicalEnvironment를 언제나 사용하는 것은 아니다. **내부함수에서 외부 변수를 참조하는 경우에 한해서만 '상호관계'의 의미가 있을 것이다.**



```javascript
// 외부 함수의 변수를 참조하는 내부 함수
const outer = function () {
  let count = 1
  const inner = function () {
    console.log(++count)
  }
  inner()
}
outer()

// 외부 함수의 변수를 참조하는 내부 함수(2)
const outer = function () {
  let count = 1
  const inner = function () {
    return ++count
  }
  return inner()
}
const outer2 = outer()
console.log(outer2)
```

inner 함수 내부에서는 count 변수를 선언하지 않았기 때문에 environmentRecord에서 값을 찾지 못하므로 outerEnvironmentReference에 지정된 상위 컨텍스트인 outer의 LexicalEnvironment에 접근해서 count를 찾는다. outer 함수의 실행 컨텍스트가 종료되면 LexicalEnvironment에 저장된 식별자들 (counter, inner)에 대한 참조를 지운다. 그러면 각 주소에 저장돼 있던 값들은 자신을 참조하는 변수가 하나도 없게 되므로 가비지 컬렉션의 수집 대상이 된다.



```javascript
// 외부 함수의 변수를 참조하는 내부 함수(2)
const outer = function () {
  let count = 1
  const inner = function () {
    return ++count
  }
  return inner
}
const outer2 = outer()
console.log(outer2)
console.log(outer2)
```

위 코드는 **inner 함수의 실행 결과가 아닌 inner 함수 자체를 반환하였기 때문에 outer의 실행 컨텍스트가 종료된 후에도 inner 함수를 호출할 수 있다.** outer 함수의 실행 컨텍스트가 종료될 때 outer2 변수는 outer의 실행 결과인 inner 함수를 참조하게 된다.

inner 함수의 outerEnvironmentReference에는 inner 함수가 선언된 위치의 LexicalEnvironment가 참조 복사된다. inner 함수는 outer 함수 내부에서 선언됐으므로, outer 함수의 LexicalEnvironment가 담긴다. inner함수가 호출되면 스코프 체이닝에 따라 outer에서 선언한 변수 count에 접근해서 1만큼 증가시킨 후 그 값인 2를 반환한다.

여기서 이상한 점이 있는데 **inner 함수의 실행 시점에는 outer 함수는 이미 실행이 종료된 상태인데 outer 함수의 LexicalEnvironment에 어떻게 접근할 수 있는 것일까??** 이는 가비지 컬렉션의 동작 방식 때문이다. 가비지 컬렉터는 어떤 값을 참조하는 변수가 하나라도 있다면 그 값은 수집 대상에 포함시키지 않는다.

함수의 실행 컨텍스트가 종료된 후에도 LexicalEnvironment가 가비지 컬렉터의 수집 대상에서 제외되는 경우는 지역변수를 참조하는 내부함수가 외부로 전달된 경우가 유일하다. 

위 내용들을 바탕으로 클로저를 다시 정의해보면 이렇다. **클로저란 어떤 함수 A에서 선언한 변수 a를 참조하는 내부함수 B를 외부로 전달한 경우 A의 실행 컨텍스트가 종료된 이후에도 변수 a가 사라지지 않는 현상**이다.

여기서 유의할 점은 '외부로 전달'이 곧 return만을 의미하는 것은 아니라는 점이다.

```javascript
// (1) setInterval / setTimeout
(function () {
  let a = 0
  let interValid = null
  const inner = function () {
    if (++a >= 10) {
      clearInterval(interValid)
    }
    console.log(a)
  }
  interValid = setInterval(inner, 1000)
})();

// (2) eventListener
(function () {
  let count = 0
  const button = document.createElement('button')
  button.innerText = 'click'
  button.addEventListener('click', function () {
    console.log(++count, 'times clicked')
  })
  document.body.appendChild(button)
})();
```

(1)은 별도의 외부객체인 window의 메서드(setTimeout 또는 setInterval)에 전달한 콜백 함수 내부에서 지역변수를 참조한다. (2)는 별도의 외부객체인 DOM의 메서드(addEventListner)에 등록한 handler 함수 내부에서 지역변수를 참조한다.  두 상황 모두 **지역변수를 참조하는 내부함수를 외부에 전달했기 때문에 클로저이다.** 





## 2. 클로저와 메모리 관리

클로저는 객체지향과 함수형 모두를 아우르는 매우 중요한 개념이다. 메모리 누수의 위험을 이유로 사용을 조심해야 한다는 주장이 있지만 메모리 누스는 클로저의 본질적인 특성일 뿐이다. 오히려 이런 특성을 잘 활용해야 한다. '메모리 누수'는 개발자의 의도와는 달리 어떤 값의 참조 카운트가 0이 되지 않아 CG(Garbage Collector)의 수거 대상이 되지 않는 경우에 맞는 표현이지만 의도적으로 참조 카운트를 0이 되지 않게 설계한 경우는 '누수'라고 할 수 없다.

클로저를 이용할 때 메모리를 관리하는 방법은 간단하다. 필요성이 사라진 시점에 더는 메모리를 소비하지 않도록 해주면 된다. 참조 카운트를 0으로 만들면 GC의 수거 대상이 될 것이고 이때 소모됐던 메모리가 회수된다. 참조 카운트를 0으로 만드는 방법은 기본형 데이터 (보통 null이나 undefined)를 할당하면 된다.



```javascript
// (1) return에 의한 클로저의 메모리 관리
const outer = (function () {
  let a = 1
  const inner = function () {
    return ++a
  }
  return inner
})()

console.log(outer())
console.log(outer())
outer = null

// (2) setInterval에 의한 클로저의 메모리 해제
(function () {
  let a = 0
  let intervalid = null
  const inner = function () {
    if(++a >= 10) {
      clearInterval(intervalid)
      inner = null
    }
    console.log(a)
  }
  intervalid = setInterval(inner, 1000)
})()

// (3) eventListener에 의한 클로저 메모리 해제
(function () {
  let count = 0
  const button = document.createElement('button')
  button.innerText = 'click'
  
  const clickHandler = function () {
    console.log(++count, 'times clicked')
    if (count >= 10) {
      button.removeEventListener('click', clickHandler)
      clickHandler = null
    }
  }
  button.addEventListener('click', clickHandler)
  document.body.appendChild(button)
})()
```



## 3. 클로저 활용 사례



### 콜백 함수 내부에서 외부 데이터를 사용하고자 할 때

```javascript
const fruits = ['apple', 'banana', 'peach']
const $ul = document.createElement('ul')


fruits.forEach(function(fruit) { // (A)
  const $li = document.createElement('li')
  $li.innerText = fruit
  $li.addEventListener('click', function(fruit) { // (B)
    alert(fruit)
  })
  $ul.appendChild($li)
})

document.body.appendChild($ul)
```



forEach 메서드에 넘겨준 익명의 콜백 함수(A)는 그 내부에서 외부 변수를 사용하지 않고 있으므로 클로저가 없지만 addEventListener에 넘겨준 콜백 함수(B)에는 fruit이라는 외부 변수를 참조하고 있으므로 클로저가 있다. (A)는 fruits의 개수만큼 실행되며, 그때마다 새로운 실행 컨텍스트가 활성화 된다. A의 실행 종료 여부와 무관하게  클릭 이벤트에 의한 각 켄텍스트의 (B)가 실행될 때는 (B)의 outerEnvironmentReference가 (A)의 LexicalEnvironment를 참조하게 된다. 따라서 최소한 (B) 함수가 참조할 예정인 변수 fruit에 대해서는 (A)가 종료된 후에도 CG 대상에서 제외되어 계속 참조가 가능하다.

(B) 함수의 쓰임새가 콜백 함수에 국한되지 않는 경우에는 반복을 줄이기 위해 (B)를 외부로 분리하는 편이 나을 수 있다. 그렇게 바꾸어 보도록 하겠다.

```javascript
const alertFruit = function (fruit) {
  alert(fruit)
}

fruits.forEach(function(fruit) { // (A)
  const $li = document.createElement('li')
  $li.innerText = fruit
  $li.addEventListener('click', alertFruit)
  $ul.appendChild($li)
})

document.body.appendChild($ul)
alertFruit(fruits[1])
```

위 코드를 실행하고  li를 클릭하면 [object MouseEvent]라는 값이 출력된다.  **이는 콜백 함수의 인자에 대한 제어권을 addEventListener가 가진 상태이며, addEventListener는 콜백함수를 호출할 때 첫번쩨 인자에 '이벤트 객체'를 주입하기 때문이다**. 이 문제는 bind 메서드를 활용하면 쉽게 해결할 수 있다.

```javascript
...
fruits.forEach(function(fruit) { // (A)
  const $li = document.createElement('li')
  $li.innerText = fruit
  $li.addEventListener('click', alertFruit.bind(null, fruit))
  $ul.appendChild($li)
})
...
```

다만 위 코드는 이벤트 객체가 인자로 넘어오는 순서가 바뀌는 점 및 함수 내부에서의 this가 원래의 그것과 달라지는 점을 감안해야 한다. 고차함수 방식으로 위 단점을 보완할 수 있습니다.

```javascript
...
const alertFruitBuilder = function (fruit) {
  return function () {
    alert(fruit)
  }
}

fruits.forEach(function(fruit) {
  const $li = document.createElement('li')
  $li.innerText = fruit
  $li.addEventListener('click', alertFruitBuilder(fruit))
  $ul.appendChild($li)
})
...
```

alertFruitBuilder 내부에서 다시 익명함수를 반환하는데 이 익명함수가 바로 기존의 alertFruit 함수 이다. 반환된 함수를 리스너에 콜백 함수로써 전달한다. alertFruitBuilder의 인자로 넘어온 fruit를 outerEnvironmentReference에 의해 참조할 수 있다. 즉, alertFruitBuilder의 실행 결과롤 반환된 함수에는 클로저가 존재한다.



### 접근 권한 제어(정보 은닉)

정보 은닉은 어떤 모듈의 내부 로직에 대해 외부로의 노출을 최소화해서 모듈간의 결합도를 낮추고 유연성을 높이고자  하는 현대 프로그래밍 언어의 중요한 개념 중 하나이다. 흔히 접근 권한에는 public, private, protected의 세 종류가 있다.

자바스크립트는 기본적으로 변수 자체에 접근 권한을 직접 부여하도록 설계되어 있지 않다. 하지만 클로저를 이용하면 함수 차원에서 public한 값과 private한 값을 구분하는 것이 가능하다.

```javascript
const outer = function () {
  let a = 1
  const inner = function () {
    return ++a
  }
  return inner
}

const outer2 = outer()
console.log(outer2())
console.log(outer2())
```

위 코드처럼 클로저를 활용하면 외부 스코프에서 함수 내부의 변수들 중 선택적으로 일부의 변수에 대한 접근 권한을 부여할 수 있다. 바로 return을 활용해서!

'**closure**' 라는 영어 단어는 사전적으로 '닫혀있음, **폐쇄성**, 완결성' 정도의 의미를 가진다. outer 함수는 **외부로부터 철저하게 격리된 닫힌 공간**이다. 외부에서는 outer라는 변수를 통해 outer 함수를 실행할 수는 있지만, outer 함수 내부에는 어떠한 개입도 할 수 없다. **외부에서는 오직 outer 함수가 return한 정보에만 접근할 수 있다.**

외부에 제공하고자 하는 정보들을 모아서 return하고 내부에서만 사용할 정보들은 return 하지 않는 것으로 접근 권한 제어가 가능한 것이다. return한 변수들은 공개 멤버가 되고 그렇지 않은 변수들은 비공개 멤버가 되는 것이다.

클로저를 활용해 접근 권한을 제어하는 방법을 정리하자면 다음과 같다.

1. 함수에서 지역변수 및 내부함수 등을 생성한다.
2. 외부에 접근권한을 주고자 하는 대상들로 구성된 참조형 데이터(대상이 여럿일 때는 객체 또는 배열, 하나일 때는 함수)를 return 합니다. -> return한 변수들은 공개 멤버가 되고, 그렇지 않는 변수들은 비공개 멤버가 됩니다.



### 부분 적용 함수

부분 적용 함수(partially applied function)란 n개의 인자를 받는 함수에 미리 m개의 인자만 넘겨 기억시켰다가, 나중에 (n-m)개의 인자를 넘기면 비로소 원래 함수의 실행 결과를 얻을 수 있게끔 하는 함수이다. this를 바인딩해야 하는 점을 제외하면 앞서 살펴본 bind 메서드의 실행 결과가 바로 부분 적용 함수이다.

```javascript
const add = function () {
  let result = 0
  for (let i = 0; i < arguments.length; i++) {
    result += arguments[i]
  }
  return result
}
const addPartial = add.bind(null, 1,2,3,4,5)
console.log(addPartial(6,7,8,9))
```

위 코드는 this의 값을 변경할 수밖에 없기 때문에 메서드에서는 사용할 수 없을 것 같다. this에 관여하지 않는 별도의 부분 적용 함수가 있다면  범용성 측면에서 더욱 좋을 수 있다.



실무에서 부분 함수를 사용하기에 적합한 예로 **디바운스**가 있다. 디바운스는 짧은 시간 동안 동일한 이벤트가 많이 발생할 경우 이를 전부 처리하지 않고 처음 또는 마지막 발생한 이벤트에 대해 한 번만 처리하는 것으로 프런트엔드 성능 최적화에 큰 도움을 주는 기능 중 하나이다. scroll, wheel, mousemove, resize 등에 적용하기 좋다.

```javascript
const debounce = function (eventName, func, wait) {
  let timeoutId = null
  return function (event) {
    const self = this
    console.log(eventName, '이벤트 발생')
    console.log('timeoutId', timeoutId)
    clearTimeout(timeoutId)
    timeoutId = setTimeout(func.bind(self, event), wait)
  }
}

const moveHandler = function (e) {
  console.log('move event 처리')
}

const wheelHandler = function (e) {
  console.log('wheel event 처리')
}

document.body.addEventListener('mousemove', debounce('move', moveHandler, 500))
document.body.addEventListener('mousewheel', debounce('wheel', wheelHandler, 500))
```

  wait 시간이 경과하기 이전에 다시 동일한 이벤트가 발생하면 clearTimeout으로 앞서 저장했던 대기열을 초기화하고 setTimeout으로 다시 새로운 대기열을 등록한다. 결국 wait 시간 경과 전 이벤트는 실행되지 못하고 초기화되며 wait 시간 이내에 발생하는 한 마지막에 발생한 이벤트만이 초괴화되지 않고 무사히 실행될 것이다.





### 커링 함수

커링 함수(currying function)란 **여러 개의 인자를 받는 함수를 하나의 인자만 받는 함수로 나눠서 순차적으로 호출될 수 있게 체인 형태로 구성한 것**을 말한다. 커링은 한번에 하나의 인자만 전달하는 것을 원칙으로 한다. 마지막 인자가 전달되기 전까지는 원본 함수가 실행되지 않는다. 필요한 원자 개수만큼 함수를 만들어 계속 리턴해주다가 마지막에만 잘 조합해서 리턴해주면 된다.

```javascript
const curry3 = function (func) {
  return function (a) {
    return function (b) {
      return func(a, b)
    }
  }
}

const getMaxWith10 = curry3(Math.max)(10)
console.log(getMaxWith10(8))
console.log(getMaxWith10(25))
```

인자가 많아질수록 가독성이 떨어진다는 단점이 있다. 단점을 보완하기 위하여 화살표 함수를 사용할 수 있다.

```javascript
const curry5 = func => a => b => c => d => e => func(a,b,c,d,e)
```

당장 필요한 정보만 받아서 전달하고 또 필요한 정보가 들어오면 전달하는 식으로 하면 결국 마지막 인자가 넘어갈 때까지 함수 실행을 미루는 셈이 되는데 이를 **지연실행**이라고 칭한다. **원하는 시점까지 지연시켰다가 실행하는 것이 주요한 상황이라면 커링을 쓰기에 적합하다.**

```javascript
const getInformation = function (baseUrl) {
  return function (path) {
    return function (id) {
      return fetch(baseUrl + path + '/' + id)
    }
  }
}

// ES6
const getInformation = baseUrl => path => id => fetch(baseUrl + path + '/' + )
```



## 문제

1.  여러 개의 인자를 받는 함수를 하나의 인자만 받는 함수로 나눠서 순차적으로 호출될 수 있게 체인 형태로 구성한 함수는 무엇인가?

2. 다음 중 클로저를 이용하지 않은 코드는 몇번인가?

   ```javascript
   // (1)
   (function () {
     let a = 0
     let interValid = null
     const inner = function () {
       if (++a >= 10) {
         clearInterval(interValid)
       }
       console.log(a)
     }
     interValid = setInterval(inner, 1000)
   })();
   
   // (2)
   (function () {
     let count = 0
     const button = document.createElement('button')
     button.innerText = '클릭!!!'
     button.addEventListener('click', function () {
       console.log(++count, 'clicked')
     })
     document.body.appendChild(button)
   })();
   
   // (3)
   const outer = function () {
     let count = 1
     const inner = function () {
       return ++count
     }
     return inner()
   }
   const outer2 = outer()
   console.log(outer2)
   ```

3. ```javascript
   // 다음 코드의 실행 결과는?
   const debounce = (eventName, func, wait) => {
     let timeoutId = null
     return (event) => {
       console.log(eventName, '이벤트 발생')
       clearTimeout(timeoutId)
       timeoutId = setTimeout(event => func(event), wait)
     }
   }
   
   const moveHandler = (e) => console.log(`${e} move event 처리`)
   
   document.body.addEventListener('mousemove', debounce('move', moveHandler, 500))
   ```







### 해답

1. 커링함수

2.  (3)

3. move 이벤트 발생
   undefined move event 처리

   ```javascript
   // 제대로 동작하는 코드
   const debounce = (eventName, func, wait) => {
     let timeoutId = null
     return (event) => {
       console.log(eventName, '이벤트 발생')
       clearTimeout(timeoutId)
       timeoutId = setTimeout(() => func(event), wait)
     }
   }
   
   const moveHandler = (e) => console.log(`${e} move event 처리`)
   
   document.body.addEventListener('mousemove', debounce('move', moveHandler, 500))
   ```

   