[참고 링크](https://medium.com/@mbleigh/when-should-i-server-side-render-c2a383ff2d0f)



## SSR이 필요없는 경우
### 1. 콘텐츠를 보기 위해 로그인이 필요한 경우(예: 이메일)  
- SSG나 SSR caching으로 인한 성능 이득을 보기 어렵다.  
- 어차피 이런 경우는 SEO도 필요하지 않다.
  
### 2. 상호작용을 위한 페이지인 경우(예: 메모 입력 페이지)  
- 렌더링 직후에 상호작용이 되지 않는 불편함을 느낄 수도 있다.  
   

## SSR이 필요한 경우
### 1. SEO가 필요한 경우
- 대안이 없다.
