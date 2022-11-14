# 16장 프로퍼티 어트리뷰트

<br/>

## 16.1 내부 슬롯과 내부 메서드
앞으로 살펴볼 프로퍼티 어트리뷰트를 이해하기 위해 먼저 내부 슬롯(internal slot)과 내부 메서드(internal method)의 개념에 대해 알아보자.

내부 슬롯과 내부 메서드는 자바스크립트 엔진의 구현 알고리즘을 설명하기 위해 ECMAScript 사양에서 사용하는 의사 프로퍼티(pseudo property)와 의사 메서드(pseudo method)다. ECMAScript 사양에 등장하는 이중 대괄호(`[[...]]`)로 감싼 이들이 내부 슬롯과 내부 메서드다.

(사진 : https://262.ecma-international.org/11.0/#sec-object-internal-methods-and-internal-slots)

내부 슬롯과 내부 메서드는 ECMAScript 사양에 정의된 대로 구현되어 자바스크립트 엔진에서 실제로 동작하지만 개발자가 직접 접근할 수 있도록 외부로 공개된 객체의 프로퍼티는 아니다. 즉, 내부 슬롯과 내부 메서드는 자바스크립트 엔진의 내부 로직이므로 원칙적으로 자바스크립트는 내부 슬롯과 내부 메서드에 직접적으로 접근하거나 호출할 수 있는 방법을 제공하지 않는다. 단, 일부 내부 슬롯과 내부 메서드에 한하여 간접적으로 접근할 수 있는 수단을 제공하기는 한다.

예를 들어, 모든 객체는 `[[Prototype]]`이라는 내부 슬롯을 갖는다. 내부 슬롯은 자바스크립트 엔진의 내부 로직이므로 원칙적으로 직접 접근할 수 없지만 `[[Prototype]]` 내부 슬롯의 경우, `__proto__`를 통해 간접적으로 접근할 수 있다.
```javascript
const o = {};

// 내부 슬롯은 자바스크립트 엔진의 내부 로직이므로 직접 접근할 수 없다.
o.[[Prototype]] // -> Uncaught SyntaxError: Unexpected token '['
// 단, 일부 내부 슬롯과 내부 메서드에 한하여 간접적으로 접근할 수 있는 수단을 제공하기는 한다.
o.__proto__ // -> Object.prototype
```

<br/>

## 16.2 프로퍼티 어트리뷰트와 프로퍼티 디스크립터 객체
**자바스크립트 엔진은 프로퍼티를 생성할 때 프로퍼티의 상태를 나타내는 프로퍼티 어트리뷰트를 기본값으로 자동 정의한다.** 프로퍼티의 상태란 프로퍼티의 값(value), 값의 갱신 여부(writable), 열거 가능 여부(enumerable), 재정의 가능 여부(configurable)를 말한다.

프로퍼티 어트리뷰트는 자바스크립트 엔진이 관리하는 내부 상태 값(meta-property)인 내부 슬롯 `[[Value]]`, `[[Writable]]`, `[[Enumerable]]`, `[[Configurable]]`이다. 따라서 프로퍼티 어트리뷰트에 직접 접근할 수 없지만 `Object.getOwnPropertyDescriptor` 메서드를 사용하여 간접적으로 확인할 수는 있다.
```javascript
const person = {
  name: 'Lee'
};

// 프로퍼티 어트리뷰트 정보를 제공하는 프로퍼티 디스크립터 객체를 반환한다.
console.log(Object.getOwnPropertyDescriptor(person, 'name'));
// {value: "Lee", writable: true, enumerable: true, configurable: true}
```

> descriptor : 기술자, 설명자
>
> 컴퓨터에서 프로그램 단위의 성질을 정하거나 파일의 각 블록마다 레코드마다의 특성을 정의(define)하는 데 쓰이는 용어이며 「기술자(記述子)」, 「기술어」로 번역된다. 이 기술자는 정보의 내용을 적절히 표시하는 짧은 단어이다. 파일 내에서 목적 레코드와 목적 프로그램 등을 발견하기 위한 키워드로서 사용되며, 어휘의 사용을 한정한 시스템 검사에 쓰여진다. 정보 검색에서는 데이터 베이스 중의 정보를 분류하거나 정보에 색인(index)을 붙이는 데 쓰여지는 이름과 종류명, 이러한 이름과 종류명을 키워드로 검색하고 목적한 것을 조사해낸다.

`Object.getOwnPropertyDescriptor` 메서드를 호출할 때 첫 번째 매개변수에는 객체의 참조를 전달하고, 두 번째 매개변수에는 프로퍼티 키를 문자열로 전달한다. 이때 `Object.getOwnPropertyDescriptor` 메서드는 프로퍼티 어트리뷰트 정보를 제공하는 **프로퍼티 디스크립터(PropertyDescriptor) 객체**를 반환한다. 만약 존재하지 않는 프로퍼티나 상속받은 프로퍼티에 대한 프로퍼티 디스크립터를 요구하면 `undefined`가 반환된다.

`Object.getOwnPropertyDescriptor` 메서드는 하나의 프로퍼티에 대해 프로퍼티 디스크립터 객체를 반환하지만 ES8에서 도입된 `Object.getOwnPropertyDescriptors` 메서드는 모든 프로퍼티의 프로퍼티 어트리뷰트 정보를 제공하는 프로퍼티 디스크립터 객체들을 반환한다.
```javascript
const person = {
  name: 'Lee'
};

// 프로퍼티 동적 생성
person.age = 20;

// 모든 프로퍼티의 프로퍼티 어트리뷰트 정보를 제공하는 프로퍼티 디스크립터 객체들을 반환한다.
console.log(Object.getOwnPropertyDescriptors(person));
/*
{
  name: {value: "Lee", writable: true, enumerable: true, configurable: true},
  age: {value: 20, writable: true, enumerable: true, configurable: true}
}
*/
```

<br/>

## 16.3 데이터 프로퍼티와 접근자 프로퍼티
프로퍼티는 데이터 프로퍼티와 접근자 프로퍼티로 구분할 수 있다.
- **데이터 프로퍼티**(data property)
  - 키와 값으로 구성된 일반적인 프로퍼티다. 지금까지 살펴본 모든 프로퍼티는 데이터 프로퍼티다.
- **접근자 프로퍼티**(accessor property)
  - 자체적으로 값을 갖지 않고 다른 데이터 프로퍼티의 값을 읽거나 저장할 때 호출되는 접근자 함수(accessor function)로 구성된 프로퍼티다.

<br/>

### 16.3.1 데이터 프로퍼티
데이터 프로퍼티(data property)는 다음과 같은 프로퍼티 어트리뷰트를 갖는다. 이 프로퍼티 어트리뷰트는 자바스크립트 엔진이 프로퍼티를 생성할 때 기본값으로 자동 정의된다.
|<center>프로퍼티<br/>어트리뷰트</center>|<center>프로퍼티 디스크립터<br/>객체의 프로퍼티</center>|<center>설명</center>|
|---|---|---|
|`[[Value]]`|`value`|<li>프로퍼티 키를 통해 프로퍼티 값에 접근하면 반환되는 값이다.</li><li>프로퍼티 키를 통해 프로퍼티 값을 변경하면 `[[Value]]`에 값을 재할당한다. 이때 프로퍼티가 없으면 프로퍼티를 동적 생성하고 생성된 프로퍼티의 `[[Value]]`에 값을 저장한다.</li>|
|`[[Writable]]`|`writable`|<li>프로퍼티 값의 변경 가능 여부를 나타내며 불리언 값을 갖는다.</li><li>`[[Writable]]`의 값이 `false`인 경우 해당 프로퍼티의 `[[Value]]`의 값을 변경할 수 없는 읽기 전용 프로퍼티가 된다.</li>|
|`[[Enumerable]]`|`enumerable`|<li>프로퍼티의 열거 가능 여부를 나타내며 불리언 값을 갖는다.</li><li>`[[Enumerable]]`의 값이 `false`인 경우 해당 프로퍼티는 `for...in` 문이나 `Object.keys` 메서드 등으로 열거할 수 없다.</li>|
|`[[Configurable]]`|`configurable`|<li>프로퍼티의 재정의 가능 여부를 나타내며 불리언 값을 갖는다.</li><li>`[[Configurable]]`의 값이 `false`인 경우 해당 프로퍼티의 삭제, 프로퍼티 어트리뷰트 값의 변경이 금지된다. 단, `[[Writable]]`이 `true`인 경우 `[[Value]]`의 변경과 `[[Writable]]`을 `false`로 변경하는 것은 허용된다.</li>|

```javascript
const person = {
  name: 'Lee'
};

// 프로퍼티 어트리뷰트 정보를 제공하는 프로퍼티 디스크립터 객체를 취득한다.
console.log(Object.getOwnPropertyDescriptor(person, 'name'));
// {value: "Lee", writable: true, enumerable: true, configurable: true}
```

`Object.getOwnPropertyDescriptor` 메서드가 반환한 프로퍼티 디스크립터 객체를 살펴보면 `value`의 프로퍼티의 값은 `'Lee'`다. 이것은 프로퍼티 어트리뷰트 `[[Value]]`의 값이 `'Lee'`인 것을 의미한다. 그리고 `writable`, `enumerable`, `configurable` 프로퍼티의 값은 모두 `true`다. 이것은 프로퍼티 어트리뷰트 `[[Writable]]`, `[[Enumerable]]`, `[[Configurable]]`의 값이 모두 `true`인 것을 의미한다.

이처럼 프로퍼티가 생성될 때 `[[Value]]`의 값은 프로퍼티 값으로 초기화되며 `[[Writable]]`, `[[Enumerable]]`, `[[Configurable]]`의 값은 `true`로 초기화된다. 이것은 프로퍼티를 동적 추가해도 마찬가지다.
```javascript
const person = {
  name: 'Lee'
};

// 프로퍼티 동적 생성
person.age = 20;

console.log(Object.getOwnPropertyDescriptors(person));
/*
{
  name: {value: "Lee", writable: true, enumerable: true, configurable: true},
  age: {value: 20, writable: true, enumerable: true, configurable: true}
}
*/
```