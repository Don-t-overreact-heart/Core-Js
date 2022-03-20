# 2. 실행 컨텍스트

![https://blog.kakaocdn.net/dn/bgcS56/btrvrgeoDY4/N0rHhpPsbmJSJsiFowGAh0/img.jpg](https://blog.kakaocdn.net/dn/bgcS56/btrvrgeoDY4/N0rHhpPsbmJSJsiFowGAh0/img.jpg)

### 스택 stack

출입구가 하나뿐인 깊은 우물 같은 데이터 구조 / a, b, c, d 순서로 ,

데이터가 들어가면 나올 때는 d, c, b, a로 나온다.

### 큐 queue

양쪽 모두 열려있는 파이프 같은 데이터 구조 /  a, b, c, d 순서로,

데이터가 들어가면 나올때는 a, b, c, d로 나온다.

## **실행 컨텍스트란?**

- 실행할 코드에 제공하는 환경 정보를 모아 놓은 객체
- **동일한 환경에 있는 코드들을 실행할 때 필요한 환경 정보들을 모아** 컨텍스트를 구성하고, **콜 스택**에 쌓아 올린 후, 가장 위에 쌓여있는 컨텍스트와 관련 있는 코드들을 실행하는 식으로 전체 코드의 환경과 순서를 보장

여기서 동일한 환경은 하나의 실행 컨텍스트를 구성할 수 있는 방법으로

- 전역 공간
- eval() 함수
- 함수

등이 있다. 자동으로 생성되는 전역 공간과 사용을 지양하는 eval 함수를 제외하면,

우리가 흔히 실행하는 **컨텍스트 구성 방법은 함수를 실행하는 것**이다.

```jsx
// ------------------------------ (1)var a = 1;
function outer() {
  function inner() {
    console.log(a);
    var a = 3;
  }
  inner(); // ----------------- (2)console.log(a);
}
outer(); // --------------------- (3)console.log(a);
```

예제를 보며 우선 간단하게 실행 컨텍스트가 어떤 순서로 쌓이고, 어떤 순서로 실행하는지 확인할 수 있다.

1. 전역 컨텍스트가 콜 스택에 담긴다.
2. (3)에서 outer 함수를 호출하면 자바스크립트 엔진은 outer에 대한 환경 정보를 수집해서 outer 실행 컨텍스트를 생성한 후 콜 스택에 담는다. 여기서 콜스택 맨 위에 outer 실행 컨텍스트가 놓인 상태이므로, 전역 컨텍스트와 관련된 코드의 실행은 중단되고 outer 실행 컨텍스트와 관련된 코드를 순차적으로 실행한다.
3. (2)에서 inner 함수의 실행 컨텍스트가 콜 스택 위에 담기면, outer 컨텍스트가 중단된고 inner 함수 내부의 코드를 순서대로 진행한다.

![https://blog.kakaocdn.net/dn/bm6jdO/btrvvbxratc/dP2VaK4M3FEKos2bBcxZ1K/img.png](https://blog.kakaocdn.net/dn/bm6jdO/btrvvbxratc/dP2VaK4M3FEKos2bBcxZ1K/img.png)

어떤 실행 컨텍스트가 활성화될 때 자바스크립트 엔진은 해당 컨텍스트에 관련된 코드를 실행하는데 필요한 환경 정보들을 수집해 실행 컨텍스트에 저장한다. 여기에 담긴 정보는 다음과 같다.

- VariableEnvironment : 현재 컨텍스트 내의 식별자들에 대한 정보 + 외부 환경 정보 선언 시점의 LexicalEnvironment의 스냅샷으로 변경 사항은 반영되지 않음
- LexicalEnvironment : 처음에는 VariableEnvironment와 같지만 변경 사항이 실시간으로 반영
- ThisBinding : this 식별자가 바라봐야 할 대상 객체

![https://blog.kakaocdn.net/dn/bPniIS/btrvuPuHwqs/rfItgl15U187lyI3ZvrVw1/img.png](https://blog.kakaocdn.net/dn/bPniIS/btrvuPuHwqs/rfItgl15U187lyI3ZvrVw1/img.png)

### **VariableEnvironment**

VariableEnvironment에 담기는 내용은 LexicalEnvironment와 같지만 최초 실행 시의 스냅샷을 유지한다는 점이 다르다. 즉, **초기 상태를 유지한다.**

- 실행 컨텍스트를 생성할 때 VariableEnvironment에 정보를 담는다.
- 담긴 정보를 복사한 LexicalEnvironment를 만들고 이를 주로 활용한다.
- VariableEnvironment와 LexicalEnvironment의 내부는 environmentRecord와 outer-EnvironmentReference로 구성되어 있다.

### **LexicalEnvironment**

- 자바스크립트 코드에서 변수나 함수 등의 식별자를 정의하는 데 사용하는 객체.
- VariableEnvironment와 달리 함수 실행 도중에 변경되는 사항이 즉시 반영된다.
- 식별자와 참조 혹은 값을 기록하는 Environment Record와 outerEnvironmentReference라는 또 다른 LexicalEnvironment로 구성된다.
- 여기서 outerEnvironmentReference는 **외부 Lexical Environment를 참조하는 포인터**로, **중첩된 자바스크립트 코드에서 스코프 탐색을 하기 위해 사용**한다.

### **EnvironmentRecord**

- 현재 컨텍스트와 관련된 코드의 정보들이 저장된다.
- 컨텍스트를 구성하는 함수에 지정된 매개변수 식별자, **선언한 함수가 있을 경우 그 함수 자체**, **var로 선언된 변수의 식별자** 등이 식별자에 해당한다.
- 컨텍스트 내부 전체를 처음부터 끝까지 쭉 훑어나가며 순서대로 수집한다. (호이스팅)

## **Hoisting**

- 변수 정보를 수집하는 과정을 모두 마쳤더라도 아직 실행 컨텍스트가 관여할 코드들은 실행되기 전의 상태이다.
  - 코드가 실행되기 전임에도 불구하고 자바스크립트 엔진은 이미 해당 환경에 속한 코드의 변수명들을 모두 알고 있다.
- 자바스크립트 엔진은 식별자를 최상단으로 끌어올려놓은 다음 실제로 코드를 실행한다고 봐도 무방함
- 즉, 호이스팅은 변수 정보를 수집하는 과정을 이해하기 쉬운 방법으로 대체한 가상의 개념이다. ( 자바스크립트 엔진이 실제로 끌어올리지는 않는다.)

구동 방식을 이해해보기 위해 코드를 변경해보았다.

```jsx
// 원본 코드 ( 호이스팅 전 )function a() {
  var x = 1;// 매개변수console.log(x);
  var x;// 매개변수console.log(x);
  var x = 2;// 매개변수console.log(x);
}
a();

//호이스팅 진행 중 ( 매개변수를 변수 선언/할당과 같다고 간주하고 변환한 상태)function a() {
  var x = 1;// 매개변수console.log(x);
  var x;
  console.log(x);
  var x = 2;
  console.log(x);
}
a();

//호이스팅 완료function a() {
  var x;
  var x;
  var x;

  x = 1;// 매개변수console.log(x);// 1console.log(x);// 1
  x = 2;
  console.log(x);// 2
}
a();
```

## **함수 선언문과 함수 표현식**

```jsx
//함수 선언문
function a(){
...
}

//함수 표현식
const a = function(){
...
}
```

함수 선언문 (기명 함수) : fuction 정의부만 존재하고 별도의 할당 명령이 없는 것을 의미

함수 표현식 (익명 함수) : 정의한 function을 별도의 변수에 할당하는 것을 말한다.

**함수 선언문과 함수 표현식의 실질적인 차이**

```jsx
// 원본 코드 (호이스팅 전)console.log(sum(1, 2));
console.log(multiply(3, 4));

function sum(a, b) {
  // 함수 선언문return a + b;
}

var multiply = function (a, b) {
  // 함수 표현식return a * b;
};

// 호이스팅 후
var sum = function sum(a, b) {
  return a + b;
};
var multiply;

console.log(sum(1, 2)); // 3console.log(multiply(3, 4));// multiply is not a function

multiply = function (a, b) {
  return a * b;
};
```

함수 선언문은 전체를 호이스팅 한 반면 **함수 표현식은 변수 선언부만 호이스팅 함.**

=> 디버깅이 하기 쉬워지고 이러한 이유들로 함수 표현식을 사용하는 것을 권장

> 🤝 원활한 협업을 위해,
>
> **전역 공간에 함수를 선언하거나 동명의 함수를 중복으로 선언하는 경우는 없어야 한다.**

## **스코프**

- 식별자에 대한 유효 범위
- 어떤 경계 A의 외부에서 선언한 변수는 A의 외부뿐 아니라 A의 내부에서도 접근이 가능하지만, A의 내부에서 선언한 변수는 오직 A의 내부에서만 접근할 수 있다.

```jsx
const a = 29;

function b() {
  console.log(a); // a에 접근할 수 있다.
}

function c() {
  const d = 30;
}
console.log(d); // 접근할 수 없다. 오직 c() 내부에서만 접근 가능하다.
```

### **스코프 체인**

- 위와 코드 같은 식별자의 유효 범위를 안에서 바깥으로 차례로 검색해나가는 것
- 스코프 체인을 가능하게 하는 것이 LexcialEnvironment의 두 번째 수집 자료인 outerEnvironmentReference이다.

### **OuterEnvironmentReference**

- **현재 호출된 함수가 선언될 당시의 LexicalEnvironment를 참조한다.**
- 예를 들어 A 함수 내부에 B 함수를 선언하고, 다시 B 함수 내부에 C 함수를 선언한 경우, 함수 C의 outerEnvironmentReference는 함수 B의 LexicalEnvironment를 참조함. 함수 B의 LexicalEnvironment에 있는 outerEnvironmentReference는 다시 함수 B가 선언되던 때(A)의 LexicalEnvironment를 참조한다.
- 이런 식으로 OuterEnvironmentReference는**연결 리스트 형태를 띤다.**
- 각 OuterEnvironmentReference는 오직 자신이 선언된 시점의 LexicalEnvironment만 참조하고 있으므로 **가장 가까운 요소부터 차례대로만 접근할 수 있고 다른 순서로 접근은 불가능하다.**
  - 그렇기 때문에 여러 스코프에서 동일한 식별자를 선언한 경우 무조건 스코프 체인 상에서 가장 먼저 발견된 식별자에만 접근이 가능하다.

## **변수 은닉화**

- 스코프 체인 상에 있는 변수라고 해서 무조건 접근 가능한 것은 아니다.
- 아래 코드에서 b() 내부에서 a에 접근하려고 하면 무조건 스코프 체인 상의 첫 번째 인자부터 검색할 수밖에 없다.
  - = inner 함수 내부에서 a 변수를 선언했기 때문에 전역 공간에서 선언한 동일한 이름의 a 변수에는 접근할 수 없다.

```jsx
// 변수 은닉화 예시var a = 22;
function b() {
  var a = 33;
  console.log(a);
}
```

## **전역 변수와 지역변수**

- 전역변수 : 전역 공간에서 선언한 변수
- 지역변수 : 함수 내부에서 선언한 변수
- 코드의 안정성을 위해 전역변수 사용을 최소화하고자 노력하는 것이 좋다.

## **ThisBinding**

- 실행 컨텍스트의 thisBinding에는 this로 지정된 객체가 저장된다.
- 실행 컨텍스트 활성화 당시 this가 지정되지 않는 경우 this에는 전역 객체가 저장된다.

## 복습

- 실행 컨텍스트
  실행할 코드에 제공할 환경 정보를 모아 놓은 객체.
  활성화 되는 시점에서 VariableEnvironment와 LexicalEnvironment, thisBinding의 세가지 정보 수집.
- VariableEnvironment와 LexicalEnvironment 차이
  Variable은 초기 상태를 유지하는 반면
  Lexical은 함수 실행 도중에 변경되는 사항이 즉시 반영한다.
  Lexical은 매개변수명, 변수의 식별자, 선언한 함수의 함수명을 수집하는 environmentRecord와 바로 직전 컨텍스트의 LexicalEnvironment 정보를 참조하는 outerEnvironmentReference로 구성돼 있다.
- 호이스팅
  코드 해석을 더 수월하게 하기 위해 environmentRecord 수집 과정을 추상화한 개념
  변수 선언과 값 할당이 동시에 이뤄진 문항은 ‘선언부'만을 호이스팅, 할당 과정은 원래 자리로 남음.
  함수 선언문과 함수 표현식의 차이가 발생( 권장 : **함수 표현식은 변수 선언부만 호이스팅 함. )**
- 스코프
  변수의 유효 범위
  스코프 체인 식별자의 유효 범위를 안에서 바깥으로 차례로 검색해나가는 것
  전역 컨텍스트의 LexicalEnvironment까지 탐색해도 해당 변수를 찾지 못하면 undefiend를 반환
- 전역변수? 지역변수?
  전역 컨텍스트의 LexicalEnvironment에 담긴 변수를 전역변수,
  함수에 의해 생성된 변수는 지역변수
  ⇒ 전역변수 최소화 권장
- this
  실행 컨텍스트 활성화하는 당시에 지정된 this가 저장.
  함수를 호출하는 방법에 따라 값이 달라지는데, 지정되지 않은 경우 전역객체가 저장
