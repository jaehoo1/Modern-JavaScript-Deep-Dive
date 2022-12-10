# 21장 빌트인 객체

<br/>

## 21.1 자바스크립트 객체의 분류
자바스크립트 객체는 다음과 같이 크게 3개의 객체로 분류할 수 있다.
- **표준 빌트인 객체**(standard built-in objects/native objects/global objects)<br/>표준 빌트인 객체는 ECMAScript 사양에 정의된 객체를 말하며, 애플리케이션 전역의 공통 기능을 제공한다. 표준 빌트인 객체는 ECMAScript 사양에 정의된 객체이므로 자바스크립트 실행 환경(브라우저 또는 Node.js 환경)과 관계없이 언제나 사용할 수 있다.<br/>표준 빌트인 객체는 전역 객체의 프로퍼티로서 제공된다. 따라서 별도의 선언 없이 전역 변수처럼 언제나 참조할 수 있다.
- **호스트 객체**(host objects)<br/>호스트 객체는 ECMAScript 사양에 정의되어 있지 않지만 자바스크립트 실행 환경(브라우저 또는 Node.js 환경)에서 추가로 제공하는 객체를 말한다.<br/>브라우저 환경에서는 DOM, BOM, Canvas, `XMLHttpRequest`, `fetch`, `requestAnimationFrame`, SVG, Web Component, Web Worker와 같은 [클라이언트 사이드 Web API](https://developer.mozilla.org/ko/docs/Web/API)를 호스트 객체로 제공하고, Node.js 환경에서는 [Node.js 고유의 API](https://nodejs.org/dist/latest/docs/api/repl.html)를 호스트 객체로 제공한다.
- **사용자 정의 객체**(user-defined objects)<br/>사용자 정의 객체는 표준 빌트인 객체와 호스트 객체처럼 기본 제공되는 객체가 아닌 사용자가 직접 정의한 객체를 말한다.

<br/>

## 21.2 표준 빌트인 객체
자바스크립트는 `Object`, `String`, `Number`, `Boolean`, `Symbol`, `Date`, `Math`, `RegExp`, `Array`, `Map/Set`, `WeakMap/WeakSet`, `Function`, `Promise`, `Reflect`, `Proxy`, `JSON`, `Error` 등 40여 개의 [표준 빌트인 객체](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects)를 제공한다.

`Math`, `Reflect`, `JSON`을 제외한 표준 빌트인 객체는 모두 인스턴스를 생성할 수 있는 생성자 함수 객체다. 생성자 함수 객체인 표준 빌트인 객체는 프로토타입 메서드와 정적 메서드를 제공하고 생성자 함수 객체가 아닌 표준 빌트인 객체는 정적 메서드만 제공한다.

예를 들어, 표준 빌트인 객체인 `String`, `Number`, `Boolean`, `Function`, `Array`, `Date`는 생성자 함수로 호출하여 인스턴스를 생성할 수 있다.
```javascript
// String 생성자 함수에 의한 String 객체 생성
const strObj = new String('Lee'); // String {"Lee"}
console.log(typeof strObj);       // object

// Number 생성자 함수에 의한 Number 객체 생성
const numObj = new Number(123); // Number {123}
console.log(typeof numObj);     // object

// Boolean 생성자 함수에 의한 Boolean 객체 생성
const boolObj= new Boolean(true); // Boolean {true}
console.log(typeof boolObj);      // object

// Function 생성자 함수에 의한 Function 객체(함수) 생성
const func = new Function('x', 'return x * x'); // ƒ anonymous(x )
console.log(typeof func);                       // function

// Array 생성자 함수에 의한 Array 객체(배열) 생성
const arr = new Array(1, 2, 3); // (3) [1, 2, 3]
console.log(typeof arr);        // object

// RegExp 생성자 함수에 의한 RegExp 객체(정규 표현식) 생성
const regExp = new RegExp(/ab+c/i); // /ab+c/i
console.log(typeof regExp);         // object

// Date 생성자 함수에 의한 Date 객체 생성
const date = new Date();  // Fri May 08 2020 10:43:25 GMT+0900 (대한민국 표준시)
console.log(typeof date); // object
```

생성자 함수인 표준 빌트인 객체가 생성한 인스턴스의 프로토타입은 표준 빌트인 객체의 `prototype` 프로퍼티에 바인딩된 객체다. 예를 들어, 표준 빌트인 객체인 `String`을 생성자 함수로서 호출하여 생성한 `String` 인스턴스의 프로토타입은 `String.prototype`이다.
```javascript
// String 생성자 함수에 의한 String 객체 생성
const strObj = new String('Lee'); // String {"Lee"}

// String 생성자 함수를 통해 생성한 strObj 객체의 프로토타입은 String.prototype이다.
console.log(Object.getPrototypeOf(strObj) === String.prototype); // true
```

표준 빌트인 객체의 `prototype` 프로퍼티에 바인딩된 객체(예를 들어, `String.prototype`)는 다양한 기능의 빌트인 프로토타입 메서드를 제공한다. 그리고 표준 빌트인 객체는 인스턴스 없이도 호출 가능한 빌트인 정적 메서드를 제공한다.

예를 들어, 표준 빌트인 객체인 `Number`의 `prototype` 프로퍼티에 바인딩된 객체, `Number.prototype`은 다양한 기능의 빌트인 프로토타입 메서드를 제공한다. 이 프로토타입 메서드는 모든 `Number` 인스턴스가 상속을 통해 사용할 수 있다. 그리고 표준 빌트인 객체인 `Number`는 인스턴스 없이 정적으로 호출할 수 있는 정적 메서드를 제공한다.
```javascript
// Number 생성자 함수에 의한 Number 객체 생성
const numObj = new Number(1.5); // Number {1.5}

// toFixed는 Number.prototype의 프로토타입 메서드다.
// Number.prototype.toFixed는 소수점 자리를 반올림하여 문자열로 반환한다.
console.log(numObj.toFixed()); // 2

// isInteger는 Number의 정적 메서드다.
// Number.isInteger는 인수가 정수(integer)인지 검사하여 그 결과를 Boolean으로 반환한다.
console.log(Number.isInteger(0.5)); // false
```