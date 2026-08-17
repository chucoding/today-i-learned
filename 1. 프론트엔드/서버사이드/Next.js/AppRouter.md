# App Router

Next.js 13부터 도입된 `app/` 디렉터리 기반 라우팅 방식.
Pages Router와 가장 큰 차이는 **컴포넌트의 기본값이 서버 컴포넌트(React Server Component)** 라는 점.

## 1. 폴더 구조 — 파일 이름이 곧 API

App Router는 폴더가 URL 경로, 파일 이름이 역할을 결정.

```
app/
├── layout.tsx          # 루트 레이아웃 (필수, <html>/<body> 포함)
├── page.tsx            # "/" 화면
├── loading.tsx         # 하위 트리 로딩 UI (Suspense 자동 래핑)
├── error.tsx           # 하위 트리 에러 UI ('use client' 필수)
├── not-found.tsx       # 404 화면
└── products/
    ├── page.tsx        # "/products"
    ├── layout.tsx      # /products 이하 공통 레이아웃
    ├── [id]/
    │   └── page.tsx    # "/products/123"
    └── api/
        └── route.ts    # "/products/api" HTTP 엔드포인트
```

| 파일 | 역할 | 서버/클라이언트 |
| --- | --- | --- |
| `page.tsx` | 해당 경로의 화면 | 기본 서버 |
| `layout.tsx` | 경로 이동 시에도 **유지**되는 껍데기 (상태 보존) | 기본 서버 |
| `template.tsx` | 경로 이동마다 **새로 마운트**되는 껍데기 | 기본 서버 |
| `loading.tsx` | 해당 세그먼트를 `Suspense`로 감싼 fallback | 기본 서버 |
| `error.tsx` | ErrorBoundary. `reset()` 제공 | **클라이언트 전용** |
| `not-found.tsx` | `notFound()` 호출 시 표시 | 기본 서버 |
| `route.ts` | `GET`/`POST` … HTTP 핸들러 (화면 아님) | 서버 전용 |

`page.tsx`와 `route.ts`는 같은 경로에 공존할 수 없음. 하나의 경로는 화면이거나 엔드포인트이거나 둘 중 하나.

### 라우팅 보조 규칙

- `(group)` : URL에 포함되지 않는 폴더. 레이아웃 분리용. `app/(marketing)/about/page.tsx` → `/about`
- `_folder` : 라우팅에서 완전히 제외되는 폴더. 컴포넌트/유틸 코로케이션용
- `[id]` 동적, `[...slug]` catch-all, `[[...slug]]` optional catch-all

---

## 2. 서버 컴포넌트가 기본이다

`'use client'`가 없는 모든 컴포넌트는 서버에서만 실행되고, **JS 번들로 브라우저에 전송되지 않음**. 브라우저는 그 결과(RSC Payload)만 받음.

```tsx
// app/products/page.tsx — 'use client' 없음 = 서버 컴포넌트
import { db } from '@/shared/lib/db';

export default async function ProductsPage() {
  // 서버에서만 실행되므로 DB 직접 접근 가능
  const products = await db.product.findMany();

  return (
    <ul>
      {products.map((product) => (
        <li key={product.id}>{product.name}</li>
      ))}
    </ul>
  );
}
```

| | 서버 컴포넌트 | 클라이언트 컴포넌트 |
| --- | --- | --- |
| `async`/`await` 컴포넌트 | 가능 | 불가 |
| DB·파일시스템·비밀키 | 가능 | 불가 (노출됨) |
| `useState` / `useEffect` | 불가 | 가능 |
| `onClick` 등 이벤트 | 불가 | 가능 |
| `window` / `localStorage` | 불가 | 가능 |
| JS 번들 크기 기여 | **0** | 있음 |

### 흔한 오해

> "`'use client'`를 붙이면 CSR이라 SEO가 안 된다"

아님. 클라이언트 컴포넌트도 **서버에서 HTML로 미리 렌더링(SSR)된 뒤 브라우저에서 hydration**됨.
`'use client'`의 의미는 "클라이언트에서만 실행"이 아니라 **"여기서부터 클라이언트 번들에 포함된다"** 는 경계 선언.

---

## 3. `'use client'`는 "경계" 선언

`'use client'`를 선언한 파일이 import하는 모든 하위 모듈은 자동으로 클라이언트 번들에 포함. 그래서 **경계를 최대한 잎(leaf)에 가깝게** 내려야 함.

### 나쁜 예 — 페이지 전체를 클라이언트로

```tsx
// ❌ app/products/page.tsx
'use client';

export default function ProductsPage() {
  const [keyword, setKeyword] = useState('');
  const { data: products } = useQuery({ ... }); // 데이터까지 클라이언트에서 조회

  return (
    <>
      <ProductBanner />   {/* 상호작용 없는데 번들에 포함 */}
      <ProductList items={products} />
      <input value={keyword} onChange={(e) => setKeyword(e.target.value)} />
    </>
  );
}
```

문제: 배너·목록처럼 상호작용이 없는 UI까지 전부 JS로 내려감. 데이터도 브라우저가 렌더링 후에야 요청하므로 초기 표시가 느림.

### 좋은 예 — 상호작용 부분만 잘라내기

```tsx
// ✅ app/products/page.tsx (서버 컴포넌트)
import { getProducts } from '@/entities/product/api/getProducts';
import { ProductFilter } from './_components/ProductFilter';

export default async function ProductsPage() {
  const products = await getProducts();

  return (
    <>
      <ProductBanner />          {/* 서버 렌더링, 번들 0 */}
      <ProductList items={products} />
      <ProductFilter />          {/* 여기만 클라이언트 */}
    </>
  );
}
```

```tsx
// ✅ app/products/_components/ProductFilter.tsx
'use client';

import { useState } from 'react';

export function ProductFilter() {
  const [keyword, setKeyword] = useState('');
  return <input value={keyword} onChange={(e) => setKeyword(e.target.value)} />;
}
```

### 서버 컴포넌트를 클라이언트 안에 넣는 법

클라이언트 컴포넌트는 서버 컴포넌트를 **import 할 수 없지만**, `children`으로 **주입받을 수는 있음**. Provider·아코디언·모달 같은 껍데기에 자주 쓰이는 패턴.

```tsx
// app/layout.tsx (서버)
<ThemeProvider>          {/* 클라이언트 껍데기 */}
  <ServerSideBanner />   {/* 서버 컴포넌트가 children으로 들어감 */}
</ThemeProvider>
```

### props 제약

서버 → 클라이언트로 넘기는 props는 **직렬화 가능한 값만** 가능. 함수, 클래스 인스턴스, `Date`가 아닌 커스텀 객체 등은 넘길 수 없음.

```tsx
// ❌ 서버 컴포넌트에서 함수 전달 → 런타임 에러
<LikeButton onLike={() => db.like.create()} />

// ✅ Server Action(뒤에서 설명)이나 id만 전달
<LikeButton productId={product.id} />
```

---

## 4. 데이터는 서버에서 직접 조회

App Router에서 데이터 조회의 기본 위치는 **그 데이터를 쓰는 서버 컴포넌트 내부**. 부모가 모아서 내려주는 구조를 만들지 않아도 됨.

```tsx
// app/products/[id]/page.tsx
export default async function ProductDetailPage({
  params,
}: {
  // Next.js 15부터 params / searchParams 는 Promise
  params: Promise<{ id: string }>;
}) {
  const { id } = await params;
  const product = await getProduct(id);

  if (!product) notFound();

  return <ProductDetail product={product} />;
}
```

> **Next.js 15 변경점**
> `params`, `searchParams`, `cookies()`, `headers()`, `draftMode()`가 모두 비동기.
> 14 이하 코드를 옮길 때 `await` 누락이 가장 흔한 마이그레이션 버그.

### 안티패턴 — 자기 Route Handler를 HTTP로 다시 호출

```tsx
// ❌ 서버가 자기 자신에게 HTTP 요청
export default async function ProductsPage() {
  const res = await fetch(`${process.env.BASE_URL}/api/products`);
  const products = await res.json();
  return <ProductList items={products} />;
}
```

무엇이 문제인가.

1. **불필요한 네트워크 왕복**: 이미 서버 안인데 자기 서버로 한 번 더 나갔다 옴
2. **절대 URL 필요**: 배포 환경마다 `BASE_URL`을 맞춰야 하고, 프리뷰/내부망에서 자주 깨짐
3. **인증 컨텍스트 유실**: 쿠키·헤더를 손으로 다시 붙여야 함
4. **타입 소실**: `res.json()`은 `any`. 함수 직접 호출이면 타입이 그대로 유지
5. **에러 처리 이중화**: 예외가 HTTP 상태코드로 변환됐다가 다시 파싱됨
6. **서버리스에서 자기 호출 데드락 위험**: 동시 실행 제한이 있는 환경에서 자기 함수를 호출하면 잠길 수 있음

### 해결 — 조회 로직을 함수로 빼고 양쪽에서 공유

```ts
// entities/product/api/getProducts.ts — 서버 전용 모듈
import 'server-only'; // 클라이언트에서 import되면 빌드 에러

/**
 * 상품 목록 조회
 * @param filter - 카테고리 등 필터 조건
 */
export async function getProducts(filter?: ProductFilter) {
  return db.product.findMany({ where: filter });
}
```

```tsx
// 서버 컴포넌트: 함수 직접 호출
const products = await getProducts();
```

```ts
// app/api/products/route.ts: 브라우저·외부용 엔드포인트도 같은 함수 사용
import { getProducts } from '@/entities/product/api/getProducts';

export async function GET() {
  return Response.json(await getProducts());
}
```

### 그럼 Route Handler는 언제 쓰나

- 브라우저(클라이언트 컴포넌트)가 직접 호출해야 할 때 — 무한 스크롤, 자동완성
- 외부 시스템의 웹훅 수신
- 외부에 공개할 API, OG 이미지, 파일 스트리밍 응답
- 서드파티 API 키를 숨긴 채 프록시할 때

---

## 5. 독립 데이터는 병렬로 시작

`await`를 순서대로 쓰면 그대로 워터폴이 됨.

```tsx
// ❌ 총 소요 = 300ms + 400ms
const user = await getUser();       // 300ms
const coupons = await getCoupons(); // 400ms
```

```tsx
// ✅ 총 소요 = max(300ms, 400ms)
const [user, coupons] = await Promise.all([getUser(), getCoupons()]);
```

단, **의존 관계가 있으면 순차가 맞음**. `user.id`가 있어야 쿠폰을 조회할 수 있다면 병렬로 만들 수 없음. 이때는 5절의 `Suspense` 분리를 검토.

### 같은 데이터를 여러 컴포넌트가 필요로 할 때

props로 내리지 말고 **각자 조회**해도 됨. 한 번의 렌더링 패스 안에서 중복 요청은 자동으로 합쳐짐.

- `fetch`: React가 동일 URL·옵션 요청을 자동 메모이제이션
- `fetch`가 아닌 DB 접근: `React.cache()`로 감싸면 동일하게 동작

```ts
import { cache } from 'react';

/** 사용자 조회 (동일 렌더링 내 중복 호출 시 1회만 실행) */
export const getUser = cache(async (id: string) => {
  return db.user.findUnique({ where: { id } });
});
```

### preload 패턴

하위에서 필요할 데이터를 **미리 발화만 시켜두는** 기법. `await` 없이 호출해 요청을 먼저 띄움.

```tsx
export default async function Page({ params }) {
  const { id } = await params;

  void getProduct(id); // 요청만 시작 (await 안 함)

  const reviews = await getReviews(id);
  return <ProductDetail id={id} reviews={reviews} />; // 내부에서 await 시 이미 진행 중
}
```

---

## 6. 느린 데이터는 Suspense 경계로

페이지 전체가 가장 느린 데이터를 기다리게 하지 말 것. 빠른 영역은 먼저 보내고 느린 영역만 스트리밍.

```tsx
import { Suspense } from 'react';

export default async function ProductDetailPage({ params }) {
  const { id } = await params;
  const product = await getProduct(id); // 빠름 — 즉시 표시

  return (
    <>
      <ProductDetail product={product} />

      {/* 느린 영역만 별도 경계로 분리 */}
      <Suspense fallback={<ReviewSkeleton />}>
        <ReviewList productId={id} />
      </Suspense>
    </>
  );
}

/** 리뷰 목록 (자체적으로 데이터 조회) */
async function ReviewList({ productId }: { productId: string }) {
  const reviews = await getReviews(productId); // 느림 — 준비되면 스트리밍
  return <ul>{reviews.map((r) => <li key={r.id}>{r.content}</li>)}</ul>;
}
```

`loading.tsx`는 **해당 세그먼트 전체를 감싸는 `Suspense`** 와 같음. 페이지 단위 로딩은 `loading.tsx`, 페이지 내부 부분 로딩은 직접 `Suspense`.

### 경계를 나누는 기준

| 상황 | 판단 |
| --- | --- |
| 페이지 첫 화면에 반드시 필요한 데이터 | 경계 없이 `await` |
| LCP에 영향 없는 하단 영역 (리뷰, 추천) | `Suspense` 분리 |
| 응답 시간이 들쭉날쭉한 외부 API | `Suspense` 분리 |
| 실패해도 페이지가 성립하는 영역 | `Suspense` + `error.tsx` |

주의: `Suspense` 경계를 잘게 나눌수록 레이아웃 시프트가 늘어남. fallback의 크기를 실제 콘텐츠와 맞출 것.

---

## 7. 캐싱과 렌더링 모드

Next.js 15에서 캐싱 기본값이 크게 바뀜. **"기본은 캐시하지 않는다, 필요하면 켠다"** 로 이해할 것.

```ts
// 캐시 안 함 (Next.js 15 기본값)
await fetch(url);

// 빌드 시점 캐시 후 계속 재사용
await fetch(url, { cache: 'force-cache' });

// 60초마다 재검증 (ISR)
await fetch(url, { next: { revalidate: 60 } });

// 태그 기반 재검증
await fetch(url, { next: { tags: ['product'] } });
```

세그먼트 단위 설정.

```ts
// app/products/page.tsx
export const revalidate = 60;            // 60초 ISR
export const dynamic = 'force-dynamic';  // 항상 요청 시 렌더링
```

`cookies()`, `headers()`, `searchParams`를 사용하면 그 경로는 자동으로 동적 렌더링으로 전환.

데이터 변경 후 캐시 무효화.

```ts
import { revalidatePath, revalidateTag } from 'next/cache';

revalidatePath('/products'); // 경로 단위
revalidateTag('product');    // 태그 단위
```

> **Next.js 16 참고**
> `cacheComponents` 옵션과 `'use cache'` 지시어로 캐싱을 함수/컴포넌트 단위로 선언하는 방식이 추가됨. 옵트인 기능이므로 실무 도입 전에는 사용 중인 버전의 문서를 먼저 확인할 것.

---

## 8. 상태 변경: Server Actions

폼 제출·좋아요·삭제 같은 변경 작업은 Route Handler를 만들지 않고 **Server Action**으로 처리 가능.

```ts
// app/products/_actions/toggleLike.ts
'use server';

import { revalidatePath } from 'next/cache';

/**
 * 상품 좋아요 토글
 * @param productId - 대상 상품 ID
 */
export async function toggleLike(productId: string) {
  const user = await getCurrentUser();
  if (!user) throw new Error('로그인 필요');

  await db.like.toggle({ userId: user.id, productId });
  revalidatePath(`/products/${productId}`);
}
```

```tsx
// app/products/_components/LikeButton.tsx
'use client';

import { useOptimistic, useTransition } from 'react';
import { toggleLike } from '../_actions/toggleLike';

export function LikeButton({ productId, liked }: { productId: string; liked: boolean }) {
  const [isPending, startTransition] = useTransition();
  const [optimisticLiked, setOptimisticLiked] = useOptimistic(liked);

  return (
    <button
      disabled={isPending}
      onClick={() =>
        startTransition(async () => {
          setOptimisticLiked(!optimisticLiked); // 낙관적 갱신
          await toggleLike(productId);
        })
      }
    >
      {optimisticLiked ? '♥' : '♡'}
    </button>
  );
}
```

> Server Action은 결국 POST 엔드포인트로 노출됨. **함수 안에서 반드시 인증·권한을 다시 검증**할 것. 클라이언트에서 호출됐다는 사실만 믿으면 안 됨.

| | Server Action | Route Handler |
| --- | --- | --- |
| 호출 주체 | 주로 자기 앱의 폼/버튼 | 브라우저 fetch, 외부 시스템 |
| 타입 | 함수 시그니처 그대로 유지 | 직접 정의 필요 |
| 캐시 무효화 | `revalidatePath/Tag` 직접 호출 | 별도 처리 |
| 적합한 경우 | 생성·수정·삭제 | 조회 API, 웹훅, 파일 응답 |

---

## 9. TanStack Query는 언제 쓰나

**서버 컴포넌트에서 조회 가능한 데이터는 TanStack Query가 필요 없음.** 다음 중 하나라도 해당될 때만 검토.

- 클라이언트에서 재조회가 잦음 (폴링, 포커스 시 refetch, 무한 스크롤)
- 여러 클라이언트 컴포넌트가 같은 데이터를 공유하며 낙관적 갱신이 필요
- 필터·페이지네이션 결과를 클라이언트 캐시에 보관해 즉시 전환해야 함

이 경우 **서버에서 prefetch → dehydrate → 클라이언트에서 hydrate** 구조를 사용. 초기 데이터는 SSR로 내려가고, 이후 갱신은 Query가 담당.

```ts
// app/get-query-client.ts
import { QueryClient, isServer } from '@tanstack/react-query';

function makeQueryClient() {
  return new QueryClient({
    defaultOptions: {
      // SSR에서 즉시 refetch되는 것을 막기 위한 최소 staleTime
      queries: { staleTime: 60 * 1000 },
    },
  });
}

let browserQueryClient: QueryClient | undefined;

/** 서버는 요청마다 새 인스턴스, 브라우저는 싱글턴 반환 */
export function getQueryClient() {
  if (isServer) return makeQueryClient();
  browserQueryClient ??= makeQueryClient();
  return browserQueryClient;
}
```

```ts
// entities/product/api/productKeys.ts
import { createQueryKeys } from '@lukemorales/query-key-factory';

export const productKeys = createQueryKeys('product', {
  list: (filter: ProductFilter) => ({ queryKey: [filter] }),
  detail: (id: string) => ({ queryKey: [id] }),
});
```

```tsx
// app/products/page.tsx (서버 컴포넌트)
import { dehydrate, HydrationBoundary } from '@tanstack/react-query';
import { getQueryClient } from '@/app/get-query-client';

export default async function ProductsPage({ searchParams }) {
  const filter = await searchParams;
  const queryClient = getQueryClient();

  await queryClient.prefetchQuery({
    ...productKeys.list(filter),
    queryFn: () => getProducts(filter),
  });

  return (
    <HydrationBoundary state={dehydrate(queryClient)}>
      <ProductListWithFilter />  {/* 클라이언트 컴포넌트, useQuery 사용 */}
    </HydrationBoundary>
  );
}
```

주의점.

- `QueryClient`를 모듈 최상단에서 `new`로 만들면 **서버에서 요청 간 캐시가 공유되어 다른 사용자 데이터가 섞임**
- `staleTime: 0`이면 hydration 직후 곧바로 refetch가 발생해 SSR 이점이 사라짐
- `prefetchQuery`의 `queryFn`과 클라이언트의 `queryFn`이 **같은 키**를 써야 hydration이 맞물림

---

## 10. 실습 과제

연습 목표는 다음 구조입니다.

```
서버 페이지
├── 스키마와 초기 데이터 직접 조회
├── 정적·읽기 중심 UI
└── 클라이언트 기능 경계
    ├── 필터
    ├── 쿠폰
    └── 좋아요
```

### 단계

1. **서버 페이지 만들기**
   `app/products/page.tsx`에서 `getProducts()`를 직접 호출해 목록을 렌더링. `'use client'` 없이 화면이 뜨는지 확인.
2. **번들 확인**
   개발자도구 Network에서 JS 번들 크기를 기록. 이후 단계마다 다시 측정.
3. **필터를 클라이언트 경계로 분리**
   `ProductFilter`만 `'use client'`. 선택값은 `useRouter().push()`로 URL 쿼리에 반영하고, 실제 필터링은 서버 페이지가 `searchParams`로 수행.
4. **쿠폰·배너 병렬 조회**
   `Promise.all`로 상품·쿠폰·배너를 동시에 조회. 순차 버전과 응답 시간을 비교.
5. **느린 영역 스트리밍**
   추천 상품 조회에 인위적으로 2초 지연을 준 뒤 `Suspense`로 감싸고, 감싸기 전후의 첫 화면 표시 시점을 비교.
6. **좋아요를 Server Action으로**
   `toggleLike` Server Action + `useOptimistic`. 액션 안에서 인증 검증 추가.
7. **(선택) TanStack Query 도입**
   필터 결과를 클라이언트 캐시에 보관하도록 `HydrationBoundary` 적용. 도입 전후로 **정말 나아진 게 있는지** 스스로 판정.

### 완료 기준

- [ ] `'use client'` 파일이 3개(필터·쿠폰·좋아요) 이하
- [ ] 서버 컴포넌트에서 `fetch('/api/...')` 호출이 0건
- [ ] 독립 데이터가 워터폴 없이 병렬 조회됨
- [ ] JS 비활성 상태에서도 상품 목록이 HTML로 보임
- [ ] 느린 영역 때문에 첫 화면이 늦어지지 않음

---

## 11. 자주 하는 실수 체크리스트

| 증상 | 원인 | 해결 |
| --- | --- | --- |
| `useState is not defined` 류 에러 | 서버 컴포넌트에서 훅 사용 | 해당 컴포넌트만 `'use client'` 분리 |
| 번들이 계속 커짐 | 최상위에 `'use client'` | 경계를 잎 쪽으로 이동 |
| `params.id`가 `undefined` | Next.js 15에서 `await` 누락 | `const { id } = await params` |
| API 키가 브라우저에 노출 | 클라이언트 컴포넌트에서 사용 | 서버 전용 모듈로 이동 + `import 'server-only'` |
| 데이터가 갱신되지 않음 | 캐시된 응답 재사용 | `revalidatePath/Tag` 또는 `cache` 옵션 조정 |
| 배포 후 데이터가 섞임 | 서버에서 `QueryClient` 공유 | 요청마다 새 인스턴스 생성 |
| 페이지 전체가 느림 | 가장 느린 `await`가 전체를 블로킹 | `Promise.all` + `Suspense` |
| 함수를 props로 넘길 때 에러 | 서버 → 클라이언트 직렬화 불가 | 값만 전달하거나 Server Action 사용 |

---

## 12. 확인 문제

<details>
<summary>1. <code>'use client'</code>를 붙이면 그 컴포넌트는 SSR되지 않는다? (O/X)</summary>

X. 서버에서 HTML로 렌더링된 뒤 브라우저에서 hydration됨. `'use client'`는 "클라이언트 번들에 포함된다"는 경계 선언.
</details>

<details>
<summary>2. 서버 컴포넌트가 자기 앱의 <code>/api/products</code>를 fetch하면 안 되는 이유 3가지</summary>

불필요한 네트워크 왕복, 절대 URL·인증 헤더 수동 관리, 타입 소실. (추가로 에러 처리 이중화, 서버리스 자기 호출 위험)
</details>

<details>
<summary>3. <code>layout.tsx</code>와 <code>template.tsx</code>의 차이</summary>

`layout`은 경로 이동 시에도 유지되어 상태가 보존되고, `template`은 이동마다 새로 마운트됨.
</details>

<details>
<summary>4. <code>Promise.all</code>로 묶으면 안 되는 경우</summary>

앞 요청의 결과가 뒤 요청의 입력이 되는 의존 관계일 때. 이때는 `Suspense`로 컴포넌트를 분리해 독립 스트리밍을 검토.
</details>

<details>
<summary>5. TanStack Query를 도입해야 하는 신호</summary>

클라이언트 재조회가 잦거나(폴링·무한 스크롤), 여러 클라이언트 컴포넌트가 같은 데이터를 공유하며 낙관적 갱신이 필요할 때. 단순 초기 조회만이면 불필요.
</details>

---

## 참고자료

| TITLE | URL |
| --- | --- |
| Server/Client Components | https://nextjs.org/docs/app/getting-started/server-and-client-components |
| 데이터 가져오기 | https://nextjs.org/docs/app/getting-started/fetching-data |
| 캐싱과 재검증 | https://nextjs.org/docs/app/guides/caching |
| Server Actions | https://nextjs.org/docs/app/getting-started/updating-data |
| 스트리밍과 Suspense | https://nextjs.org/docs/app/api-reference/file-conventions/loading |
| TanStack Query Advanced SSR | https://tanstack.com/query/latest/docs/framework/react/guides/advanced-ssr |
