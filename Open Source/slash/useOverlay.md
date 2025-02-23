https://github.dev/toss/slash
https://www.slash.page/ko/libraries/react/use-overlay/src/useOverlay.i18n

useOverlay는 Overlay를 선언적으로 다루기 위한 유틸리티입니다.

>- Overlay란? BottomSheet과 Dialog처럼 별도의 UI 레이어에 띄우는 컴포넌트

>- 사용하기 위해선 `_app.tsx` 에 `<OverlayProvider />`를 추가해야 합니다.

내부에서 context를 사용하고 있기 때문에 추가해야 한다.

>- useOverlay를 여러 번 호출해서 여러 개의 Overlay를 만들 수 있습니다.

Overlay를 Map으로 관리하여 여러 개의 Overlay를 만들고 관리할 수 있다.

>- Promise와 함께 사용할 수 있습니다.

Promise를 반환하여 콜백 함수의 로직을 기다리게 한 뒤 사용자의 행동에 따라 조건부로 로직을 수행할 수 있다.
```ts
import { OverlayProvider } from '@toss/use-overlay';

export default function App({ Component, pageProps }: AppProps) {
  return (
    <OverlayProvider> // OverlayProvider를 최상위 컴포넌트에 적용
      <Component {...pageProps} />
    </OverlayProvider>
  );
}

// Page.tsx
import { useOverlay } from '@toss/use-overlay';

const overlay = useOverlay();
const openFooConfirmDialog = () => {
  return new Promise<boolean>(resolve => {
    overlay.open(({ isOpen, close }) => (
      <FooConfirmDialog
        open={isOpen}
        onClose={() => {
          resolve(false);
          close();
        }}
        onConfirm={() => {
          resolve(true);
          close();
        }}
      />
    ));
  });
};

await openFooConfirmDialog(); // 사용자가 Dialog을 닫았을 때 false를 리턴, 확인을 눌렀을 때 true를 반환하여 그 뒤로 조건부 로직을 구현할 수 있다.

// ConfirmDialog의 confirmButton을 누르거나 onClose가 호출된 후
console.log('dialog closed');
```

## 사용법 살펴보기
### isOpen
>open()을 호출했을 때 isOpen이 true로 바뀝니다. 이 값을 이용해서 Overlay에 띄울 컴포넌트의 open 상태를 관리합니다.
### close
>이 함수가 호출되면 isOpen이 false로 바뀝니다. 주로 Overlay로 띄울 컴포넌트의 onClose 함수에 이 함수를 주입합니다.
### exit
>이 함수가 호출되면 해당 Overlay가 unmount됩니다.
>close와 exit이 분리되어 있는 이유는 Overlay를 닫으면서 fade-out 애니메이션을 주고 싶을 때 close와 동시에 unmount시켜버리면 애니메이션이 먹히기 때문입니다.
### options
>useOverlay를 호출한 컴포넌트가 unmount 되면 overlay도 같이 unmount(=exit) 됩니다.  
exitOnUnmount의 값을 false로 설정하였다면 useOverlay를 호출한 컴포넌트가 unmount 되어도 overlay가 같이 unmount 되지 않습니다.  
따라서 원하는 타이밍에 overlay의 exit 함수를 직접 실행하여 overlay를 unmount 시킬 수 있습니다. exit 함수를 실행시키지 않는다면  
등록된 overlay가 메모리 상에 계속 남아있게 됩니다. exitOnUnmount의 값을 false로 설정하였다면 반드시 exit 함수를 실행시켜주세요.

보통의 경우 오버레이를 부른 화면이 언마운트 되면 오버레이도 같이 언마운트 되는 것이 일반적이라서 기본 값을 true로 설정한 것이라고 생각함

## 내부 구현체 살펴보기
toss/slash/packages/react/use-overlay
### 1. useOverlay
1. 오버레이의 id 생성
2. exitOnUnmount 옵션을 통해 overlay를 호출한 컴포넌트가 언마운트 될 때 해당 overlay가 같이 언마운트 될 것인지를 선택할 수 있게 함
3. 훅 사용처에 open, close, exit을 노출
```ts
let elementId = 1;
```
#### elementId
Overlay를 관리하는 Map에 key로 사용된다. 오버레이가 새로 생길 때마다 elementId의 값이 증가된다.


```ts
interface Options {
	exitOnUnmount?: boolean;
}

export function useOverlay({ exitOnUnmount = true }: Options = {}) {
	...
	useEffect(() => {
		return () => {
			if (exitOnUnmount) {
				unmount(id);
			}
		};
	}, [exitOnUnmount, id, unmount]);
	...
```
#### exitOnUnmount
useEffect의 클린업 함수에 unmount가 호출되도록 구현, exitOnUnmount를 false로 줄 경우 unmount가 호출되지 않아 해당 오버레이는 호출한 컴포넌트가 언마운트가 되어도 언마운트 되지 않는다.

### 2. OverlayProvider
1. overlayById 상태 -> 렌더링 관리(마운트, 언마운트)
2. useOverlay 훅이 mount, unmount를 사용할 수 있도록 함
3. overlayById 키 값에 따라 overlay를 렌더링
```ts
const [overlayById, setOverlayById] = useState<Map<string, ReactNode>>(new Map());
const mount = useCallback((id: string, element: ReactNode) => {
	setOverlayById(overlayById => { const cloned = new Map(overlayById);
		cloned.set(id, element);
		return cloned;
	});
}, []);
```
overlayById 상태를 선언, 여러개의 오버레이를 띄울 수 있도록 Map 객체를 사용
##### 왜 Map을 사용했는지? (객체도 있는데)
1. 객체는 순서를 보장하지 않는다. Map은 순서 보장이 된다. Overlay가 나중에 렌더링 될수록 위쪽에 떠야한다.(쌓임 맥락에 따라 나중에 추가된 Overlay가 더 위로 보여진다.)
2. useOverlay 특성상 값 변경이 잦아 비교적 효율적으로 값 변경이 되는 Map을 사용
##### new Map(oldMap)을 호출하면 어떻게 동작하는가? 왜 이렇게 사용했는지?
기존 Map의 모든 데이터를 새로운 Map 인스턴스로 복사한다.
React의 useState에서 Map을 직접 수정하면 상태 변경이 감지되지 않는다.(리렌더링이 되지 않음)

### 3. OverlayController
1. isOpenOverlay를 별도로 관리(직접적인 렌더링에 관여하지 않음) -> 오버레이의 애니메이션을 위한 상태값
2. OverlayElement를 받아 isOpen, close, exit를 주입

## 나의 생각
isOpen과 close는 렌더링에 직접 관여하지 않고 애니메이션을 위한 값이다. open 함수로 컴포넌트를 넣었을 때 elementId를 증가시켜 아이디로 사용하고 이를 OverlayProvider에서 렌더링하고 있기 때문