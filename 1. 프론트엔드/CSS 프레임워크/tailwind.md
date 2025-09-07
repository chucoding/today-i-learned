# Tailwindcss

### 설치
- vite 프로젝트에 설치
  - https://tailwindcss.com/docs/guides/vite
- nextjs 프로젝트에 설치
  - https://tailwindcss.com/docs/guides/nextjs

> 💡 postcss, autoprefixer    
> tailwind v3에서 같이 설치해야하는 써드파티 모듈(v4부터는 상황에 맞게 설치)
> - postcss
>    - tailwind 클래스를 스타일로 변환해주는 도구
>    - Next에서 사용시 v4에서도 여전히 필요
>    - vite의 경우 PostCSS 내장
> - autoprefixer
>    - 크로스 브라우징 도구(-mz-, -webkit- 등의 접두사를 자동으로 붙여주는 플러그인)
>    - v4부터는 Lightning CSS 엔진 사용으로 불 필요(Rust로 작성되어 성능이 훨씬 빠름)

※ Tailwind CSS v2 부터는 IE를 지원하지 않기 때문에 PostCSS의 autoprefixer 등을 함께 사용해야함


### Preflight
브라우저 기본 설정 스타일 무효화

```css
/* tailwind.css */
@tailwind
```

※ Preflight 원하지 않는 경우 다음과 같이 설정
```js
// tailwind.config.js
module.exports = {
  corePlugins: {
    preflight: false,
  }
}
```