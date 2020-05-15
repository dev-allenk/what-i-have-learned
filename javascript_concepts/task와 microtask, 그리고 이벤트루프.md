## 이벤트루프

- 모든 코드를 스케쥴링하고 실행하는데에 사용됨.
- tasks 와 microtasks를 관리함.

## Tasks

- task란, 일반적인 방식으로 실행되는 자바스크립트 코드를 말하며, 최초로 실행되는 전역 코드, 이벤트에 의해 실행되는 콜백, 그리고 interval 과 timeout이 있다. 이러한 코드는 task queue에 의해 순서대로 관리된다.
- task는 다음과 같은 상황에 task queue에 추가된다.
  1. 새로운 자바스크립트 프로그램이 실행될 때(스크립트 엘리먼트에 의한 코드실행)
  2. 이벤트가 발생할 때, 콜백 함수를 task queue에 추가한다.
  3. `setTimeout()`이나 `setInterval()`에 의해 생성된 timeout이나 interval이 해당 시점에 도달했을 때, 콜백함수를 task queue에 추가한다.
- 이벤트 루프는 이러한 task 를 등록된 순서대로 실행하며, 하나의 task가 완전히 실행되고나면 다음 task를 실행한다.

## Microtasks

- task가 종료되고 콜스택이 비워지면, microtask queue에 대기중인 모든 microtask를 하나씩 실행한다. 이 작업은 microtask queue가 완전히 비워질 때까지 계속 된다.
- 만약 어떤 microtask가 실행 도중 새로운 microtask를 생성하여 microtask queue에 등록하게 되면 해당 microtask도 이어서 실행된다. 따라서 microtask 내부에서 재귀적으로 microtask를 생성하면 task의 실행이 블로킹 되므로 주의를 기울여야 한다.
