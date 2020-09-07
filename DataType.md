# 콜버스랩 스터디 1일차 - 데이터 타입

### 01 데이터 타입의 종류

- 기본형(primitive type)

  - number
  - string
  - boolean
  - null
  - undefined
  - symbol

- 참조형(reference type)

  - object
  - array
  - function
  - Date
  - RegExp
  - Map, WeakMap
  - Set, WeakSet

  

  기본형은 값이 담긴 주솟값을 바로 복제하는 반면, 참조형은 값이 담긴 주솟값들로 이루어진 묶음을 가리키는 주솟값을 복제한다.

  기본형은 불변성을 띈다.

  

### 02 데이터 타입에 관한 배경지식

#### 메모리와 데이터

모든 데이터는 바이트 단위의 식별자, 메모리 주솟값을 통해 서로 구분하고 연결할 수 있다.

#### 식별자와 변수

식별자 - 어떤 데이터를 식별하는 데 사용하는 이름, 즉 변수명

변수 - 변할 수 있는 데이터



### 03 변수 선언과 데이터 할당



#### 변수 선언

```javascript
var a;
```

"변할 수 있는 데이터를 만든다. 이 데이터의 식별자는  a로 한다."

**변수란 결국 변경 가능한 데이터가 담길 수 있는 공간 또는 그릇**



#### 데이터 할당



메모리에서 비어있는 공간을 확보하고 그 공간의 이름을 설정. **이름을 설정한 공간 즉, 변수 영역**에 직접 데이터를 저장하지는 않는다. **데이터를 저장하기 위한 별도의 메모리 공간, 즉, 데이터 영역**을 다시 확보해서 저장한다.

변수 영역에 직접 값을 대입하지 않고 굳이 한단계를 더 거쳐서 데이터 영역에 대입하는 이유는 무엇일까?

- 데이터 변환를 자유롭게 할 수 있다.
  - 필요한 메모리 용량은 가변적 일 수 있다. 데이터 변환 시 확보된 공간을 변환된 크기에 맞게 늘리는 작업이 필요한데 중간에 데이터가 있을 시 컴퓨터가 처리해야할 연산이 많아진다.
- 메모리를 효율적으로 관리할 수 있다.
  - 이미 메모리에 저장된 특정 식별자의 다른 값을 할당 시 앞서 할당된 데이터가 저장된 공간에 변화된 값을 할당하는 것이 아니라 **새로운** 데이터 영역에 데이터를 저장한다.
  - 예) 500개의 변수를 생성해셔 모든 변수에 숫자 5를 할당하는 상황
    - 각 변수 공간마다 숫자 5를 할당한다고 가정하면 숫자형은 8바이트가 필요하므로 4000(500* 8) 바이트를 써야한다.
    - 5를 별도의 공간에 한 번만 저장하고 해당 주소만 입력했을 시 1008(500 * 2) + 8 바이트만 이용하면 된다.
    - 결론: 중복된 데이터에 대한 처리 효율이 높아진다.



### 4. 기본형 데이터와 참조형 데이터

#### 불변값

기본형 데이터는 모두 불변값이다.

변수와 상수 구분 짓는 변경 가능성의 대상 => 변수 영역

불변성 여부를 구분할 때의 변경 가능성의 대상 => 데이터 영역

```javascript
var a = 'abc';
a = a + 'def';
// 새로운 문자열 'abcdef'를 만들어 그 주소를 변수 a에 저장한다.


var b = 5;
var c = 5;
// 데이터영역에 있는 숫자 5의 주소를 재활용 한다.
b = 7;
// 기존에 저장된 5를 7로 바꾸는 것이 아니라 7이 있으면 재활용하고 없으면 새로 만든다.
```



#### 가변값

객체의 프로퍼티를 변경하면 주소가 변경되지 않는다. 즉, 새로운 객체가 만들어진 것이 아니라 기존 객체 내부의 값만 바뀌는 것이다.

```javascript
// 참조형 데이터의 프로퍼티 재할당
 
var obj1 = {
  a: 1,
  b: 'bbb'
};
obj1.a = 2;
```

|  주소  |   1001    |           1002            | 1003 | 1004  | 1005 | ...  |
| :----: | :-------: | :-----------------------: | :--: | :---: | :--: | :--: |
| 데이터 |           | 이름: obj1<br />값: @5001 |      |       |      |      |
|  주소  |   5001    |           5002            | 5003 | 5004  | 5005 | ...  |
| 데이터 | @7103 ~ ? |                           |  1   | 'bbb' |  2   |      |

| 주소   | 7103                   | 7104                   | 7105 | 7106 | 7107 | ...  |
| ------ | ---------------------- | ---------------------- | ---- | ---- | ---- | ---- |
| 데이터 | 이름: a<br />값: @5005 | 이름: b<br />값: @5004 |      |      |      |      |

참조형 데이터의 프로퍼티에 다시 참조형 데이터를 할당하는 경우 => 중첩 객체



#### 변수 복사 비교

변수를 복사하는 과정은 모두 같은 주소를 바라보게 되는 점에서 동일하다. 복사 과정은 동일하지만 데이터 할당 과정에서 이미 차이가 있기 때문에 변수 복사 이후의 동작에도 큰 차이가 발생한다.

기본형은 값을 복사하고 참조형은 주솟값을 복사한다고 설명하지만, 사실은 어떤 데이터 타입이든 변수에 할당하기 위해서는 주솟값을 복사해야 한다. 기본형은 주솟값을 복사하는 과정이 한 번만 이뤄지고 참조형은 한 단계를 더 거치게 된다는 차이가 있다.

```javascript
// 변수 복사 이후 값 변경 결과 비교 - 객체의 프로퍼티 변경 시
// 기본형 타입은 변수가 참조하는 주솟값이 달라지지만 참조형 타입은 변수가 참조하는 주솟값이 달라지지 않는다.
var a = 10;
var b = a;
var obj1 = { c: 10, d: 'ddd' };
var obj2 = obj1;

b = 15;
obj2.c = 20;
```



```javascript
// 변수 복사 이후 값 변경 결과 비교 - 객체 자체를 변경 했을 때
// 두 타입 모두 변수가 참조하는 주솟값이 달라진다.
var a = 10;
var b = a;
var obj1 = { c: 10, d: 'ddd' };
var obj2 = obj1;

b = 15;
obj2 = { c: 20, d: 'ddd' };
```

**참조형 데이터가 '가변값' 일 때는 참조형 데이터 자체를 변경할 경우가 아니라 그 내부의 프로퍼티를 변경할 때만 성립한다.  즉, 참조형 데이터 자체를 변경할 경우 기존 데이터는 변하지 않는다.**



### 5 불변 객체

#### 불변 객체를 만드는 간단한 방법

값으로 전달받은 객체에 변경을 가하더라도 원본 객체는 변하지 않아야 하는 경우 불변 객체가 필요하다.

```javascript
// 객체의 가변성에 따른 문제점

var user = {
    name: 'lee',
    age: 30
}

var changeName = function (user, newName) {
    var newUser = user;
    newUser.name = newName;
    return newUser;
}

var user2 = changeName(user, 'kim');

console.log(user.name, user2.name); // ?
console.log(user === user2); // ?
```

변경 전과 변경 후에 서로 다른 객체를 바라보게 만들어야 한다.



for in 문법을 통해 객체의 프로퍼티를 복사하는 함수 생성

```javascript
var copyObject = function (target) {
    var result = {};
    for (var prop in target) {
        result[prop] = target[prop];
    }
    return result;
}

// 아쉬운 점
// 1. 프로토타입 체이닝 상의 모든 프로퍼티를 복사한다.
// 2. getter / setter는 복사하지 않는다.
// 3. 얕은 복사만을 수행한다.
```



#### 얕은 복사와 깊은 복사

- 얕은 복사
  - 바로 아래 단계의 값만 복사하는 방법
  - 중첩된 객체에서 참조형 데이터가 저장된 프로퍼티를 복사할 때 그 주솟값만 복사한다. 결국 원본과 사본이 모두 동일한 참조형 데이터의 주소를 가리키게 된다.



- 깊은 복사
  - 내부의 모든 값들을 전부 복사하는 방법

객체의 프로퍼티 중 그 값이 기본형인 경우 그대로 복사하면 되지만, 참조형 데이터는 다시 그 내부의 프로퍼티들을 복사해야 한다.



getter / setter 를 복사하는 방법은 ES6의 Object.getOwnPropertyDescriptor와 ES2017의 Object.getOwnPropertyDescriptors를 이용하는 방법이 있다.



**JSON을 활용한 간단한 깊은 복사**

객체를 JSON 문법으로 표현된 문자열로 전환했다가 다시 JSON 객체로 바꾸는 것.

다만, 메서드(함수), `__proto__`, getter / setter 등과 같이 JSON으로 변경할 수 없는 프로퍼티들은 모두 무시한다.

```javascript
var copyObjectViaJson = function (target) {
    return JSON.pare(JSON.stringify(target));
};

var obj = {
    a: 1,
    b: {
        c: null,
        d: [1,2],
        func1: function () { console.log(3); },
        func2: function () { console.log(4); }
    }
};

var obj2 = copyObjectViaJson(obj);

obj2.a = 3;
obj2.b.c = 4;
obj2.b.d[1] = 3;

console.log(obj); // ?
console.log(obj2); // ?
```



### 6. undefined와 null

자바스크립트에서 '없음'을 나타내는 값 두 가지

- undefined
  - 값이 존재하지 않을 때 자바스크립트 엔진이 자동으로 부여하는 경우도 있다.
  - 자바스크립트 엔진은 사용자가 응당 어떤 값을 지정할 것이라고 예상되는 상황임에도 실제로는 그렇게 하지 않았을 때, undefined를 반환한다.
    - 값을 대입하지 않은 변수, 즉 데이터 영역의 메모리 주소를 지정하지 않는 식별자에 접근할 때
    - 객체 내부의 존재하는 않는 프로퍼티에 접근하려고 할 때
    - return 문이 없거나 호출되지 않는 함수의 실행 결과
  - 값으로써 어딘가에 할당된 undefined는 실존하는 데이터인 반면, 자바스크립트 엔진이 반환해주는 undefined는 문자 그대로 값이 없음을 나타낸다.
- null
  - 비어있음을 명시적으로 나타내고 싶을 때 사용
  - typeof null이 object라는 점을 주의해야 한다.



### Symbol 타입

- 유니크한 값
- 객체의 유니크한 키로 많이 사용한다.

```javascript
// id는 새로운 심볼이 된다.
let id = Symbol();

// 심볼을 만들 때 심볼 이름이라 불리는 설명을 붙일 수도 있다.
let id = Symbol("id");
```

- 심볼형은 다른 자료형으로 암묵적 형변환이 되지 않는다.

- 심볼은 서드파티 코드가 모르게 식별자를 부여할 수 있다.

  - ```javascript
    let user = { // 서드파티 코드에서 가져온 객체
      name: "John"
    };
    
    let id = Symbol("id");
    
    user[id] = 1;
    
    alert( user[id] ); // 심볼을 키로 사용해 데이터에 접근할 수 있다.
    ```

- 객체 리터럴을 사용해 객체를 만든 경우, 대괄호를 사용해 심볼형 키를 만들어야 한다.

  - ```javascript
    let id = Symbol("id");
    
    let user = {
      name: "John",
      [id]: 123 // "id": 123은 안됨
    };
    ```

- 키가 심볼인 프로퍼티는 `for..in` 반복문에서 제외된다.

- Object.keys()도 키가 심볼인 프로퍼티는 배제한다. Object.assgign, 스프레드 연산자은 키가 심볼인 프로퍼티도 배제하지 않고 객제 내 모든 프로퍼티를 복사한다.

### Map

- Object와 유사

- Object에 비해 갖는 장점

  - **의도치 않은 키가 존재하지 않는다.** Object는 프로토타입을 가지고 있으므로 기본 키가 존재할 수 있어 직접 제공한 키와 충돌할 수 있다.

  - Object의 키는 반드시 String, Symbol 이어야 하는 것에 비하여 **어떤 값이든 키 값으로 설정**할 수 있다. ex) function, object를 key로 설정 가능

  - **Map의 키는 정렬**되며 따라서 **삽입순으로 순회**가 이루어진다.

  - Map의 항목 개수는 size 프로퍼티를 통해 쉽게 알아낼 수 있다.

    - 기존에는 Object를 배열로 변환하여서 개수를 알아낼 수 있었다.

    ```javascript
    const person = {
        name: 'lee',
        age: 30
    };
    
    Object.keys(person).length;
    Object.entries(person).length;
    ```

  - Map은 순회가 가능(iterable)하므로, 바로 순회할 수 있다.

    - Object를 순회하기 위해서는 모든 키를 알아낸 후, 그 키의 배열을 순회해야 한다.

      ```javascript
      const person = {
          name: 'lee',
          age: 30
      };
      
      // 1
      for ( const key in person ) {
          const value = person[key];
          console.log(value);
      }
      
      // 2
      const keys = Object.keys(person);
      
      for (let i = 0; i < keys.length; i++) {
          const key = keys[i];
          const value = person[key];
          console.log(value);
      }
      ```

- 잦은 키-값 쌍의 추가와 제거에서 더 좋은 성능을 보인다.

- WeakMap

  - 키가 반드시 참조형이어야 한다.
  - 위크맵의 키로 사용된 객체를 참조하는 것이 아무것도 없다면 가비지 컬렉션의 대상이 된다.
  - 반복작업, keys(), values(), entries() 메서드를 지원하지 않기 때문에 키나 값 전체를 얻는 것이 불가능하다.



### Set

- 배열과 유사

- iterator를 반환한다.

- 배열에 비해 갖는 장점

  - 중복제거 기능

  ```javascript
  let set = new Set();
  
  let john = { name: "John" };
  let pete = { name: "Pete" };
  let mary = { name: "Mary" };
  
  // 어떤 고객(john, mary)은 여러 번 방문할 수 있다.
  set.add(john);
  set.add(pete);
  set.add(mary);
  set.add(john);
  set.add(mary);
  
  // 셋에는 유일무이한 값만 저장된다.
  alert( set.size ); // 3
  
  for (let user of set) {
    alert(user.name); // // John, Pete, Mary 순으로 출력된다.
  }
  ```

  

  - update, clear, delete 메서드 제공

- WeakSet

  - 원소 데이터 타입이 반드시 참조타입이어야 한다.
  - 더이상 아무 곳에서도 참조하지 않는 원소는 가비지 컬랙션 대상이되고 WeakSet 원소에서도 제거된다. 메모리 관점에서 유리하다.



### 참고자료

[모던 자바스크립트 튜토리얼 Symbol](https://ko.javascript.info/symbol)

[모던 자바스크립트 튜토리얼 Map, Set](https://ko.javascript.info/map-set)

[모던 자바스크립트 튜토리얼 WeakMap, WeakSet](https://ko.javascript.info/weakmap-weakset)

[MDN map](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Map)



### 질문

1. 참조형 데이터가 가변값일 경우는 언제인가?

2. Map이 Object에 비해 갖는 장점은?

3. ``` javascript
   typeof null // ?
   ```

4. null 체킹은 어떻게 해야할까?

5. NaN 체킹은 어떻게 해야하나?

   ```javascript
   Number.isNaN 과 isNaN의 차이
   ```

6. ```javascript
   Number와 parseInt의 차이
   ```

7. Object.assign, Object.freeze, Spread Operator, immutalbe.js, immer.js

8. 리액트에서 뷰에서 불변성을 유지해야 하는 이유는? => 가상돔이 변경을 알아챌 수 없다. 

   Object.is 메서드 사용 : 주소값으로 비교해서 변경하기 때문에 객체를 새롭게 재할당 해야한다.

9. ```javascript
   const target = { a: 1, b: 2 };
   const source = { b: 4, c: 5 };
   const returnedTarget = Object.assign(target, source);
   console.log(target);
   // expected output: Object { a: 1, b: 4, c: 5 }
   console.log(returnedTarget);
   // expected output: Object { a: 1, b: 4, c: 5 }
   console.log(target === returnedTarget)
   ```

   











### 답변

**1. 참조형 데이터가 '가변값' 일 때는 참조형 데이터 자체를 변경할 경우가 아니라 그 내부의 프로퍼티를 변경할 때만 성립한다.  즉, 참조형 데이터 자체를 변경할 경우 기존 데이터는 변하지 않는다.**

2. - Object의 키는 반드시 String, Symbol 이어야 하는 것에 비하여 **어떤 값이든 키 값으로 설정**할 수 있다
   - **의도치 않은 키가 존재하지 않는다.** Object는 프로토타입을 가지고 있으므로 기본 키가 존재할 수 있어 직접 제공한 키와 충돌할 수 있다.
   - **Map의 키는 정렬**되며 따라서 **삽입순으로 순회**가 이루어진다.
   - Map의 항목 개수는 size 프로퍼티를 통해 쉽게 알아낼 수 있다.
   - 잦은 키-값 쌍의 추가와 제거에서 더 좋은 성능을 보인다.
3. object

