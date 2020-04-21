# REST와 REST API

## API

- api란, 어플리케이션 사이에서 데이터를 주고받기 위한 방법(인터페이스)
- 특정 데이터를 얻기 위해서 어떤 요청을 해야하는지 정의해둔 것.

---

## REST

### 정의

- URI를 통해 자원을 명시하고 HTTP Method를 통해 해당 자원에 대한 동작을 표현해서 데이터를 주고받는 방식에 일관성을 부여하기 위한 아키텍쳐 스타일입니다.
- 다음 6가지를 준수했을 때 REST 아키텍처라고 할 수 있습니다.
  1. client/server architecture : 클라이언트와 서버로 역할과 의존성을 분리하여 각각 독립적으로 개발한다.
  2. uniform interface : 인터페이스 일관성. URI 명시, http 메서드 사용, self-descriptive message, HATEOS
  3. layered system
  4. stateless
  5. cacheable
  6. code-on-demand

### 장점 = 사용하는 이유

- 직관성, 일관성.
- 행위와 자원으로 표현되기 때문에 직관적이고, 이렇게 일관성 있는 컨벤션을 통해서 쉽게 이해할 수 있다.
- 멀티플랫폼에 유리하다고 하는데 SOAP와 비교를 안 해봐서 잘 모르겠음.

### 주의사항

- URI는 동사보다는 명사를 사용해서 자원을 표현해야 함. 왜냐하면 api정의가 행위 + 자원의 형태로 작성되어야 직관적이고 일관성있기 때문.

### REST api란?

- REST라는 설계를 따른 api.
- 구체적인 형태는 데이터를 json형식이나 xml형식으로 담아서 통신하는 api.

---

### 의문들

### REST는 서버/클라이언트의 의존성을 낮춘다고 하는데..

- 의존성이 높다는 것의 구체적인 예시는 어떤걸까? 잘 모르겠다.

### REST가 아닌 것은 대체 어떤거지?

- REST를 이해하는데에 어려웠던 부분은 `REST가 아닌 것은 대체 어떤거지?` 라는 의문이었음. http 메서드로 동작을 표현하고, uri로 자원을 명시하는 방식이 너무 당연하다고 생각했기 때문에.
- 하지만 RESTful하지 않은 api도 존재할 수 있음. 예를들면 URL은 1개 뿐이고 body에 필요한 자원에 대한 정보를 담아서 처리하는 방식도 존재할 수 있음. 또는 URI에 동사형 행위가 포함되어 구분하는 경우도 존재할 수 있음.

```
//이런 api는 RESTful 하지 않음.
예약 생성 : POST /reservation/{UUID}?cmd=insert
예약 수정 : POST /reservation/{UUID}?cmd=update
예약 조회 : POST /reservation/{UUID}?cmd=select
예약 취소 : POST /reservation/{UUID}?cmd=delete
```

- 이 외에도 SOAP 같은 방식이 있다고 함. XML기반 통신이라고 함. 복잡하고 무거운 단점이 있음.

### REST의 의미를 몰라도 개발할 수 있는데?

- 또 하나 들었던 의문은, `왜 REST 같은걸 알아야하는가? REST의 의미를 몰라도 개발할 수 있는데?` 였다. 이 의문에 대한 답은 아직 완전히 와닿지는 않지만 `일관적인 컨벤션을 통한 API의 이해도를 높이기 위해서` 라는 말이 가장 그럴듯하다고 생각한다.  
  협업을 위해서라면 다른 사람의 코드라고 하더라도 최대한 빠른 시간 내에 이해가 가능해야한다. 그러므로 널리 알려진 컨벤션을 잘 이해해서 코드에 적용하고, 이를 통해 팀 전체의 생산성을 높이기 위한 것이라고 생각하니 조금은 이해가 간다.
  > _API 333 목표 : 3초만에 API를 이해하고, 30초만에 API 키를 발급 받아서,_ > _3분안에 첫번째 요청이 오도록 해라_

---

### REST 설계 팁

1. 어플리케이션에서 필요한 자원 별로 URI가 존재한다.
2. URI는 명사로 구성한다.
   `/reservation/001/activate`보다 `/reservation/001/status`가 바람직
3. 요청의 동작을 http method로 구분한다.
4. 응답에 대한 메타데이터를 헤더로 표현한다.
   body에는 순수하게 resource자체만 담겨야한다.

---

### 참고자료

- [당신의 API가 Restful 하지 않은 5가지 증거 – BeyondJ2EE](https://beyondj2ee.wordpress.com/2013/03/21/%EB%8B%B9%EC%8B%A0%EC%9D%98-api%EA%B0%80-restful-%ED%95%98%EC%A7%80-%EC%95%8A%EC%9D%80-5%EA%B0%80%EC%A7%80-%EC%A6%9D%EA%B1%B0/)
- [Network REST란? REST API란? RESTful이란? - Heee’s Development Blog](https://gmlwjd9405.github.io/2018/09/21/rest-and-restful.html)
- [WEB REST는 웹의 독립 된 진화 · linked2ev](https://linked2ev.github.io/devlog/2019/10/06/WEB-REST-is-the-independent-evolution-of-the-web/)
- [WEB RESTful 디자인 · linked2ev](https://linked2ev.github.io/devlog/2019/12/28/WEB-RESTful-design/)
