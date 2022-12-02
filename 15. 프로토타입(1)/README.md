# 19장 프로토타입
자바스크립트는 명령형(imperative), 함수형(functional), 프로토타입 기반(prototype-based) 객체지향 프로그래밍(OOP; Object Oriented Programming)을 지원하는 멀티 패러다임 프로그래밍 언어다.

간혹 C++나 자바 같은 클래스 기반 객체지향 프로그래밍 언어의 특징인 클래스 상속, 캡슐화를 위한 키워드인 `public`, `private`, `protected` 등이 없어서 자바스크립트는 객체지향 언어가 아니라고 오해(자바스크립트는 가장 많은 오해를 받는 언어다)하는 경우도 있다. 하지만 자바스크립트는 클래스 기반 객체지향 프로그래밍 언어보다 효율적이며 더 강력한 객체지향 프로그래밍 능력을 지니고 있는 프로토타입 기반의 객체지향 프로그래밍 언어다.

**클래스(class)**
> ES6에서 클래스가 도입되었다. 하지만 ES6의 클래스가 기존의 프로토타입 기반의 객체지향 모델을 폐지하고 새로운 객체지향 모델을 제공하는 것은 아니다. 사실 클래스도 함수이며, 기존 프로토타입 기반 패턴의 문법적 설탕(syntactic sugar)이라고 볼 수 있다.
>
> 클래스와 생성자 함수는 모두 프로토타입 기반의 인스턴스를 생성하지만 정확히 동일하게 동작하지는 않는다. 클래스는 생성자 함수보다 엄격하며 클래스는 생성자 함수에서는 제공하지 않는 기능도 제공한다.
>
> 따라서 클래스를 프로토타입 기반 객체 생성 패턴의 단순한 문법적 설탕으로 보기보다는 새로운 객체 생성 매커니즘으로 보는 것이 좀 더 합당하다고 할 수 있다. 클래스에 대해서는 25장 "클래스"에서 자세히 살펴보자.

자바스크립트는 객체 기반의 프로그래밍 언어이며 **자바스크립트를 이루고 있는 거의 "모든 것"이 객체**다. 원시 타입(primitive type)의 값을 제외한 나머지 값들(함수, 배열, 정규 표현식 등)은 모두 객체다.

먼저 객체지향 프로그래밍에 대해 간단히 살펴보자.

<br/>

## 19.1 객체지향 프로그래밍
객체지향 프로그래밍은 프로그램을 명령어 또는 함수의 목록으로 보는 전통적인 명령형 프로그래밍(imperative programming)의 절차지향적 관점에서 벗어나 여러 개의 독립적인 단위, 즉 객체(object)의 집합으로 프로그램을 표현하려는 프로그래밍 패러다임을 말한다.

객체지향 프로그래밍은 실세계의 실체(사물이나 개념)를 인식하는 철학적 사고를 프로그래밍에 접목하려는 시도에서 시작한다. 실체는 특징이나 성질을 나타내는 **속성(attribute/property)** 을 가지고 있고, 이를 통해 실체를 인식하거나 구별할 수 있다.

예를 들어, 사람은 이름, 주소, 성별, 나이, 신장, 체중, 학력, 성격, 직업 등 다양한 속성을 갖는다. 이때 "이름이 아무개이고 성별은 여성이며 나이는 20세인 사람"과 같이 속성을 구체적으로 표현하면 특정한 사람을 다른 사람과 구별하여 인식할 수 있다.

이러한 방식을 프로그래밍에 접목시켜보자. 사람에게는 다양한 속성이 있으나 우리가 구현하려는 프로그램에서는 사람의 "이름"과 "주소"라는 속성에만 관심이 있다고 가정하자. 이처럼 다양한 속성 중에서 프로그램에 필요한 속성만 간추려 내어 표현하는 것을 **추상화(abstraction)** 라 한다.

"이름"과 "주소"라는 속성을 갖는 `person`이라는 객체를 자바스크립트로 표현하면 다음과 같다.
```javascript
// 이름과 주소 속성을 갖는 객체
const person = {
  name: 'Lee',
  address: 'Seoul'
};

console.log(person); // {name: "Lee", address: "Seoul"}
```

이때 프로그래머(subject, 주체)는 이름과 주소 속성으로 표현된 객체(object)인 `person`을 다른 객체와 구별하여 인식할 수 있다. 이처럼 **속성을 통해 여러 개의 값을 하나의 단위로 구성한 복잡적인 자료구조**를 **객체**라 하며, **객체지향 프로그래밍**은 **독립적인 객체의 집합으로 프로그램을 표현하려는 프로그래밍 패러다임**이다.

이번에는 원(Circle)이라는 개념을 객체로 만들어보자. 원에는 반지름이라는 속성이 있다. 이 반지름을 가지고 원의 지름, 둘레, 넓이를 구할 수 있다. 이때 반지름은 원의 **상태를 나타내는 데이터**이며 원의 지름, 둘레, 넓이를 구하는 것은 **동작**이다.
```javascript
const circle = {
  radius: 5, // 반지름

  // 원의 지름: 2r
  getDiameter() {
    return 2 * this.radius;
  },

  // 원의 둘레: 2πr
  getPerimeter() {
    return 2 * Math.PI * this.radius;
  },

  // 원의 넓이: πrr
  getArea() {
    return Math.PI * this.radius ** 2;
  }
};

console.log(circle);
// {radius: 5, getDiameter: ƒ, getPerimeter: ƒ, getArea: ƒ}

console.log(circle.getDiameter());  // 10
console.log(circle.getPerimeter()); // 31.41592653589793
console.log(circle.getArea());      // 78.53981633974483
```

이처럼 객체지향 프로그래밍은 객체의 **상태(state)** 를 나타내는 데이터와 상태 데이터를 조작할 수 있는 **동작(behavior)** 을 하나의 논리적인 단위로 묶어 생각한다. 따라서 **객체**는 **상태 데이터와 동작을 하나의 논리적인 단위로 묶은 복합적인 자료구조**라고 할 수 있다. 이때 객체의 상태 데이터를 프로퍼티(property), 동작을 메서드(method)라 부른다.

각 객체는 고유의 기능을 갖는 독립적인 부품으로 볼 수 있지만 자신의 고유한 기능을 수행하면서 다른 객체와 관계성(relationship)을 가질 수 있다. 다른 객체와 메시지를 주고받거나 데이터를 처리할 수도 있다. 또는 다른 객체의 상태 데이터나 동작을 상속받아 사용하기도 한다.

<br/>

## 19.2 상속과 프로토타입
상속(inheritance)은 객체지향 프로그래밍의 핵심 개념으로, 어떤 객체의 프로퍼티 또는 메서드를 다른 객체가 상속받아 그대로 사용할 수 있는 것을 말한다.

자바스크립트는 프로토타입을 기반으로 상속을 구현하여 불필요한 중복을 제거한다. 중복을 제거하는 방법은 기존의 코드를 적극적으로 재사용하는 것이다. 코드 재사용은 개발 비용을 현저히 줄일 수 있는 잠재력이 있으므로 매우 중요하다. 다음 예제를 살펴보자.
```javascript
// 생성자 함수
function Circle(radius) {
  this.radius = radius;
  this.getArea = function () {
    // Math.PI는 원주율을 나타내는 상수다.
    return Math.PI * this.radius ** 2;
  };
}

// 반지름이 1인 인스턴스 생성
const circle1 = new Circle(1);
// 반지름이 2인 인스턴스 생성
const circle2 = new Circle(2);

// Circle 생성자 함수는 인스턴스를 생성할 때마다 동일한 동작을 하는
// getArea 메서드를 중복 생성하고 모든 인스턴스가 중복 소유한다.
// getArea 메서드는 하나만 생성하여 모든 인스턴스가 공유해서 사용하는 것이 바람직하다.
console.log(circle1.getArea === circle2.getArea); // false

console.log(circle1.getArea()); // 3.141592653589793
console.log(circle2.getArea()); // 12.566370614359172
```

[17.2절 "생성자 함수"](https://github.com/jaehoo1/Modern-JavaScript-Deep-Dive/tree/main/13.%20%EC%83%9D%EC%84%B1%EC%9E%90%20%ED%95%A8%EC%88%98%EC%97%90%20%EC%9D%98%ED%95%9C%20%EA%B0%9D%EC%B2%B4%20%EC%83%9D%EC%84%B1#172-%EC%83%9D%EC%84%B1%EC%9E%90-%ED%95%A8%EC%88%98)에서 살펴본 바와 같이 생성자 함수는 동일한 프로퍼티(메서드 포함) 구조를 갖는 객체를 여러 개 생성할 때 유용하다. 하지만 위 예제의 생성자 함수는 문제가 있다.

`Circle` 생성자 함수가 생성하는 모든 객체(인스턴스)는 `radius` 프로퍼티와 `getArea` 메서드를 갖는다. `radius` 프로퍼티 값은 일반적으로 인스턴스마다 다르다(같은 상태를 갖는 여러 개의 인스턴스가 필요하다면 `radius` 프로퍼티 값이 같을 수도 있다.) 하지만 `getArea` 메서드는 모든 인스턴스가 동일한 내용의 메서드를 사용하므로 단 하나만 생성하여 모든 인스턴스가 공유해서 사용하는 것이 바람직하다. 그런데 `Circle` 생성자 함수는 인스턴스를 생성할 때마다 `getArea` 메서드를 중복 생성하고 모든 인스턴스가 중복 소유한다.

이처럼 동일한 생성자 함수에 의해 생성된 모든 인스턴스가 동일한 메서드를 중복 소유하는 것은 메모리를 불필요하게 낭비한다. 또한 인스턴스를 생성할 때마다 메서드를 생성하므로 퍼포먼스에도 악영향을 준다. 만약 10개의 인스턴스를 생성하면 내용이 동일한 메서드도 10개 생성된다.

상속을 통해 불필요한 중복을 제거해 보자. **자바스크립트는 프로토타입(prototype)을 기반으로 상속을 구현한다.**
```javascript
// 생성자 함수
function Circle(radius) {
  this.radius = radius;
}

// Circle 생성자 함수가 생성한 모든 인스턴스가 getArea 메서드를
// 공유해서 사용할 수 있도록 프로토타입에 추가한다.
// 프로토타입은 Circle 생성자 함수의 prototype 프로퍼티에 바인딩되어 있다.
Circle.prototype.getArea = function () {
  return Math.PI * this.radius ** 2;
};

// 인스턴스 생성
const circle1 = new Circle(1);
const circle2 = new Circle(2);

// Circle 생성자 함수가 생성한 모든 인스턴스는 부모 객체의 역할을 하는
// 프로토타입 Circle.prototype으로부터 getArea 메서드를 상속받는다.
// 즉, Circle 생성자 함수가 생성하는 모든 인스턴스는 하나의 getArea 메서드를 공유한다.
console.log(circle1.getArea === circle2.getArea); // true

console.log(circle1.getArea()); // 3.141592653589793
console.log(circle2.getArea()); // 12.566370614359172
```

(그림 19-2)

`Circle` 생성자 함수가 생성한 모든 인스턴스는 자신의 프로토타입, 즉 상위(부모) 객체 역할을 하는 `Circle.prototype`의 모든 프로퍼티와 메서드를 상속받는다.

`getArea` 메서드는 단 하나만 생성되어 프로토타입인 `Circle.prototype`의 메서드로 할당되어 있다. 따라서 `Circle` 생성자 함수가 생성하는 모든 인스턴스는 `getArea` 메서드를 상속받아 사용할 수 있다. 즉, 자신의 상태를 나타내는 `radius` 프로퍼티만 개별적으로 소유하고 내용이 동일한 메서드는 상속을 통해 공유하여 사용하는 것이다.

상속은 코드의 재사용이란 관점에서 매우 유용하다. 생성자 함수가 생성할 모든 인스턴스가 공통적으로 사용할 프로퍼티나 메서드를 프로토타입에 미리 구현해 두면 생성자 함수가 생성할 모든 인스턴스는 별도의 구현없이 상위(부모) 객체인 프로토타입의 자산을 공유하여 사용할 수 있다.

<br/>

## 19.3 프로토타입 객체
프로토타입 객체(또는 줄여서 프로토타입)란 객체지향 프로그래밍의 근간을 이루는 객체 간 상속(inheritance)을 구현하기 위해 사용된다. 프로토타입은 어떤 객체의 상위(부모) 객체의 역할을 하는 객체로서 다른 객체에 공유 프로퍼티(메서드 포함)를 제공한다. 프로토타입을 상속받은 하위(자식) 객체는 상위 객체의 프로퍼티를 자신의 프로퍼티처럼 자유롭게 사용할 수 있다.

모든 객체는 [`[[Prototype]]`](https://262.ecma-international.org/11.0/#sec-ordinary-object-internal-methods-and-internal-slots)이라는 내부 슬롯을 가지며, 이 내부 슬롯의 값은 프로토타입의 참조(`null`인 경우도 있다)다. `[[Prototype]]`에 저장되는 프로토타입은 객체 생성 방식에 의해 결정된다. 즉, 객체가 생성될 때 객체 생성 방식에 따라 프로토타입이 결정되고 `[[Prototype]]`에 저장된다.

예를 들어, 객체 리터럴에 의해 생성된 객체의 프로토타입은 `Object.prototype`이고 생성자 함수에 의해 생성된 객체의 프로토타입은 생성자 함수의 `prototype` 프로퍼티에 바인딩되어 있는 객체다. 이에 대해서는 19.6절 "객체 생성 방식과 프로토타입의 결정"에서 자세히 살펴볼 것이다.

모든 객체는 하나의 프로토타입을 갖는다. 그리고 모든 프로토타입은 생성자 함수와 연결되어 있다. 즉, 객체와 프로토타입과 생성자 함수는 다음 그림과 같이 서로 연결되어 있다.

(그림 19-3)

> `[[Prototype]]` 내부 슬롯의 값이 `null`인 객체는 프로토타입이 없다.

`[[Prototype]]` 내부 슬롯에는 직접 접근할 수 없지만, 위 그림처럼 `__proto__` 접근자 프로퍼티를 통해 자신의 프로토타입, 즉 자신의 `[[Prototype]]` 내부 슬롯이 가리키는 프로토타입에 간접적으로 접근할 수 있다. 그리고 프로토타입은 자신의 `constructor` 프로퍼티를 통해 생성자 함수에 접근할 수 있고, 생성자 함수는 자신의 `prototype` 프로퍼티를 통해 프로토타입에 접근할 수 있다.

<br/>

### 19.3.1 \_\_proto\_\_ 접근자 프로퍼티
**모든 객체는 `__proto__` 접근자 프로퍼티를 통해 자신의 프로토타입, 즉 `[[Prototype]]` 내부 슬롯에 간접적으로 접근할 수 있다.** 다음 예제를 크롬 브라우저의 콘솔에서 출력해보자.
```javascript
const person = { name: 'Lee' };
console.log(person);
```

(그림 19-4)

직접 출력해보니 조금 다름(Edge 기준, Chrome도 마찬가지)  
(사진)

그림의 빨간 박스로 표시한 것이 `person` 객체의 프로토타입인 `Object.prototype`이다. 이는 `__proto__` 접근자 프로퍼티를 통해 `person` 객체의 `[[Prototype]]` 내부 슬롯이 가리키는 객체인 `Object.prototype`에 접근한 결과를 크롬 브라우저가 콘솔에 표시한 것이다. 이처럼 모든 객체는 `__proto__` 접근자 프로퍼티를 통해 프로토타입을 가리키는 `[[Prototype]]` 내부 슬롯에 접근할 수 있다.

### __proto__는 접근자 프로퍼티다.
[16.1절 "내부 슬롯과 내부 메서드"](https://github.com/jaehoo1/Modern-JavaScript-Deep-Dive/tree/main/12.%20%ED%94%84%EB%A1%9C%ED%8D%BC%ED%8B%B0%20%EC%96%B4%ED%8A%B8%EB%A6%AC%EB%B7%B0%ED%8A%B8#161-%EB%82%B4%EB%B6%80-%EC%8A%AC%EB%A1%AF%EA%B3%BC-%EB%82%B4%EB%B6%80-%EB%A9%94%EC%84%9C%EB%93%9C)에서 살펴보았듯이 내부 슬롯은 프로퍼티가 아니다. 따라서 자바스크립트는 원칙적으로 내부 슬롯과 내부 메서드에 직접적으로 접근하거나 호출할 수 있는 방법을 제공하지 않는다. 단, 일부 내부 슬롯과 내부 메서드에 한하여 간접적으로 접근할 수 있는 수단을 제공하기는 한다. `[[Prototype]]` 내부 슬롯에도 직접 접근할 수 없으며 `__proto__` 접근자 프로퍼티를 통해 간접적으로 `[[Prototype]]` 내부 슬롯의 값, 즉 프로토타입에 접근할 수 있다.

[16.3.2절 "접근자 프로퍼티"](https://github.com/jaehoo1/Modern-JavaScript-Deep-Dive/tree/main/12.%20%ED%94%84%EB%A1%9C%ED%8D%BC%ED%8B%B0%20%EC%96%B4%ED%8A%B8%EB%A6%AC%EB%B7%B0%ED%8A%B8#1632-%EC%A0%91%EA%B7%BC%EC%9E%90-%ED%94%84%EB%A1%9C%ED%8D%BC%ED%8B%B0)에서 살펴본 것처럼 접근자 프로퍼티는 자체적으로는 값(`[[Value]]` 프로퍼티 어트리뷰트)을 갖지 않고 다른 데이터 프로퍼티의 값을 읽거나 저장할 때 사용하는 접근자 함수(accessor function), 즉 `[[Get]]`, `[[Set]]` 프로퍼티 어트리뷰트로 구성된 프로퍼티다.

(사진)

`Object.prototype`의 접근자 프로퍼티인 `__proto__`는 getter/setter 함수라고 부르는 접근자 함수(`[[Get]]`, `[[Set]]` 프로퍼티 어트리뷰트에 할당된 함수)를 통해 `[[Prototype]]` 내부 슬롯의 값, 즉 프로토타입을 취득하거나 할당한다. `__proto__` 접근자 프로퍼티를 통해 프로토타입에 접근하면 내부적으로 `__proto__` 접근자 프로퍼티의 getter 함수인 `[[Get]]`(그림의 `get __proto__`)이 호출된다. `__proto__` 접근자 프로퍼티를 통해 새로운 프로토타입을 할당하면 `__proto__` 접근자 프로퍼티의 setter 함수인 `[[Set]]`(그림의 `set __proto__`)이 호출된다.
```javascript
const obj = {};
const parent = { x: 1 };

// getter 함수인 get __proto__가 호출되어 obj 객체의 프로토타입을 취득
obj.__proto__;
// setter함수인 set __proto__가 호출되어 obj 객체의 프로토타입을 교체
obj.__proto__ = parent;

console.log(obj.x); // 1
```

### \_\_proto\_\_ 접근자 프로퍼티는 상속을 통해 사용된다.
`__proto__` 접근자 프로퍼티는 객체가 직접 소유하는 프로퍼티가 아니라 `Object.prototype`의 프로퍼티다. 모든 객체는 상속을 통해 `Object.prototype.__proto__` 접근자 프로퍼티를 사용할 수 있다.
```javascript
const person = { name: 'Lee' };

// person 객체는 __proto__ 프로퍼티를 소유하지 않는다.
console.log(person.hasOwnProperty('__proto__')); // false

// __proto__ 프로퍼티는 모든 객체의 프로토타입 객체인 Object.prototype의 접근자 프로퍼티다.
console.log(Object.getOwnPropertyDescriptor(Object.prototype, '__proto__'));
// {get: ƒ, set: ƒ, enumerable: false, configurable: true}

// 모든 객체는 Object.prototype의 접근자 프로퍼티 __proto__를 상속받아 사용할 수 있다.
console.log({}.__proto__ === Object.prototype); // true
```

**`Object.prototype`**
> 모든 객체는 프로토타입의 계층 구조인 프로토타입 체인에 묶여 있다. 자바스크립트 엔진은 객체의 프로퍼티(메서드 포함)에 접근하려고 할 때 해당 객체에 접근하는 프로퍼티가 없다면 `__proto__` 접근자 프로퍼티가 가리키는 참조를 따라 자신의 부모 역할을 하는 프로토타입의 프로퍼티를 순차적으로 검색한다. 프로토타입 체인의 종점, 즉 프로토타입 체인의 최상위 객체는 `Object.prototype`이며, 이 객체의 프로퍼티와 메서드는 모든 객체에 상속된다. 이에 대해서는 19.7절 "프로토타입 체인"에서 자세히 살펴보도록 하자.

### \_\_proto\_\_ 접근자 프로퍼티를 통해 프로토타입에 접근하는 이유
`[[Prototype]]` 내부 슬롯의 값, 즉 프로토타입에 접근하기 위해 접근자 프로퍼티를 사용하는 이유는 상호 참조에 의해 프로토타입 체인이 생성되는 것을 방지하기 위해서다. 다음 예제를 살펴보자.
```javascript
const parent = {};
const child = {};

// child의 프로토타입을 parent로 설정
child.__proto__ = parent;
// parent의 프로토타입을 child로 설정
parent.__proto__ = child; // TypeError: Cyclic __proto__ value
```

위 예제에서는 `parent` 객체를 `child` 객체의 프로토타입으로 설정한 후, `child` 객체를 `parent` 객체의 프로토타입으로 설정했다. 이러한 코드가 에러 없이 정상적으로 처리되면 서로가 자신의 프로토타입이 되는 비정상적인 프로토타입 체인이 만들어지기 때문에 `__proto__` 접근자 프로퍼티는 에러를 발생시킨다.

프로토타입 체인은 단방향 링크드 리스트로 구현되어야 한다. 즉, 프로퍼티 검색 방향이 한쪽 방향으로만 흘러가야 한다. 하지만 서로가 자신의 프로토타입이 되는 비정상적인 프로토타입 체인, 다시 말해 순환 참조(circular reference)하는 프로토타입 체인이 만들어지면 프로토타입 체인 종점이 존재하지 않기 때문에 프로토타입 체인에서 프로퍼티를 검색할 때 무한 루프에 빠진다. 따라서 아무런 체크 없이 무조건적으로 프로토타입을 교체할 수 없도록 `__proto__` 접근자 프로퍼티를 통해 프로토타입에 접근하고 교체하도록 구현되어 있다.

### \_\_proto\_\_ 접근자 프로퍼티를 코드 내에서 직접 사용하는 것은 권장하지 않는다.
`__proto__` 접근자 프로퍼티는 ES5까지 ECMAScript 사양에 포함되지 않은 비표준이었다. 하지만 일부 브라우저에서 `__proto__`를 지원하고 있었기 때문에 브라우저 호환성을 고려하여 ES6에서 __proto__를 표준으로 채택했다. 현재 대부분의 브라우저(IE 11 이상)가 `__proto__`를 지원한다.

하지만 코드 내에서 `__proto__` 접근자 프로퍼티를 직접 사용하는 것은 권장하지 않는다. 모든 객체가 `__proto__` 접근자 프로퍼티를 사용할 수 있는 것은 아니기 때문이다. 나중에 살펴보겠지만 직접 상속을 통해 다음과 같이 `Object.prototype`을 상속받지 않는 객체를 생성할 수도 있기 때문에 `__proto__` 접근자 프로퍼티를 사용할 수 없는 경우가 있다.
```javascript
// obj는 프로토타입 체인의 종점이다. 따라서 Object.__proto__를 상속받을 수 없다.
const obj = Object.create(null);

// obj는 Object.__proto__를 상속받을 수 없다.
console.log(obj.__proto__); // undefined

// 따라서 __proto__보다 Object.getPrototypeOf 메서드를 사용하는 편이 좋다.
console.log(Object.getPrototypeOf(obj)); // null
```

따라서 `__proto__` 접근자 프로퍼티 대신 프로토타입의 참조를 취득하고 싶은 경우에는 [`Object.getPrototypeOf` 메서드](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Object/getPrototypeOf)를 사용하고, 프로토타입을 교체하고 싶은 경우에는 [`Object.setPrototypeOf` 메서드](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Object/setPrototypeOf)를 사용할 것을 권장한다.
```javascript
const obj = {};
const parent = { x: 1 };

// obj 객체의 프로토타입을 취득
Object.getPrototypeOf(obj); // obj.__proto__;
// obj 객체의 프로토타입을 교체
Object.setPrototypeOf(obj, parent); // obj.__proto__ = parent;

console.log(obj.x); // 1
```

[`Object.getPrototypeOf` 메서드](https://262.ecma-international.org/11.0/#sec-object.getprototypeof)와 [`Object.setPrototypeOf` 메서드](https://262.ecma-international.org/11.0/#sec-object.setprototypeof)는 `get Object.prototype.__proto__`와 `set Object.prototype.__proto__`의 처리 내용과 정확히 일치한다. `Object.getPrototypeOf` 메서드는 ES5에서 도입된 메서드이며, IE9 이상에서 지원한다. `Object.setPrototypeOf` 메서드는 ES6에서 도입된 메서드이며, IE11 이상에서 지원한다.

<br/>

### 19.3.2 함수 객체의 prototype 프로퍼티
**함수 객체만이 소유하는 `prototype` 프로퍼티는 생성자 함수가 생성할 인스턴스의 프로토타입을 가리킨다.**
```javascript
// 함수 객체는 prototype 프로퍼티를 소유한다.
(function () {}).hasOwnProperty('prototype'); // -> true

// 일반 객체는 prototype 프로퍼티를 소유하지 않는다.
({}).hasOwnProperty('prototype'); // -> false
```

`prototype` 프로퍼티는 생성자 함수가 생성할 객체(인스턴스)의 프로토타입을 가리킨다. 따라서 생성자 함수로서 호출할 수 없는 함수, 즉 [non-constructor](https://github.com/jaehoo1/Modern-JavaScript-Deep-Dive/tree/main/13.%20%EC%83%9D%EC%84%B1%EC%9E%90%20%ED%95%A8%EC%88%98%EC%97%90%20%EC%9D%98%ED%95%9C%20%EA%B0%9D%EC%B2%B4%20%EC%83%9D%EC%84%B1#1725-constructor%EC%99%80-non-constructor%EC%9D%98-%EA%B5%AC%EB%B6%84)인 화살표 함수와 ES6 메서드 축약 표현으로 정의한 메서드는 `prototype` 프로퍼티를 소유하지 않으며 프로토타입도 생성하지 않는다.
```javascript
// 화살표 함수는 non-constructor다.
const Person = name => {
  this.name = name;
};

// non-constructor는 prototype 프로퍼티를 소유하지 않는다.
console.log(Person.hasOwnProperty('prototype')); // false

// non-constructor는 프로토타입을 생성하지 않는다.
console.log(Person.prototype); // undefined

// ES6의 메서드 축약 표현으로 정의한 메서드는 non-constructor다.
const obj = {
  foo() {}
};

// non-constructor는 prototype 프로퍼티를 소유하지 않는다.
console.log(obj.foo.hasOwnProperty('prototype')); // false

// non-constructor는 프로토타입을 생성하지 않는다.
console.log(obj.foo.prototype); // undefined
```

생성자 함수로 호출하기 위해 정의하지 않은 일반 함수(함수 선언문, 함수 표현식)도 `prototype` 프로퍼티를 소유하지만 객체를 생성하지 않는 일반 함수의 `prototype` 프로퍼티는 아무런 의미가 없다.

**모든 객체가 가지고 있는(엄밀히 말하면 `Object.prototype`으로부터 상속받은) `__proto__` 접근자 프로퍼티와 함수 객체만이 가지고 있는 `prototype` 프로퍼티는 결국 동일한 프로토타입을 가리킨다.** 하지만 이들 프로퍼티를 사용하는 주체가 다르다.
|<center>구분</center>|<center>소유</center>|<center>값</center>|<center>사용 주체</center>|<center>사용 목적</center>|
|---|---|---|---|---|
|`__proto__` 접근자 프로퍼티|모든 객체|프로토타입의 참조|모든 객체|객체가 자신의 프로토타입에 접근 또는 교체하기 위해 사용|
|`prototype` 프로퍼티|constructor|프로토타입의 참조|생성자 함수|생성자 함수가 자신이 생성할 객체(인스턴스)의 프로토타입을 할당하기 위해 사용|

예를 들어, 생성자 함수로 객체를 생성한 후 `__proto__` 접근자 프로퍼티와 `prototype` 프로퍼티로 프로토타입 객체에 접근해보자.
```javascript
// 생성자 함수
function Person(name) {
  this.name = name;
}

const me = new Person('Lee');

// 결국 Person.prototype과 me.__proto__는 결국 동일한 프로토타입을 가리킨다.
console.log(Person.prototype === me.__proto__);  // true
```

(그림 19-7)

<br/>

### 19.3.3 프로토타입의 constructor 프로퍼티와 생성자 함수
모든 프로토타입은 `constructor` 프로퍼티를 갖는다. 이 `constructor` 프로퍼티는 `prototype` 프로퍼티로 자신을 참조하고 있는 생성자 함수를 가리킨다. 이 연결은 생성자 함수가 생성될 때, 즉 함수 객체가 생성될 때 이뤄진다. 다음 예제를 살펴보자.
```javascript
// 생성자 함수
function Person(name) {
  this.name = name;
}

const me = new Person('Lee');

// me 객체의 생성자 함수는 Person이다.
console.log(me.constructor === Person);  // true
// me의 constructor 프로퍼티는 me의 것이 아님
```

(그림 19-8)

위 예제에서 `Person` 생성자 함수는 `me` 객체를 생성했다. 이때 `me` 객체는 프로토타입의 `constructor` 프로퍼티를 통해 생성자 함수와 연결된다. `me` 객체에는 `constructor` 프로퍼티가 없지만 `me` 객체의 프로토타입인 `Person.prototype`에는 `constructor` 프로퍼티가 있다. 따라서 `me` 객체는 프로토타입인 `Person.prototype`의 `constructor` 프로퍼티를 상속받아 사용할 수 있다.

<br/>

## 19.4 리터럴 표기법에 의해 생성된 객체의 생성자 함수와 프로토타입
앞에서 살펴본 바와 같이 생성자 함수에 의해 생성된 인스턴스는 프로토타입의 `constructor` 프로퍼티에 의해 생성자 함수와 연결된다. 이때 `constructor` 프로퍼티가 가리키는 생성자 함수는 인스턴스를 생성한 생성자 함수다.
```javascript
// obj 객체를 생성한 생성자 함수는 Object다.
const obj = new Object();
console.log(obj.constructor === Object); // true

// add 함수 객체를 생성한 생성자 함수는 Function이다.
const add = new Function('a', 'b', 'return a + b');
console.log(add.constructor === Function); // true

// 생성자 함수
function Person(name) {
  this.name = name;
}

// me 객체를 생성한 생성자 함수는 Person이다.
const me = new Person('Lee');
console.log(me.constructor === Person); // true
```

하지만 리터럴 표기법에 의한 객체 생성 방식과 같이 명시적으로 `new` 연산자와 함께 생성자 함수를 호출하여 인스턴스를 생성하지 않는 객체 생성 방식도 있다.
```javascript
// 객체 리터럴
const obj = {};

// 함수 리터럴
const add = function (a, b) { return a + b; };

// 배열 리터럴
const arr = [1, 2, 3];

// 정규표현식 리터럴
const regexp = /is/ig;
```

리터럴 표기법에 의해 생성된 객체도 물론 프로토타입이 존재한다. 하지만 리터럴 표기법에 의해 생성된 객체의 경우 프로토타입의 `constructor` 프로퍼티가 가리키는 생성자 함수가 반드시 객체를 생성한 생성자 함수라고 단정할 수는 없다.
```javascript
// obj 객체는 Object 생성자 함수로 생성한 객체가 아니라 객체 리터럴로 생성했다.
const obj = {};

// 하지만 obj 객체의 생성자 함수는 Object 생성자 함수다.
console.log(obj.constructor === Object); // true
```

위 예제의 `obj` 객체는 `Object` 생성자 함수로 생성한 객체가 아니라 객체 리터럴에 의해 생성된 객체다. 하지만 `obj` 객체는 `Object` 생성자 함수와 `constructor` 프로퍼티로 연결되어 있다. 그렇다면 객체 리터럴에 의해 생성된 객체는 사실 `Object` 생성자 함수로 생성되는 것은 아닐까? ECMAScript 사양을 살펴보자. [`Object` 생성자 함수](https://262.ecma-international.org/11.0/#sec-object.setprototypeof)는 다음과 같이 구현하도록 정의되어 있다.

(사진)

2에서 `Object` 생성자 함수에 인수를 전달하지 않거나 `undefined` 또는 `null`을 인수로 전달하면서 호출하면 내부적으로는 추상 연산 `OrdinaryObjectCreate`를 호출하여 `Object.prototype`을 프로토타입으로 갖는 빈 객체를 생성한다.

> 참고로 1은 `class Foo extends Object {}`와 같이 `Object` 생성자 함수를 확장한 클래스를 `new` 연산자와 함께 호출하는 경우다. 이에 대해서는 25장 "클래스"에서 살펴보자. 3은 `new`없이 `Object` 생성자 함수를 호출하는 경우다.

**추상 연산(abstract operation)**
> 추상 연산은 ECMAScript 사양에서 내부 동작의 구현 알고리즘을 표현한 것이다. ECMAScript 사양에서 설명을 위해 사용되는 함수와 유사한 의사 코드라고 이해하자.

```javascript
// 2. Object 생성자 함수에 의한 객체 생성
// Object 생성자 함수는 new 연산자와 함께 호출하지 않아도 new 연산자와 함께 호출한 것과 동일하게 동작한다.
// 인수가 전달되지 않았을 때 추상 연산 OrdinaryObjectCreate를 호출하여 빈 객체를 생성한다.
let obj = new Object();
console.log(obj); // {}

// 1. new.target이 undefined나 Object가 아닌 경우
// 인스턴스 -> Foo.prototype -> Object.prototype 순으로 프로토타입 체인이 생성된다.
class Foo extends Object {}
new Foo(); // Foo {}

// 3. 인수가 전달된 경우에는 인수를 객체로 변환한다.
// Number 객체 생성
obj = new Object(123);
console.log(obj); // Number {123}

// String  객체 생성
obj = new Object('123');
console.log(obj); // String {"123"}
```

[객체 리터럴이 평가](https://262.ecma-international.org/11.0/#sec-object-initializer-runtime-semantics-evaluation)될 때는 다음과 같이 추상 연산 `OrdinaryObjectCreate`를 호출하여 빈 객체를 생성하고 프로퍼티를 추가하도록 정의되어 있다.

(사진)

이처럼 `Object` 생성자 함수 호출과 객체 리터럴의 평가는 추상 연산 `OrdinaryObjectCreate`를 호출하여 빈 객체를 생성하는 점에서 동일하나 `new.target`의 확인이나 프로퍼티를 추가하는 처리 등 세부 내용은 다르다. 따라서 객체 리터럴에 의해 생성된 객체는 `Object` 생성자 함수가 생성한 객체가 아니다.

함수 객체의 경우 차이가 더 명확하다. [12.4.4절 "`Function` 생성자 함수"](https://github.com/jaehoo1/Modern-JavaScript-Deep-Dive/tree/main/07.%20%ED%95%A8%EC%88%98(1)#1244-function-%EC%83%9D%EC%84%B1%EC%9E%90-%ED%95%A8%EC%88%98)에서 살펴보았듯이 `Function` 생성자 함수를 호출하여 생성한 함수는 렉시컬 스코프를 만들지 않고 전역 함수인 것처럼 스코프를 생성하며 클로저도 만들지 않는다. 따라서 함수 선언문과 함수 표현식을 평가하여 함수 객체를 생성한 것은 `Function` 생성자 함수가 아니다. 하지만 `constructor` 프로퍼티를 통해 확인해보면 `foo` 함수의 생성자 함수는 `Function` 생성자 함수다.
```javascript
// foo 함수는 Function 생성자 함수로 생성한 함수 객체가 아니라 함수 선언문으로 생성했다.
function foo() {}

// 하지만 constructor 프로퍼티를 통해 확인해보면 함수 foo의 생성자 함수는 Function 생성자 함수다.
console.log(foo.constructor === Function); // true
```

리터럴 표기법에 의해 생성된 객체도 상속을 위해 프로토타입이 필요하다. 따라서 리터럴 표기법에 의해 생성된 객체도 가상적인 생성자 함수를 갖는다. 프로토타입은 생성자 함수와 더불어 생성되며 `prototype`, `constructor` 프로퍼티에 의해 연결되어 있기 때문이다. 다시 말해, **프로토타입과 생성자 함수는 단독으로 존재할 수 없고 언제나 쌍(pair)으로 존재한다.**

리터럴 표기법(객체 리터럴, 함수 리터럴, 배열 리터럴, 정규 표현식 리터럴 등)에 의해 생성된 객체는 생성자 함수에 의해 생성된 객체는 아니다. 하지만 큰 틀에서 생각해 보면 리터럴 표기법으로 생성한 객체도 생성자 함수로 생성한 객체와 본질적인 면에서 큰 차이는 없다.

예를 들어, 객체 리터럴에 의해 생성한 객체와 `Object` 생성자 함수에 의해 생성한 객체는 생성 과정에 미묘한 차이는 있지만 결국 객체로서 동일한 특성을 갖는다. 함수 리터럴에 의해 생성한 함수와 `Function` 생성자 함수에 의해 생성한 함수는 생성 과정과 스코프, 클로저 등의 차이가 있지만 결국 함수로서 동일한 특성을 갖는다.

따라서 프로토타입의 `constructor` 프로퍼티를 통해 연결되어 있는 생성자 함수를 리터럴 표기법으로 생성한 객체를 생성한 생성자 함수로 생각해도 크게 무리는 없다. 리터럴 표기법에 의해 생성된 객체의 생성자 함수와 프로토타입은 다음과 같다.
|<center>리터럴 표기법</center>|<center>생성자 함수</center>|<center>프로토타입</center>|
|---|---|---|
|객체 리터럴|`Object`|`Object.prototype`|
|함수 리터럴|`Function`|`Function.prototype`|
|배열 리터럴|`Array`|`Array.prototype`|
|정규 표현식 리터럴|`RegExp`|`RegExp.prototype`|

<br/>

## 19.5 프로토타입의 생성 시점
리터럴 표기법에 의해 생성된 객체도 생성자 함수와 연결되는 것을 살펴보았다. 객체는 리터럴 표기법 또는 생성자 함수에 의해 생성되므로 결국 모든 객체는 생성자 함수와 연결되어 있다.

**`Object.create` 메서드와 클래스에 의한 객체 생성**
> 아직 살펴보지 않았지만 `Object.create` 메서드와 클래스로 객체를 생성하는 방법도 있다. `Object.create` 메서드와 클래스로 생성한 객체도 생성자 함수와 연결되어 있다. 이에 대해서는 19.11.1절 "`Object.create`에 의한 직접 상속"과 25장 "클래스"에서 살펴보도록 하자.

**프로토타입은 생성자 함수가 생성되는 시점에 더불어 생성된다.** 19.4절 "리터럴 표기법에 의해 생성된 객체의 생성자 함수와 프로토타입"에서 살펴본 바와 같이 프로토타입과 생성자 함수는 단독으로 존재할 수 없고 언제나 쌍으로 존재하기 때문이다.

생성자 함수는 사용자가 직접 정의한 사용자 정의 생성자 함수와 자바스크립트가 기본 제공하는 빌트인 생성자 함수로 구분할 수 있다. 사용자 정의 생성자 함수와 빌트인 생성자 함수를 구분하여 프로토타입 생성 시점에 대해 살펴보자.

<br/>

### 19.5.1 사용자 정의 생성자 함수와 프로토타입 생성 시점
[17.2.5절 "constructor와 non-constructor의 구분"](https://github.com/jaehoo1/Modern-JavaScript-Deep-Dive/tree/main/13.%20%EC%83%9D%EC%84%B1%EC%9E%90%20%ED%95%A8%EC%88%98%EC%97%90%20%EC%9D%98%ED%95%9C%20%EA%B0%9D%EC%B2%B4%20%EC%83%9D%EC%84%B1#1725-constructor%EC%99%80-non-constructor%EC%9D%98-%EA%B5%AC%EB%B6%84)에서 살펴본 바와 같이 내부 메서드 `[[Construct]]`를 갖는 함수 객체, 즉 화살표 함수나 ES6의 메서드 축약 표현으로 정의하지 않고 일반 함수(함수 선언문, 함수 표현식)로 정의한 함수 객체는 `new` 연산자와 함께 생성자 함수로서 호출할 수 있다.

**생성자 함수로서 호출할 수 있는 함수, 즉 `constructor`는 함수 정의가 평가되어 함수 객체를 생성하는 시점에 프로토타입도 더불어 생성된다.**
```javascript
// 함수 정의(constructor)가 평가되어 함수 객체를 생성하는 시점에 프로토타입도 더불어 생성된다.
console.log(Person.prototype); // {constructor: ƒ}

// 생성자 함수
function Person(name) {
  this.name = name;
}
```

생성자 함수로서 호출할 수 없는 함수, 즉 non-constructor는 프로토타입이 생성되지 않는다.
```javascript
// 화살표 함수는 non-constructor다.
const Person = name => {
  this.name = name;
};

// non-constructor는 프로토타입이 생성되지 않는다.
console.log(Person.prototype); // undefined
```

[12.4.3절 "함수 생성 시점과 함수 호이스팅"](https://github.com/jaehoo1/Modern-JavaScript-Deep-Dive/tree/main/07.%20%ED%95%A8%EC%88%98(1)#1243-%ED%95%A8%EC%88%98-%EC%83%9D%EC%84%B1-%EC%8B%9C%EC%A0%90%EA%B3%BC-%ED%95%A8%EC%88%98-%ED%98%B8%EC%9D%B4%EC%8A%A4%ED%8C%85)에서 살펴보았듯이 함수 선언문은 런타임 이전에 자바스크립트 엔진에 의해 먼저 실행된다. 따라서 함수 선언문으로 정의된 `Person` 생성자 함수는 어떤 코드보다 먼저 평가되어 함수 객체가 된다. 이때 프로토타입도 더불어 생성된다. 생성된 프로토타입은 `Person` 생성자 함수의 `prototype` 프로퍼티에 바인딩된다. `Person` 생성자 함수와 더불어 생성된 프로토타입의 내부를 살펴보자.

(사진)

생성된 프로토타입은 오직 `constructor` 프로퍼티만을 갖는 객체다. 프로토타입도 객체이고 모든 객체는 프로토타입을 가지므로 프로토타입도 자신의 프로포타입을 갖는다. 생성된 프로토타입의 프로토타입은 `Object.prototype`이다.

(그림 19-12)

이처럼 빌트인 생성자 함수가 아닌 사용자 정의 생성자 함수는 자신이 평가되어 함수 객체로 생성되는 시점에 프로토타입도 더불어 생성되며, 생성된 프로토타입의 프로토타입은 언제나 `Object.prototype`이다.

### 19.5.2 빌트인 생성자 함수와 프로토타입 생성 시점
`Object`, `String`, `Number`, `Function`, `Array`, `RegExp`, `Date`, `Promise` 등과 같은 빌트인 생성자 함수도 일반 함수와 마찬가지로 빌트인 생성자 함수가 생성되는 시점에 프로토타입이 생성된다. 모든 빌트인 생성자 함수는 전역 객체가 생성되는 시점에 생성된다. 생성된 프로토타입은 빌트인 생성자 함수의 `prototype` 프로퍼티에 바인딩된다.

(그림 19-13)

**전역 객체(global object)**
> 전역 객체는 코드가 실행되기 이전 단계에 자바스크립트 엔진에 의해 생성되는 특수한 객체다. 전역 객체는 클라이언트 사이드 환경(브라우저)에서는 `window`, 서버 사이드 환경(Node.js)에서는 `global` 객체를 의미한다.
>
> 전역 객체는 표준 빌트인 객체(`Object`, `String`, `Number`, `Function`, `Array...`)들과 환경에 따른 호스트 객체(클라이언트 Web API 또는 Node.js의 호스트 API), 그리고 `var` 키워드로 선언한 전역 변수와 전역 함수를 프로퍼티로 갖는다. `Math`, `Reflect`, `JSON`을 제외한 표준 빌트인 객체는 모두 생성자 함수다.
> ```javascript
> // 전역 객체 window는 브라우저에 종속적이므로 아래 코드는 브라우저 환경에서 실행해야 한다.
> // 빌트인 객체인 Object는 전역 객체 window의 프로퍼티다.
> window.Object === Object // true
> ```
> 표준 빌트인 객체인 `Object`도 전역 객체의 프로퍼티이며, 전역 객체가 생성되는 시점에 생성된다. 전역 객체와 표준 빌트인 객체에 대해서는 21장 "빌트인 객체"에서 자세히 살펴보자.

이처럼 객체가 생성되기 이전에 생성자 함수와 프로토타입은 이미 객체화되어 존재한다. **이후 생성자 함수 또는 리터럴 표기법으로 객체를 생성하면 프로토타입은 생성된 객체의 `[[Prototype]]` 내부 슬롯에 할당된다.** 이로써 생성된 객체는 프로토타입을 상속받는다.

<br/>

## 19.6 객체 생성 방식과 프로토타입의 결정
객체는 다음과 같이 다양한 생성 방법이 있다.
- 객체 리터럴
- `Object` 생성자 함수
- 생성자 함수
- `Object.create` 메서드
- 클래스(ES6)

이처럼 다양한 방식으로 생성된 모든 객체는 각 방식마다 세부적인 객체 생성 방식의 차이는 있으나 [추상 연산 `OrdinaryObjectCreate`](https://262.ecma-international.org/11.0/#sec-ordinaryobjectcreate)에 의해 생성된다는 공통점이 있다.

추상 연산 `OrdinaryObjectCreate`는 필수적으로 자신이 생성할 객체의 프로토타입을 인수로 전달받는다. 그리고 자신이 생성할 객체에 추가할 프로퍼티 목록을 옵션으로 전달할 수 있다. 추상 연산 `OrdinaryObjectCreate`는 빈 객체를 생성한 후, 객체에 추가할 프로퍼티 목록이 인수로 전달된 경우 프로퍼티를 객체에 추가한다. 그리고 인수로 전달받은 프로토타입을 자신이 생성한 객체의 `[[Prototype]]` 내부 슬롯에 할당한 다음, 생성한 객체를 반환한다.

즉, 프로토타입은 추상 연산 `OrdinaryObjectCreate`에 전달되는 인수에 의해 결정된다. 이 인수는 객체가 생성되는 시점에 객체 생성 방식에 의해 결정된다.

<br/>

### 19.6.1 객체 리터럴에 의해 생성된 객체의 프로토타입
자바스크립트 엔진은 [객체 리터럴을 평가하여 객체를 생성할 때 추상 연산 `OrdinaryObjectCreate`를 호출한다.](https://262.ecma-international.org/11.0/#sec-object-initializer-runtime-semantics-evaluation) 이때 추상 연산 `OrdinaryObjectCreate`에 전달되는 프로토타입은 `Object.prototype`이다. 즉, 객체 리터럴에 의해 생성되는 객체의 프로토타입은 `Object.prototype`이다. 다음 예제를 살펴보자.
```javascript
const obj = { x: 1 };
```

위 객체 리터럴이 평가되면 추상 연산 `OrdinaryObjectCreate`에 의해 다음과 같이 `Object` 생성자 함수와 `Object.prototype`과 생성된 객체 사이에 연결이 만들어진다.

(그림 19-14)

이처럼 객체 리터럴에 의해 생성된 `obj` 객체는 `Object.prototype`을 프로토타입으로 갖게 되며, 이로써 `Object.prototype`을 상속받는다. `obj` 객체는 `constructor` 프로퍼티와 `hasOwnProperty` 메서드 등을 소유하지 않지만 자신의 프로토타입인 `Object.prototype`의 `constructor` 프로퍼티와 `hasOwnProperty` 메서드를 자신의 자산인 것처럼 자유롭게 사용할 수 있다. 이는 `obj` 객체가 자신의 프로토타입인 `Object.prototype` 객체를 상속받았기 때문이다.
```javascript
const obj = { x: 1 };

// 객체 리터럴에 의해 생성된 obj 객체는 Object.prototype을 상속받는다.
console.log(obj.constructor === Object); // true
console.log(obj.hasOwnProperty('x'));    // true
```

<br/>

### 19.6.2 Object 생성자 함수에 의해 생성된 객체의 프로토타입
`Object` 생성자 함수를 인수 없이 호출하면 빈 객체가 생성된다. [`Object` 생성자 함수를 호출하면 객체 리터럴과 마찬가지로 추상 연산 `OrdinaryObjectCreate`가 호출된다.](https://262.ecma-international.org/11.0/#sec-object-value) 이때 추상 연산 `OrdinaryObjectCreate`에 전달되는 프로토타입은 `Object.prototype`이다. 즉, `Object` 생성자 함수에 의해 생성되는 객체의 프로토타입은 `Object.prototype`이다. 다음 예제를 살펴보자.
```javascript
const obj = new Object();
obj.x = 1;
```

위 코드가 실행되면 추상 연산 `OrdinaryObjectCreate`에 의해 다음과 같이 `Object` 생성자 함수와 `Object.prototype`과 생성된 객체 사이에 연결이 만들어진다. 객체 리터럴에 의해 생성된 객체와 동일한 구조를 갖는 것을 알 수 있다.

(그림 19-15)

이처럼 `Object` 생성자 함수에 의해 생성된 객체는 `Object.prototype`을 프로토타입으로 갖게 되며, 이로써 `Object.prototype`을 상속받는다.
```javascript
const obj = new Object();
obj.x = 1;

// Object 생성자 함수에 의해 생성된 obj 객체는 Object.prototype을 상속받는다.
console.log(obj.constructor === Object); // true
console.log(obj.hasOwnProperty('x'));    // true
```

객체 리터럴과 `Object` 생성자 함수에 의한 객체 생성 방식의 차이는 프로퍼티를 추가하는 방식에 있다. 객체 리터럴 방식은 객체 리터럴 내부에 프로퍼티를 추가하지만 `Object` 생성자 함수 방식은 일단 빈 객체를 생성한 이후 프로퍼티를 추가해야 한다.

<br/>

### 19.6.3 생성자 함수에 의해 생성된 객체의 프로토타입
[`new` 연산자와 함께 생성자 함수를 호출하여 인스턴스를 생성하면 다른 객체 생성 방식과 마찬가지로 추상 연산 `OrdinaryObjectCreate`가 호출된다.](https://262.ecma-international.org/11.0/#sec-ordinarycreatefromconstructor) 이때 추상 연산 `OrdinaryObjectCreate`에 전달되는 프로토타입은 생성자 함수의 `prototype` 프로퍼티에 바인딩되어 있는 객체다. 즉, 생성자 함수에 의해 생성되는 객체의 프로토타입은 생성자 함수의 `prototype` 프로퍼티에 바인딩되어 있는 객체다. 다음 에제를 살펴보자.
```javascript
function Person(name) {
  this.name = name;
}

const me = new Person('Lee');
```

위 코드가 실행되면 추상 연산 `OrdinaryObjectCreate`에 의해 다음과 같이 생성자 함수와 생성자 함수의 `prototype` 프로퍼티에 바인딩되어 있는 객체와 생성된 객체 사이에 연결이 만들어진다.

(그림 19-16)

표준 빌트인 객체인 `Object` 생성자 함수와 더불어 생성된 프로토타입 `Object.prototype`은 다양한 빌트인 메서드(`hasOwnProperty`, `propertyIsEnumerable` 등)를 갖고 있다. 하지만 사용자 정의 생성자 함수 `Person`과 더불어 생성된 프로토타입 `Person.prototype`의 프로퍼티는 `constructor`뿐이다.

프로토타입 `Person.prototype`에 프로퍼티를 추가하여 하위(자식) 객체가 상속받을 수 있도록 구현해보자. 프로토타입은 객체다. 따라서 일반 객체와 같이 프로토타입에도 프로퍼티를 추가/삭제할 수 있다. 그리고 이렇게 추가/삭제된 프로퍼티는 프로토타입 체인에 즉각 반영된다.
```javascript
function Person(name) {
  this.name = name;
}

// 프로토타입 메서드
Person.prototype.sayHello = function () {
  console.log(`Hi! My name is ${this.name}`);
};

const me = new Person('Lee');
const you = new Person('Kim');

me.sayHello();  // Hi! My name is Lee
you.sayHello(); // Hi! My name is Kim
```

`Person` 생성자 함수를 통해 생성된 모든 객체는 프로토타입에 추가된 `sayHello` 메서드를 상속받아 자신의 메서드처럼 사용할 수 있다.

(그림 19-17)