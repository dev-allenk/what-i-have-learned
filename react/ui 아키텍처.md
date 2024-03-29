[잘 알려진 ui 패턴을 사용하여 리액트 애플리케이션 모듈화하기](https://velog.io/@eunbinn/modularizing-react-apps)

## TL;DR
- [데이터 모델링](https://velog.io/@eunbinn/modularizing-react-apps#%EB%A1%9C%EC%A7%81%EC%9D%84-%EC%BA%A1%EC%8A%90%ED%99%94%ED%95%98%EB%8A%94-%EB%8D%B0%EC%9D%B4%ED%84%B0-%EB%AA%A8%EB%8D%B8%EB%A7%81): 형태 변환을 하고 있다면 클래스를 생성하고 로직을 캡슐화하기(`new PaymentMethod()`)
- 컴포넌트 -> useState/useApi -> getter/fetcher -> domain


## 도메인 객체 (domain layer)
- 데이터를 한 형식에서 다른 형식으로 변환
- 필요에 따라 null 체크 후 폴백 값 사용
- 데이터가 원격에서 온 것인지, 로컬 스토리지에서 온 것인지, 캐시에서 온 것인지는 신경쓰지 않음
```typescript
class PaymentMethod {
  private remotePaymentMethod: RemotePaymentMethod;

  constructor(remotePaymentMethod: RemotePaymentMethod) {
    this.remotePaymentMethod = remotePaymentMethod;
  }

  get provider() {
    return this.remotePaymentMethod.name;
  }

  get label() {
    if (this.provider === "cash") {
      return `Pay in ${this.provider}`;
    }
    return `Pay with ${this.provider}`;
  }

  get isDefaultMethod() {
    return this.provider === "cash";
  }
}
```

## fetcher (infra layer)
- 네트워크 요청 및 도메인 객체로 변환
```typescript
const fetchPaymentMethods = async () => {
  const response = await fetch(
    "https://5a2f495fa871f00012678d70.mockapi.io/api/payment-methods?countryCode=AU"
  );
  const methods: RemotePaymentMethod[] = await response.json();

  return convertPaymentMethods(methods);
};

const convertPaymentMethods = (methods: RemotePaymentMethod[]) => {
  if (methods.length === 0) {
    return [];
  }

  const extended: PaymentMethod[] = methods.map(
    (method) => new PaymentMethod(method)
  );
  extended.push(payInCash);

  return extended;
};
```

## 상태 관리
- fetcher와 같은 스토리지 getter를 활용해 데이터를 가져옴
```typescript
export const usePaymentMethods = () => {
  const [paymentMethods, setPaymentMethods] = useState<PaymentMethod[]>([]);

  useEffect(() => {
    fetchPaymentMethods().then((methods) => setPaymentMethods(methods));
  }, []);

  return {
    paymentMethods,
  };
};
```
