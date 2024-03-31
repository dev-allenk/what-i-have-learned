## 모듈

- 브라우저에서 import, export 지시자를 사용하려면 <script type="module">같은 속성이 필요하다.
- 모듈은 자신만의 스코프를 갖는다. A 모듈에서 정의한 변수를 B 모듈에서 그냥 접근할 수는 없다. export를 해야 접근 가능.
- 모듈은 항상 defer 속성을 붙인것과 같이 지연 실행된다. 덕분에 모듈 스크립트는 항상 완전한 HTML 페이지를 '볼 수' 있고 문서 내 요소에도 접근할 수 있다.
- 번들러는 이런 모듈을 하나 혹은 여러개의 파일로 합치는 도구. 이 때, import, export는 특별한 번들러 함수로 대체되기 때문에 `type='module'`이 필요 없어짐

## reference

- [모듈](https://ko.javascript.info/modules-intro)
