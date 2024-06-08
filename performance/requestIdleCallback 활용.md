## requestIdleCallback 활용하여 초기 렌더링 단축하기
- 참고 링크 : [requestIdleCallback으로 초기 렌더링 시간 14% 단축하기](https://engineering.linecorp.com/ko/blog/line-securities-frontend-4)

초기 렌더링 속도를 높이기 위해 페이지 하단의 컨텐츠를 지연 로딩할 수 있음  
```typescript
import { lazy, Suspense } from "react";
import MainContents from "./mainContents";
  
const OtherContents = lazy(() => import('./otherContents'));
  
const Home: React.VFC = () => {
  return (
    <div>
      <MainContents />
      <Suspense fallback={<p>Loading...</p>}>
        <OtherContents />
      </Suspense>
    </div>
  );
};
```
이 때, 청크로 분리된 스크립트를 import해서 script 태그로 추가하는 웹팩의 동작이 초기 렌더링 시 포함되면서 불필요한 지연을 발생시킴. (블로그에선 React.lazy 로 인한 지연도 있다고 하는데 이해를 못 했음)  
import 동작을 requestIdleCallback을 통해 지연 시킬 수 있음. safari에선 지원하지 않기 때문에 [polyfill](https://www.npmjs.com/package/requestidlecallback-polyfill) 사용 필요.  
```typescript
import { lazy } from 'react'
  
export const lazyIdle: typeof lazy = (factory) => {
  return lazy(
    () =>
      new Promise((resolve) => {
        window.requestIdleCallback(() => resolve(factory()), {
          timeout: 3000
        })
      })
  )
}
```

## requestIdleCallback 활용하여 분석 데이터 전송
- [requestIdleCallback 활용하여 분석 데이터 전송](https://developer.chrome.com/blog/using-requestidlecallback?hl=ko#using_requestidlecallback_for_sending_analytics_data)
- [mdn 예제](https://developer.mozilla.org/ko/docs/Web/API/Background_Tasks_API#javascript_content)
```typescript
var eventsToSend = [];

function onNavOpenClick () {
    // Animate the menu.
    menu.classList.add('open');

    // Store the event for later.
    eventsToSend.push(
    {
        category: 'button',
        action: 'click',
        label: 'nav',
        value: 'open'
    });

    schedulePendingEvents();
}

function schedulePendingEvents() {
    // rIC가 이미 스케쥴링 되지 않은 경우에만 스케쥴을 추가합니다.
    if (isRequestIdleCallbackScheduled)
    return;

    isRequestIdleCallbackScheduled = true;

    if ('requestIdleCallback' in window) {
    requestIdleCallback(processPendingAnalyticsEvents, { timeout: 1000 });
    } else {
    // rIC를 지원하지 않는 경우 즉시 전송하는 예시. 일반적으로는 폴리필을 사용해서 적절히 지연시키는 것이 더 나을 수 있음
    processPendingAnalyticsEvents();
    }
}

function processPendingAnalyticsEvents (deadline) {
    // Reset the boolean so future rICs can be set.
    isRequestIdleCallbackScheduled = false;

    // rIC를 지원하지 않는 경우, 바로 실행
    if (typeof deadline === 'undefined')
    deadline = { timeRemaining: function () { return Number.MAX_VALUE } };

    // 작업이 남아있고, 여유가 있는 경우 실행
    while (deadline.timeRemaining() > 0 && eventsToSend.length > 0) {
    var evt = eventsToSend.shift();

    ga('send', 'event',
        evt.category,
        evt.action,
        evt.label,
        evt.value);
    }

    // Check if there are more events still to send.
    if (eventsToSend.length > 0)
    schedulePendingEvents();
}
```
