# 코어자바스크립트 클래스

자바스크립트 = 프로토타입 기반 언어 ⇒ 상속X 프로토타입 체이닝O

ES6에서 클래스 문법 추가
<br>

## 1. 클래스와 인스턴스의 개념 이해

<br>

클래스 : 계급, 집단, 집합

![image](https://user-images.githubusercontent.com/93499158/159274389-dbb2d661-a2f1-4a73-a2d1-af3072f564e4.png)

<br>

클래스 : 음식, 과일, 귤류

하위 클래스는 상위 클래스의 개념을 포함하면서 더 구체적인 개념이 추가된다. 아무리 구체화 되더라도 추상적인 개념에 불과함.

인스턴스 : 감귤, 자몽, 천혜향, 오렌지, 레몬

인스턴스는 클래스의 속성을 지니는 실존하는 개체를 의미한다.
<br>

## 2. 자바스크립트의 클래스

<br>

프로토타입을 일반적인 의미의 클래스 관점에서 접근하면 비슷하게 해석할 수 있다.

`var arr = new Array()`

Array : 생성자함수 ( 클래스 )

arr : 인스턴스

Array.prototype ⇒ arr.**proto** : 프로토타입 체이닝 ( 상속 )

![image](https://user-images.githubusercontent.com/93499158/159274502-4f03d501-f511-4daa-886d-814ebfb19f60.png)

Array 내부 프로퍼티들 중 prototype을 제외한 나머지 프로퍼티는 상속 X

상속 되는 prototype : 인스턴스 멤버

상속 안되는 나머지 : 스태틱 멤버
![image](https://user-images.githubusercontent.com/93499158/159274644-2204ca84-bc59-4898-9c09-592a32ad7294.png)

```jsx
let Rectangle = function (width, height) {
  this.width = width;
  this.height = height;
}; // 생성자

Rectangle.prototype.getArea = function () {
  return this.width * this.height;
}; // (프로토타입)메소드

Rectangle.isRectangle = function (instance) {
  return (
    instance instanceof Rectangle && instance.width > 0 && instance.height > 0
  );
}; // 스태틱 메소드

let rect1 = new Rectangle(3, 4);
console.log(rect1.getArea()); // 12
console.log(rect1.isRectangle(rect1)); // Error
console.log(Rectangle.isRectangle(rect1)); // true
```

isRectangle ⇒ rect1 있나? X ⇒ rect1.**proto** 있나 ? X ⇒ rect1.**proto**.**proto** (Object) 있나? X

즉 undefined 를 실행해라 = 함수가 아닙니다 Type Error

![image](https://user-images.githubusercontent.com/93499158/159274790-f39c44d4-770c-4d2e-8c95-50223b70aa02.png)
<br>

## 3. 클래스 상속

<br>

### 3.1 ( ES6 이전 ) 기본 구현 및 문제점

<br>

**예시 1**

```jsx
var Grade = function () {
  var args = Array.prototype.slice.call(arguments); // [100, 80]
  for (var i = 0; i < args.length; i++) {
    this[i] = args[i];
  }
  this.length = args.length;
};
Grade.prototype = []; // Array 클래스의 인스턴스 [] => Grade의 프로토타입 프로퍼티
var g = new Grade(100, 80);
g.push(90);
console.log(g); // Grade { 0: 100, 1: 80, 2: 90, length: 3 }  Good
delete g.length;
g.push(70);
console.log(g); // Grade { 0: 70, 1: 80, 2: 90, length: 1 }   Bad
```

length 프로퍼티 삭제 후 push 한 값이 index 0에 들어가고 length 는 1로 변경됨.

배열 인스턴스의 length 프로퍼티는 configurable 속성이 false ⇒ `삭제 불가능`

Grade 클래스의 인스턴스는 배열 메서드를 상속하지만 기본적으로는 일반 객체 ⇒ `삭제 가능`

index 0의 값이 바뀌고 length 가 1로 찍힌 이유 ??

g.length 있나? X ⇒ g.**proto**.length 있나? O ( [ ] , length : 0 ) ⇒ index 0에 70을 넣고 length + 1

<br>

**예시 2**

<br>

```jsx
let Rectangle = function (width, height) {
  this.width = width;
  this.height = height;
};
Rectangle.prototype.getArea = function () {
  return this.width * this.height;
};
let rect = new Rectangle(3, 4);
console.log(rect.getArea()); // 12

let Square = function (width) {
  this.width = width;
  this.height = width;
};
Square.prototype.getArea = function () {
  return this.width * this.height;
};
let sq = new Square(5);
console.log(sq.getArea()); // 25
```

정사각형은 직사각형에 '네 변의 길이가 모두 같다'라는 구체적인 조건이 하나 추가된 개념이다. 위 처럼 고치고 나니 이제 소스상으로도 Suqare를 Rectangle의 하위 클래스로 삼을 수 있다. getArea라는 메소드는 동일한 동작을 하므로 상위클래스에만 정의하고, 하위 클래스에는 해당 메소드를 상속하면서 height 대신 width를 넣어주면 된다!

```jsx
let Rectangle = function (width, height) {
  this.width = width;
  this.height = height;
};
Rectangle.prototype.getArea = function () {
  return this.width * this.height;
};
let rect = new Rectangle(3, 4);
console.log(rect.getArea()); // 12

let Square = function (width) {
  Rectangle.call(this, width, width);
};
Square.prototype = new Rectangle();
let sq = new Square(5);
console.log(sq.getArea()); // 25
```

Square 생성자 함수 내부에서 Rectangle 생성자 함수를 함수로써 호출.

Square 프로토타입 객체에 Rectangle 인스턴스를 부여하면서 Rectangle 메소드를 상속받음.

```jsx
Square.prototype.width = 10;
delete sq.width;
sq.width: undefined,
sq.height: 5,

console.log(sq.getArea())  // 50
```

<br>

문제 1. **클래스에 있는 값이 인스턴스의 동작에 영향을 준다.**

<br>

```jsx
var rect2 = new sq.constructor(2, 3);
console.log(rect2); // Reactangle { width: 2, height: 3 }
```

sq.constructor ≠ Square

sq.constructor = Rectangle

<br>

문제 2. **subClass 인스턴스의 constructor 가 SuperClass를 가리킨다.**

<br>

### 3.2 문제1 해결방법) 클래스가 구체적인 데이터를 지니지 않게 하는 방법

<br>

**방법 1. 프로퍼티들을 일일이 지우고 더는 새로운 프로퍼티를 추가할 수 없게 하는 것**

<br>

```jsx
delete Square.prototype.width;
delete Square.prototype.height;
Object.freeze(Square.prototype);
```

- freeze?

  - **`Object.freeze()`** 메서드는 객체를 **동결**합니다. 동결된 객체는 더 이상 변경될 수 없습니다. 즉, 동결된 객체는 새로운 속성을 추가하거나 존재하는 속성을 제거하는 것을 방지하며 존재하는 속성의 불변성, 설정 가능성(configurability), 작성 가능성이 변경되는 것을 방지하고, 존재하는 속성의 값이 변경되는 것도 방지합니다. 또한, 동결 객체는 그 프로토타입이 변경되는것도 방지합니다. `freeze()`는 전달된 동일한 객체를 반환합니다.

![image](https://user-images.githubusercontent.com/93499158/159275166-29ae234e-98ab-469d-9094-5c19066c89a7.png)

프로퍼티가 많으면 ?

```jsx
let extendClass1 = function (SuperClass, SubClass, subMethods) {
  Subclass.prototype = new SuperClass();
  for (let prop in SubClass.prototype) {
    if (SubClass.prototype.hasOwnProperty(prop)) {
      delete SubClass.prototype[prop];
    }
  }
  if (subMethods) {
    for (let method in subMethods) {
      SubClass.prototype[method] = subMethods[method];
    }
  }
  Object.freeze(SubClass.prototype);
  return SubClass;
};

let Square = extendClass1(Rectangle, function (width) {
  Rectangle.call(this, width, width);
});
```

<br>

**방법 2. 빈 Bridge 생성자를 다리 역할로 사용하는 방법**

<br>

```jsx
let Rectangle = function (width, height) {
  this.width = width;
  this.height = height;
};
Rectangle.prototype.getArea = function () {
  return this.width * this.height;
};
let Square = function (width) {
  Rectangle.call(this, width, width);
};
let Bridge = function () {};
Bridge.prototype = Rectangle.prototype;
Square.prototype = new Bridge();
Object.freeze(Square.prototype);
```

SubClass의 prototype에 직접 SuperClass의 인스턴스를 할당하는 대신 아무런 프로퍼티를 생성하지 않는 빈 생성자 함수(Bridge)를 하나 더 만들어서 그 prototype이 SuperClass의 prototype을 바라보게끔 한 다음, SubClass의 prototype에는 Bridge의 인스턴스를 할당하게 하는 것.

인스턴스를 제외한 프로토타입 체인 경로상에는 더는 구체적인 데이터가 남아있지 않게 된다.
![image](https://user-images.githubusercontent.com/93499158/159275219-c6d1b342-b057-4271-90eb-ae40cc76384b.png)

범용적인 코드

```jsx
let extendClass2 = (function () {
  let Bridge = function () {};
  return function (SuperClass, SubClass, subMethods) {
    Bridge.prototype = SuperClass.prototype;
    SubClass.prototype = new Bridge();
    if (subMethods) {
      for (let method in subMethods) {
        SubClass.prototype[method] = subMethods[method];
      }
    }
    Object.freeze(SubClass.prototype);
    return SubClass;
  };
})();
```

즉시 실행함수 내부에서 Bridge를 선언해서 이를 클로저를 활용함으로써 메모리에 불필요한 함수 선언을 줄였다. subMethods에는 subClass의 prototype에 담길 메소드들을 객체로 전달하게끔 했다.

<br>

**방법 3. Object.create 활용**

<br>

```jsx
Square.prototype = Object.create(Rectangle.prototype);
Object.freeze(Square.prototype);
```

지정된 프로토타입 객체 및 속성(property)을 갖는 새 객체를 만드는 메서드.

SubClass의 prototype의 `__proto__`가 SuperClass의 prototype을 바라보되, SuperClass의 인스턴스가 되지 않으므로 앞서 소개한 두 방법보다 간단하면서 안전함.

클래스 상속, 추상화를 흉내내기 위한 다양한 라이브러리가 있지만 기본적인 접근 방법은 위 세가지 아이디어를 크게 벗어나지 않음.

<br>

### 3.3 문제2 해결방법) constructor 복구하기

<br>

`SubClass.prototype.constructor = SubClass`

<br>

### 3.4 하위 클래스에서 상위 클래스로 접근하기 ( 건뛰 가능 )

<br>

하위 클래스 메서드에서 상위 클래스의 메서드 실행한 결과를 사용하고 싶은 경우.

`SuperClass.prototype.method.apply(this, arguments)` ⇒ 매번 사용하기 귀찮음.

```jsx
var extendClass = function (SuperClass, SubClass, subMethods) {
  SubClass.prototype = Object.create(SuperClass.prototype);
  SubClass.prototype.constructor = SubClass;
  SubClass.prototype.super = function (propName) {
    var self = this;
    if (!propName)
      return function () {
        SuperClass.apply(self, arguments);
      };
    var prop = SuperClass.prototype[propName];
    if (typeof prop !== "funcion") return prop;
    return function () {
      return prop.apply(self, arguments);
    };
  };
  if (subMethods) {
    for (let method in subMethods) {
      SubClass.prototype[method] = subMethods[method];
    }
  }
  Object.freeze(SubClass.prototype);
  return SubClass;
};

var Rectangle = function (width, height) {
  this.width = width;
  this.height = height;
};
Rectangle.prototype.getArea = function () {
  return this.width * this.height;
};
var Square = extendClass(
  Rectangle,
  function (width) {
    this.super()(width, width);
  },
  {
    getArea: function () {
      console.log("size is :", this.super("getArea")());
    },
  }
);
var sq = new Square(10);
sq.getArea(); // size is : 100
console.log(sq.super("getArea")()); // 100
```

<br>

## 4. ES6의 클래스 및 상속

<br>

### 클래스

<br>

```jsx
let ES5 = function (name) {
  this.name = name;
};
ES5.staticMethod = function () {
  return this.name + " staticMethod";
};
ES5.prototype.method = function () {
  return this.name + " method";
};
let es5Instance = new ES5("es5");
console.log(ES5.staticMethod()); // es5 staticMethod
console.log(es5Instance.method()); // es5 method

let ES6 = class {
  constructor(name) {
    this.name = name;
  }
  static staticMethod() {
    return this.name + " staticMethod";
  }
  method() {
    return this.name + " method";
  }
};
let es6Instance = new ES6("es6");
console.log(ES6.staticMethod()); // es6 staticMethod
console.log(es6Instance.method()); // es6 method
```

클래스 내부에서는 function 키워드를 생략하더라도 모두 메서드로 인식함.

static : 해당 메서드가 static 메서드임.

method() : prototype 객체 내부에 할당되는 메서드

<br>

### 클래스 상속

<br>

```jsx
let Rectangle = class {
  constructor(width, height) {
    this.width = width;
    this.height = height;
  }
  getArea() {
    return this.width * this.height;
  }
};
let Square = class extends Rectangle {
  constructor(width) {
    super(width, width);
  }
  getArea() {
    cosnole.log("size is :", super.getArea());
  }
};
```

extends A : A 클래스를 상속받는다.

constructor 안의 super : SuperClass의 constructor를 실행함.

constructor 밖의 super : 객체는 SuperClass.prototype을 바라보는 객체처럼 사용됨. 호출한 메서드의 this는 super가 아닌, 원래의 this.
