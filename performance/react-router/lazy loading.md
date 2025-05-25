## Reference

- [Faster Lazy Loading](https://remix.run/blog/faster-lazy-loading)
- [Split Route Modules](https://remix.run/blog/split-route-modules)

## TL;DR

- v7.5+에서 Framework mode를 사용 중인 경우 최적화가 자동으로 이루어짐
  - (아직 unstable인데 내부적으로 사용중인 건지는 모르겠음)
- Data mode를 사용 중인 경우 최적화를 수동으로 해야 함

## Data mode를 사용 중인 경우 (v7.5.0+)

> data mode에서는 router를 직접 정의 함.  
> 따라서 loader, component를 별도로 분리하고 각각 lazy load 해야 함

1. loader를 사용
2. loader를 Component와는 별도의 파일로 분리
   1. clientLoader는 보통 작은 함수임.
   2. 따라서 코드 스플리팅을 통해 Component보다 먼저 호출될 수 있도록 의도
3. route에서 loader와 component를 각각 lazy load

```javascript
// Before
const route = {
  lazy: () => import("./projects"),
}

// After
const route = {
  lazy: {
    loader: async () => {
      return (await import("./projects/loader")).loader
    },
    Component: async () => {
      return (await import("./projects/component")).Component
    },
  },
}
```

## Framework mode를 사용 중인 경우

### 자동 최적화 시 주의사항 (v7.2.0+ unstable)

- clientLoader와 Component가 공유하는 값이 존재한다면 자동 스플리팅이 실행되지 않음
- 공유하는 값을 별도 파일로 분리하면 자동 스플리팅이 실행됨
- future.unstable_splitRouteModules 옵션을 enforce로 설정하면 공유 값이 감지되었을 때 빌드가 실패하도록 강제할 수 있음
