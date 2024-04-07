[feature sliced design 번역](https://emewjin.github.io/feature-sliced-design/)  
[예제 - nukeapp](https://github.com/noveogroup-amorgunov/nukeapp/tree/main)

## 구조

```
src
├── app
│   ├── appRouter.tsx // router
│   └── types.ts
├── entities
│   ├── cart
│   └── user(=auth)
├── features
│   ├── addToCart
│   └── authenticate
│       ├── login
│       └── logout
├── pages
├── shared
│   ├── components(=ui)
│   ├── constants
│   ├── helpers(=utils)
│   └── hooks
└── index.tsx
```

## app

- 애플리케이션 로직이 초기화되는 곳
- 프로바이더, 라우터, 전역 스타일, 전역 타입 선언 등을 정의
- 애플리케이션의 진입점

## features

- 비즈니스 가치를 전달하는 사용자 시나리오와 기능
- 좋아요, 리뷰 작성, 제품 평가 등

## entities

- 비즈니스 엔티티를 나타냅니다
- 사용자, 리뷰, 댓글 등

## shared

- 특정 비즈니스 로직에 종속되지 않은 재사용 가능한 컴포넌트와 유틸리티
- UI 키트, axios 설정, 애플리케이션 설정, 비즈니스 로직에 묶이지 않은 헬퍼 등
