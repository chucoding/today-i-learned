# NextJS
리액트 서버사이드렌더링(Server Side Randering, SSR) 프레임워크

- 검색앤진최적화(Search engine Optimization, SEO)
- pages폴더로 라우팅
- SSR 렌더링을 지원

# 설치 및 초기 셋팅
```powershell
npx create-next-app
```

# Style
- styled-jsx를 기본으로 지원하지만 코드 지저분해지는 단점이 있음
```
<style jsx> </style>
```
- tailwind 사용할것

# next/script
- script 태그를 사용하는것보다 조금 더 최적화된 사용이 가능하다.
- script 태그를 사용하면 페이지 렌더링을 도중에 막는다. (스크립트 사이즈가 큰 경우 로딩 지연시간있음)

- next/script는 비동기로 진행됨.

- **보다 안전한 방식으로 타사 스크립트를 로드하기 위한 자동 스크립트 우선 순위 지정 및 지원**

- 스크립트를 추적하고 최적화하기 위해 반드시 `id` 속성을 정의(안그럼 빌드할때 오류남)

```jsx
import Script from 'next/script'

export default function Dashboard() {
  return (
    <>
      <Script src="https://example.com/script.js" id="my-script" />
    </>
  )
}
```

# next/font
자동으로 폰트 최적화

모든 글꼴 파일에 대한 자동 자체 호스팅 내장

Google폰트 사용시 

빌드시 폰트 파일이 다운로드 되며 자체 호스팅되기 때문에 브라우저에서 따로 Google로 요청을 보내지 않는다.

https://nextjs.org/blog/next-13#new-app-directory-beta

https://phrygia.github.io/react/2022-11-06-nextjs-13/

[https://velog.io/@yoosion030/Next.js-13-정리](https://velog.io/@yoosion030/Next.js-13-%EC%A0%95%EB%A6%AC)


# 참고자료

---

| TITLE | URL |
| --- | --- |
| next/script | https://nextjs.org/docs/api-reference/next/script |
|  |  |