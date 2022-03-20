# 1. 데이터 타입

# 데이터 타입의 종류

| 기본형 | Number, String, Boolean, null, undefined, Symbol          |
| ------ | --------------------------------------------------------- |
| 참조형 | Array, Function, Date, RegExp, Map(WeakMap), Set(WeakSet) |

## 기본형

![](https://github.com/Don-t-overreact-heart/Core-Js/blob/main/1.%20%EB%8D%B0%EC%9D%B4%ED%84%B0%20%ED%83%80%EC%9E%85/image.png)

기본형은 선언되면 각자의 stack이 생김. 생긴 stack에 할당된 값이 같더라도 서로의 값을 변경했을 때 서로 할당된 값에 영향을 미치지 않음. (**값이 담긴 주솟값을 바로 복제**)

## 참조형

![https://github.com/Don-t-overreact-heart/Core-Js/blob/main/1.%20%EB%8D%B0%EC%9D%B4%ED%84%B0%20%ED%83%80%EC%9E%85/image%20(1).png](<https://github.com/Don-t-overreact-heart/Core-Js/blob/main/1.%20%EB%8D%B0%EC%9D%B4%ED%84%B0%20%ED%83%80%EC%9E%85/image%20(1).png>)

데이터가 들어있는 주솟값을 heap에 저장하는 구조

객체 자체는 heap에 저장되며, 객체를 가리키는 포인터는 stack에 저장됨. 포인터는 객체 변수의 이름에 따라 구분되고, 그 객체를 가리킴 (**값이 담긴 주솟값들로 이루어진 묶음을 가리키는 주솟값을 복제**)

<aside>
✅ **왜 stack이 아니라 heap에 값이 저장될까**

참조형(객체)은 기본형과 다르게 **사이즈를 정해놓지 않음.** 사이즈가 동적으로 할당될 수 있기 때문에 동적 메모리 할당이 가능한 heap에 데이터를 저장해놓고 그 주소를 저장한 stack을 통해 값을 불러오는 구조

</aside>

![https://github.com/Don-t-overreact-heart/Core-Js/blob/main/1.%20%EB%8D%B0%EC%9D%B4%ED%84%B0%20%ED%83%80%EC%9E%85/image%20(2).png](<https://github.com/Don-t-overreact-heart/Core-Js/blob/main/1.%20%EB%8D%B0%EC%9D%B4%ED%84%B0%20%ED%83%80%EC%9E%85/image%20(2).png>)

> 참고 : Primitive vs Reference Data Types in JavaScript

# 데이터 타입에 관한 배경지식

## 메모리와 데이터

비트(bit) : 0 또는 1로만 표현할 수 있는 하나의 메모리 조각. 각 비트는 고유한 식별자를 통해 위치를 확인바이트(byte) : `8bit = 1byte` 1비트마다 0 또는 1 두가지 표현이 가능하므로, 1바이트는 2^8 표현이 가능함.

모든 데이터는 바이트 단위의 식별자 (메모리 주솟값)을 통해 서로 구분하고 연결

# 변수 선언과 데이터 할당

## 변수 선언

변수 : 변할 수 있는 데이터가 담길 수 있는 공간식별자 : 어떤 데이터를 식별하기 위한 변수명

### `let a`; 선언시 메모리 영역

사용자가 a에 접근 시, 컴퓨터는 메모리에서 a라는 이름을 가진 주소를 검색해 해당 공간에 담긴 데이터를 반환

## 데이터 할당

```jsx
1
let a
a = 100

2
let a = 100
```

두 가지 방법 모두 같은 과정을 거침

### 데이터 할당에 대한 메모리 영역의 변화

|        | 변수 영역             | 데이터 영역 |
| ------ | --------------------- | ----------- |
| 주소   | 1300                  | 5300        |
| 데이터 | 이름 : a , 값 : @5300 | 'abc'       |

데이터 변환을 자유롭게 할 수 있게 함과 동시에 메모리를 더욱 효율적으로 관리하기 위함.기존 문자열에 어떤 변환을 가하든 상관 없이 무조건 새로 만들어 별도의 공간에 저장

어떤 데이터에 의해 자신의 주소를 참조하는 변수의 개수 = `참조 카운트`자신을 저장하는 변수가 하나도 없게 되면 (= 참조 카운트가 0이 되면) 가비지 컬렉터가 수거

# 기본형 데이터와 참조형 데이터

## 불변값

변수와 상수를 구분하는 성질 = 변경 가능성 -> `변수 영역의 메모리`한 번 데이터 할당이 이뤄진 변수 공간에 다른 데이터를 재할당할 수 있는지 여부

불변성 여부를 구분할 때의 변경 가능성의 대상 -> `데이터 영역 메모리`

> ✅ **불변성 여부 ?**
> 데이터의 변경은 새로 만드는 동작을 통해서만 이뤄짐. (데이터 영역)한 번 만들어진 값은 가비지 컬렉팅을 당하지 않는 한 영원히 변하지 않음.

## 가변값

### 참조형 데이터의 할당

기본형 데이터와 다르게 `객체의 변수(프로퍼티)영역`이 별도로 존재객체가 별도로 할당한 영역은 변수 영역일 뿐, 데이터 영역은 기존의 메모리 공간을 그대로 활용하고 있음.

### 참조형 데이터의 프로퍼티를 다시 참조형 데이터에 할당하는 경우

## 변수 복사 비교

```
a !== b
obj1 === obj2
```

어떤 데이터 타입이든 변수에 할당하기 위해서는 주솟값을 복사=> **자바스크립트의 모든 데이터 타입은 참조형 데이터**

기본형 데이터는 주솟값을 한 번 만 복사참조형은 주솟값을 두번 이상 복사

```
let obj1 = {c : 10 , d: 'ddd'};
let obj2 = obj1;

// 주솟값이 적혀있는 데이터영역의 주솟값이 바뀌게 됨.
obj2 = {c : 20, d: 'ddd'};
```

메모리의 데이터 영역의 새로운 공간에 새 객체가 생성되고, 그 주소를 변수 영역의 obj2 위치에 저장

즉, 참조형 데이터가 '가변'한 속성을 지니는 경우 = 참조형 데이터 자체가 아닌 **객체 내부의 프로퍼티를 변경하는 경우**

# 불변 객체

## 불변한 객체를 만드는 방법

- `기본형 데이터`의 데이터 영역은 불변함.
- `참조형 데이터`의 데이터 자체를 변경하고자 한다면 기존 데이터는 변하지 않음 (불변).
- 내부 프로퍼티 변경 시에도 불변성을 확보하고 싶다면...내부 프로퍼티를 변경할 때마다 매번 새로운 객체를 만들어 재할당자동으로 새로운 객체를 만드는 도구(spread operator, Object.assign 등) 활용

### 불변 객체가 필요한 경우

값으로 전달받은 객체에 변경을 가하더라도, 원본 객체는 변하지 않아야 하는 경우

## 얕은 복사와 깊은 복사

얕은 복사 : 바로 아래 단계의 값만 복사깊은 복사 : 내부의 참조형 객체들을 모두 복사

### 얕은 복사

### 기존 정보를 복사해 새로운 객체를 반환하는 함수

```js
let copyObject = function (target) {
  let result = []
  for (let props in target) {
    result[props] = target[props]
  }
  return result
}
```

### copyObject를 이용한 객체 복사

```js
let user = {
  name: "minju",
  gender: "female",
}

let user2 = copyObject(user)
user2.name = "kim"

if (user !== user2) {
  console.log("유저 정보가 변경되었습니다.") // 유저 정보가 변경되었습니다.
}

console.log(user.name, user2.name) // minju kim
console.log(user === user2) // false
```

얕은 복사시, 한 단계 더 들어가게 되면 기존의 데이터를 복사하기 때문에, 기존의 데이터가 참조형 데이터의 주솟값이라면 기존의 데이터를 그대로 참조하게 됨.

```js
let user = {
  name: "minju",
  skills: {
    html: "high",
    css: "high",
  },
}

let user2 = copyObject(user)
user2.name = "kim"
user2.skills.html = "low"

if (user !== user2) {
  console.log("유저 정보가 변경되었습니다.") // 유저 정보가 변경되었습니다.
}

console.log(user.name, user2.name) // minju kim
console.log(user === user2) // false
console.log(user.skills === user2.skills) // true
```

### 깊은 복사

기본형 데이터인 경우 그대로 복사 가능참조형 데이터인 경우 다시 그 내부의 프로퍼티를 복사해야함. -> 참조형 데이터가 있을 때 마다 재귀적으로 복사 수행

### 객체의 깊은 복사를 수용하는 범용 함수

```js
let copyObjectDeep = function (target) {
  let result = {}
  if (typeof target === "object" && target !== null) {
    for (let prop in target) {
      result[prop] = copyObjectDeep(target[prop])
    }
  } else {
    result = target
  }
  return result
}
```

![https://github.com/Don-t-overreact-heart/Core-Js/blob/main/1.%20%EB%8D%B0%EC%9D%B4%ED%84%B0%20%ED%83%80%EC%9E%85/image%20(3).png](<https://github.com/Don-t-overreact-heart/Core-Js/blob/main/1.%20%EB%8D%B0%EC%9D%B4%ED%84%B0%20%ED%83%80%EC%9E%85/image%20(3).png>)

> ✅ **null비교가 존재하는 이유**
> typeof 명령어가 Null의 타입으로 Object를 반환하기 때문에

### JSON을 활용한 깊은 복사

```js
let copyObjectViaJson = function (target) {
  return JSON.parse(JSON.stringify(target))
}
```

![https://github.com/Don-t-overreact-heart/Core-Js/blob/main/1.%20%EB%8D%B0%EC%9D%B4%ED%84%B0%20%ED%83%80%EC%9E%85/image%20(4).png](<https://github.com/Don-t-overreact-heart/Core-Js/blob/main/1.%20%EB%8D%B0%EC%9D%B4%ED%84%B0%20%ED%83%80%EC%9E%85/image%20(4).png>)

이 경우는 메서드, 숨겨진 프로퍼티인 `__proto__`, getter/setter등과 같이 JSON으로 변경될 수 없는 파일은 생략된다는 단점이 있음. `httpRequest`로 받은 데이터를 저장한 객체 복사 등 순수 정보를 다룰 때 활용하기 좋음.

이 외에도 `hasOwnProperty` 메서드를 활용해 프로토타입의 체이닝을 통해 상속된 프로퍼티를 복사하지 않도록 하는 방법도 존재함.

# undefined와 null

## undefined

자바스트립트 엔진이 자동으로 undefined를 부여하는 경우: 사용자가 어떤 값을 지불할 것이라고 예상되는 상황에서 **사용자가 어떤 값도 할당하지 않을 때** undefined를 반환

- 값을 대입하지 않은 변수, 즉 데이터 영역의 메모리 주소를 지정하지 않은 식별자에 접근할 때

  값을 대입하지 않는 배열의 경우, `비어있는 요소`와 `undefined`를 할당한 요소에 차이가 있음을 알 수 있음.

  ![https://github.com/Don-t-overreact-heart/Core-Js/blob/main/1.%20%EB%8D%B0%EC%9D%B4%ED%84%B0%20%ED%83%80%EC%9E%85/image%20(5).png](<https://github.com/Don-t-overreact-heart/Core-Js/blob/main/1.%20%EB%8D%B0%EC%9D%B4%ED%84%B0%20%ED%83%80%EC%9E%85/image%20(5).png>)

  `비어있는 요소`는 배열 메소드의 순회에서 제외됨.

  > ❓ **배열도 객체**이기 때문에 객체와 마찬가지로 특정 인덱스에 값을 지정할 때, 빈 공간을 확보하고 인덱스를 이름으로 지정 등의 과정을 거침

  따라서 값이 지정되지 않은 인덱스 == 아직 존재하지 않는 프로퍼티

- 객체 내부의 존재하지 않는 프로퍼티에 접근하려고 할 때
- return문이 없거나 호출되지 않는 함수의 실행 결과

![https://github.com/Don-t-overreact-heart/Core-Js/blob/main/1.%20%EB%8D%B0%EC%9D%B4%ED%84%B0%20%ED%83%80%EC%9E%85/image%20(5).png](<https://github.com/Don-t-overreact-heart/Core-Js/blob/main/1.%20%EB%8D%B0%EC%9D%B4%ED%84%B0%20%ED%83%80%EC%9E%85/image%20(5).png>)

> ✅ **var vs let**

var 선언의 경우 생성과 동시에 undefined로 초기화let,const 선언의 경우 undefined를 할당하지 않은 채 초기화를 마침.따라서 특정 값을 할당하기 전까지 해당 변수에 접근할 수 없음.

## null

자바스크립트가 통제 범위를 벗어나는 경우와 사용자가 직접 undefined를 사용하는 경우두 경우를 구분하기 어려우니 사용자가 직접 비어있음을 나타내고 싶은 경우, undefined 대신 null을 사용

앞서 말한바와 같이 typeof시 Object타입으로 출력되는 자바스트립트 자체 버그가 존재.어떤 변수의 값이 Null인지 확인하기 위해선, `일치연산자(===)`를 이용해 비교하는 과정이 필요

> ✅ **동등연산자(==)를 사용할 수 없는 이유**

![https://github.com/Don-t-overreact-heart/Core-Js/blob/main/1.%20%EB%8D%B0%EC%9D%B4%ED%84%B0%20%ED%83%80%EC%9E%85/image%20(6).png](<https://github.com/Don-t-overreact-heart/Core-Js/blob/main/1.%20%EB%8D%B0%EC%9D%B4%ED%84%B0%20%ED%83%80%EC%9E%85/image%20(6).png>)
