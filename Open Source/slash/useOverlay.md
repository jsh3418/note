https://www.slash.page/ko/libraries/react/use-overlay/src/useOverlay.i18n
useOverlay는 Overlay를 선언적으로 다루기 위한 유틸리티이다.

Overlay는 BottomSheet와 Dialog(모달)처럼 별도의 UI 레이어를 띄우는 컴포넌트를 말함.

### 사용법 살펴보기
#### isOpen
open을 호출했을 때 isOpen이 true로 바뀐다. 이 값을 이용해서 Overlay에 띄울 컴포넌트의 open 상태를 관리한다.
### close
이 함수를 호출하면 isOpen이 false로 바뀐다. Overlay가 unmount된다.
### exit
이 함수를 호출하면 해당 Overlay가 unmount 된다.
### options

## close와 exit
close와 exit이 분리되어 있는 이유는 Overlay를 닫으면서 fade-out 애니메이션을 주고 싶을 때 close와 동시에 unmount 시켜버리면 애니메이션이 먹히기 때문이라고 설명되어 있다.

### 내부 구현체 살펴보기
