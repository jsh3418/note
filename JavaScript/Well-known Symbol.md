# Well-known Symbol

- 자바스크립트가 기본 제공하는 빌트인 심벌 값을 말한다.
- 빌트인 심벌 값은 Symbol 함수의 프로퍼티에 할당되어 있다.(length와 prototype 제외)

  ![Symbol 함수의 프로퍼티](./refFiles/Symbol%20Properties.png)

- 자바스크립트 엔진에 상수로 존재한다.
- 자바스크립트 엔진의 내부 알고리즘에 사용된다.
  - Symbol.iterator
    - 어떤 객체가 Symbol.iterator를 프로퍼티 key로 가지고 있으면 자바스크립트 엔진은 [이 객체가 이터레이션 프로토콜을 따르는 것](Iterable.md)으로 간주하고 이터레이터로 동작한다.
  - Symbol.for
  - ...etc

## REF

[7번째 타입 심볼 - Poiemaweb](https://poiemaweb.com/es6-symbol)
