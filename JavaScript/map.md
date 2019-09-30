# Map

Node.js 디자인 패턴을 공부하면서 1장에 Map에 대한 내용이 나와 있어 이를 정리한다. 항상 객체를 이용하여 Map을 만들어 사용하였는데 ES2015부터 보다 안전하고 유연하며 직관적인 방식으로 해시 맵 컬렉션을 사용할 수 설계 되었다고 한다.

``` js
const clients = new Map();
// 키 삽입
clients.set('1', 'jone');
clients.set('100', 'mary');
clients.set('111', 'david');

// Map 길이
clients.size;       // 3

// 해당 키값 존재 유무 확인
clients.has('1');   // true
clients.has('30');  // false

// 삭제
clients.delete('1');
clients.has('1');   // false

// 해당 키에 대한 value 꺼내기
clients.get('1');   // undefined

// 반목
for (const client of clients) {
  console.log(client)
}

// 함수를 키로도 사용 가능하다!!!!
const tests = new Map();
tests.set(() => 2+2, 4);
tests.set(() => 2*2, 4);
tests.set(() => 2/2, 1);

for (const entry of tests) {
  console.log((entry[0]() === entry[1] ? 'PASS' : 'FAIL'))
}
```
