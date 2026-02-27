# SEO
검색엔진 최적화(Search Engine Optimize)
- 검색엔진이 이해하기 쉽도록 구조와 페이지를 개발해 검색 결과 상위에 노출될 수 있도록 하는 작업
- 기본적으로 특정 검색어를 웹 페이지에 적절하게 배치하고 다른 웹 페이지에서 링크가 많이 연결되도록 작업 

## 1. Title
검색 결과에서 클릭 가능한 파란색 제목(= SERP Title) 으로 거의 그대로 사용됨.
```html
<title>RecallBuddy - GitHub 커밋을 플래시카드로 복습하세요</title>
```

## 2. description
검색 결과에서 제목 아래에 나오는 회색 설명문(Snippet)
```html
<meta name="description" content="GitHub 리포지토리의 커밋 기록을 AI가 분석하여 자동으로 학습용 플래시카드를 생성합니다. TIL, 코드 리뷰, 학습 내용을 매일 효과적으로 복습하세요." />
```
- Google 랭킹 점수에는 거의 영향 없음
- 대신 CTR(클릭률)에 매우 큰 영향 있음

## 3. robots
SEO 허용 스위치(이게 있어야 검색 결과에 나타남)
```html
<meta name="robots" content="index, follow" />
```
- index → 이 페이지 검색 결과에 올려도 됨
- follow → 페이지 안의 링크를 따라가도 됨
- 기본값이 index, follow이기 때문에 안넣어도 되긴하지만 명시적으로 선언하여 SEO 페이지임을 알림.

## 4. canonical
중복페이지 방지
```html
<link rel="canonical" href="https://recallbuddy.web.app/" />
```
- 원본 URL을 명시하여 SEO 점수 분산되는 것을 방지(ex. /?utm=twitter, /home 등)

> 💡 URL 뒤에 trailling slash (/)를 붙이는 이유?
> - 대부분의 웹서버(Firebase Hosting 포함)는 요청 시 내부적으로 /를 붙여서 리다이렉트합니다. 즉 서버가 실제로 응답하는 최종 URL에는 slash가 붙어 있습니다.

## 5. Open Graph
SNS 미리보기 구성
```html
<!-- Open Graph -->
<meta property="og:type" content="website" />
<meta property="og:title" content="RecallBuddy - GitHub 커밋을 플래시카드로 복습하세요" />
<meta property="og:description" content="GitHub 리포지토리의 커밋 기록을 AI가 분석하여 자동으로 학습용 플래시카드를 생성합니다." />
<meta property="og:url" content="https://recallbuddy.web.app/" />
<meta property="og:site_name" content="RecallBuddy" />
<meta property="og:locale" content="ko_KR" />
<meta property="og:image" content="https://recallbuddy.web.app/og-image.png" />
<meta property="og:image:width" content="1200" />
<meta property="og:image:height" content="630" />

<!-- Twitter Card -->
<meta name="twitter:card" content="summary_large_image" />
<meta name="twitter:title" content="RecallBuddy - GitHub 커밋을 플래시카드로 복습하세요" />
<meta name="twitter:description" content="GitHub 커밋 기록을 AI가 분석하여 자동으로 학습용 플래시카드를 생성합니다." />
<meta name="twitter:image" content="https://recallbuddy.web.app/og-image.png" />
```
- Open Graph = “대부분의 SNS 공통 규격”
- Twitter Card = 트위터(X) 전용 규격

## 6. Structured Data (JSON-LD)
Google이 페이지/서비스 성격을 이해하는 데 도움 됨.
```html
<script type="application/ld+json">
{
    "@context": "https://schema.org",
    "@type": "WebApplication",
    "@id": "https://recallbuddy.web.app/#app",
    "name": "RecallBuddy",
    "url": "https://recallbuddy.web.app/",
    "description": "GitHub 리포지토리의 커밋 기록을 AI가 분석하여 자동으로 학습용 플래시카드를 생성합니다.",
    "image": "https://recallbuddy.web.app/og-image.png",
    "inLanguage": "ko-KR",
    "applicationCategory": "EducationalApplication",
    "operatingSystem": "Web",
    "offers": {
    "@type": "Offer",
    "price": "0",
    "priceCurrency": "KRW"
    },
    "featureList": [
    "GitHub 커밋 자동 분석",
    "AI 기반 플래시카드 생성",
    "매일 학습 알림",
    "코드 diff 기반 복습"
    ]
}
</script>
```
- @id : Google이 엔티티를 안정적으로 식별할 수 있게 해주는 고유 식별자
- image : OG 이미지와 동일한 이미지를 구조화 데이터에서도 참조. 리치 스니펫에 이미지 노출 가능성 증가
- inLanguage : 한국어 콘텐츠임을 명시적으로 알려줌. 한국어 검색 매칭 정확도 향상