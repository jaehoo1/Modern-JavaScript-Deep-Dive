# 19장 프로토타입
다음 예제를 살펴보자.
```javascript
function Person(name) {
  this.name = name;
}

// 프로토타입 메서드
Person.prototype.sayHello = function () {
  console.log(`Hi! My name is ${this.name}`);
};

const me = new Person('Lee');

// hasOwnProperty는 Object.prototype의 메서드다.
console.log(me.hasOwnProperty('name')); // true
```

`Person` 생성자 함수에 의해 생성된 `me` 객체는 `Object.prototype`의 메서드인 `hasOwnProperty`를 호출할 수 있다. 이것은 `me` 객체가 `Person.prototype`뿐만 아니라 `Object.prototype`도 상속받았다는 것을 의미한다.

`me` 객체의 프로토타입은 `Person.prototype`이다.
```javascript
Object.getPrototypeOf(me) === Person.prototype; // -> true
```

`Person.prototype`의 프로토타입은 `Object.prototype`이다. 프로토타입의 프로토타입은 언제나 `Object.prototype`이다.
```javascript
Object.getPrototypeOf(Person.prototype) === Object.prototype; // -> true
```

따라서 위 예제를 그림으로 표현하면 다음과 같다.

(그림 19-18)

**자바스크립트는 객체의 프로퍼티(메서드 포함)에 접근하려고 할 때 해당 객체에 접근하려는 프로퍼티가 없다면 `[[Prototype]]` 내부 슬롯의 참조를 따라 자신의 부모 역할을 하는 프로토타입의 프로퍼티를 순차적으로 검색한다. 이를 프로토타입 체인이라 한다. 프로토타입 체인은 자바스크립트가 객체지향 프로그래밍의 상속을 구현하는 메커니즘이다.**
```javascript
// hasOwnProperty는 Object.prototype의 메서드다.
// me 객체는 프로토타입 체인을 따라 hasOwnProperty 메서드를 검색하여 사용한다.
me.hasOwnProperty('name'); // -> true
```

`me.hasOwnProperty('name')`과 같이 메서드를 호출하면 자바스크립트 엔진은 다음과 같은 과정을 거쳐 메서드를 검색한다. 물론 프로퍼티를 참조하는 경우도 마찬가지다.
1. 먼저 `hasOwnProperty` 메서드를 호출한 `me` 객체에서 `hasOwnProperty` 메서드를 검색한다. `me` 객체에는 `hasOwnProperty` 메서드가 없으므로 프로토타입 체인을 따라, 다시 말해 `[[Prototype]]` 내부 슬롯에 바인딩되어 있는 프로토타입(위 예제의 경우 `Person.prototype`)으로 이동하여 `hasOwnProperty` 메서드를 검색한다.
2. `Person.prototype`에도 `hasOwnProperty` 메서드가 없으므로 프로토타입 체인을 따라, 다시 말해 `[[Prototype]]` 내부 슬롯에 바인딩되어 있는 프로토타입(위 예제의 경우 `Object.prototype`)으로 이동하여 `hasOwnProperty` 메서드를 검색한다.
3. `Object.prototype`에는 `hasOwnProperty` 메서드가 존재한다. 자바스크립트 엔진은 `Object.prototype.hasOwnProperty` 메서드를 호출한다. 이때 `Object.prototype.hasOwnProperty` 메서드의 `this`에는 `me` 객체가 바인딩된다.

```javascript
Object.prototype.hasOwnProperty.call(me, 'name');
```

**`call` 메서드**
> `call` 메서드는 `this`로 사용할 객체를 전달하면서 함수를 호출한다. 이에 대해서는 22.2.4절 "`Function.prototype.apply/call/bind` 메서드에 의한 간접 호출"에서 자세히 살펴볼 것이다. 지금은 `this`로 사용할 `me` 객체를 전달하면서 `Object.prototype.hasOwnProperty` 메서드를 호출한다고 이해하자.

프로토타입 체인의 최상위에 위치하는 객체는 언제나 `Object.prototype`이다. 따라서 모든 객체는 `Object.prototype`을 상속받는다. **`Object.prototype`을 프로토타입 체인의 종점(end of prototype chain)** 이라 한다. `Object.prototype`의 프로토타입, 즉 `[[Prototype]]` 내부 슬롯의 값은 `null`이다.

프로토타입 체인의 종점인 `Object.prototype`에서도 프로퍼티를 검색할 수 없는 경우 `undefined`를 반환한다. 이때 에러가 발생하지 않는 것에 주의하자.
```javascript
console.log(me.foo); // undefined
```

이처럼 자바스크립트 엔진은 프로토타입 체인을 따라 프로퍼티/메서드를 검색한다. 다시 말해, 자바스크립트 엔진은 객체 간의 상속 관계로 이루어진 프로토타입의 계층적인 구조에서 객체의 프로퍼티를 검색한다. 따라서 **프로토타입 체인은 상속과 프로퍼티 검색을 위한 메커니즘**이라고 할 수 있다.

이에 반해, 프로퍼티가 아닌 식별자는 스코프 체인에서 검색한다. 다시 말해, 자바스크립트 엔진은 함수의 중첩 관계로 이루어진 스코프의 계층적 구조에서 식별자를 검색한다. 따라서 **스코프 체인은 식별자 검색을 위한 메커니즘**이라고 할 수 있다.
```javascript
me.hasOwnProperty('name');
```

위 예제의 경우, 먼저 스코프 체인에서 `me` 식별자를 검색한다. `me` 식별자는 전역에서 선언되었으므로 전역 스코프에서 검색된다. `me` 식별자를 검색한 다음, `me` 객체의 프로토타입 체인에서 `hasOwnProperty` 메서드를 검색한다.

이처럼 **스코프 체인과 프로토타입 체인은 서로 연관없이 별도로 동작하는 것이 아니라 서로 협력하여 식별자와 프로퍼티를 검색하는 데 사용된다.**

<br/>

## 19.8 오버라이딩과 프로퍼티 섀도잉
다음 예제를 살펴보자.
```javascript
const Person = (function () {
  // 생성자 함수
  function Person(name) {
    this.name = name;
  }

  // 프로토타입 메서드
  Person.prototype.sayHello = function () {
    console.log(`Hi! My name is ${this.name}`);
  };

  // 생성자 함수를 반환
  return Person;
}());

const me = new Person('Lee');

// 인스턴스 메서드
me.sayHello = function () {
  console.log(`Hey! My name is ${this.name}`);
};

// 인스턴스 메서드가 호출된다. 프로토타입 메서드는 인스턴스 메서드에 의해 가려진다.
me.sayHello(); // Hey! My name is Lee
```

생성자 함수로 객체(인스턴스)를 생성한 다음, 인스턴스에 메서드를 추가했다. 이를 그림으로 나타내면 다음과 같다.

(그림 19-19)

프로토타입이 소유한 프로퍼티(메서드 포함)를 프로토타입 프로퍼티, 인스턴스가 소유한 프로퍼티를 인스턴스 프로퍼티라고 부른다.

프로토타입 프로퍼티와 같은 이름의 프로퍼티를 인스턴스에 추가하면 프로토타입 체인을 따라 프로토타입 프로퍼티를 검색하여 프로토타입 프로퍼티를 덮어쓰는 것이 아니라 인스턴스 프로퍼티로 추가한다. 이때 인스턴스 메서드 `sayHello`는 프로토타입 메서드 `sayHello`를 오버라이딩했고 프로토타입 메서드 `sayHello`는 가려진다. 이처럼 상속관계에 의해 프로퍼티가 가려지는 현상을 섀도잉(property shadowing)이라 한다.

**오버라이딩(overriding)**
> 상위 클래스가 가지고 있는 메서드를 하위 클래스가 재정의하여 사용하는 방식이다.

**오버로딩(overloading)**
> 함수의 이름은 동일하지만 매개변수의 타입 또는 개수가 다른 메서드를 구현하고 매개변수에 의해 메서드를 구별하여 호출하는 방식이다. 자바스크립트는 오버로딩을 지원하지 않지만 `arguments` 객체를 사용하여 구현할 수는 있다.

프로퍼티를 삭제하는 경우도 마찬가지다. 위 예제에서 추가한 인스턴스 메서드 `sayHello`를 삭제해보자.
```javascript
// 인스턴스 메서드를 삭제한다.
delete me.sayHello;
// 인스턴스에는 sayHello 메서드가 없으므로 프로토타입 메서드가 호출된다.
me.sayHello(); // Hi! My name is Lee
```

당연히 프로토타입 메서드가 아닌 인스턴스 메서드 `sayHello`가 삭제된다. 다시 한번 `sayHello` 메서드를 삭제하여 프로토타입 메서드의 삭제를 시도해보자.
```javascript
// 프로토타입 체인을 통해 프로토타입 메서드가 삭제되지 않는다.
delete me.sayHello;
// 프로토타입 메서드가 호출된다.
me.sayHello(); // Hi! My name is Lee
```

이와 같이 하위 객체를 통해 프로토타입의 프로퍼티를 변경 또는 삭제하는 것은 불가능하다. 다시 말해 하위 객체를 통해 프로토타입에 get 액세스는 허용되나 set 액세스는 허용되지 않는다.

프로토타입 프로퍼티를 변경 또는 삭제하려면 하위 객체를 통해 프로토타입 체인으로 접근하는 것이 아니라 프로토타입에 직접 접근해야 한다.
```javascript
// 프로토타입 메서드 변경
Person.prototype.sayHello = function () {
  console.log(`Hey! My name is ${this.name}`);
};
me.sayHello(); // Hey! My name is Lee

// 프로토타입 메서드 삭제
delete Person.prototype.sayHello;
me.sayHello(); // TypeError: me.sayHello is not a function
```

<br/>

## 19.9 프로토타입의 교체
프로토타입은 임의의 다른 객체로 변경할 수 있다. 이것은 부모 객체인 프로토타입을 동적으로 변경할 수 있다는 것을 의미한다. 이러한 특징을 활용하여 객체 간의 상속 관계를 동적으로 변경할 수 있다. 프로토타입은 생성자 함수 또는 인스턴스에 의해 교체할 수 있다.

<br/>

### 19.9.1 생성자 함수에 의한 프로토타입의 교체
다음 예제를 살펴보자.
```javascript
const Person = (function () {
  function Person(name) {
    this.name = name;
  }

  // ① 생성자 함수의 prototype 프로퍼티를 통해 프로토타입을 교체
  Person.prototype = {
    sayHello() {
      console.log(`Hi! My name is ${this.name}`);
    }
  };

  return Person;
}());

const me = new Person('Lee');
```

①에서 `Person.prototype`에 객체 리터럴을 할당했다. 이는 `Person` 생성자 함수가 생성할 객체의 프로토타입을 객체 리터럴로 교체한 것이다. 이를 그림으로 나타내면 다음과 같다.

(그림 19-20)

프로토타입으로 교체한 객체 리터럴에는 `constructor` 프로퍼티가 없다. `constructor` 프로퍼티는 자바스크립트 엔진이 프로토타입을 생성할 때 암묵적으로 추가한 프로퍼티다. 따라서 `me` 객체의 생성자 함수를 검색하면 `Person`이 아닌 `Object`가 나온다.
```javascript
// 프로토타입을 교체하면 constructor 프로퍼티와 생성자 함수 간의 연결이 파괴된다.
console.log(me.constructor === Person); // false
// 프로토타입 체인을 따라 Object.prototype의 constructor 프로퍼티가 검색된다.
console.log(me.constructor === Object); // true
```

이처럼 프로토타입을 교체하면 `constructor` 프로퍼티와 생성자 함수 간의 연결이 파괴된다. 파괴된 `constructor` 프로퍼티와 생성자 함수 간의 연결을 되살려 보자. 프로토타입으로 교체한 객체 리터럴에 `constructor` 프로퍼티를 추가하여 프로토타입의 `constructor` 프로퍼티를 되살린다.
```javascript
const Person = (function () {
  function Person(name) {
    this.name = name;
  }

  // 생성자 함수의 prototype 프로퍼티를 통해 프로토타입을 교체
  Person.prototype = {
    // constructor 프로퍼티와 생성자 함수 간의 연결을 설정
    constructor: Person,
    sayHello() {
      console.log(`Hi! My name is ${this.name}`);
    }
  };

  return Person;
}());

const me = new Person('Lee');

// constructor 프로퍼티가 생성자 함수를 가리킨다.
console.log(me.constructor === Person); // true
console.log(me.constructor === Object); // false
```

<br/>

### 19.9.2 인스턴스에 의한 프로토타입의 교체
프로토타입은 생성자 함수의 `prototype` 프로퍼티뿐만 아니라 인스턴스의 `__proto__` 접근자 프로퍼티(또는 `Object.getPrototypeOf` 메서드)를 통해 접근할 수 있다. 따라서 인스턴스의 `__proto__` 접근자 프로퍼티(또는 `Object.setPrototypeOf` 메서드)를 통해 프로토타입을 교체할 수 있다.

생성자 함수의 `prototype` 프로퍼티에 다른 임의의 객체를 바인딩하는 것은 미래에 생성할 인스턴스의 프로토타입을 교체하는 것이다. `__proto__` 접근자 프로퍼티를 통해 프로토타입을 교체하는 것은 이미 생성된 객체의 프로토타입을 교체하는 것이다. 다음 예제를 살펴보자.
```javascript
function Person(name) {
  this.name = name;
}

const me = new Person('Lee');

// 프로토타입으로 교체할 객체
const parent = {
  sayHello() {
    console.log(`Hi! My name is ${this.name}`);
  }
};

// ① me 객체의 프로토타입을 parent 객체로 교체한다.
Object.setPrototypeOf(me, parent);
// 위 코드는 아래의 코드와 동일하게 동작한다.
// me.__proto__ = parent;

me.sayHello(); // Hi! My name is Lee
```

①에서 `me` 객체의 프로토타입을 `parent` 객체로 교체했다. 이를 그림으로 나타내면 다음과 같다.

(그림 19-21)

19.9.1절 "생성자 함수에 의한 프로토타입의 교체"와 마찬가지로 프로토타입으로 교체한 객체에는 `constructor` 프로퍼티가 없으므로 `constructor` 프로퍼티와 생성자 함수 간의 연결이 파괴된다. 따라서 프로토타입의 `constructor` 프로퍼티로 `me` 객체의 생성자 함수를 검색하면 `Person`이 아닌 `Object`가 나온다.
```javascript
// 프로토타입을 교체하면 constructor 프로퍼티와 생성자 함수 간의 연결이 파괴된다.
console.log(me.constructor === Person); // false
// 프로토타입 체인을 따라 Object.prototype의 constructor 프로퍼티가 검색된다.
console.log(me.constructor === Object); // true
```

생성자 함수에 의한 프로토타입의 교체와 인스턴스에 의한 프로토타입 교체는 별다른 차이가 없어 보인다. 하지만 미묘한 차이가 있다. 말로 설명하는 것보다 그림으로 설명하는 것이 이해하는 데 도움이 될 것 같아 그림으로 설명한다.

(그림 19-22)

프로토타입으로 교체한 객체 리터럴에 `constructor` 프로퍼티를 추가하고 생성자 함수의 `prototype` 프로퍼티를 재설정하여 파괴된 생성자 함수와 프로토타입 간의 연결을 되살려 보자.
```javascript
function Person(name) {
  this.name = name;
}

const me = new Person('Lee');

// 프로토타입으로 교체할 객체
const parent = {
  // constructor 프로퍼티와 생성자 함수 간의 연결을 설정
  constructor: Person,
  sayHello() {
    console.log(`Hi! My name is ${this.name}`);
  }
};

// 생성자 함수의 prototype 프로퍼티와 프로토타입 간의 연결을 설정
Person.prototype = parent;

// me 객체의 프로토타입을 parent 객체로 교체한다.
Object.setPrototypeOf(me, parent);
// 위 코드는 아래의 코드와 동일하게 동작한다.
// me.__proto__ = parent;

me.sayHello(); // Hi! My name is Lee

// constructor 프로퍼티가 생성자 함수를 가리킨다.
console.log(me.constructor === Person); // true
console.log(me.constructor === Object); // false

// 생성자 함수의 prototype 프로퍼티가 교체된 프로토타입을 가리킨다.
console.log(Person.prototype === Object.getPrototypeOf(me)); // true
```

이처럼 프로토타입 교체를 통해 객체 간의 상속 관계를 동적으로 변경하는 것은 꽤나 번거롭다. 따라서 프로토타입은 직접 교체하지 않는 것이 좋다. 상속 관계를 인위적으로 설정하려면 19.11절 "직접 상속"에서 살펴볼 직접 상속이 더 편리하고 안전하다. 또는 ES6에서 도입된 클래스를 사용하면 간편하고 직관적으로 상속 관계를 구현할 수 있다. 이에 대해서는 25장 "클래스"에서 자세히 살펴보도록 하자.

<br/>

## 19.10 instanceof 연산자
`instanceof` 연산자는 이항 연산자로서 좌변에 객체를 가리키는 식별자, 우변에 생성자 함수를 가리키는 식별자를 피연산자로 받는다. 만약 우변의 피연산자가 함수가 아닌 경우 `TypeError`가 발생한다.
```javascript
객체 instanceof 생성자 함수
```

**우변의 생성자 함수의 `prototype`에 바인딩된 객체가 좌변의 객체의 프로토타입 체인 상에 존재하면 `true`로 평가되고, 그렇지 않은 경우에는 `false`로 평가된다.**
```javascript
// 생성자 함수
function Person(name) {
  this.name = name;
}

const me = new Person('Lee');

// Person.prototype이 me 객체의 프로토타입 체인 상에 존재하므로 true로 평가된다.
console.log(me instanceof Person); // true

// Object.prototype이 me 객체의 프로토타입 체인 상에 존재하므로 true로 평가된다.
console.log(me instanceof Object); // true
```

`instanceof` 연산자가 어떻게 동작하는지 이해하기 위해 프로토타입을 교체해 보자.
```javascript
// 생성자 함수
function Person(name) {
  this.name = name;
}

const me = new Person('Lee');

// 프로토타입으로 교체할 객체
const parent = {};

// 프로토타입의 교체
Object.setPrototypeOf(me, parent);

// Person 생성자 함수와 parent 객체는 연결되어 있지 않다.
console.log(Person.prototype === parent); // false
console.log(parent.constructor === Person); // false

// Person.prototype이 me 객체의 프로토타입 체인 상에 존재하지 않기 때문에 false로 평가된다.
console.log(me instanceof Person); // false

// Object.prototype이 me 객체의 프로토타입 체인 상에 존재하므로 true로 평가된다.
console.log(me instanceof Object); // true
```

`me` 객체는 비록 프로토타입이 교체되어 프로토타입과 생성자 함수 간의 연결이 파괴되었지만 `Person` 생성자 함수에 의해 생성된 인스턴스임에는 틀림이 없다. 그러나 `me instanceof Person`은 `false`로 평가된다.

이는 `Person.prototype`이 `me` 객체의 프로토타입 체인 상에 존재하지 않기 때문이다. 따라서 프로토타입으로 교체한 `parent` 객체를 `Person` 생성자 함수의 `prototype` 프로퍼티에 바인딩하면 `me instanceof Person`은 `true`로 평가될 것이다.
```javascript
// 생성자 함수
function Person(name) {
  this.name = name;
}

const me = new Person('Lee');

// 프로토타입으로 교체할 객체
const parent = {};

// 프로토타입의 교체
Object.setPrototypeOf(me, parent);

// Person 생성자 함수와 parent 객체는 연결되어 있지 않다.
console.log(Person.prototype === parent); // false
console.log(parent.constructor === Person); // false

// parent 객체를 Person 생성자 함수의 prototype 프로퍼티에 바인딩한다.
Person.prototype = parent;

// Person.prototype이 me 객체의 프로토타입 체인 상에 존재하므로 true로 평가된다.
console.log(me instanceof Person); // true

// Object.prototype이 me 객체의 프로토타입 체인 상에 존재하므로 true로 평가된다.
console.log(me instanceof Object); // true
```

이처럼 `instanceof` 연산자는 프로토타입의 `constructor` 프로퍼티가 가리키는 생성자 함수를 찾는 것이 아니라 **생성자 함수의 `prototype`에 바인딩된 객체가 프로토타입 체인 상에 존재하는지 확인한다.** 다음 그림을 살펴보자.

(그림 19-23)

`me instanceof Person`의 경우 `me` 객체의 프로토타입 체인 상에 `Person.prototype`에 바인딩된 객체가 존재하는지 확인한다.

`me instanceof Object`의 경우도 마찬가지다. `me` 객체의 프로토타입 체인 상에 `Object.prototype`에 바인딩된 객체가 존재하는지 확인한다. `instanceof` 연산자를 함수로 표현하면 다음과 같다.
```javascript
function isInstanceof(instance, constructor) {
  // 프로토타입 취득
  const prototype = Object.getPrototypeOf(instance);

  // 재귀 탈출 조건
  // prototype이 null이면 프로토타입 체인의 종점에 다다른 것이다.
  if (prototype === null) return false;

  // 프로토타입이 생성자 함수의 prototype 프로퍼티에 바인딩된 객체라면 true를 반환한다.
  // 그렇지 않다면 재귀 호출로 프로토타입 체인 상의 상위 프로토타입으로 이동하여 확인한다.
  return prototype === constructor.prototype || isInstanceof(prototype, constructor);
}

console.log(isInstanceof(me, Person)); // true
console.log(isInstanceof(me, Object)); // true
console.log(isInstanceof(me, Array));  // false
```

따라서 생성자 함수에 의해 프로토타입이 교체되어 `constructor` 프로퍼티와 생성자 함수 간의 연결이 파괴되어도 생성자 함수의 `prototype` 프로퍼티와 프로토타입 간의 연결은 파괴되지 않으므로 `instanceof`는 아무런 영향을 받지 않는다.
```javascript
const Person = (function () {
  function Person(name) {
    this.name = name;
  }

  // 생성자 함수의 prototype 프로퍼티를 통해 프로토타입을 교체
  Person.prototype = {
    sayHello() {
      console.log(`Hi! My name is ${this.name}`);
    }
  };

  return Person;
}());

const me = new Person('Lee');

// constructor 프로퍼티와 생성자 함수 간의 연결은 파괴되어도 instanceof는 아무런 영향을 받지 않는다.
console.log(me.constructor === Person); // false

// Person.prototype이 me 객체의 프로토타입 체인 상에 존재하므로 true로 평가된다.
console.log(me instanceof Person); // true
// Object.prototype이 me 객체의 프로토타입 체인 상에 존재하므로 true로 평가된다.
console.log(me instanceof Object); // true
```

<br/>

## 19.11 직접 상속

<br/>

### 19.11.1 Object.create에 의한 직접 상속
`Object.create` 메서드는 명시적으로 프로토타입을 지정하여 새로운 객체를 생성한다. [`Object.create` 메서드도 다른 객체 생성 방식과 마찬가지로 추상 연산 `OrdinaryObjectCreate`를 호출한다.](https://262.ecma-international.org/11.0/#sec-object.create)

`Object.create` 메서드의 첫 번째 매개변수에는 생성할 객체의 프로토타입으로 지정할 객체를 전달한다. 두 번째 매개변수에는 생성할 객체의 프로퍼티 키와 프로퍼티 디스크립터 객체로 이뤄진 객체를 전달한다. 이 객체의 형식은 [`Object.defineProperties` 메서드](https://github.com/jaehoo1/Modern-JavaScript-Deep-Dive/tree/main/12.%20%ED%94%84%EB%A1%9C%ED%8D%BC%ED%8B%B0%20%EC%96%B4%ED%8A%B8%EB%A6%AC%EB%B7%B0%ED%8A%B8#164-%ED%94%84%EB%A1%9C%ED%8D%BC%ED%8B%B0-%EC%A0%95%EC%9D%98)의 두 번째 인수와 동일하다. 두 번째 인수는 옵션이므로 생략 가능하다.
```javascript
/**
  * 지정된 프로토타입 및 프로퍼티를 갖는 새로운 객체를 생성하여 반환한다.
  * @param {Object} prototype - 생성할 객체의 프로토타입으로 지정할 객체
  * @param {Object} [propertiesObject] - 생성할 객체의 프로퍼티를 갖는 객체
  * @returns {Object} 지정된 프로토타입 및 프로퍼티를 갖는 새로운 객체
  */
Object.create(prototype, [propertiesObject])
```

```javascript
// 프로토타입이 null인 객체를 생성한다. 생성된 객체는 프로토타입 체인의 종점에 위치한다.
// obj → null
let obj = Object.create(null);
console.log(Object.getPrototypeOf(obj) === null); // true
// Object.prototype을 상속받지 못한다.
console.log(obj.toString()); // TypeError: obj.toString is not a function

// obj → Object.prototype → null
// obj = {};와 동일하다.
obj = Object.create(Object.prototype);
console.log(Object.getPrototypeOf(obj) === Object.prototype); // true

// obj → Object.prototype → null
// obj = { x: 1 };와 동일하다.
obj = Object.create(Object.prototype, {
  x: { value: 1, writable: true, enumerable: true, configurable: true }
});
// 위 코드는 다음과 동일하다.
// obj = Object.create(Object.prototype);
// obj.x = 1;
console.log(obj.x); // 1
console.log(Object.getPrototypeOf(obj) === Object.prototype); // true

const myProto = { x: 10 };
// 임의의 객체를 직접 상속받는다.
// obj → myProto → Object.prototype → null
obj = Object.create(myProto);
console.log(obj.x); // 10
console.log(Object.getPrototypeOf(obj) === myProto); // true

// 생성자 함수
function Person(name) {
  this.name = name;
}

// obj → Person.prototype → Object.prototype → null
// obj = new Person('Lee')와 동일하다.
obj = Object.create(Person.prototype);
obj.name = 'Lee';
console.log(obj.name); // Lee
console.log(Object.getPrototypeOf(obj) === Person.prototype); // true
```

이처럼 `Object.create` 메서드는 첫 번째 매개변수에 전달한 객체의 프로토타입 체인에 속하는 객체를 생성한다. 즉, 객체를 생성하면서 직접적으로 상속을 구현하는 것이다. 이 메서드의 장점은 다음과 같다.
- `new` 연산자가 없어도 객체를 생성할 수 있다.
- 프로토타입을 지정하면서 객체를 생성할 수 있다.
- 객체 리터럴에 의해 생성된 객체도 상속받을 수 있다.

참고로 `Object.prototype`의 빌트인 메서드인 `Object.prototype.hasOwnProperty`, `Object.prototype.isPrototypeOf`, `Object.prototype.propertyIsEnumerable` 등은 모든 객체의 프로토타입 체인의 종점, 즉 `Object.prototype`의 메서드이므로 모든 객체가 상속받아 호출할 수 있다.
```javascript
const obj = { a: 1 };

obj.hasOwnProperty('a');       // -> true
obj.propertyIsEnumerable('a'); // -> true
```

그런데 ESLint에서는 앞의 예제와 같이 `Object.prototype`의 빌트인 메서드를 객체가 직접 호출하는 것을 권장하지 않는다. 그 이유는 `Object.create` 메서드를 통해 프로토타입 체인의 종점에 위치하는 객체를 생성할 수 있기 때문이다. 프로토타입 체인의 종점에 위치하는 객체는 `Object.prototype`의 빌트인 메서드를 사용할 수 없다.
```javascript
// 프로토타입이 null인 객체, 즉 프로토타입 체인의 종점에 위치하는 객체를 생성한다.
const obj = Object.create(null);
obj.a = 1;

console.log(Object.getPrototypeOf(obj) === null); // true

// obj는 Object.prototype의 빌트인 메서드를 사용할 수 없다.
console.log(obj.hasOwnProperty('a')); // TypeError: obj.hasOwnProperty is not a function
```

따라서 이 같은 에러를 발생시킬 위험을 없애기 위해 `Object.prototype`의 빌트인 메서드는 다음과 같이 간접적으로 호출하는 것이 좋다.
```javascript
// 프로토타입이 null인 객체를 생성한다.
const obj = Object.create(null);
obj.a = 1;

// console.log(obj.hasOwnProperty('a')); // TypeError: obj.hasOwnProperty is not a function

// Object.prototype의 빌트인 메서드는 객체로 직접 호출하지 않는다.
console.log(Object.prototype.hasOwnProperty.call(obj, 'a')); // true
```

`Function.prototype.call` 메서드에 대해서는 22.2.4절 "`Function.prototype.apply/call/bind` 메서드에 의한 간접 호출"에서 살펴보도록 하자.

<br/>

### 19.11.2 객체 리터럴 내부에서 __proto__에 의한 직접 상속
`Object.create` 메서드에 의한 직접 상속은 앞에서 다룬 것과 같이 여러 장점이 있다. 하지만 두 번째 인자로 프로퍼티를 정의하는 것은 번거롭다. 일단 객체를 생성한 이후 프로퍼티를 추가하는 방법도 있으나 이 또한 깔끔한 방법은 아니다.

ES6에서는 객체 리터럴 내부에서 `__proto__` 접근자 프로퍼티를 사용하여 직접 상속을 구현할 수 있다.
```javascript
const myProto = { x: 10 };

// 객체 리터럴에 의해 객체를 생성하면서 프로토타입을 지정하여 직접 상속받을 수 있다.
const obj = {
  y: 20,
  // 객체를 직접 상속받는다.
  // obj → myProto → Object.prototype → null
  __proto__: myProto
};
/* 위 코드는 아래와 동일하다.
const obj = Object.create(myProto, {
  y: { value: 20, writable: true, enumerable: true, configurable: true }
});
*/

console.log(obj.x, obj.y); // 10 20
console.log(Object.getPrototypeOf(obj) === myProto); // true
```

<br/>

## 19.12 정적 프로퍼티/메서드
정적(static) 프로퍼티/메서드는 생성자 함수로 인스턴스를 생성하지 않아도 참조/호출할 수 있는 프로퍼티/메서드를 말한다. 다음 예제를 살펴보자.
```javascript
// 생성자 함수
function Person(name) {
  this.name = name;
}

// 프로토타입 메서드
Person.prototype.sayHello = function () {
  console.log(`Hi! My name is ${this.name}`);
};

// 정적 프로퍼티
Person.staticProp = 'static prop';

// 정적 메서드
Person.staticMethod = function () {
  console.log('staticMethod');
};

const me = new Person('Lee');

// 생성자 함수에 추가한 정적 프로퍼티/메서드는 생성자 함수로 참조/호출한다.
Person.staticMethod(); // staticMethod

// 정적 프로퍼티/메서드는 생성자 함수가 생성한 인스턴스로 참조/호출할 수 없다.
// 인스턴스로 참조/호출할 수 있는 프로퍼티/메서드는 프로토타입 체인 상에 존재해야 한다.
me.staticMethod(); // TypeError: me.staticMethod is not a function
```

`Person` 생성자 함수는 객체이므로 자신의 프로퍼티/메서드를 소유할 수 있다. `Person` 생성자 함수 객체가 소유한 프로퍼티/메서드를 정적 프로퍼티/메서드라고 한다. 정적 프로퍼티/메서드는 생성자 함수가 생성한 인스턴스로 참조/호출할 수 없다.

(그림 19-24)

생성자 함수가 생성한 인스턴스는 자신의 프로토타입 체인에 속한 객체의 프로퍼티/메서드에 접근할 수 있다. 하지만 정적 프로퍼티/메서드는 인스턴스의 프로토타입 체인에 속한 객체의 프로퍼티/메서드가 아니므로 인스턴스로 접근할 수 없다.

앞에서 살펴본 `Object.create` 메서드는 `Object` 생성자 함수의 정적 메서드고 `Object.prototype.hasOwnProperty` 메서드는 `Object.prototype`의 메서드다. 따라서 `Object.create` 메서드는 인스턴스, 즉 `Object` 생성자 함수가 생성한 객체로 호출할 수 없다. 하지만 `Object.prototype.hasOwnProperty` 메서드는 모든 객체의 프로토타입 체인의 종점, 즉 `Object.prototype`의 메서드이므로 모든 객체가 호출할 수 있다.
```javascript
// Object.create는 정적 메서드다.
const obj = Object.create({ name: 'Lee' });

// Object.prototype.hasOwnProperty는 프로토타입 메서드다.
obj.hasOwnProperty('name'); // -> false
```

만약 인스턴스/프로토타입 메서드 내에서 `this`를 사용하지 않는다면 그 메서드는 정적 메서드로 변경할 수 있다. 인스턴스가 호출한 인스턴스/프로토타입 메서드 내에서 `this`는 인스턴스를 가리킨다. 메서드 내에서 인스턴스를 참조할 필요가 없다면 정적 메서드로 변경하여도 동작한다. 프로토타입 메서드를 호출하려면 인스턴스를 생성해야 하지만 정적 메서드는 인스턴스를 생성하지 않아도 호출할 수 있다.
```javascript
function Foo() {}

// 프로토타입 메서드
// this를 참조하지 않는 프로토타입 메소드는 정적 메서드로 변경해도 동일한 효과를 얻을 수 있다.
Foo.prototype.x = function () {
  console.log('x');
};

const foo = new Foo();
// 프로토타입 메서드를 호출하려면 인스턴스를 생성해야 한다.
foo.x(); // x

// 정적 메서드
Foo.x = function () {
  console.log('x');
};

// 정적 메서드는 인스턴스를 생성하지 않아도 호출할 수 있다.
Foo.x(); // x
```

[MDN](https://developer.mozilla.org/ko/)과 같은 문서를 보면 다음과 같이 정적 프로퍼티/메서드와 프로토타입 프로퍼티/메서드를 구분하여 소개하고 있다. 따라서 표기법만으로도 정적 프로퍼티/메서드와 프로토타입 프로퍼티/메서드를 구별할 수 있어야 한다.

(그림 19-25)

참고로 프로토타입 프로퍼티/메서드를 표기할 때 `prototype`을 `#`으로 표기(예를 들어, `Object.prototype.isPrototypeOf`를 `Object#isPrototypeOf`으로 표기)하는 경우도 있으니 알아두도록 하자.

<br/>

## 19.13 프로퍼티 존재 확인

<br/>

### 19.13.1 in 연산자
`in` 연산자는 객체 내에 특정 프로퍼티가 존재하는지 여부를 확인한다. `in` 연산자의 사용법은 다음과 같다.
```javascript
/**
 * key: 프로퍼티 키를 나타내는 문자열
 * object: 객체로 평가되는 표현식
 */
key in object
```

```javascript
const person = {
  name: 'Lee',
  address: 'Seoul'
};

// person 객체에 name 프로퍼티가 존재한다.
console.log('name' in person);    // true
// person 객체에 address 프로퍼티가 존재한다.
console.log('address' in person); // true
// person 객체에 age 프로퍼티가 존재하지 않는다.
console.log('age' in person);     // false
```

`in` 연산자는 확인 대상 객체(위 예제의 경우 `person` 객체)의 프로퍼티뿐만 아니라 확인 대상 객체가 상속받은 모든 프로퍼티를 확인하므로 주의가 필요하다. `person` 객체에는 `toString`이라는 프로퍼티가 없지만 다음 코드의 실행 결과는 `true`다.
```javascript
console.log('toString' in person); // true
```

이는 `in` 연산자가 `person` 객체가 속한 프로토타입 체인 상에 존재하는 모든 프로토타입에서 `toString` 프로퍼티를 검색했기 때문이다. `toString`은 `Object.prototype`의 메서드다.

`in` 연산자 대신 ES6에서 도입된 [`Reflect.has` 메서드](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Reflect/has)를 사용할 수도 있다. `Reflect.has` 메서드는 `in` 연산자와 동일하게 동작한다.
```javascript
const person = { name: 'Lee' };

console.log(Reflect.has(person, 'name'));     // true
console.log(Reflect.has(person, 'toString')); // true
```

<br/>

### 19.13.2 Object.prototype.hasOwnProperty 메서드
`Object.prototype.hasOwnProperty` 메서드를 사용해도 객체에 특정 프로퍼티가 존재하는지 확인할 수 있다.
```javascript
console.log(person.hasOwnProperty('name')); // true
console.log(person.hasOwnProperty('age'));  // false
console.log(person.hasOwnProperty('toString')); // false
```

`Object.prototype.hasOwnProperty` 메서드는 이름에서 알 수 있듯이 인수로 전달받은 프로퍼티 키가 객체 고유의 프로퍼티 키인 경우에만 `true`를 반환하고 상속받은 프로토타입의 프로퍼티 키인 경우 `false`를 반환한다.

<br/>

## 19.14 프로퍼티 열거

<br/>

### 19.14.1 for...in 문
객체의 모든 프로퍼티를 순회하며 열거(enumeration)하려면 `for...in` 문을 사용한다.
```javascript
for (변수선언문 in 객체) {...}
```

```javascript
const person = {
  name: 'Lee',
  address: 'Seoul'
};

// for...in 문의 변수 key에 person 객체의 프로퍼티 키가 할당된다.
for (const key in person) {
  console.log(key + ': ' + person[key]);
}
// name: Lee
// address: Seoul
```

`for...in` 문은 객체의 프로퍼티 개수만큼 순회하며 `for...in` 문의 변수 선언문에서 선언한 변수에 프로퍼티 키를 할당한다. 위 예제의 경우 `person` 객체에는 2개의 프로퍼티가 있으므로 객체를 2번 순회하면서 프로퍼티 키를 `key` 변수에 할당한 후 코드 블록을 실행한다. 첫 번째 순회에서는 프로퍼티 키 `'name'`을 `key` 변수에 할당한 후 코드 블록을 실행하고 두 번째 순회에서는 프로퍼티 키 `'address'`를 `key` 변수에 할당한 후 코드 블록을 실행한다.

`for...in` 문은 `in` 연산자처럼 순회 대상 객체의 프로퍼티뿐만 아니라 상속받은 프로토타입의 프로퍼티까지 열거한다. 하지만 위 예제의 경우 `toString`과 같은 `Object.prototype`의 프로퍼티가 열거되지 않는다.
```javascript
const person = {
  name: 'Lee',
  address: 'Seoul'
};

// in 연산자는 객체가 상속받은 모든 프로토타입의 프로퍼티를 확인한다.
console.log('toString' in person); // true

// for...in 문도 객체가 상속받은 모든 프로토타입의 프로퍼티를 열거한다.
// 하지만 toString과 같은 Object.prototype의 프로퍼티가 열거되지 않는다.
for (const key in person) {
  console.log(key + ': ' + person[key]);
}

// name: Lee
// address: Seoul
```

이는 `toString` 메서드가 열거할 수 없도록 정의되어 있는 프로퍼티이기 때문이다. 다시 말해, `Object.prototype.toString` 프로퍼티의 프로퍼티 어트리뷰트 `[[Enumerable]]`의 값이 `false`이기 때문이다. [프로퍼티 어트리뷰트 `[[Enumerable]]`은 프로퍼티의 열거 가능 여부를 나타태며 불리언 값을 갖는다.](https://github.com/jaehoo1/Modern-JavaScript-Deep-Dive/tree/main/12.%20%ED%94%84%EB%A1%9C%ED%8D%BC%ED%8B%B0%20%EC%96%B4%ED%8A%B8%EB%A6%AC%EB%B7%B0%ED%8A%B8)
```javascript
// Object.getOwnPropertyDescriptor 메서드는 프로퍼티 디스크립터 객체를 반환한다.
// 프로퍼티 디스크립터 객체는 프로퍼티 어트리뷰트 정보를 담고 있는 객체다.
console.log(Object.getOwnPropertyDescriptor(Object.prototype, 'toString'));
// {value: ƒ, writable: true, enumerable: false, configurable: true}
```

따라서 `for...in` 문에 대해 좀 더 정확히 표현하면 아래와 같다.

**`for...in` 문은 객체의 프로토타입 체인 상에 존재하는 모든 프로토타입의 프로퍼티 중에서 프로퍼티 어트리뷰트 `[[Enumerable]]`의 값이 `true`인 프로퍼티를 순회하며 열거(enumeration)한다.**
```javascript
const person = {
  name: 'Lee',
  address: 'Seoul',
  __proto__: { age: 20 }
};

for (const key in person) {
  console.log(key + ': ' + person[key]);
}
// name: Lee
// address: Seoul
// age: 20
```

`for...in` 문은 프로퍼티 키가 심벌인 프로퍼티는 열거하지 않는다.
```javascript
const sym = Symbol();
const obj = {
  a: 1,
  [sym]: 10
};

for (const key in obj) {
  console.log(key + ': ' + obj[key]);
}
// a: 1
```

상속받은 프로퍼티는 제외하고 객체 자신의 프로퍼티만 열거하려면 `Object.prototype.hasOwnProperty` 메서드를 사용하여 객체 자신의 프로퍼티인지 확인해야 한다.
```javascript
const person = {
  name: 'Lee',
  address: 'Seoul',
  __proto__: { age: 20 }
};

for (const key in person) {
  // 객체 자신의 프로퍼티인지 확인한다.
  if (!person.hasOwnProperty(key)) continue;
  console.log(key + ': ' + person[key]);
}
// name: Lee
// address: Seoul
```

위 예제의 결과는 `person` 객체의 프로퍼티가 정의된 순서대로 열거되었다. 하지만 `for...in` 문은 프로퍼티를 열거할 때 순서를 보장하지 않으므로 주의하기 바란다. 하지만 대부분의 모던 브라우저는 순서를 보장하고 숫자(사실은 문자열)인 프로퍼티 키에 대해서는 정렬을 실시한다.
```javascript
const obj = {
  2: 2,
  3: 3,
  1: 1,
  b: 'b',
  a: 'a'
};

for (const key in obj) {
  if (!obj.hasOwnProperty(key)) continue;
  console.log(key + ': ' + obj[key]);
}

/*
1: 1
2: 2
3: 3
b: b
a: a
*/
```

배열에는 `for...in` 문을 사용하지 말고 일반적인 `for` 문이나 `for...of` 문 또는 `Array.prototype.forEach` 메서드를 사용하기를 권장한다. 사실 배열도 객체이므로 프로퍼티와 상속받은 프로퍼티가 포함될 수 있다.
```javascript
const arr = [1, 2, 3];
arr.x = 10; // 배열도 객체이므로 프로퍼티를 가질 수 있다.

for (const i in arr) {
  // 프로퍼티 x도 출력된다.
  console.log(arr[i]); // 1 2 3 10
};

// arr.length는 3이다.
for (let i = 0; i < arr.length; i++) {
  console.log(arr[i]); // 1 2 3
}

// forEach 메서드는 요소가 아닌 프로퍼티는 제외한다.
arr.forEach(v => console.log(v)); // 1 2 3

// for...of는 변수 선언문에서 선언한 변수에 키가 아닌 값을 할당한다.
for (const value of arr) {
  console.log(value); // 1 2 3
};
```

`forEach` 메서드에 대해서는 27.9.2절 `"Array.prototype.forEach"`에서, `for...of` 문에 대해서는 34.3절 `"for...of"`문에서 자세히 살펴보도록 하자.

<br/>

### 19.14.2 Object.keys/values/entries 메서드
지금까지 살펴보았듯이 `for...in` 문은 객체 자신의 고유 프로퍼티뿐 아니라 상속받은 프로퍼티도 열거한다. 따라서 `Object.prototype.hasOwnProperty` 메서드를 사용하여 객체 자신의 프로퍼티인지 확인하는 추가 처리가 필요하다.

객체 자신의 고유 프로퍼티만 열거하기 위해서는 `for...in` 문을 사용하는 것보다 `Object.keys/values/entries` 메서드를 사용하는 것을 권장한다.

`Object.keys` 메서드는 객체 자신의 열거 가능한(enumerable) 프로퍼티 키를 배열로 반환한다.
```javascript
const person = {
  name: 'Lee',
  address: 'Seoul',
  __proto__: { age: 20 }
};

console.log(Object.keys(person)); // ["name", "address"]
```

ES8에서 도입된 `Object.values` 메서드는 객체 자신의 열거 가능한 프로퍼티 값을 배열로 반환한다.
```javascript
console.log(Object.values(person)); // ["Lee", "Seoul"]
```

ES8에서 도입된 `Object.entries` 메서드는 객체 자신의 열거 가능한 프로퍼티 키와 값의 쌍의 배열을 배열에 담아 반환한다.
```javascript
console.log(Object.entries(person)); // [["name", "Lee"], ["address", "Seoul"]]

Object.entries(person).forEach(([key, value]) => console.log(key, value));
/*
name Lee
address Seoul
*/
```