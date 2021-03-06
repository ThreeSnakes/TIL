# Chapter2. Node.js 필수 패턴

## 작성 날짜: 2019.09.30

## 스터디 날짜: 2019.09.30

### 목차

- [콜백 패턴](#콜백-패턴)
- [모듈 시스템과 그 패턴](#모듈-시스템과-그-패턴)
- [관찰자 패턴](#관찰자-패턴)

### 콜백 패턴

- 동기식 프로그램은 특정 문제를 해결 하기 정의된 일련의 연속적인 연산 단계들로 코드를 작성한다. 하지만 Node.js처럼 비동기 프로그래밍은 일부 작업을 백그라운드에서 동작 시킬 수 있다. 비동기 작업은 호출 되면 이전 작업이 완료되지 않았더라도 다음 작업이 즉시 실행된다. 그리고 이러한 비동기 호출이 완료되면 적절한 방식으로 이를 처리하도록 해야 한다. 이러한 비동기 특성을 받아들이고 이해 하고 넘어가야 한다. 비동기 패턴 중 가장 중요한 `콜백 패턴`과 `이벤트 이미터`를 살펴 보자.
- `콜백 패턴`
  - 콜백은 작업 결과를 전달하기 위해 호출 되는 함수이며, 비동기 작업을 처리할 때 반드시 필요하다
  - JavaScript는 다음과 같은 이유때문에 콜백을 표현할 수 있는 훌륭한 언어이다
    - 함수가 일급 객체이다. 함수를 반환하거나 인자로 넘길수 있다
    - 클로저. 클로저를 사용하면 콜백이 어디서 호출되는 지에 관계없이 비동기 작업이 요청된 컨텍스트를 유지할수 있기 때문이다

  ``` js
  // 콜백 패턴 예제

  // 동기식 연속 전달 방식
  // 일반적인 함수
  function add(a, b) {
    return a + b;
  }

  // 이를 콜백 스타일로 바꾼다면.. 다음과 같은 형태가 된다.
  function add(a, b, callback) {
    callback(a +b);
  }

  // 동기식 연속 전달 방식 예제
  console.log("BEFORE");
  add(1, 2, result => console.log('RESULT: ' + result));
  console.log("AFTER");

  //  위 코드느 아래처럼 실행 될 것이다.
  //  BEFORE
  //  RESULT: 3
  //  AFTER

  // 비동기식 연속 전달 방식
  // setTimeout은 비동기 함수이다. 이를 이용하여 테스트해보자.
  function additionalAsync(a, b, callback) {
    setTimeout(() => callback(a + b), 100);
  }

  // 비동기식 연속 전달 방식 예제
  console.log("BEFORE");
  additionalAsync(1, 2, result => console.log('RESULT: ' + result));
  console.log("AFTER");

  // 위 코드는 아래처럼 실행 될 것이다.
  //  BEFORE
  //  AFTER
  //  RESULT: 3

  // additionalAsync함수보다 AFTER가 먼저 찍힌 이유를 알아야 한다.
  // 비동기 함수는 차례대로 실행 되지 않는다.비동기 함수는 백그라운드로 넘어간뒤 바로 리턴되며
  // 그 아래에 있는 console.log("AFTER")가 호출 된다.
  // 그 다음 additionalAsync callback이 호출되어 RESULT가 찍힌다.
  ```

  - 비동기 작업이 완료되면 실행은 비동기 함수에 제공된 콜백에서부터 다시 계속된다. 실행은 이벤트 루프에서 시작되기 때문에 새로운 스택을 갖게 된다
  - 콜백 함수를 사용할때 어떤 함수가 특정 조건에서는 동기/비동기 함수를 따로 리턴하게 되면 안된다. 이런 경우 발견하기 어렵고 재현이 불가능한 버그가 생길수 있다. 그렇기 떄문에 API의 동기 또는 비동기 특성은 명확하게 정의하여 함수를 작성하는 것은 필수적이다
  - JavaScript에서 동기 API는 이벤트 루프를 블록하고 동시에 요청을 보류한다. JavaScript 동시성 모델을 깨뜨려서 전체 어플리케이션 속도를 떨어 뜨리기 때문에 비동기 함수로 작성하는 것이 좋다
  - 동기 함수를 비동기 함수로 만드는 방법은 다음과 같은 방법으로 만들 수 있다
    1. **Process.nextTick()**
        - 이벤트 루프의 다음 사이클까지 함수의 실행을 지연시킨다. 다만 I/O이벤트들의 맨 앞에서 실행된다
    2. **SetImmediate()**
        - 마찬가지로 다음 사이클까지 함수를 지연 시키지만 이미 큐에 있는 I/O 이벤트들의 뒤에 대기 하게 된다
    - **nextTick()**은 재귀 호출과 같은 특정 상황에서 I/O 기아를 일으키기 때문에 상황에 맞추어 잘 사용해야 한다
  - 콜백 규칙
    - 콜백은 맨 마지막에 작성한다
    - 콜백 함수에서 오류는 맨 앞에 리턴한다
    - 에러를 발생했을때는 바로 return하여 코드가 추가로 실행되는 것을 막는다
  - 어플리케이션은 예외가 이벤트 루프에 도착하는 순간 중된다. 어플리케이션이 중단 되는 것을 막고 자원을 정리하기 위해 다음과 같은 코드를 사용할 수있다.

    ``` js
    // Node.js는 이벤트 루프에 예외가 도착하면 프로세스를 종료 하기전에 uncaughtException라는
    // 특수 이벤트를 내보낸다. 이를 catch해서 사용하면 프로세스를 무작정 종료 하는것을 막을 수 있다.
    // 다만. 해당 코드는 사용하지 않는것이 좋으며 로그정도를 확인하고 process를 죽이는게 좋다.
    process.on('uncaughtException', (err) => {
      console.error('err: ', err);

      process.exit(1) // 종료 시킨다
    })
    ```
