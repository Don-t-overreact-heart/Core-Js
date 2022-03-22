## **프로토타입? 그거 어려운거 아니야?**

코어자바스크립트 스터디를 시작하며, 항상 어려워했던 "프로토타입"을 주제로 발표를 하게 되었다..  
하여 블로그에 프로토타입에 대해 내가 이해한바를 공유하고자 한다. (미래의 내가 이 글을 본다면.. 대체 왜 이렇게 이해했지? 라는 생각을 들수도 있을 것 같단 생각이 들지만 최선을 다해보고자 한다)

## **프로토타입이란?**

먼저 코어자바스크립트의 저자는 아래의 그림을 이해하면 프로토타입의 모든것을 이해할 수 있다고 말한다.

![image](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FpLcPP%2FbtrvNH92XcR%2FuKxFKF8sV58TH6N5Llwok0%2Fimg.png)

```
var instance = new Constructor(); 
```

위의 코드를 형상화 한 것이 바로 위의 도식이다. 이에 대해 저자는 다음과 같이 설명하고 있다.

- 생성자 함수를 new 연산자와 함께 호출하면..
- Constructor를 바탕으로 새로운 instance 가 생성된다
- instance 에는 **proto** 라는 프로퍼티가 자동으로 주어지며
- 프로퍼티는 constructor의 prototype이라는 프로퍼티를 참조한다

결론적으로 프로토타입 `prototype` 그리고 (던더)프로토 `__proto__` 또는 `[[prototype]]`는 **프로토타입 개념의 핵심**이다.  
프로토타입 안에는 다양한 메서드가 저장되어 있으며 **proto** 를 통해 메서드들에 접근할 수 있다.

글로는 (내가)이해가 잘 안되니 예시를 살펴보려고 한다.

```
    var Jiyun = function (name) {
      this._name = name; 
    }; 

    Jiyun.prototype.getName = function () {
      return this._name; 
    }; 
```

위와 같이 `Jiyun`이라는 생성자에 `getName`이라는 메서드를 만들어 주었다. 해당 메서드는 자신의 `_name`을 반환하도록 한다.  
아래와 같이 메서드가 잘 들어가 있음을 확인할 수 있다.

![image](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FcbXISd%2FbtrvNTh7HU4%2FFawwYFSH6kf56HawSsx0c0%2Fimg.png)

위의 코드에서 Jiyun의 인스턴스는 이제 `__proto__`프로퍼티를 통해 getName을 호출할 수 있다!

```
    var june = new Jiyun('june'); 
    june.__proto__.getName(); // undefined 

    june.__proto__._name = 'JIYUN__proto__'; 
    june.__proto__.getName(); //'JIYUN__proto__' 출력
```

위의 실행문에서 `undefined`가 발생한 것은 변수가 호출할 수 있는 함수에 해당한다는 것을 의미한다.  
`june`이라는 새로운 인스턴스는 `Jiyun` 생성자를 통해 `getName`을 사용할 수 있다.

## **proto는 사실 생략이 가능하다**

```
    var june = new Jiyun('June', 20); 
    june.getName(); //'June'
```

`__proto__` 를 만들며 브랜든 아이크는 이를 생략하고, 메서드가 호출되도록.. 원하는 값이 나오도록 만들었다. 의외로 정상인 부분으로 저자는 '그냥 그런가보다' 라고.. 생각하라고 이야기하고 있다. 꼭 기억하자 굳이 써주지 않아도 됨을..!

## **🔍프로토타입 자세히 보기**

아래는 코어자바스크립트의 예제이다.

```
    var Constructor = function (name) {
      this.name = name; 
    }; 
    Constructor.prototype.method1 = function() {}; 
    Constructor.prototype.property1 = 'Constructor Prototype Property'; 

    var instance = new Constructor('Instance'); 
    console.dir(Constructor); 
    console.dir(instance); 
```

위의 코드를 실행하고 생성자와 인스턴스의 내부를 `console.dir`로 살펴보면 다음과 같다.

![image](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fk0oa8%2FbtrvL4ShqSM%2F95MiZZ1umsZ2AqPUQmNHQk%2Fimg.png)

`Constructor` 디렉터리 구조안엔 함수 이름인 "Constructor" 그리고 인자인 "name"을 확인할 수 있다.  
이외 `arguments`, `caller`, `length`, `name`, `prototype`, `__proto__` 등의 **프로퍼티**를 확인할 수 있다.

`instance`의 출력 내용을 살펴보면, "Contructor"의 인스턴스임을 표기하고 있으며, 대부분의 프로퍼티가 동일한 내용으로 구성되어 있음을 확인할 수 있다.

### **생성자 함수인 Array는 어떨까?**

```
    var arr = ['jiyun', 'park']; 
    console.dir(arr); 
    console.dir(Array);
```

![image](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FupNMn%2FbtrvJ0bH8Xd%2FJcxl6sHjl4bMMbYuXKCRl1%2Fimg.png)

변수인 `arr`는 배열에 사용하는 메서드들의 거의 모두 들어있으며, `Array`의 구조 첫 문장에 f가 표시되어 생성자 함수임을 명시하고 있다.  
동일하게 기본적인 프로퍼티 들이 옅은 색으로 표시되어 있다.

## **constructor 프로퍼티**

`constructor` 프로퍼티는 프로토타입 객체 내부에 존쟇나다. 물론 인스턴스의 `__proto__`의 내부 안에도 존재한다.  
해당 프로퍼티는 생성자 함수, 즉 자기 자신을 참조하는데 이를 통해 **인스턴스와의 관계에 있어 필요한 정보를 확인할 수 있다.**

```
    var arr = ['apple', 'banana']; 
    arr.constructor = Array; //true 

    var arr2 = new arr.constructor('avocado', 'pineapple'); 
    console.log(arr2); //['avocado', 'pineapple'] 
```

위와 같이 `__proto__`를 생략하고 직접 `constructor`에 접근하여 값을 변경할 수 있다. (읽기 전용 속성이 부여된 예외적인 경우에만)

```
    var Rabbit = function (name) {
      this.name = name; 
    }; 

    var r1 = new Rabbit ('peter'); 
    var r1Proto = Object.getPrototypeOf(r1); 
    var r2 = new Rabbit.prototype.constructor('peter2'); 
    var r3 = new r1Proto.constructor('peter3'); 
    var r4 = new r1.__proto__.constructor('peter4'); 
    var r5 = new r1.constructor('peter5'); 

[r1, r2, r3, r4, r5].forEach(function (r) {
  console.log(r, r instanceof Rabbit); 
});                     
```

위의 코드의 출력값은 아래와 같다.

![image](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FwyNE3%2FbtrvJyNkSOp%2F94RjOQB9oudqovMVsv0u91%2Fimg.png)

위와 같이 r1~r5 는 모두 동일한 대상을 가리키고, 모두 동일한 객체 즉 `prototype`에 접근할 수 있다.

## **⛓ 프로토타입 체인**

### **❓"메서드 오버라이드" if 인스턴스가 동일한 이름의 프로퍼티나 메서드를 가지고 있는 상황이라면???**

```
    var Lion = function (name) {
      this.name = name; 
    }; 

    Lion.prototype.getName = function () {
      return this.name; 
    }; 

    var cub = new Lion('어흥'); 
    cub.getName = function () {
      return '어흥흥' + this.name; 
    }; 
    console.log(cub.getName()); // 어흥흥어흥
```

위의 코드에서 `Lion` 생성자함수의 메서드인 `getName`을 만들어 주었다. 이후 `cub` 인스턴스를 만들어 `getName` 메서드를 덮어 씌웠다. 따라서 출력 값이 '어흥'이 아닌 '어흥흥어흥'이 되었다. 이는 원본이 제거 된 것이 아닌 그 위에 얹어진 것, **메서드 오버라이딩** 이라고 할 수 있다. **그렇다면, 원본이 아래에 유지되고 있는 것이라면 어떻게 접근할 수 있을까?**

```
    console.log(cub.__proto__.getName()); //undefined 

    Lion.prototype.name = '뀨'; 
    console.log(cub.__proto__.getName()); // 뀨
     console.log(cub.__proto__.getName.call(cub)); // 어흥
```

**(내가)헷갈리니까 한줄 한줄 살펴보자.**  
첫째로 `cub`은 `Lion`의 인스턴스로, `getName`을 호출하였을 때 안에 `name`이 비어있어 `undefined`를 반환한다.

![image](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fb5rSBt%2FbtrvKJ9hg65%2FobjxJcxzFYZkOGT3DCuiX0%2Fimg.png)

`this`가 `prototype`객체 (cub.**proto**)를 가리키는데 `prototype`에는 `name` 프로퍼티가 없기 때문이다.  
`prototype`에 `name`이 있다면 이를 위와 같이 출력할 것이다.  
이후 `this`가 `prototype`을 바라보고 있는 것을 인스턴스 즉 `cub`을 바라보도록 바꿔준다. 이때 `call` 또는 `apply`를 사용한다.

자신으로 부터 가장 가까운 메서드에만 접근할 수 있지만, 그 다음으로 가까운 `__proto__`의 메서드 즉 우회적인 방법으로 접근할 수 있다. 불가능 하지 않다!

### **프로토타입 체인**

아래는 코어자바스크립트 예제이다.

```
    var arr = [1,2]; 
    Array.prototype.toString.call(arr); // 1,2 
    Object.prototype.toString.call(arr); // [object Array] 
    arr.toString(); // 1,2 

    arr.toString = function () {
      return this.join('_'); 
    }; 
    arr.toString(); // 1_2
```

`arr`변수는 배열이므로 `arr.__proto__`는 `Array.prototype`을 참조한다. 또한 `Array.prototype`은 객체라 `Array.prototype.__proto__`는 `Object.prototype`을 참조한다... 이를 그림으로 그려보면 다음과 같다.

![image](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fp60s1%2FbtrvPe0OPaT%2FIsLBDlKXXXvDxODDb3vJjk%2Fimg.png)

모든 프로토타입엔 toString메서드가 존재한다. 어떤 값이 출력되는지 이후 각각 실행해 본 결과,  
`Array.prototype.toString.call(arr)`와 `arr.toString();`의 값이 같은 걸 확인할 수 있다.

방금 알아본 배열 뿐만 아닌 자바스크립트 데이터 (Number, String, Boolean 등) 모두 동일한 형태의 프로토타입 체인 구조를 지닌다.

## **객체 전용 메서드 예외사항**

```
    Object.prototype.getEntries = function() {
      var res = []; 
      for (var prop in this) {
        if (this.hasOwnProperty(prop)) {
          res.push([prop, this[prop]]); 
        }
      }
      return res; 
    };

    var data = [
      ['object', { a:1, b:2, c:3}], 
      ['number', 345], 
      ['string', 'abc'], 
      ['boolean', false], 
      ['func', function () {}], 
      ['array', [1,2,3]]
      ]; 

    data.forEach(function (datum) {
      console.log(datum[1].getEntries()); 
    }); 
```

위의 코드의 경우 `Object`에 `getEntries`라는 메서드를 만들어 주고, 아래 `data` 객체를 열거하며 `getEntries` 함수를 통해 결과를 반환하도록 만들어 주었다. 이 결과, 큰 오류 없이 결과 값들을 반환하는데, 어느 데이터타입이든 프로토타입 체이닝을 통해 해당 메서드에 접근할 수 있기 때문이다.

## **🧐 다중 프로토타입 체인**

위에서 살펴본 도식의 대각선 `__proto__`를 연결해 나갈 수 있다면 무한대로 체인을 이어나갈 수 있다.  
생성자 함수의 `prototype`이 연결하고자 하는 생성자 함수의 인스턴스를 바라보게끔 해주면 된다. 이는 다음과 같이 할 수 있다.  
코어자바스크립트 예제를 살펴보도록 한다.

```
    var Grade = function () {
      var args = Array.prototype.slice.call(arguments); 
      for (var i=0; i < args.length; i++) {
        this[i] = args[i]; 
      }
      this.length = args.length; 
    }; 
    var g = new Grade(100,80);     

    Grade.prototype = []; 
```

위의 예제에서 변수 g는 Grade의 인스턴스를 바라본다. `Grade`의 인스턴스는 인자들을 받아 인덱싱하여 저장한다.  
위의 예제에서는 인스턴스에서 배열 메서드를 직접 사용할 수 있게끔 만들기 위해 Grade.prototype이 배열의 인스턴스를 바라보게 하도록 만들었다. 이를 도식화 하면 다음과 같다.

아래의 도식이

![image](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fb52Q8G%2FbtrvPKFlaVU%2FUQZeyMEokkbbaAITiEehl1%2Fimg.png)

다음과 같이 변경됨을 확인할 수 있다.

![image](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FyXGuR%2FbtrvJTLloLN%2Fbw8I1qzbOVoXLzevJzydn1%2Fimg.png)

```
console.log(g); // Grade(2) [100, 80] 
g.pop(); 
console.log(g); // Grade(1) [100] 
g.push(90); 
console.log(g); // Grade(2) [100, 90]; 
```

이후 위와 같이 직접 `Grade.prototype`에 접근하여 해당 메서드를 사용할 수 있음을 확인할 수 있다.

## **🤷🏻‍♀️ 그래서?**

정리해보자면.. 생성자 함수를 new 연산자와 함게 호출하면 `Constructor`의 내용을 참고하여 새로운 인스턴스를 만든다.  
해당 인스턴스는 `__proto__`라는 이름의 `Constructor`의 `prototype` 프로퍼티가 주어진다.  
`__proto__`는 생략 가능하므로 `Constructor.prototype`의 메서드를 자기것마냥 호출할 수 있다.  
`Constructor.prototype`의 `constructor`프로퍼티는 다시 생성자 함수를 가리키는데, 인스턴스가 자신의 생성자 함수가 무엇인지 확인할 때 유용하게 사용될 수 있다.

위에서 살펴본 프로토타입 구조에 대한 도식(삼각형)의 최상단에는 `Object.prototype`이 있으며 `__proto__`를 연결하고 찾아가는 과정을 **프로토타입 체이닝**이라고 한다. 해당 체이닝을 통해 메서드들을 자신의 것처럼 호출할 수 있다. 접근 방식은 가까운 순에서 먼 대상으로 진행되며, 원하는 값을 찾으면 중단된다. **프로토타입 체인은 2단계에서 끝나는 것이 아닌 무한대로 이어질 수 있다.**
