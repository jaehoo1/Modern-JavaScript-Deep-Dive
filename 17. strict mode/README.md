# 20장 strict mode

<br/>

## 20.1 strict mode란?
아래 예제의 실행 결과는 무엇일지 생각해보자.
```javascript
function foo() {
  x = 10;
}
foo();

console.log(x); // ?
```

`foo` 함수 내에서 선언하지 않은 `x` 변수에 값 10을 할당했다. 이때 `x` 변수를 찾아야 `x`에 값을 할당할 수 있기 때문에 자바스크립트 엔진은 `x` 변수가 어디에서 선언되었는지 스코프 체인을 통해 검색하기 시작한다.

자바스크립트 엔진은 먼저 `foo` 함수의 스코프에서 `x` 변수의 선언을 검색한다. `foo` 함수의 스코프에는 `x` 변수의 선언이 없으므로 검색에 실패할 것이고, 자바스크립트 엔진은 `x` 변수를 검색하기 위해 `foo` 함수 컨텍스트의 상위 스코프(위 예제의 경우 전역 스코프)에서 `x` 변수의 선언을 검색한다.

전역 스코프에도 `x` 변수의 선언이 존재하지 않기 때문에 `ReferenceError`를 발생시킬 것 같지만 자바스크립트 엔진은 암묵적으로 전역 객체에 `x` 프로퍼티를 동적 생성한다. 이때 전역 객체의 `x` 프로퍼티는 마치 전역 변수처럼 사용할 수 있다. 이러한 현상을 **암묵적 전역(implicit global)** 이라 한다.

개발자의 의도와는 상관없이 발생한 암묵적 전역은 오류를 발생시키는 원인이 될 가능성이 크다. 따라서 반드시 `var`, `let`, `const` 키워드를 사용하여 변수를 선언한 다음 사용해야 한다.

하지만 오타나 문법 지식의 미비로 인한 실수는 언제나 발생한다. 따라서 오류를 줄여 안정적인 코드를 생산하기 위해서는 좀 더 근본적인 접근이 필요하다. 다시 말해, 잠재적인 오류를 발생시키기 어려운 개발 환경을 만들고 그 환경에서 개발하는 것이 좀 더 근본적인 해결책이라고 할 수 있다.

이를 지원하기 위해 ES5부터 strict mode(엄격 모드)가 추가되었다. strict mode는 자바스크립트 언어의 문법을 좀 더 엄격히 적용하여 오류를 발생시킬 가능성이 높거나 자바스크립트 엔진의 최적화 작업에 문제를 일으킬 수 있는 코드에 대해 명시적인 에러를 발생시킨다.

[ESLint](https://eslint.org/)같은 [린트 도구](https://github.com/jaehoo1/tmi/tree/main/Lint(%EB%A6%B0%ED%8A%B8%2C%20%EB%A6%B0%ED%8A%B8%20%EB%8F%84%EA%B5%AC))를 사용해도 strict mode와 유사한 효과를 얻을 수 있다. 린트 도구는 정적 분석(static analysis) 기능을 통해 소스코드를 실행하기 전에 소스코드를 스캔하여 문법적 오류만이 아니라 잠재적 오류까지 찾아내고 오류의 원인을 리포팅해주는 유용한 도구다.

린트 도구는 strict mode가 제한하는 오류는 물론 코딩 컨벤션을 설정 파일 형태로 정의하고 강제할 수 있기 때문에 더욱 강력한 효과를 얻을 수 있다.

**ESLint를 사용하는 방법**
> ESLint의 설치 및 비주얼 스튜디오 코드에서 ESLint를 사용하는 방법에 대해서는 https://poiemaweb.com/eslint 를 참고

strict mode의 적용 방법과 strict mode가 발생시키는 에러에 대해 간략히 살펴보자. 참고로 ES6에서 도입된 클래스와 모듈은 기본적으로 strict mode가 적용된다.

<br/>

## 20.2 strict mode의 적용
strict mode를 적용하려면 전역의 선두 또는 함수 몸체의 선두에 `'use strict';`를 추가한다. 전역의 선두에 추가하면 스크립트 전체에 strict mode가 적용된다.
```javascript
'use strict';

function foo() {
  x = 10; // ReferenceError: x is not defined
}
foo();
```

함수 몸체의 선두에 추가하면 해당 함수와 중첩 함수에 strict mode가 적용된다.
```javascript
function foo() {
  'use strict';

  x = 10; // ReferenceError: x is not defined
}
foo();
```

코드의 선두에 `'use strict';`를 위치시키지 않으면 strict mode가 제대로 동작하지 않는다.
```javascript
function foo() {
  x = 10; // 에러를 발생시키지 않는다.
  'use strict';
}
foo();
```

<br/>

## 20.3 전역에 strict mode를 적용하는 것은 피하자
전역에 적용한 strict mode는 스크립트 단위로 적용된다.
```html
<!DOCTYPE html>
<html>
<body>
  <script>
    'use strict';
  </script>
  <script>
    x = 1; // 에러가 발생하지 않는다.
    console.log(x); // 1
  </script>
  <script>
    'use strict';

    y = 1; // ReferenceError: y is not defined
    console.log(y);
  </script>
</body>
</html>
```

위 예제와 같이 스크립트 단위로 적용된 strict mode는 다른 스크립트에 영향을 주지 않고 해당 스크립트에 한정되어 적용된다.

하지만 strict mode 스크립트와 non-strict mode 스크립트를 혼용하는 것은 오류를 발생시킬 수 있다. 특히 외부 서드파티 라이브러리를 사용하는 경우 라이브러리가 non-strict mode인 경우도 있기 때문에 전역에 strict mode를 적용하는 것은 바람직하지 않다. 이러한 경우 즉시 실행 함수로 스크립트 전체를 감싸서 스코프를 구분하고 즉시 실행 함수의 선두에 strict mode를 적용한다.
```javascript
// 즉시 실행 함수의 선두에 strict mode 적용
(function () {
  'use strict';

  // Do something...
}());
```