# [Talks at Google] A Philosophy of Software design

## Course - 각 페이즈는 3주간 진행
1. Phase 1  
  a. 2~3천 줄 정도 규모의 시스템을 구현  
2. Phase 2  
  a. 코드 리뷰를 진행 - 리뷰는 '설계'에 관하여  
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
세부 구현을 조금만 바꾸더라도 인터페이스를 바꾸어야 할 확률이 높은 경우 shallow class에 가깝다.  

deep class는 복잡하지 않으면서 많은 이득을 제공한다.  
인터페이스는 단순하고, 얻는 효용은 크다.  
이를 잘 추상화되었다고 말한다.  

shallow class를 전부 없앨 수는 없다.  
하지만 shallow class가 복잡성을 줄이는데에 도움이 되지 않는다는 사실을 기억해야 한다.  
너무 많은 shallow class가 문제가 되는 경우가 많다.  
함수를 작게 유지하는 것에만 몰두해서 그렇다.  

### Don't - 1
```java
private void addNullValueForAttribute(String attribute) {
  data.put(attribute, null)
}
```

### Don't - 2
```java
// 파일 하나 읽기 위해 3개의 클래스를 사용. 시스템에 비용이 부과됨
FileInputStream fileStream = new FileInputStream(fileName);
BufferedInputStream bufferedStream = new BufferedInputStream(fileStream);
ObjectInputStream objectStream = new ObjectInputStream(bufferedStream);
```

### Do - Unix file I/O
```c++
int open(const char* path, int flags, mode_t permissions);
int close(int fd);
ssize_t read(int fd, void* buffer, size_t count);
ssize_t write(int fd, const void* buffer, size_t count);
off_t lseek(int fd, off_t offset, int referencePosition);
```
- 인터페이스 아래에 숨겨져있는 것
  - On-disk representation, disk block allocation
  - Directory management, path lookup
  - Permission management
  - Disk scheduling
  - Block caching
  - Deivce independence
 
## 2. Define errors out of existence 
에러를 던지지 않아도 된다면 그렇게 만들기  
그리고, 에러를 가능한 멀리 보내기(이해못함)  

### 예시 1. Unix의 파일 삭제 솔루션  
윈도우에서 파일이 사용중일 때 지운다면, 에러가 발생하며 사용중인 프로그램을 종료하라고 함.  
Unix에서는 일단 파일 시스템 상에서 안 보이도록 지우고, 사용중인 프로그램이 파일 사용을 마쳤을 때 실제로 지움.  

### 예시 2. Java의 substring
자바는 문자열을 자를 때 범위를 벗어나면 에러가 발생함.  
이는 사람들의 실수를 방지하기 위한 배려이지만 복잡성을 늘린다고 생각함.  
흔하고 올바른 케이스를 아주 쉽게 다룰 수 있도록 구현하고, 예외는 단위 테스트를 통해 제어하는게 좋다고 생각함.  

## 3. Tactical vs Strategic Programming  
### Tactical Programming  
- 목표: 기능 구현 및 버그 수정을 가능한 빨리 하기  
- 몇개의 지름길과 임시방편을 허용함  
- 결과: 나쁜 설계, 높은 복잡성  
- 복잡성은 조금씩 점진적으로 증가한다  
  - 따라서 문제를 인식했을 때는 이미 수천개의 실수가 겹쳐있기 때문에 돌이키기 힘들다  
- *절대로 동작만 하는 코드를 목표로 하지 말 것.*  

### Strategic Programming  
- 목표: 좋은 설계  
- 왜? 미래의 개발을 빠르고 단순하게 하기 위해서  
  - 반대도 성립함. 나쁜 설계는 미래의 개발을 점점 느리게 만듬  
  - 여기서 '미래' 라는 것은 정확하진 않지만 6~12개월 정도의 미래임. 내가 짠 코드를 완전히 잊어버렸을 때.  
- 복잡성 최소화  
- 작은 일에 땀 흘려야만 달성 가능함  

- 투자한다는 생각을 가져야 함  
- 오늘 조금 더 시간을 쏟고  
- 장기적으로 가치를 얻는다고 생각하기  

### How much to invest?  
- 10~20% 정도 더 투자한다고 생각하기  
- 새로운 코드를 작성할 때는  
  - 조심스럽게 설계하고  
  - 문서화에 신경쓰기  
- 기존 코드를 수정할 때는  
  - 항상 개선할 것을 찾기  
  - 수정이 두려워서 최소한으로 수정하는 방법을 찾는데에 몰두하지 않기(인터페이스를 무시하고 전역 변수에 접근하기 등)
  - clean way를 찾기  
- *변경 후의 모습은, 바닥부터 설계해서 코드를 작성했을 때와 일치하는 모습이어야한다.*  
