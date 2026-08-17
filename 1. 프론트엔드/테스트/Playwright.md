# Playwright

Microsoft에서 개발한 테스트 도구로, 빠른 속도와 안정성을 자랑

- 폼 채우기, 필터링 후 데이터 검증, 크로스 브라우저 테스트에 강력
- Chromium / Firefox / WebKit을 하나의 API로 제어
- 모든 액션과 검증에 **auto-waiting**이 내장되어 `sleep` 없이도 안정적

## 설치

```bash
npm init playwright@latest
```

- `playwright.config.ts`, `tests/`, `.github/workflows` 까지 한 번에 생성
- 브라우저 바이너리는 `npx playwright install`로 별도 다운로드

```bash
npx playwright test              # 전체 실행 (headless)
npx playwright test --ui         # UI 모드, 타임라인 보며 디버깅
npx playwright codegen <url>     # 클릭하면 코드 자동 생성
npx playwright show-report       # HTML 리포트 열기
```

## 1. locator와 사용자 역할 기반 선택

### Locator는 "요소"가 아니라 "찾는 방법"

```ts
const button = page.getByRole('button', { name: '저장' });
await button.click();
```

- `getByRole`은 요소를 즉시 찾지 않고 **찾는 방법을 담은 객체**를 반환
- `click()` 같은 액션 시점에 다시 조회 → 리렌더링으로 DOM이 교체돼도 stale 하지 않음
- 이 성질 덕분에 `waitForSelector` 없이도 동작 (auto-waiting)

### 우선순위: 사용자가 보는 방식 그대로

| 순위 | Locator | 용도 |
|------|---------|------|
| 1 | `getByRole(role, { name })` | 버튼, 링크, 입력, 헤딩 등 대부분 |
| 2 | `getByLabel` | 폼 입력 (label과 연결된 input) |
| 3 | `getByPlaceholder` | label이 없는 입력 |
| 4 | `getByText` | 문단, 안내 문구 |
| 5 | `getByTestId` | 위 방법으로 특정 불가할 때만 |
| ❌ | CSS / XPath | 최후의 수단 |

```ts
await page.getByRole('textbox', { name: '이메일' }).fill('test@example.com');
await page.getByLabel('비밀번호').fill('1234');
await page.getByRole('button', { name: '로그인' }).click();
```

**왜 role 기반인가**

- CSS 클래스는 스타일 리팩터링 한 번에 전부 깨짐 → 테스트가 구현에 결합
- role/name은 **접근성 트리** 기준이라 마크업이 바뀌어도 유지됨
- 덤으로 접근성 검증 효과: `getByRole('button')`으로 못 찾으면 `<div onClick>`을 쓰고 있다는 신호

### 여러 개가 잡힐 때

```ts
// ❌ strict mode violation: 2개 이상 매칭되면 에러
await page.getByRole('listitem').click();

// ✅ 범위를 좁힌다
await page.getByRole('row', { name: '홍길동' })
          .getByRole('button', { name: '삭제' })
          .click();

// ✅ 또는 필터
await page.getByRole('listitem').filter({ hasText: '재고 없음' }).first().click();
```

- Playwright는 **strict mode**가 기본 → 모호한 선택을 에러로 알려줌
- `.first()`를 습관적으로 붙이지 말 것. 먼저 범위를 좁히는 게 정답

### 웹 우선 단언(assertion)

```ts
await expect(page.getByRole('alert')).toBeVisible();       // 나타날 때까지 재시도
await expect(page.getByRole('row')).toHaveCount(3);
await expect(page.getByRole('button', { name: '저장' })).toBeDisabled();
```

- `expect(locator)`는 **조건이 만족될 때까지 폴링**하다가 타임아웃 시 실패
- `expect(await locator.textContent())` 처럼 값을 먼저 꺼내면 재시도가 사라짐 → flaky의 주범

---

## 2. 클릭·입력·라우팅 검증

### 액션은 actionability 체크를 통과해야 실행

`click()` 하나가 내부적으로 확인하는 것들:

1. 요소가 DOM에 붙어 있는가 (attached)
2. 보이는가 (visible)
3. 애니메이션이 멈췄는가 (stable)
4. 다른 요소에 가려지지 않았는가 (receives events)
5. 비활성 상태가 아닌가 (enabled)

→ 모달 애니메이션, 스켈레톤 UI 때문에 넣던 `waitForTimeout`이 대부분 필요 없어짐

### 입력

```ts
await page.getByLabel('검색어').fill('플레이라이트');   // 값 교체 (빠름)
await page.getByLabel('검색어').pressSequentially('플');  // 한 글자씩 (자동완성 테스트)
await page.getByRole('checkbox', { name: '동의' }).check();
await page.getByLabel('국가').selectOption('KR');
await page.getByLabel('첨부').setInputFiles('./fixtures/sample.pdf');
```

- 기본은 `fill` — 값 설정 + `input`/`change` 이벤트 발생까지 한 번에
- 키 입력마다 반응하는 UI(디바운스 검색, 자동완성)만 `pressSequentially`

### 라우팅 검증

```ts
await page.getByRole('link', { name: '상세보기' }).click();

await expect(page).toHaveURL(/\/products\/\d+/);          // 정규식 가능
await expect(page.getByRole('heading', { level: 1 })).toHaveText('상품 상세');
```

- SPA에서는 URL만 검증하면 부족. **URL + 화면에 실제로 그려진 것**을 같이 본다
- 뒤로가기 검증까지 하면 히스토리 처리 버그를 잡을 수 있음

```ts
await page.goBack();
await expect(page).toHaveURL('/products');
await expect(page.getByRole('searchbox')).toHaveValue('플레이라이트'); // 검색 상태 복원 확인
```

---

## 3. 네트워크 성공·지연·실패 mocking

`page.route`로 요청을 가로채서 응답을 마음대로 조작한다. **E2E에서 가장 자주 쓰는 무기**.

### 성공 응답 고정

```ts
await page.route('**/api/products*', async (route) => {
  await route.fulfill({
    status: 200,
    contentType: 'application/json',
    body: JSON.stringify({ items: [{ id: 1, name: '노트북' }], total: 1 }),
  });
});
await page.goto('/products');
await expect(page.getByRole('row')).toHaveCount(1);
```

- 백엔드 상태에 의존하지 않아 테스트가 **결정적(deterministic)** 이 됨
- 빈 목록, 1건, 1000건 같은 경계 케이스를 데이터 준비 없이 만들 수 있음

### 지연 → 로딩 UI 검증

```ts
await page.route('**/api/products*', async (route) => {
  await new Promise((r) => setTimeout(r, 2000));
  await route.fulfill({ json: { items: [] } });
});

await page.goto('/products');
await expect(page.getByTestId('skeleton')).toBeVisible();   // 로딩 중
await expect(page.getByText('결과가 없습니다')).toBeVisible(); // 완료 후
```

- 스켈레톤·스피너는 실제 API가 빠르면 **눈 깜짝할 새 사라져서 검증 자체가 불가능**
- 지연 mocking이 있어야 로딩 상태를 안정적으로 테스트할 수 있음

### 실패 → 에러 UI / 재시도 검증

```ts
// 서버 에러
await page.route('**/api/products*', (route) =>
  route.fulfill({ status: 500, json: { message: 'Internal Server Error' } })
);

// 네트워크 자체 단절
await page.route('**/api/products*', (route) => route.abort('failed'));

await expect(page.getByRole('alert')).toContainText('불러오지 못했습니다');
```

첫 요청만 실패시키고 재시도는 성공시키는 패턴:

```ts
let calls = 0;
await page.route('**/api/products*', async (route) => {
  calls += 1;
  if (calls === 1) return route.fulfill({ status: 500, body: '{}' });
  return route.fulfill({ json: { items: [{ id: 1, name: '노트북' }] } });
});

await page.getByRole('button', { name: '다시 시도' }).click();
await expect(page.getByText('노트북')).toBeVisible();
```

### 그 외 유용한 것

```ts
// 실제 서버 응답을 받아서 일부만 변조
await page.route('**/api/user', async (route) => {
  const res = await route.fetch();
  const json = await res.json();
  await route.fulfill({ json: { ...json, role: 'admin' } });
});

// 요청 본문 검증
const [req] = await Promise.all([
  page.waitForRequest('**/api/orders'),
  page.getByRole('button', { name: '주문' }).click(),
]);
expect(req.postDataJSON()).toMatchObject({ productId: 1, qty: 2 });

// 광고/트래킹 차단으로 테스트 속도 개선
await page.route('**/*.{png,jpg,woff2}', (route) => route.abort());
```

- `page.route`는 **등록 순서의 역순**으로 매칭 → 나중에 등록한 핸들러가 우선
- 여러 테스트가 공유하면 `test.beforeEach`나 `context.route`로 올려서 중복 제거

---

## 4. popup 및 여러 페이지 제어

Playwright는 하나의 BrowserContext 안에서 여러 탭이나 popup을 제어할 수 있으므로, 웹뷰 유사 시나리오 검증에 적합합니다. [Playwright 여러 페이지 공식 문서](https://playwright.dev/docs/pages)

### popup 잡기

```ts
const [popup] = await Promise.all([
  page.waitForEvent('popup'),                                // 먼저 리스너 등록
  page.getByRole('button', { name: '카카오 로그인' }).click(), // 그 다음 트리거
]);

await popup.waitForLoadState();
await popup.getByLabel('아이디').fill('test');
await popup.getByRole('button', { name: '확인' }).click();
await popup.waitForEvent('close');

await expect(page.getByText('환영합니다')).toBeVisible(); // 원래 페이지 상태 확인
```

- **순서가 핵심**: 클릭을 먼저 `await` 하면 popup 이벤트를 놓칠 수 있음 → `Promise.all`로 동시에
- `popup`은 그냥 또 하나의 `Page` 객체 → `page`에 쓰던 API 그대로 사용
- 소셜 로그인, 결제(PG) 창, 본인인증 iframe 팝업이 대표적인 대상

### 새 탭 / 여러 페이지

```ts
const [newTab] = await Promise.all([
  context.waitForEvent('page'),   // target="_blank" 는 context 레벨 이벤트
  page.getByRole('link', { name: '약관' }).click(),
]);

context.pages();        // 현재 열린 페이지 목록
await newTab.close();
await page.bringToFront();
```

### 탭 간 상태 공유 확인

같은 context 안의 페이지들은 **쿠키·localStorage를 공유**한다.

```ts
const tabA = await context.newPage();
const tabB = await context.newPage();

await tabA.goto('/settings');
await tabA.getByRole('switch', { name: '다크모드' }).click();

await tabB.goto('/');
await expect(tabB.locator('html')).toHaveAttribute('data-theme', 'dark');
```

- 웹뷰/멀티탭 앱에서 "한쪽에서 로그아웃하면 다른 쪽도 로그아웃되는가" 같은 시나리오 검증에 사용

### 다이얼로그(alert/confirm)

```ts
page.on('dialog', (dialog) => dialog.accept());   // 리스너가 없으면 자동 dismiss
await page.getByRole('button', { name: '삭제' }).click();
```

---

## 5. 브라우저 컨텍스트 격리

### 계층 구조

```
Browser (프로세스 하나, 무거움)
└── BrowserContext (쿠키/스토리지/캐시 완전 분리, 생성 비용 거의 0)
    └── Page (탭)
        └── Frame (iframe)
```

- **BrowserContext = 시크릿 창** 이라고 설명하면 이해가 빠름
- Playwright는 테스트마다 새 context를 만들어 줌 → 테스트 간 로그인 상태가 절대 새지 않음
- 브라우저를 매번 새로 띄우지 않으므로 격리와 속도를 동시에 확보 (Selenium 대비 강점)

### 로그인 상태 재사용 (storageState)

매 테스트마다 로그인 폼을 채우면 느리고 깨지기 쉽다. **한 번 로그인해서 저장하고 주입**한다.

```ts
// auth.setup.ts
import { test as setup, expect } from '@playwright/test';

setup('로그인', async ({ page }) => {
  await page.goto('/login');
  await page.getByLabel('아이디').fill('test');
  await page.getByLabel('비밀번호').fill('1234');
  await page.getByRole('button', { name: '로그인' }).click();
  await expect(page.getByText('환영합니다')).toBeVisible();

  await page.context().storageState({ path: '.auth/user.json' });
});
```

```ts
// playwright.config.ts
projects: [
  { name: 'setup', testMatch: /auth\.setup\.ts/ },
  {
    name: 'chromium',
    use: { ...devices['Desktop Chrome'], storageState: '.auth/user.json' },
    dependencies: ['setup'],
  },
],
```

- `.auth/`는 반드시 `.gitignore`에 추가
- 권한별로 `admin.json`, `user.json`을 만들어 프로젝트를 나누면 역할 테스트가 깔끔해짐

### 한 테스트 안에서 서로 다른 사용자

```ts
test('실시간 채팅', async ({ browser }) => {
  const alice = await browser.newContext({ storageState: '.auth/alice.json' });
  const bob   = await browser.newContext({ storageState: '.auth/bob.json' });

  const alicePage = await alice.newPage();
  const bobPage   = await bob.newPage();

  await alicePage.goto('/chat/1');
  await bobPage.goto('/chat/1');

  await alicePage.getByRole('textbox').fill('안녕');
  await alicePage.getByRole('button', { name: '전송' }).press('Enter');

  await expect(bobPage.getByText('안녕')).toBeVisible();

  await alice.close();
  await bob.close();
});
```

### context 단위 환경 설정

```ts
const context = await browser.newContext({
  locale: 'ko-KR',
  timezoneId: 'Asia/Seoul',
  viewport: { width: 390, height: 844 },
  geolocation: { latitude: 37.5665, longitude: 126.9780 },
  permissions: ['geolocation'],
  colorScheme: 'dark',
});
```

- 다국어·타임존 의존 로직(날짜 포맷, 정렬)을 실제 환경 세팅으로 검증 가능

---

## 6. trace·screenshot·video를 통한 실패 분석

CI에서 실패했을 때 "내 로컬에선 되는데"를 없애 주는 부분. **팀에 도입할 때 가장 설득력 있는 기능**.

### 설정

```ts
// playwright.config.ts
use: {
  trace: 'on-first-retry',       // 재시도할 때만 수집 (권장)
  screenshot: 'only-on-failure',
  video: 'retain-on-failure',
},
retries: process.env.CI ? 2 : 0,
```

| 옵션 값 | 의미 |
|---------|------|
| `'off'` | 수집 안 함 |
| `'on'` | 항상 수집 (느리고 용량 큼, 로컬 디버깅용) |
| `'retain-on-failure'` | 실패한 테스트만 남김 |
| `'on-first-retry'` | 첫 재시도에서만 수집 — 성능/정보 균형이 가장 좋음 |

### Trace Viewer

```bash
npx playwright show-trace test-results/.../trace.zip
```

trace 하나에 들어 있는 것:

- **타임라인 + 각 액션 시점의 DOM 스냅샷** → 시간을 되감아 실제 화면을 볼 수 있음
- 스냅샷은 이미지가 아니라 **실제 DOM** → 개발자도구로 요소 검사 가능
- 네트워크 요청/응답 전체 목록
- 콘솔 로그, 소스 코드상 실패 위치
- "Before / Action / After" 3단 비교

> **가르칠 포인트**: 스크린샷은 "실패한 순간"만 보여주지만,
> trace는 "그 순간까지 어떻게 왔는지"를 보여준다. 원인 분석은 후자에서 나옴

### 실패 지점을 읽기 쉽게: test.step

```ts
await test.step('상품 검색', async () => {
  await page.getByRole('searchbox').fill('노트북');
  await page.getByRole('button', { name: '검색' }).click();
});

await test.step('장바구니 담기', async () => {
  await page.getByRole('button', { name: '담기' }).first().click();
});
```

- 리포트와 trace에 단계가 접히는 형태로 표시 → 어느 단계에서 무너졌는지 즉시 파악

### 시각적 회귀 테스트

```ts
await expect(page).toHaveScreenshot('product-list.png', {
  maxDiffPixelRatio: 0.01,
  mask: [page.getByTestId('timestamp')],   // 매번 바뀌는 영역 가리기
});
```

- 최초 실행 시 기준 이미지 생성, 이후 비교
- 기준 갱신: `npx playwright test --update-snapshots`
- OS/폰트에 따라 렌더링이 달라지므로 **CI와 동일한 도커 이미지**에서 생성해야 함

### CI 아티팩트 업로드

```yaml
- uses: actions/upload-artifact@v4
  if: ${{ !cancelled() }}
  with:
    name: playwright-report
    path: playwright-report/
    retention-days: 7
```

---

## 정리: flaky 테스트를 만드는 습관

| ❌ 안티패턴 | ✅ 대안 |
|------------|--------|
| `page.waitForTimeout(3000)` | 웹 우선 단언(`expect(locator).toBeVisible()`)의 auto-waiting |
| `page.locator('.btn-primary')` | `page.getByRole('button', { name: '저장' })` |
| `expect(await el.textContent()).toBe('x')` | `await expect(el).toHaveText('x')` (재시도됨) |
| 실제 API에 의존 | `page.route`로 응답 고정 |
| 테스트마다 로그인 폼 채우기 | `storageState` 재사용 |
| 테스트 간 순서 의존 | context 격리 + 각 테스트 독립 실행 |
| `.first()` 남발 | 부모 locator로 범위 좁히기 |

## 참고

- [Locators](https://playwright.dev/docs/locators)
- [Network mocking](https://playwright.dev/docs/mock)
- [Pages & popups](https://playwright.dev/docs/pages)
- [Isolation & authentication](https://playwright.dev/docs/auth)
- [Trace Viewer](https://playwright.dev/docs/trace-viewer)
- [Best Practices](https://playwright.dev/docs/best-practices)
