# 08장 제어문
제어문(control flow statement)은 조건에 따라 코드 블록을 실행(조건문)하거나 반복 실행(반복문)할 때 사용한다. 일반적으로 코드는 위에서 아래 방향으로 순차적으로 실행된다. 제어문을 사용하면 코드의 실행 흐름을 인위적으로 제어할 수 있다.

하지만 코드의 실행 순서가 변경된다는 것은 단순히 위에서 아래로 순차적으로 진행하는 직관적인 코드의 흐름을 혼란스럽게 만든다. 따라서 제어문은 코드의 흐름을 이해하기 어렵게 만들어 가독성을 해치는 단점이 있다. 가독성이 좋지 않은 코드는 오류를 발생시키는 원인이 된다. 나중에 살펴볼 `forEach`, `map`, `filter`, `reduce` 같은 고차 함수를 사용한 함수형 프로그래밍 기법에서는 제어문의 사용을 억제하여 복잡성을 해결하려고 노력한다.

제어문을 바르게 이해하는 것은 코딩 스킬에 많은 영향을 준다. 특히 `for` 문은 매우 중요하므로 확실히 이해하자.

<br/>

## 8.1 블록문
블록문(block statement/compound statement)은 0개 이상의 문을 중괄호로 묶은 것으로, 코드 블록 또는 블록이라고 부르기도 한다. 자바스크립트는 블록문을 하나의 실행 단위로 취급한다. 블록문은 단독으로 사용할 수도 있으나 일반적으로 제어문이나 함수를 정의할 때 사용하는 것이 일반적이다.

<br/>

## 8.2 조건문
조건문(conditional statement)은 주어진 조건식(conditional expression)의 평가 결과에 따라 코드 블록(블록문)의 실행을 결정한다. 조건식은 불리언 값으로 평가될 수 있는 표현식이다.

<br/>

### 8.2.1 if … else 문
`if … else` 문은 주어진 조건식(불리언 값으로 평가될 수 있는 표현식)의 평가 결과, 즉 논리적 참 또는 거짓에 따라 실행할 코드 블록을 결정한다. 조건식의 평가 결과가 `true`일 경우 `if` 문의 코드 블록이 실행되고, `false`일 경우 `else` 문의 코드 블록이 실행된다.

`if` 문의 조건식은 불리언 값으로 평가되어야 한다. 만약 `if` 문의 조건식이 불리언 값이 아닌 값으로 평가되면 자바스크립트 엔진에 의해 암묵적으로 불리언 값으로 강제 변환되어 실행할 코드 블록을 결정한다. 이에 대해서는 9.2절 "암묵적 타입 변환"에서 자세히 살펴보자.

`num > 0 ? '양수' : '음수'`는 표현식이다. 즉, 삼항 조건 연산자는 값으로 평가되는 표현식을 만든다. 따라서 삼항 조건 연산자 표현식은 값처럼 사용할 수 있기 때문에 변수에 할당할 수 있다. 하지만 `if … else` 문은 표현식이 아닌 문이다. 따라서 `if … else` 문은 값처럼 사용할 수 없기 때문에 변수에 할당할 수 없다.

조건에 따라 단순히 값을 결정하여 변수에 할당하는 경우 `if … else` 문보다 삼항 조건 연산자를 사용하는 편이 가독성이 좋다. 하지만 조건에 따라 실행해야 할 내용이 복잡하여 여러 줄의 문이 필요하다면 `if … else` 문을 사용하는 편이 가독성이 좋다.

<br/>

### 8.2.2 switch 문
`switch` 문은 주어진 표현식을 평가하여 그 값과 일치하는 표현식을 갖는 `case` 문으로 실행 흐름을 옮긴다. `case` 문은 상황(case)을 의미하는 표현식을 지정하고 콜론으로 마친다. 그리고 그 뒤에 실행할 문들을 위치시킨다.
```javascript
switch (표현식) {
  case 표현식1:
    ~~~ ;
    break;
  case 표현식2:
    ~~~ ;
    break;
  default:
    ~~~ ;
}

console.log(monthName); // Invalid month
```

`if … else` 문의 조건식은 불리언 값으로 평가되어야 하지만 `switch` 문의 표현식은 불리언 값보다는 문자열이나 숫자 값인 경우가 많다. 다시 말해, `if … else` 문은 논리적 참, 거짓으로 실행할 코드 블록을 결정한다. `switch` 문은 논리적 참, 거짓보다는 다양한 상황(case)에 따라 실행할 코드 블록을 결정할 때 사용한다.
```javascript
// 월을 영어로 변환한다. (11 → 'November')
var month = 11;
var monthName;

switch (month) {
  case 1: monthName = 'January';
  case 2: monthName = 'February';
  // ...
  case 12: monthName = 'December';
  default: monthName = 'Invalid month';
}

console.log(monthName); // Invalid month
```

위 예제를 실행해 보면 `'November'`가 출력되지 않고 `'Invalid month'`가 출력된다. 이는 `switch` 문의 표현식의 평가 결과와 일치하는 `case` 문으로 실행 흐름이 이동하여 문을 실행한 것은 맞지만 문을 실행한 후 `switch` 문을 탈출하지 않고 `switch` 문이 끝날 때까지 이후의 모든 `case` 문과 `default` 문을 실행했기 때문이다. 이를 **폴스루(fall through)** 라 한다.

`break` 문을 생략한 폴스루가 유용한 경우도 있다. 다음 예제와 같이 폴스루를 활용해 여러 개의 `case` 문을 하나의 조건으로 사용할 수도 있다.
```javascript
var year = 2000; // 2000년은 윤년으로 2월이 29일이다.
var month = 2;
var days = 0;

switch (month) {
  case 1: case 3: case 5: case 7: case 8: case 10: case 12:
    days = 31;
    break;
  case 4: case 6: case 9: case 11:
    days = 30;
    break;
  case 2:
    // 윤년 계산 알고리즘
    // 1. 연도가 4로 나누어떨어지는 해(2000, 2004, 2008, 2012, 2016, 2020...)는 윤년이다.
    // 2. 연도가 4로 나누어떨어지더라도 연도가 100으로 나누어떨어지는 해(2000, 2100, 2200...)는 평년이다.
    // 3. 연도가 400으로 나누어떨어지는 해(2000, 2400, 2800...)는 윤년이다.
    days = ((year % 4 === 0 && year % 100 !== 0) || (year % 400 === 0)) ? 29 : 28;
    break;
  default:
    console.log('Invalid month');
}

console.log(days); // 29
```

`switch` 문은 `case`, `default`, `break` 등 다양한 키워드를 사용해야 하고 폴스루가 발생하는 등 문법도 복잡하다. 따라서 C 언어를 기반으로 하는 프로그래밍 언어(C-family)는 대부분 `switch` 문을 지원하지만 파이썬과 같이 `switch` 문을 지원하지 않는 프로그래밍 언어도 있다.

> C-family 중 알만한(한번쯤은 들어본) 언어들  
> B, C, C++, Objective-C, Perl, Java, PHP, JavaScript, C#, D, Go, Swift, Oak, R, Rust, TypeScript

만약 `if … else` 문으로 해결할 수 있다면 `switch` 문보다 `if … else` 문을 사용하는 편이 좋다. 하지만 조건이 너무 많아서 `if … else` 문보다 `switch` 문을 사용했을 때 가독성이 더 좋다면 `switch` 문을 사용하는 편이 좋다.

<br/>

## 8.3 반복문
반복문(loop statement)은 조건식의 평가 결과가 참인 경우 코드 블록을 실행한다. 그 후 조건식을 다시 평가하여 여전히 참인 경우 코드 블록을 다시 실행한다. 이는 조건식이 거짓일 때까지 반복된다.

자바스크립티는 세 가지 반복문인 `for` 문, `while` 문, `do … while` 문을 제공한다.

반복문을 대체할 수 있는 다양한 기능
> 자바스크립트는 배열을 순회할 때 사용하는 `forEach` 메서드, 객체의 프로퍼티를 열거할 때 사용하는 `for … in` 문, ES6에서 도입된 이터러블을 순회할 수 있는 `for … of` 문과 같이 반복문을 대체할 수 있는 다양한 기능을 제공한다.

<br/>

### 8.3.1 for 문
`for` 문은 조건식이 거짓으로 평가될 때 까지 코드 블록을 반복 실행한다. 가장 일반적으로 사용되는 `for` 문의 형태는 다음과 같다.
```javascript
for (변수 선언문 또는 할당문; 조건식; 증감식) {
  조건식이 참인 경우 반복 실행될 문;
}
```

<br/>

### 8.3.2 while 문
`while` 문은 주어진 조건식의 평가 결과가 참이면 코드 블록을 계속해서 반복 실행한다. `for` 문은 반복 횟수가 명확할 때 주로 사용하고 `while` 문은 반복 횟수가 불명확할 때 주로 사용한다.

`while` 문은 조건문의 평가 결과가 거짓이 되면 코드 블록을 실행하지 않고 종료한다. 만약 조건식의 평가 결과가 불리언 값이 아니면 불리언 값으로 강제 변환하여 논리적 참, 거짓을 구별한다.

<br/>

### 8.3.3 do … while 문
`do … while` 문은 코드 블록을 먼저 실행하고 조건식을 평가한다. 따라서 코드 블록은 무조건 한 번 이상 실행된다.

<br/>

## 8.4 break 문
`break` 문은 레이블 문, 반복문(`for`, `for … in`, `for … of`, `while`, `do … while`) 또는 `switch` 문의 코드 블록을 탈출한다. 레이블 문, 반복문, `switch` 문의 코드 블록 외에 `break` 문을 사용하면 `SyntaxError`(문법 에러)가 발생한다.
```javascript
if (true) {
  break; // Uncaught SyntaxError: Illegal break statement
}
```

참고로 레이블 문(label statement)이란 식별자가 붙은 문을 말한다.
```javascript
// foo라는 레이블 식별자가 붙은 레이블 문
foo: console.log('foo');
```

레이블 문은 프로그램의 실행 순서를 제어하는 데 사용된다. 사실 `switch` 문의 `case` 문과 `default` 문도 레이블 문이다. 레이블 문을 탈출하려면 `break` 문에 레이블 식별자를 지정한다.
```javascript
// foo라는 식별자가 붙은 레이블 블록문
foo: {
  console.log(1);
  break foo; // foo 레이블 블록문을 탈출한다.
  console.log(2);
}

console.log('Done!');
```

중첩된 `for` 문의 내부 `for` 문에서 `break` 문을 실행하면 내부 `for` 문을 탈출하여 외부 `for` 문으로 진입한다. 이때 내부 `for` 문이 아닌 외부 `for` 문을 탈출하려면 레이블 문을 사용한다.
```javascript
// outer라는 식별자가 붙은 레이블 for 문
outer: for (var i = 0; i < 3; i++) {
  for (var j = 0; j < 3; j++) {
    // i + j === 3이면 outer라는 식별자가 붙은 레이블 for 문을 탈출한다.
    if (i + j === 3) break outer;
    console.log(`inner [${i}, ${j}]`);
  }
}

console.log('Done!');
```

레이블 문은 중첩된 `for` 문 외부로 탈출할 때 유용하지만 그 밖의 경우에는 일반적으로 권장하지 않는다. 레이블 문을 사용하면 프로그램의 흐름이 복잡해져서 가독성이 나빠지고 오류를 발생시킬 가능성이 높아지기 때문이다.

`break` 문은 레이블 문뿐 아니라 반복문, `switch` 문에서도 사용할 수 있다. 이 경우에는 `break` 문에 레이블 식별자를 지정하지 않는다. `break` 문은 반복문을 더 이상 진행하지 않아도 될 때 불필요한 반복을 회피할 수 있어 유용하다.

<br/>

## 8.5 continue 문
`continue` 문은 반복문의 코드 블록 실행을 현 지점에서 중단하고 반복문의 증감식으로 실행 흐름을 이동시킨다. `break` 문처럼 반복문을 탈출하지는 않는다.
```javascript
var string = 'Hello World.';
var search = 'l';
var count = 0;

// 문자열은 유사배열이므로 for 문으로 순회할 수 있다.
for (var i = 0; i < string.length; i++) {
  // 'l'이 아니면 현 지점에서 실행을 중단하고 반복문의 증감식으로 이동한다.
  if (string[i] !== search) continue;
  count++; // continue 문이 실행되면 이 문은 실행되지 않는다.
}

console.log(count); // 3

// 참고로 String.prototype.match 메서드를 사용해도 같은 동작을 한다.
const regexp = new RegExp(search, 'g');
console.log(string.match(regexp).length); // 3
```

위 예제의 `for` 문은 다음 코드와 동일하게 동작한다.
```javascript
for (var i = 0; i < string.length; i++) {
  // 'l'이면 카운트를 증가시킨다.
  if (string[i] === search) count++;
}
```

위와 같이 `if` 문 내에서 실행해야 할 코드가 한 줄이라면 `continue` 문을 사용했을 때보다 간편하고 가독성도 좋다. 하지만 `if` 문 내에서 실행해야 할 코드가 길다면 들여쓰기가 한 단계 더 깊어지므로 `continue` 문을 사용하는 편이 가독성이 더 좋다.
```javascript
// continue 문을 사용하지 않으면 if 문 내에 코드를 작성해야 한다.
for (var i = 0; i < string.length; i++) {
  // 'l'이면 카운트를 증가시킨다.
  if (string[i] === search) {
    count++;
    // code
    // code
    // code
  }
}

// continue 문을 사용하면 if 문 밖에 코드를 작성할 수 있다.
for (var i = 0; i < string.length; i++) {
  // 'l'이 아니면 카운트를 증가시키지 않는다.
  if (string[i] !== search) continue;

  count++;
  // code
  // code
  // code
}
```

<br/>

## 공격자(질문)