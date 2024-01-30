## 목표
Node.js 의 핵심 개념인 콜 스택과 이벤트 루프를 시뮬레이션 합니다.

## 이벤트 루프 시뮬레이션
- 이벤트 루프는 Node.js의 비동기 동작을 관리합니다. 이벤트 루프는 콜백 큐에서 작업을 가져와 콜 스택이 비어 있을 때 실행합니다.
- 이벤트 루프를 시뮬레이션하려면 **콜백 큐**를 만들고, **콜 스택이 비어 있을 때 큐에서 함수를 꺼내 실행**하는 로직을 구현해야 합니다.

## 콜 스택 시뮬레이션

- **콜 스택은 함수 호출을 추적**합니다. **함수가 호출되면 스택에 추가**되고, **작업이 완료되면 스택에서 제거**됩니다.
- 콜 스택을 시뮬레이션하려면 함수 호출을 추적하는 스택 구조를 만들어야 합니다.
- JavaScript에서 배열을 사용하여 스택을 구현할 수 있습니다. `.push()`로 요소를 추가하고 `.pop()`으로 제거합니다.

## 설계

### 개요

```js
loop(() => {
	call(function add(a, b) {
		assign('x', a + b)
	}, [1, 2])
	
	call(function sub(a, b)) {
	  assign('y', a - b)
	}, [get('x'), 1])

	event(function addPromise(a, b) {
	  return (resolve) => {
		  setTimeout(() => {
			resolve(a + b)
		  }, 1000)
	  }
	}, function callback(x) {
		console.log(x)
	}, [get('x'), get('y')])
})

// 5

```

#### 루프 함수

##### loop(func: () => void): void

node-like 런타임의 핵심 함수로써 node.js 의 이벤트 루프와 유사한 역할을 수행합니다. 이벤트 루프는 동기 및 비동기 작업들의 실행 순서를 제어합니다. 모든 `제어함수` 는 실행 함수의 `func` 내에서 호출해야 정상 동작합니다.

#### 제어 함수
##### call(func: (...any) => void, arguments: any[]): void

콜 스택에 `func` 를 push 합니다. `arguments` 는 `func` 가 실행될 때 인자로 전달됩니다.

##### assign(identifier: string, value: any): void

변수 `identifier` 에 어떤 `value` 를 할당합니다. 전역 변수를 사용합니다. 변수 할당(`assignemnt`), 함수 반환(`return`) 등을 시뮬레이션하기 위해 사용됩니다.

##### get(identifier: string): any

변수 `identifier` 의 `value` 를 가져옵니다. 변수 값 사용을 시뮬레이션 할 때 사용합니다.

##### event(promise: (...any): void, callback: (...any): void, arguments: any[]): void

`event` 함수는 비동기 함수를 호출을 시뮬레이션 합니다. event 가 정상 종료되려면 `resolve` 함수를 호출해야합니다. `resolve` 함수를 호출하면 콜백 큐에 `callback` 함수를 enqueue 합니다. `callback` 가 호출되면 `resolve` 함수에 전달된 인자가 전달됩니다.

