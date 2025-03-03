# [Talks at Google] A Philosophy of Software design

## Course
1. Phase 1
  a. 3주간 2~3천 줄 정도 규모의 시스템을 구현
2. Phase 2
  a. 코드 리뷰를 진행
  b. 코드 리뷰를 바탕으로 수정
3. Phase 3
  a. 다른 시스템을 새롭게 구현

## The Magic Secrets
### Principles
1. 동작하는 코드만으로는 충분하지 않다. 복잡성을 최소화하는 것이 중요하다. - Working code isn't enough: must minimize complexity
2. 복잡성은 의존성과 모호함으로부터 발생한다. - Complexity comes from dependencies and obscurity
3. Strategic vs tactical programming
4. Classes should be deep
5. General-purpose classes are deeper
6. New layer, new abstraction
7. 주석은 코드에서 명확하지 않은 사항을 설명해야 한다. - Comments should describe things that are not obvious from the code
8. 존재하지 않는 오류를 정의하라. - Define errors out of existence
9. Pull complexity downwards

추가로,
- 좋은 설계를 하는 것보다 red flag를 피하는 것이 중요하다

## 1. Classes should be deep
클래스를 사각형이라고 가정해보자.  
이 때, 위쪽의 한 변을 인터페이스라고 상상해보자.  
이 인터페이스는 함수의 시그니처뿐만 아니라 사이드 이펙트와 의존성 등 클래스를 사용하는데에 알아야 하는 모든 것을 포함하는 것으로 정의한다.  
이것은 이 클래스가 전체 시스템에 부과하는 비용이다.  
따라서 비용을 줄이고, 이득을 최대화하는 것이 이상적이다.  

비용이 큰 클래스를 shallow class라고 부른다.  
비용이 작은 클래스는 deep class라고 부른다.  
클래스는 깊어야 한다.  

shallow class는 복잡성에 비해 별로 이득을 가져다주지 않는다.  
인터페이스가 지나치게 복잡하거나, 효용이 적거나, 아니면 둘 다인 때도 있다.  
최악은 클래스가 전체 시스템을 더 복잡하게 만드는 경우다.  

deep class는 복잡하지 않으면서 많은 이득을 제공한다.  
인터페이스는 단순하고, 얻는 효용은 크다.  
이를 잘 추상화되었다고 말한다.  
