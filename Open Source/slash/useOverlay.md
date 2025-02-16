https://www.slash.page/ko/libraries/react/use-overlay/src/useOverlay.i18n
useOverlay는 Overlay를 선언적으로 다루기 위한 유틸리티이다.
Overlay는 BottomSheet와 Dialog(모달)처럼 별도의 UI 레이어를 띄우는 컴포넌트를 말함.

## 사용법 살펴보기
### isOpen
open을 호출했을 때 isOpen이 true로 바뀐다. 이 값을 이용해서 Overlay에 띄울 컴포넌트의 open 상태를 관리한다.
### close
이 함수를 호출하면 isOpen이 false로 바뀐다. Overlay가 unmount된다.
### exit
이 함수를 호출하면 해당 Overlay가 unmount 된다.
### options

## close와 exit
close와 exit이 분리되어 있는 이유는 Overlay를 닫으면서 fade-out 애니메이션을 주고 싶을 때 close와 동시에 unmount 시켜버리면 애니메이션이 먹히기 때문이라고 설명되어 있다.


## 내부 구현체 살펴보기
### 1. OverlayProvider (toss/slash/packages/react/use-overlay/src/OverlayProvider.tsx)
- mount, unmount를 Context로 프로바이더 내부 컴포넌트에서 사용할 수 있도록 구현
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
### 왜 Map을 사용했는가?
##### 객체를 사용하지 않고 Map을 사용한 이유?
1. 객체는 순서를 보장하지 않는다. Map은 순서 보장이 된다. Overlay가 나중에 렌더링 될수록 위쪽에 떠야한다.(쌓임 맥락에 따라 나중에 추가된 Overlay가 더 위로 보여진다.)
2. 
##### new Map(oldMap)을 호출하면 어떻게 동작하는가?
기존 Map의 모든 데이터를 새로운 Map 인스턴스로 복사한다.
##### 왜 이렇게 사용했는지?
React의 useState에서 Map을 직접 수정하면 상태 변경이 감지되지 않는다.(리렌더링이 되지 않음)
