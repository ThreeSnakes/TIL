# 코어 자바스크립트

## 날짜: 2020.05.28 ~ 2020.06.01

## Part_3. this

## 목차

- [this](#this)
- [상황에 따라 달라지는 this](#상황에-따라-달라지는-this)
  - [전역 공간에서의 this](#전역-공간에서의-this)
  - [메서드로서 호출할 떄 그 메서드 내부에서의 this](#메서드로서-호출할-떄-그-메서드-내부에서의-this)
- [명시적으로 this를 바인딩하는 방법](#명시적으로-this를-바인딩하는-방법)
  - [call 메서드](#call-메서드)
  - [apply 메서드](#apply-메서드)
  - [bind 메서드](#bind-메서드)
- [정리](#정리)

### this

- 대부분의 객체지향 언에서 this는 클래스로 생성한 인스턴스 객체를 의미한다. 하지만 자바스크립트에서의 this는 어디서든지 자유롭게 사용할 수 있다.
- this를 어디에서 사용하느냐에 따라서 this가 가라키는 대상이 달라진다.
- **함수와 객체(메서드)의 구분이 느슨한 자바스크립트에서 this는 실질적으로 이 둘을 구분하는 거의 유일한 기능이다.**

### 상황에 따라 달라지는 this

- this는 기본적으로 실행 컨텍스트가 생성될때 결정되는데, 실행 컨텍스트는 함수를 호출할 때 생성된다. 즉, **this는 함수를 호출할 때 결정된다.**
- 함수를 어디서 호출하느냐에 따라 this의 바인딩 되는 값이 달라진다.

#### 전역 공간에서의 this

- 전역 공간에서 this는 전역 객체를 가리킨다. 브라우저에서는 window, Node.js 에서는 global 객체를 가린키다.
- **전역 공간에서 변수를 선언하면 이는 전역객체(window, global)의 property로 항당**되는데, 자바스크립트의 모든 변수는 실은 특정 객체의 프로퍼티로서 동작한다. 정확히 말하면 **전역변수를 선언하면 자바스크립트 엔진은 이를 전역객체의 프로퍼티로 할당한다.**
- 전역 변수를 선언할때 처음부터 전역객체의 프로퍼티로 할당하면 삭제가 되지만, 전역 변수로 선언하는 경우에는 delete 연산으로 삭제가 되지 않는다.
  
  ``` js
  var a = 1;
  window.b = 1;

  delete a;
  delete window.b;

  console.log(a);         // 1
  console.log(window.b);  // undefined
  ```

#### 메서드로서 호출할 떄 그 메서드 내부에서의 this

- 일단 함수와 메서드의 차이를 이해해야 한다. 둘다 프로그래머가 정의한 동작을 수행하는 코드지만, 함수는 독립적으로 수행할 수 있지만, 메서드는 자신을 호출한 대상 객체에 관한 동작을 수행한다.
- 메서드는 **"객체에 프로퍼티에 할당된 함수"**라는 말은 반은 맞고 반은 틀리다. 함수를 객체의 프로퍼티로 할당한다고 무조건 메서드가 되는 것이 아니라 **객체의 메서드로서 호출할 경우에만 메서드로 동작하고, 그렇지 않은 경우에는 함수로 동작한다.**
  - 함수로서 호출과 메서드로서의 호출은 함수 앞에 점(.)이 있는지를 보거나 함수를 호출 할때 앞에 객체가 명시되어 있는지를 보면 쉽게 확인할 수 있다.

  ``` js
  // 함수로서의 호출, 메서드로서의 호출 비교
  var test = function () {
    console.log("CALL TEST()");
    console.log(this)
  };
  test() // 함수로서 호출, this에 전역객체 호출

  var object = {
    a: 1,
    test: test
  };
  object.test();  // 메서드로서 호출, this에 object가 들어간다.
  ```

- **메서드 내부에서 호출**을 할 경우 **this**에는 **호출한 주체에 대한 정보**가 담긴다.
- 즉 **함수 앞에 명시되어 있는 객체가 곧 this가 된다.**

#### 함수로서 호출할 때 그 함수 내부에서의 this

- **어떤 함수를 함수로서 호출할 경우에는 this가 지정되지 않는다. 다만 this가 지정되지 않으면 this는 전역 객체를 바라보게 된다.**
- 메소드를 호출 하는데 메소드 안에 다시 함수가 있거나 다른 함수를 호출 하는등 여러 복잡한 상황이 있을 수 있다. 하지만 어렵게 생각하지 말자. **해당 함수를 호출하는 구문 앞에 점 또는 대괄호가 있다면 이는 메소드로서 호출(this = 객체) 하는 것이고, 아니라면 이는 그냥 함수(this = 전역객체)이다.**
- 메소드 안에 함수에서 this가 전역객체를 바인딩 하지 않고 주변 환경의 this를 상속받아 사용하고 싶을 때가 있다. 이런 경우에는 다음처럼 사용하자.

  ``` js
  var test = {
    method_func: function() {
      console.log(this); // Method. Object를 바라본다.

      var func1 = function() {  // 함수
        console.log(this);  // 전역객체를 바라본다.
      }

      var self = this;  // self에 this를 할당
      var func2 = function() {
        console.log(self);  // 내부 함수 안에서 this를 사용할 수 있다.
      }
      func1();
      func2();
    },
    var1: 10,
    var2: 20
  }
  test.method_func();
  ```
  
  - 변수를 하나 만든 다음 여기에 this를 할당해서 사용하는 것이다. 간단!

- EX6에서는 함수 내부에서 this가 전역객체를 바라보는 문제를 보완하고자 this를 바인딩 하지 않는 화살표 함수를 새로 도입하였다. **화살표 함수는 실행 컨텍스트를 생성할 때 this 바인딩 과정 자체가 빠지게 되어서, 상위 스코프의 this를 그대로 활용할 수 있다.** this를 할당하지 않아서 스코프 체인을 통해서 this를 사용하는 형태인것이다.
- **콜백함수도 기본적으로 함수이기 때문에 this가 전역객체를 참조한다.** 다만 제어권을 받은 함수에서 콜백 함수에 별도로 this가 될 대상을 지정한 경우에는 대상을 지정한 대상을 참조한다.
- **생성자 함수에서는 new 명령어와 함꼐 함수를 호출하면, this는 새로 만들어진 인스턴스가 this에 할당 된다.**

### 명시적으로 this를 바인딩하는 방법

#### call 메서드

``` js
Function.prototype.call(thisArg[, arg1[, arg2[, ...]]])
```

- call 메서드는 메서드의 호출 주체인 함수를 즉시 실행하도록 하는 명령이다.
- call 메서드의 첫 번째 인자를 this로 바인딩하고, 이후의 인자들은 호출할 함수의 매개변수로 사용한다.
- 즉, 함수를 그냥 실행하면 this에는 전역객체가 할당되지만 call 메서드를 이용하면 임의로 this를 할당 할 수 있는것이다.

``` js
var func = function() {
  console.log(this);
}

func(); // 전역객체가 찍힌다.
func.call({ a: 10, b: 20}); // { a: 10, b: 20}
```

#### apply 메서드

``` js
Function.prototype.apply(thisArg[, argsArray])
```

- call과 완전 동일하다. 다만 apply는 두번째 인자로 배열을 받는데, 해당 배열 요소를 함수의 매개변수로 사용한다.
- apply 메서드의 첫번째 인자를 this로 바인딩하고, 이후 배열에 있는 요소를 함수의 매개변수로 사용한다.

#### bind 메서드

``` js
Function.prototype.bind(thisArg[, arg1[, arg2[, ...]]])
```

- ES5에서 추가된 기능으로, call과 비슷하지만 즉시 호출하지 않고 넘겨 받은 this 및 인수들을 바탕으로 **새로운 함수를 반환한다.**
- bind 메서드는 함수에 this를 미리 적용하는 것과 부분 적용 함수를 구현하는 두 가지 목적으로 사용된다.
- bind 메서드를 사용하여 만들어진 함수의 경우 name 프로퍼티에 bound라는 접두어가 붙는다. 이는 원본함수에서 bind를 메서드를 적용한 새로운 함수라는 의미가 붙은 것이다.

``` js
var func = function() {
  console.log(this);
}

var boundedFunc = func.bind({ a: 10 });
console.log(func.name); // func
console.log(boundedFunc.name);  // bound func
```

### 정리

- 전역공간에서의 this는 전역객체를 참조한다.
- 함수를 메서드로서 호출 할 경우 this는 호출 주체를 나타낸다.
- 함수를 함수로서 호출 할 경우 this는 전역객체를 참조한다.
- 콜백 함수에서는 this는 함수가 함수가 정의한 바에 따르며, 정의하지 않는 경우 전역객체를 참조한다.
- 생성자 함수에서는 this는 생성될 인스턴스를 참조한다.
- call/apply 메서드는 this를 명시적으로 지정할 수 있으며 바로 함수, 메서드를 호출한다.
- bind는 this를 명시적으로 지정할 수 있으며 새로운 함수를 리턴한다.
- 요소를 순회하면서 콜백 함수를 반복 호출하는 내용의 일부 메서드는 별도의 인자로 this를 받기도 한다.