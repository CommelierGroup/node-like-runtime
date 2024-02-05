## 목표

Node.js 의 핵심 구조인, 이벤트 루프와 콜 스택을 시뮬레이션 합니다.

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

	const evtId = event(function addPromise(a, b) {
	  return (resolve, reject) => {
		  setTimeout(() => {
			resolve(a + b)
		  }, 1000)
	  }
	}, function onSuccess(x) {
		console.log(x * 2) // 10
	}, function onFailure(e) {
		console.error(e)
	}, [get('x'), get('y')])
	
	wait(evtId, function callback(x) {
		console.log(x) // 5
	})
})

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

##### event(promise: (...any): void, onSucess: (...any): void, onFailure: (error: any): void arguments: any[]): Symbol

`event` 함수는 비동기 함수를 호출을 시뮬레이션 합니다. event 가 정상 종료되려면 `resolve` 함수를 호출해야합니다. `resolve` 함수를 호출하면 콜백 큐에 `callback` 함수를 enqueue 합니다. `callback` 가 호출되면 `resolve` 함수에 전달된 인자가 전달됩니다.

#### 비동기 함수

##### wait(evtId: Symbol, callback(...any): void): void

JavaScript 의 `await` 호출을 시뮬레이션 합니다. `event` 에서 반환된 Symbol 을 첫번째 인자로 넣습니다.

## 구현

구현은 `VanillaJS` + `Node.js Built-ins` 만 사용합니다.

Code Documentation 은 `JSDoc` 을 사용합니다.

TDD 를 기반으로 높은 커버리지를 달성하는 것을 목표로 합니다.

효율적인 테스팅을 위해, [vitest](https://vitest.dev/) 라이브러리를 사용하고, `jest` 컨벤션을 따릅니다.

## 개선 사항

결과 뿐만 아니라 실제 내부 로직도 Node.js 방식을 지향합니다.

100% 동일하게 구현하진 못하겠지만, 실제 [node](https://github.com/nodejs/node) 코드를 보고 점차 개선시켜 나가봅시다.
