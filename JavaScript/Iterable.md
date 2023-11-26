# Iterable

**이터러블(Iterable)**이란 이터레이션 프로토콜(Iteration Protocol)을 준수한 객체를 말한다.

## Iteration Protocol

Symbol.iterator 메서드를 호출하면 이터레이터가 반환되는 것을 **이터레이션 프로토콜**이라고 한다.

ES6에서 도입된 이터레이션 프로토콜(Iteration Protocol)은 순회 가능한(Iterable) 데이터 컬렉션(자료구조)을 만들기 위해 ECMAScript 사양에 정의하여 미리 약속한 규칙이다.

ES6 이전의 순회 가능한 배열, 문자열 등은 통일된 규약 없이 각자 나름의 구조를 가지고 for문, for...in문, forEach 메서드 등 다양한 방법으로 순회시킬 수 있었다.

ES6에서는 순회 가능한 데이터 컬렉션을 **이터레이션 프로토콜**을 준수하는 이터러블로 통일하여 for...of문, 스프레드 문법, 배열 디스트럭처링 할당을 할 수 있도록 일원화했다.

이터레이션 프로토콜에는 이터러블 프로토콜과 이터레이터 프로토콜이 있다.

### Iterable Protocol

### Iterator Protocol

## 일반 객체를 이터러블로 만드는 방법

빌트인 이터러블이 아닌 일반 객체를 이터러블처럼 동작하도록 구현하려면 이터레이션 프로토콜을 따르면 된다.

1. Well-known Symbol인 Symbol.iterator를 키로 갖는 메서드를 객체에 추가한다.
2. 그 Symbol.iterator를 호출했을 때 이터레이터를 반환하도록 구현하기
