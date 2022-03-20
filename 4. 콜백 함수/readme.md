## 콜백 함수(callback function)란?

- 다른 코드의 인자로 넘겨주는 함수
- 콜백 함수를 넘겨받은 코드 : **콜백 함수**를 필요에 따라 적절한 시점에 실행
  - 적절한 시점 = 특정 조건
  - 콜백 함수를 넘겨받은 코드는 특정 조건일 때, **콜백 함수**를 실행
  - 해당 조건이 갖추어졌는지 여부를 **콜백 함수**가 아닌 콜백 함수를 넘겨받은 코드가 판단하여 **콜백 함수**를 호출
  - **콜백 함수**의 제어권이 콜백 함수를 넘겨받은 코드에게 넘어감

⇒ **콜백 함수**는 다른 코드(함수 또는 메서드)에게 인자로 넘겨줌으로써 그 제어권도 함께 위임한 함수

## 제어권

### 호출 시점

예제 4-2) setInterval

```javascript
var count = 0;

// 콘솔창에 0.3초에 한 번씩 숫자가 0부터 1씩 증가하면서 출력, 4가 출력된 이후 종료
var cbFunc = function () {
  console.log(count);
  if (++count > 4) clearInterval(timer);
};

var timer = serInterval(cbFunction, 300);
```

`setInterval` : 콜백 함수를 넘겨받은 코드

`cbFunc` : 콜백 함수

- `setInterval`에 첫 번째 인자로 **`cbFunc`** 함수를 넘겨줌
- 제어권을 넘겨받은 `setInterval`이 스스로의 판단에 따라 적절한 시점(0.3초마다) 에 **`cbFunc`** 함수를 실행

표 4-1) 코드 실행 방식과 제어권

| code                      | 호출 주체   | 제어권      |
| ------------------------- | ----------- | ----------- |
| cbFunc();                 | 사용자      | 사용자      |
| setInterval(cbFunc, 300); | setInterval | setInterval |

⇒ 콜백 함수의 제어권을 넘겨받은 코드는 콜백 함수의 호출 시점에 대한 제어권을 가짐

### 인자

Array의 prototype에 담긴 map 메서드의 구조

```javascript
Array.prototype.map(callback[, thisArg])
callback: function(currentValue, index, array)
```

- `map` 메서드
  - 첫 번째 인자: callback 함수를 받고
  - 두 번째 인자(생략 가능): 콜백함수 내부에서 this로 인식할 대상을 특정
  - `thisArg` 를 생략할 경우, 일반적인 함수과 같이 전역객체가 바인딩 된다.

⇒ `map` 메서드는 메서드의 대상이 되는 배열의 모든 요소들을 처음부터 끝까지 하나씩 꺼내어 콜백 함수를 반복 호출하고, 콜백 함수의 실행 결과들을 모아 새로운 배열을 생성

- 콜백 함수
  - 첫 번째 인자: 배열의 요소 중 현재의 값
  - 두 번째 인자: 현재값의 인덱스
  - 세 번째 인자: `map` 메서드의 대상이 되는 배열 자체

예제 4-3) Array.prototype.map

```javascript
var newArr = [10, 20, 30].map(function (currentValue, index) {
  console.log(currentValue, index);
  return currentValue + 5;
});

console.log(newArr);

// 실행 결과
// 10 0
// 20 1
// 30 2
// [15, 25, 35]
```

예제 4-4) Array.prototype.map - 인자의 순서를 임의로 바꾸어 사용

```javascript
var newArr = [10, 20, 30].map(function (index, currentValue) {
  console.log(index, currentValue);
  return currentValue + 5;
});

console.log(newArr);

// 실행 결과
// 10 0
// 20 1
// 30 2
// [5, 6, 7]
```

- index, currentValue : 사용자가 임의로 명명
- 컴퓨터는 첫 번째, 두 번째의 순서에 의해서만 각각을 구분하고 인식
- currentValue라고 명명한 인자의 위치가 두 번째라서 컴퓨터가 여기에 인덱스 값을 부여했기 때문에 예제 4-3과 다른 결과가 나옴
- `map` 메서드를 호출해서 원하는 배열을 얻으려면 `map` 메서드에 정의된 규칙에 따라 함수를 작성해야 한다.
- `map` 메서드에 정의된 규칙에는 콜백 함수의 인자로 넘어올 값들 및 그 순서도 포함되어 있다.
- 콜백 함수를 호출하는 주체 : `map` 메서드
  - `map` 메서드가 촐백 함수를 호출할 때 인자에 어떤 값들을 어떤 순서로 넘길 것인지게 전적으로 `map` 메서드에게 달림

⇒ 콜백 함수의 제어권을 넘겨받은 코드는 콜백 함수를 호출할 때 인자에 어떤 값들을 어떤 순서로 넘길 것인지에 대한 제어권을 가진다.

### this

예제 4-6) 콜백 함수 내부에서의 this

```javascript
// (1) setTimeout
setTimeout(function () {
  console.log(this);
}, 300); // Window{ ... }

// (2) forEach
[1, 2, 3, 4, 5].forEach(function (x) {
  console.log(this); // Window{ ... }
});

// (3) addEventListener
document.body.innerHTML += '<button id="a">Click</button>';
document.body.querySelector('#a').addEventListener('Click', function (e) {
  console.log(this, e); // <button id="a">Click</button> MouseEvent { isTrusted: true, ... }
});
```

- `setTimeout` 함수는 0.3초 만큼 시간 지연을 한 뒤 콜백 함수를 실행, 0.3초 뒤 전역객체가 출력

  ⇒ `setTimeout`은 내부에서 콜백 함수를 호출할 때, call 메서드의 첫 번재 인자에 전역객체를 넘기기 때문에 콜백 함수 내부에서 this가 전역객체를 가리킨다.

- `forEach` 메서드는 배열의 각 요소를 앞에서부터 차례로 하나씩 꺼내어 그 값을 콜백 함수의 첫 번째 인자로 삼아 함수를 실행, 전역객체와 배열의 각 요소가 총 5회 출력

  ⇒ 여기에서 `forEach`는 4-2-5절의 '별도의 인자로 this를 받는 경우'에 해당하지만 별도의 인자로 this를 넘겨주지 않았기 때문에 전역객체를 가리킨다.

- `addEventListener`는 지정한 HTML 엘리먼트에 'click' 이벤트가 발생할 때마다 그 이벤트 정보를 콜백 함수의 첫 번째 인자로 삼아 함수를 실행, 버튼을 클릭하면 앞서 지정한 엘리먼트와 클릭 이벤트에 관한 정보가 담긴 객체 출력

  ⇒ `addEventListener`는 내부에서 콜백 함수를 호출할 때 call 메서드의 첫 번재 인자에 `addEventListener` 메서드의 this를 그대로 넘기도록 정의돼 있기 때문에 콜백 함수 내부에서의 this가 `addEventListener`를 호출한 주체인 HTML 엘리먼트를 가리키게 된다.

- `setTimeout` 함수와 `forEach` 메서드는 그 내부에서 콜백 함수를 호출할 때 대상이 될 this를 지정하지 않는다. 따라서 콜백 함수 내부에서의 this는 전역객체를 참조

- `addEventListener` 메서드는 콜백 함수를 호출할 때 자신의 this를 상속하도록 정의

  ⇒ 메서드명의 점(.) 앞부분이 this가 된다.

## 콜백 함수는 함수다

- 콜백 함수로 어떤 객체의 메서드를 전달하더라도 그 메서드는 메서드가 아닌 함수로서 호출된다.

예제 4-7) 메서드를 콜백 함수로 전달한 경우

```javascript
var obj = {
  vals: [1, 2, 3],
  // obj 객체의 메서드로 정의된 logValues
  logValues: function (v, i) {
    console.log(this, v, i);
  },
};

// this는 obj를 가리키고, 인자로 넘어온 1, 2가 출력
obj.logValues(1, 2); // {vals: Array(3), logValues: ƒ} 1 2

// logValues 메서드를 forEach 함수의 콜백 함수로서 전달
// obj를 this로 하는 메서드를 그대로 전달한 것이 아니라,
// obj.logValues가 가리키는 함수만 전달, obj와 직접적인 연관이 없어짐
// 별도로 this를 지정하는 인자를 지정하지 않았으므로 함수 내부에서의 this는 전역객체를 바라보게 된다.
[4, 5, 6].forEach(obj.logValues);
// Window { ... } 4 0
// Window { ... } 5 1
// Window { ... } 6 2
```

⇒ 어떤 함수의 인자에 객체의 메서드를 전달하더라도 이는 결국 메서드가 아닌 함수이다.

## 콜백 함수 내부의 this에 다른 값 바인딩하기

- 객체의 메서드를 콜백 함수로 전달하면 해당 객체를 this로 바라볼 수 없게 된다.
  그럼에도 콜백 함수 내부에서 this가 객체를 바라보게 하고 싶다면?

- this를 다른 변수에 담아, 콜백 함수로 활용할 함수에서 this 대신 그 변수를 사용
  ⇒ 전통적으로 많이 사용하는 방식
  ⇒ 이를 클로저로 만드는 방식으로 사용

예제 4-8) 콜백 함수 내부의 this에 다른 값을 바인딩하는 방법 - 전통적인 방식

```javascript
var obj1 = {
  name: 'obj1',
  func: function () {
    // func 내부에서 self 변수에 this 담고, 익명 함수를 선언과 동시에 반환
    var self = this;
    return function () {
      console.log(self.name);
    };
  },
};

var callback = obj1.func();
setTimeout(callback, 1000);
```

- 콜백 함수 내부에서 this를 사용하지 않고 간결하고 직관적으로 코드를 작성할 수 있으나, 해당 함수를 재활용 할 수 없게 된다.

예제 4-10) 예제 4-8의 func 함수 재활용

```javascript
...
var obj2 = {
  name: 'obj2',
  func: obj1.func
};

// obj1의 func를 복사한 obj2의 func를 실행한 결과를 담아 콜백으로 사용
var callback2 = obj2.func();
setTimeout(callback2, 1500);

var obj3 = { name: 'obj3' };
// obj1의 func를 실행하면서 this를 obj3가 되도록 지정해서 콜백으로 사용
var callback3 = obj1.func.call(obj3);
setTimeout(callback3, 2000);
```

예제 4-11) 콜백 함수 내부의 this에 다른 값을 바인딩하는 방법 - bind 메서드 활용

```javascript
var obj1 = {
  name: 'obj1',
  func: function () {
    console.log(this.name);
  },
};

setTimeout(obj1.func.bind(obj1), 1000);

var obj2 = { name: obj1 };
setTimeout(obj1.func.bind(obj2), 1500);
```

## 콜백 지옥과 비동기 제어

### 콜백 지옥(callback hell)

- 콜백 함수를 익명 함수로 전달하는 과정이 반복되어 코드의 들여쓰기 수준이 감당하기 힘들 정도로 깊어지는 현상
- 주로 이벤트 처리나 서버 통신과 같이 비동기적인 작업을 수행하기 위해 이런 형태가 자주 등장
  ⇒ 가독성 떨어지고 코드 수정이 어려워짐
- 동기(synchronous)적인 코드 : 현재 실행 주인 코드가 완료된 후에야 다음 코드를 실행하는 방식
  - CPU의 계산에 의해 즉시 처리가 가능한 대부분의 코드
- 비동기(asynchronous)적인 코드 : 현재 실행 중인 코드의 완료 여부와 무관하게 즉시 다음 코드로 넘어감
  - 별도의 요청, 실행 대기, 보류 등과 관련된 코드
  - 사용자의 요청에 의해 특정 시간이 경과되지 전까지 어떤 함수의 실행을 보류(`setTimeout`)
  - 사용자의 직접적인 개입이 있을 때 비로소 어떤 함수를 실행하도록 대기(`addEventListener`)
  - 웹브라우저 자체가 아닌 별도의 대상에 무언가를 요청하고 그에 대한 응답이 왔을 때 비로소 어떤 함수를 실행하도록 대기(`XMLHttpRequest`) 등

예시 4-12) 콜백 지옥 예시

```javascript
setTimeout(
  function (name) {
    var coffeeList = name;
    console.log(coffeeList);

    setTimeout(
      function (name) {
        coffeeList += ', ' + name;
        console.log(coffeeList);

        setTimeout(
          function (name) {
            coffeeList += ', ' + name;
            console.log(coffeeList);

            setTimeout(
              function (name) {
                coffeeList += ', ' + name;
                console.log(coffeeList);
              },
              500,
              '카페모카'
            );
          },
          500,
          '카페라떼'
        );
      },
      500,
      '아메리카노'
    );
  },
  500,
  '에스프레소'
);
```

- 각 콜백은 커피 이름을 전달하고 목록에 이름 추가
- 목적 달성에는 지장이 없으나 들여쓰기의 수준이 깊어졌고, 값이 전달되는 순서가 '아래에서 위로' 향하고 있다.
- 콜백 지옥을 해결하기 위한 방법
  - 기명함수로 변환
  - Promise
  - Generator
  - Promise + Async/await

### 콜백 지옥을 해결하기 위한 방법 - 기명함수로 변환

예시 4-13) 기명함수로 변환

```javascript
var coffeeList = '';

var addEspresso = function (name) {
  coffeeList = name;
  console.log(coffeeList);
  setTimeout(addAmericano, 500, '아메리카노');
};

var addAmericano = function (name) {
  coffeeList += ', ' + name;
  console.log(coffeeList);
  setTimeout(addLatte, 500, '카페라떼');
};

var addLatte = function (name) {
  coffeeList += ', ' + name;
  console.log(coffeeList);
  setTimeout(addMocha, 500, '카페모카');
};

var addMocha = function (name) {
  coffeeList += ', ' + name;
  console.log(coffeeList);
};

setTimeout(addEspresso, 500, '에스프레소');
```

- 가독성이 높아졌다.
- 함수 선언과 함수 호출만 구분할 수 있다면 위에서 아래로 순서대로 읽는데 어려움이 없다.
- 변수를 최상단으로 끌어올려 외부에 노출되게 됐지만 전체를 즉시 실행 함수 등으로 감싸면 해결 가능

⇒ 일회성 함수를 전부 변수에 할당하는 것, 코드명을 일일이 따라다녀야 하므로 오히려 헷갈릴 수 있다.

### 콜백 지옥을 해결하기 위한 방법 - Promise

```javascript
new Promise(function (resolve) {
  setTimeout(function () {
    var name = '에스프레소';
    console.log(name);
    resolve(name);
  }, 500);
})
  .then(function (prevName) {
    return new Promise(function (resolve) {
      setTimeout(function () {
        var name = prevName + ', 아메리카노';
        console.log(name);
        resolve(name);
      }, 500);
    });
  })
  .then(function (prevName) {
    return new Promise(function (resolve) {
      setTimeout(function () {
        var name = prevName + ', 카페라떼';
        console.log(name);
        resolve(name);
      }, 500);
    });
  })
  .then(function (prevName) {
    return new Promise(function (resolve) {
      setTimeout(function () {
        var name = prevName + ', 카페모카';
        console.log(name);
        resolve(name);
      }, 500);
    });
  });

// 위의 코드에서 반복적인 내용을 함수화해서 짧게 표현
var addCoffee = function (name) {
  return function (prevName) {
    return new Promise(function (resolve) {
      setTimeout(function () {
        var newName = prevName ? prevName + ', ' + name : name;
        console.log(newName);
        resolve(newName);
      }, 500);
    });
  };
};

addCoffee('에스프레소')()
  .then(addCoffee('아메리카노'))
  .then(addCoffee('카페라떼'))
  .then(addCoffee('카페모카'));
```

- new 연산자로 Promise 호출
- Promise의 인자로 넘겨주는 콜백 함수는 호출할 때 바로 실행되지만
- 내부에 resolve/reject 함수를 호출하는 구분이 있을 경우 둘 중 하나가 실행되기 전까지는 다음(then)/오류 구문(catch)으로 넘어가지 않는다.

⇒ 비동기 작업이 완료될 때 resolve/reject를 호출하는 방법으로 비동기 작업의 동기적 표현이 가능

### 콜백 지옥을 해결하기 위한 방법 - Generator

```javascript
var addcoffee = function (prevName, name) {
  setTimeout(function () {
    coffeeMaker.next(prevName ? prevName + ', ' + name : name);
  }, 500);
};

var coffeeGenerator = function* () {
  var espresso = yield addCoffee('', '에스프레소');
  console.log(espresso);
  var americano = yield addCoffee(espresso, '아메리카노');
  console.log(americano);
  var latte = yield addCoffee(americano, '카페라떼');
  console.log(latte);
  var mocha = yield addCoffee(mocha, '카페모카');
  console.log(mocha);
};

var coffeeMaker = coffeeGenerater();
coffeeMaker.next();
```

- `*`가 붙은 함수 : `Generator` 함수
- `Generator` 함수 실행하면, `Iterator` 를 반환
- `Iterator` : `next` 라는 메서드를 가지고 있다.
  - `next` 메서드 호출
  - `Generator` 함수 내부에서 가장 먼저 등장 하는 `yield`에서 함수의 실행을 멈춤
  - 다시 `next` 메서드 호출
  - 앞서 멈췄던 부분부터 시작해서 그 다음에 등장 하는 `yield`에서 함수의 실행을 멈춤

⇒ 비동기 작업이 완료되는 시점마다 `next` 메서드를 호출하면 `Generator` 함수 내부의 소스가 위에서부터 아래로 순차적으로 진행됨

### 콜백 지옥을 해결하기 위한 방법 - Promise + Async/await

```javascript
var addCoffee = function (name) {
  return new Promise(function (resolve) {
    setTimeout(function () {
      resolve(name);
    }, 500)
  });
};

var coffeeMaker = async function () {
  var coffeeList = '';
  var _addCoffee = async function (name) {
    coffeeList += (coffeeList ? ',' : '') + await addCoffee(name);
  };
  await _addCoffee('에스프레소');
  console.log(CoffeeList);
  await _addCoffee('아메리카노');
  console.log(CoffeeList);
  await _addCoffee('카페라떼');
  console.log(CoffeeList);
  await _addCoffee('카페모카');
  console.log(CoffeeList);
```

- 비동기 작업을 수행하고자 하는 함수 앞에 `async`를 표기
- 함수 내부에서 실질적인 비동기 작업이 필요한 위치마다 `await`를 표기하는 것만으로 뒤의 내용일 `Promise`로 자동 전환
- 해당 내용이 `resolve`된 이후에 다음 코드를 진행 하므로 `Primise`의 `then`과 비슷한 효과를 얻을 수 있다.
