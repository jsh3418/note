# Ambient Modules

## What are ambient modules?

**Ambient modules**는 자바스크립트로 구현된 라이브러리를 가져와 사용할 때 마치 타입스크립트로 작성한 것처럼 원활하고 안전하게 사용할 수 있도록 해주는 타입스크립트 기능이다.

Ambient declaration 파일은 모듈의 타입을 설명하지만 구현 내용은 포함하지 않는 파일이다.

Ambient declaration 파일들은 transpile이 되지 않고, 그래서 그것들은 자바스크립트로 변환되지 않는다. 타입 안전성과 IntelliSense를 위해서만 사용된다. `d.ts` 파일 형식을 따른다.

타입스크립트 생태계는 `DefinitelyTyped`를 통해 사용할 수 있는 수천개의 ambient declarations 파일들을 포함하고 있다. DefinitelyTyped는 타입스크립트 커뮤니티에서 기여하고 유지하는 선언 파일들이 포함된 저장소이다.``

당신의 프로젝트 안에서 아래와 같은 명령어를 입력한다면:

`npm install —save-dev @types/node`

DefinitelyTyped을 사용한 것입니다.

유명한 자바스크립트 라이브러리의 타입들은 대부분 DefinitelyTyped에 존재합니다. 그러나, DefinitelyTyped에 없는 자바스크립트 라이브러리를 사용한다면, 언제든지 ambient modules를 작성할 수 있다. 타입 정의는 외부 라이브러리의 모든 코드 줄에 대해 반드시 수행될 필요는 없으며 사용한 부분에 대해서만 수행된다.

## How to use ambient modules

타입스크립트에서 declaration modules를 사용할 수 있는 주요 방법 세가지가 있다.

- 삼중 슬래시 지시문을 사용한 선언 가져오기
- `tsconfig` 파일에 `typeRoots` 필드를 구성한다.
- `tsconfig` 파일에 `paths` 필드를 구성한다.

삼중 슬래시 지시문은 컴파일러가 컴파일 과정에 추가 파일을 포함하도록 지시하는 XML 태그가 있는 한 줄 주석이다. 보통 이런식으로 생겼다.

`/// <reference path="../types/sample-module/index.d.ts" />`

Note: 하나의 슬래시 지시문은 파일 상단에 위치 해야한다. Import문 아래에 넣으면 주석처리 취급된다.

tsconfig 파일의 typeRoots 필드는 선언 파일이 포함된 디렉터리를 지정하는 데 사용된다. 컴파일러는 컴파일 과정에 포함할 타입스크립트 선언 파일을 찾기 위해 디렉토리들을 검색할 것이다.

Note: 지정하는 디렉토리는 tsconfig 파일의 위치를 기준으로 합니다.

```ts
{
  "compilerOptions": {
    "typeRoots": ["./types"]
  }
}
```

tsconfig 파일의 paths 필드는 TypeScript가 사용자가 제공한 정규식 패턴을 기반으로 TypeScript 파일을 찾아야 하는 특정 경로를 지정합니다.

```ts
{
  "compilerOptions": {
    "paths": {
      "sample-module": ["../types/sample-module"]
    }
  }
}
```

## Tips when working with ambient modules

### paths를 사용하여 사용자 정의 타입을 적용하세요.

사용자 정의가 있는 모듈에 대해 사용자 정의된 유형 정의를 사용하려는 경우 경로에 조정을 추가하여 컴파일러 옵션을 재정의할 수 있습니다.

```ts
{
  "compilerOptions": {
    "paths": {
      "npm-module": ["../types/npm-module"]
    }
  }
}
```

결과적으로 타입스크립트는 외부 라이브러리에서 제공하는 타입이 아닌 사용자 정의 모듈에서 가져온 타입을 사용한다.

### import 선언시 어떻게 선언하느냐에 따라 다르다.

라이브러리에 대한 타입스크립트 정의를 생성할 때 모든 import 선언을 모듈 선언 내부에 위치시키는 것이 중요하다. 그렇지 않으면 선언 모듈이 기능 보강(argumentation)으로 처리되어 사용자 정의 타입이 등록되지 않는다.

```ts
// 잘못된 사용 예시
import * as React from "react";

declare module "react-beautiful-dnd" {
  function Draggable(): JSX.Element;
}
```

위는 정상적으로 동작하지 않는다. 모듈 내부에서 import를 선언해야 한다.

```ts
declare module "react-beautiful-dnd" {
  import * as React from "react";
  function Draggable(): JSX.Element;
}
```

### 단축 ambient declaration을 사용하여 빠르게 타입을 수정하기

라이브러리 코드를 사용할 수 있도록 선언을 만드는 데 시간을 낭비하고 싶지 않다면 대신 단축 ambient declaration을 사용할 수 있습니다.
`declare module “sample-module”;`

단축 ambient declaration을 사용하는 경우 해당 모듈의 모든 import는 any 타입이 된다.

## REF

[Ambient Modules in TypeScript: What they are and how to work with them](https://isamatov.com/typescript-ambient-module/)
