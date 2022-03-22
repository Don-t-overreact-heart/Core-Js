오늘은 클로저에 대해 알아보도록 한다.

세부 내용을 다루기 전에 저자가 이야기 하는 **outerEnvironmentReference, LexicalEnvironment**, 그리고 **environmentRecord**에 대해 먼저 짚고 넘어가고자 한다.

- **lexicalEnvironment :** 실행 환경의 프로퍼티 (다른 프로퍼티로는 variable environment가 있다). 정의에 따르면 "특정 변수와 함수에 대한 식별자의 연결을 정의하는, mapping 을 보유하고 있는 구조이다.
- **outerEnvironmentReference** **:** lexicalEnvironment에 접근할 수 있다는 뜻. lexicalEnvironment에서 변수를 찾을 수 없다면, 외부에서 찾도록  JS엔진이 동작한다.
- **environmentRecord :** lexicalEnvironment 내부에 저장된 함수와 변수가 있는 공간이다.

코어 자바스크립트의 저자는 클로저에 대한 여러 정의를 분석한 결과 **"어떤 함수에서 선언한 변수를 참조하는 내부 함수에서만 발생하는 현상"** 이라고 정의를 정리하고 있다. 해당 정의가 무슨 뜻인지 코드로 직접 알아보도록 하자.

```
var outer = function () {
 var a = 1; 
    var inner = function () {
     return(++a); 
       };
       return inner; 
    };
    
 var outer2 = outer(); 
 console.log(outer2()); //2 
 console.log(outer2()); //3
```

위의 코드에 따르면, inner 함수의 실행 시점에 outer 함수는 실행이 종료된 상태이다. 하지만 outer함수의 lexicalEnvironment에 접근하고 있으며 이는 가비지 컬렉터의 동작 방식 때문이다. 함수 내에 어떠한 값을 참조하는 변수가 하나라도 있다면, 해당 값은 수집 대상에서 제외된다. inner 함수의 값이 리턴되고, 외부의 outer2함수가 실행됨으로서 inner함수가 호출될 가능성이 열려있다.

해당 코드는 클로저로 저자에 의하면 **클로저란 어떤 함수 A에서 선언한 변수 a 를 참조하는 내부함수 B를 외부로 전달할 경우, A의 실행 컨텍스트가 종료된 이후에도 변수 a가 사라지지 않는 현상**을 말한다.

#### **클로저 예시**

```
(function () {
 var count = 0; 
    var button = document.createElement('button'); 
    button.innerText = 'click' 
    button.addEventListener('click', function () {
     console.log(++count, 'times clicked'); 
       }); 
       document.body.appendChild(button); 
    })();
```

코어자바스크립트의 예시에 의하면 함수 내부에서 지역변수인 count를 참조한다. 내부 함수를 외부에 전달했기 때문에 클로저라고 할 수 있다.

#### **클로저 활용 사례**

**1) 콜백 함수 내부에서 외부 데이터를 사용할 때**

```
var fruits = ['apple', 'banana', 'peach']; 
var $ul = document.createElement('ul'); 

fruits.forEach(function (fruit) {
 var $li = document.createElement('li'); 
    $li.innerText = fruit; 
    $li.addEventListener('click', function () {
     alert('your choice is' + fruit); 
    }); 
    $ul.appendChild($li); 
    
}); 
document.body.appendChild($ul);
```

위 코드는 fruit이라는 변수를 순회하면서 li를 만들어주고, li를 클릭하면 리스너에 기억된 콜백함수를 실행한다.

li에 대한 이벤트리스너가 실행될 때 oterEnvironentReference 가 상위의 함수의 lexicalEnvironment를 참조하게 되고, 상위 함수가 종료된 후에도 가비지컬렉터의 대상에서 제외되어 fruit이라는 변수를 참조할 수 있게 된다.

해당 이벤트리스너를 실행하는 익명함수는 외부로 분리되어 활용될 수 있다.

```
... 
var alertFruit = function (fruit) {
 alert('your choice is' + fruit); 
}; 

fruits.forEach(function(fruit) {
 var $li = document.createElement('li'); 
    $li.innerText = fruit; 
    $li.addEventListener('click', alertFruit); 
    $ul.appendChild($li); 
    
}); 
document.body.appendChild($ul); 
alertFruit(fruits[1]);
```

위의 코드에서는 alertFruit의 함수에 fruit을 인자로 받아 반복을 줄여 출력될 수 있는 형태로 변경되었다. (콜백함수를 외부로 꺼내어 alertFruit이라는 변수에 담았다)

**2) 정보 은닉 - 접근 권한 제어하기**

모듈의 내부 로직을 내부로 노출하는 것을 최소화 하기 위해 사용하는 중요한 개념 중 하나이다. 접근 권한에는 public, private, protected, 세 가지 종류가 있다.  public은 외부에서 접근 가능한 것, private은 내부에서만 접근 가능한 것이다.  

```
var outer = funtcion () {
 var a = 1; 
    var inner = function () {
     return ++a; 
    }; 
    return inner; 
 }; 
 var outer2 = outer(); 
 console.log(outer2()); 
 console.log(outer2());
```

outer 함수를 종료하며 inner함수를 리턴함으로서 outer이 지역변수 a의 값을 외부에서 읽을 수 있게 되었다. 이렇듯 return 을 활용하여 선택적으로 일부 변수에 대한 접근 권한을 부여할 수 있다.

해당 코드에서 outer라는 함수는 닫힌 공간이다. 전역에서 outer를 호출하여 함수를 실행할 수 있지만, 내부에는 개입할 수 없다.

하지만 return을 통해 외부에 outer의 정보를 제공하는 수단을 활용할 수 있다.

**외부에 제공되어야 하는 것들을 return**하고, **내부에서만 사용 할 정보를 return 하지 않으면** 되는 것이다.

해당 챕터에서는 **자동차 경주 게임에 대한 규칙과, 플레이어가 접근할 수 없는 값**을 클로저로 만드는 코드를 설명하고 있다.

코드는 다음과 같다.

```
var createCar = function () {
 var fuel = Math.ceil(Math.random() * 10 + 10); 
    var power = Math.ceil(Math.random() * 3 + 2); 
    var moved = 0; 
    return {
     get moved () {
         return moved; 
        }, 
        run: function () {
         var km = Math.ceil(Math.random() * 6); 
            var wasteFuel = km / power; 
            if (fuel < wasteFuel) {
             console.log('이동불가'); 
                return; 
            } 
            fuel -= wasteFuel; 
            moved += km; 
            console.log(km + 'km 이동 (총' + moved + 'km). 남은 연료: ' + fuel); 
        }
     }; 
 }; 
 var car = createCar();
```

위의 코드에서는 게임의 룰에 따라 fuel, power 에 대해 접근할 수 없도록 한다. 하여 car 객체는 아래와 같이 실행된다.

[##_Image|kage@Wl0c9/btrwptcPVY4/A7YyL4ClQm1htdS9GpvrKk/img.png|CDM|1.3|{"originWidth":484,"originHeight":388,"style":"alignCenter","width":346,"height":277,"filename":"Screen Shot 2022-03-19 at 9.02.48 PM.png"}_##]

moved에는 접근할 수 있으나, car 객체의 연료(fuel)에 대한 값을 수정할 수 없다.

#### **부분 적용 함수**

부분 적용 함수는 n개의 인자를 받는 함수에 미리 m개의 인자를 넘겨 기억시키고, 나중에 (n-m)개의 인자를 넘겨 원해 함수의 결과를 얻는 것이다. 책에서는 아래의 예시를 보여주고 있다.

실무에서 활용되는 **디바운스**를 소개하기도 하는데, 동일한 이벤트가 짧은 시간 내에 발생할 경우, 마지막에 발생한 이벤트만 한 번 처리하는 것으로, 프론트엔드 성능 최적화에 도움을 주는 기능 중 하나이다. scroll, wheel, mousemove, resize 에 적용하기 좋다.

```
var debounce = function (eventName, func, wait) {
 var timeoutId = null; 
    return function (event) {
     var self = this; 
        console.log(eventName, 'event 발생'); 
        clearTimeout(timeoutId); 
        timeoutId = setTimeout(func.bind(self, event), wait); 
    }; 
}; 

var moveHandler = function(e) {
 console.log('move event 처리'); 
}; 
var wheelHandler = function (e) {
 console.log('wheel event 처리'); 
}; 

document.body.addEventListener('mousemove', debounce('move', moveHandler, 500)); 
document.body.addEventListener('mousewheel', debounce('wheel', wheelHandler, 700));
```

해당 코드에서 moveHandler와 wheelHandler는 출력 용도이며, 클로저로 처리되는 변수는 eventName, func, wait, timeoutId가 있다.

#### **커링 함수**

여러개의 인자를 받는 함수를, 하나의 인자만 받는 함수로 나눠서 순차적으로 호출될 수 있게 체인 형태로 구성한 것을 말한다.

```
var curry = function (func) {
 return function (a) {
     return function (b) {
         return func(a,b); 
         };
     }; 
}; 

var getMaxWith10 = curry(Math.max)(10); 
console.log(getMaxWith10(8)); //10
console.log(getMaxWith10(25)); //25
```

커링 함수의 경우, 당장 필요한 정보만 받아서 전달하고, 필요한 정보가 들어오면 전달하는 식으로 함수형 프로그래밍의 "지연실행"을 구현할 수 있다. 저자는 함수의 매개변수가 비슷하지만 일부만 바뀌는 경우, 커링 함수가 유용하다고 한다.

```
var getInformation = function (baseUrl) { //서버에 요청할 기본 URL
 return function (path) { // path 값 
     return function (id) { //id 값
          return fetch(baseUrl + path + '/' + id); //실제 서버에 정보 요청
            }; 
        }; 
    }; 
    
//ES6에서는 화살표 함수 사용 가능
var getInformation = baseUrl => path => id => fetch(baseUrl + path + '/' + id);
```

#### **🔒 어질 어질 클로저 정리**

클로저는 선언한 변수를 참조하는 내부 함수를 외부로 전달할 때, 함수의 실행 컨텍스트가 종료된 후에도 변수가 사라지지 않는 현상이다.

내부 함수를 외부로 전달하는 방법엔 return을 사용하거나, 콜백으로 전달 할 수 있다..!

클로저는.. 여기까지..!!
