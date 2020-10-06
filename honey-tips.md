# 꿀팁



## 컴포넌트를 chunk로 나누어 import 하기

webpack에서 같은 chunk끼리 묶어서 번들한다.

```javascript
components: {
  TopBar: () => import(/* webpackChunkName: "IntroWeddingMain", webpackPreload: true */'@/pages/intro/shuttle/components/topBar.vue'),
  OrderButtons: () => import(/* webpackChunkName: "IntroWeddingMain", webpackPreload: true */'@/pages/intro/shuttle/components/orderButtons.vue'),
  StatisticsBanner: () => import(/* webpackChunkName: "IntroWeddingMain" */'@/pages/intro/shuttle/components/statisticsBanner.vue'),
  benefitBanner: () => import(/* webpackChunkName: "IntroWeddingExplain" */'@/pages/intro/shuttle/components/benefitBanner.vue'),
  ReviewBanner: () => import(/* webpackChunkName: "IntroWeddingExplain" */'@/pages/intro/shuttle/components/reviewBanner.vue'),
  PartnerBanner: () => import(/* webpackChunkName: "IntroWeddingExplain" */'@/pages/intro/shuttle/components/partnerBanner.vue'),
  Question: () => import(/* webpackChunkName: "IntroWeddingExplain" */'@/pages/intro/shuttle/components/question.vue'),
  IntroFooter: () => import(/* webpackChunkName: "IntroWeddingExplain" */'@/pages/intro/shuttle/components/footer.vue')
},
```



## Dynamic imports

https://ko.javascript.info/modules-dynamic-imports

성능상 이점이 있다.

Use case

1. intersectionObserver 등을  활용하여 이미지가 화면에 노출될 때 동적으로 이미지를 불러올 수 있다.
2. 사용자의 동작에 따라 동적으로 자원을 로드 할 수 있다. ex) 특정 버튼 클릭 시 모달 컴포넌트를 동적으로 로드

```javascript
// vue 
components: {
    Menu: () => import('@/assets/img/Icon/Monotons/ic-menu.svg?inline'),
    Bell: () => import('@/assets/img/Icon/Monotons/ic-bell.svg?inline'),
}
```

```javascript
// 이미지 자원 동적 임포트
import youtubeIcon from '@/assets/images/icons/youtube.png'

<img src="${youtubeIcon}"/>
```



## await와 then의 중요한 차이점

async await의 제어권은 어디 있을까?? ==> 개발자한테 있다고들 얘기한다.

await 로 받은건 무조건 바로 사용하지 않아도 된다. 하지만 then으로 받은건 바로 사용해야 한다.

콜백 지옥이나 then지옥이 만들어지는 이유는 콜백이 실행 될 시점을 정확히 모르기 때문이다.



## 클로저를 사용하는 이유

개발자가 사용하고 싶은 함수를 만들어 놓고 나중에 사용하고 싶을 때 사용한다.

변수 은닉화



## 콜백 함수를 이용하여 모달 만들기

```javascript
function callback(path) {
  // dynamic imports
  const c = await import(path)
  return function(b) {
    // 팝업 실행
    c.create(b)
  }
}
const a = callback(path)
a(리뷰)
a(필터)
a(코로나)
```



## 콜백 함수에 인자 전달하기

https://www.jstips.co/en/javascript/passing-arguments-to-callback-functions/

기본적으로 콜백 함수에 인자를 전달 할 수 없다.

```javascript
function callback() {
  console.log('Hi human');
}

document.getElementById('someelem').addEventListener('click', callback);
```

클로저를 활용하여 콜백 함수에 인수를 전달할 수 있다.

```javascript
function callback(a, b) {
  return function() {
    console.log('sum = ', (a+b));
  }
}

var x = 1, y = 2;
document.getElementById('someelem').addEventListener('click', callback(x, y));
```



## 모듈 패턴 이미지 처리

```javascript
const ImageModule = (function () {
  const callbackFuncMap = new Map();
  let instance = null;
  function init() {
    return new IntersectionObserver((entries, observer) => {
      entries.forEach((entry) => {
        if (!entry.isIntersecting) return;
        const target = entry.target;
        const _uid = target.getAttribute('_uid');
        if (callbackFuncMap.has(_uid) && typeof callbackFuncMap.get(_uid) === 'function') {
          const callback = callbackFuncMap.get(_uid);
          callbackFuncMap.delete(_uid);
          callback();
        }
        observer.unobserve(target)
      })
    })
  }
  return {
    getInstance () {
      if (!instance) instance = init();
      return instance;
    },
    addToObserve(target, callback) {
      const imageObserver = this.getInstance();
      imageObserver.observe(target)
      const key = target.getAttribute('_uid');
      callbackFuncMap.set(key, callback);
    }
  }
})()
```



## 하드웨어 가속화

[하드웨어 가속에 대한 이해와 적용](https://d2.naver.com/helloworld/2061385)

[https://github.com/SeonHyungJo/FrontEnd-Note/blob/master/Browser/%ED%95%98%EB%93%9C%EC%9B%A8%EC%96%B4_%EA%B0%80%EC%86%8D%ED%99%94.md](https://github.com/SeonHyungJo/FrontEnd-Note/blob/master/Browser/하드웨어_가속화.md)

```css
// 아래와 같은 방식 등으로 하드웨어 가속화를 사용할 수 있다. 

transform: translateZ(0);
```

