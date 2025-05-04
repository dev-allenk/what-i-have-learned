## [React Modal Sheet](https://github.com/Temzasse/react-modal-sheet)
- 모바일에서 터치로 끌어내려 닫을 수 있는 모달

## [VoidZero](https://voidzero.dev/)
- 테스트 러너, 번들러, 린터가 포함된 고성능 통합 툴체인  
- 모든 작업(구문 분석, 변환, 린팅, 포맷팅, 번들링, 최소화, 테스트)에 동일한 AST, 리졸버, 모듈 상호 운용성을 사용하여 불일치를 제거하고 중복된 구문 분석 비용을 줄이는 것이 하나의 목표

## react-router의 loader
- chunk를 lazy load하지 않는 경우. Page에 loader를 정의
```typescript
// router.tsx
const router = createBrowserRouter(
  createRoutesFromElements(
    <Route path="/" element={<RootLayout />}>
      <Route index element={<HomePage />} />
      <Route path="products" loader={subLayoutLoader} element={<ProductsSubLayout />}>
        <Route index element={<BrowseProductsPage />} />
        <Route path=":productId" loader={productLoader} element={<ProductPage />} />
      </Route>
    </Route>,
  ),
)

// ProductPage.tsx
export async function loader({ params }) {
  const product = await queryClient.ensureQueryData({
    queryKey: ['vacation', params.vacationId],
    queryFn: () => fetchProducts(params.productId),
    staleTime: 1000 * 30, // cache for 30 seconds
  })
  return product
}

export function ProductPage() {
  const product = useLoaderData()
  // ...
}
```

- chunk를 lazy load 하는 경우. Page와 별도로 loader를 정의
  - router 크기가 커지면 MainChunk 크기가 커질 것 같은데 sub router로 나누고 lazy load 처리가 가능한지 모르겠음.
```typescript
// router.tsx
const router = createBrowserRouter(
  createRoutesFromElements(
    <Route path="/" element={<RootLayout />}>
      <Route index element={<HomePage />} />
      <Route path="products" loader={subLayoutLoader} element={<ProductsSubLayout />}>
        <Route index element={<BrowseProductsPage />} />
        <Route path=":productId" loader={loader} lazy={() => import('../components/ProductPage')} />
      </Route>
    </Route>,
  ),
)

// ProductPage.loader.ts
export async function loader({ params }) {
  const product = await queryClient.ensureQueryData({
    queryKey: ['vacation', params.vacationId],
    queryFn: () => fetchProducts(params.productId),
    staleTime: 1000 * 30, // cache for 30 seconds
  })
  return product
}

// ProductPage.tsx
export function ProductPage() {
  const product = useLoaderData()
  // ...
}
```
