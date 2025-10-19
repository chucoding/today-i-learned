# Vite

## 설치
https://ko.vite.dev/guide/
```shell
pnpm create vite
```
- Project name
- Select a framework
- Select a variant
- Use rolldown-vite (Experimental)?
- Install with pnpm and start now?

## 장점
- 개발 서버 기동시간 단축
- 빠른 HMR(Hot Module Replacement) - Native ESM을 이용해 변경된 모듈만 즉시 교체

> 💡 사실상 RollUp의 장점   
> 이전에는 Go로 작성된 Esbuild를 사용하여 번들링하였으나, 최근에는 Rollup을 사용하여 번들링하는 것으로 변경됨. Esbuild가 더 빠르지만 앞으로 나올 Rolldown이 빌드 성능을 크게 향상시킬것

## 설정
MUI처럼 무거운 라이브러리 사용시 package.json에 다음과 같이 최적화 설정
(vite는 개발 중에 필요한 모듈을 동적으로 불러오는데 서버 시작 시 미리 처리되도록 설정) 
```json
"vite": {
    "optimizeDeps": {
      "include": [
        "@mui/material",
        "@mui/icons-material"
      ]
    }
  }
```

## alias
alias 동작방식 설명 

```tsx
// vite.config.ts
resolve: {
  alias: {
    '@': '/src'
  }
}

// 사용 시
import { Button } from '@shared/ui/Button'
```

- Vite가 런타임에 @를 /src로 변환 (브라우저에서 실제로는 변환된 경로로 요청)

> 💡 디자인시스템에서는 사용을 권장하지 않음   
> 빌드 후에 alias(@)가 남아있다면 오류가 발생할 수 있고 어떤 경로인지 파악하기 힘듬

## 빌드
```powershell
vite build
```
※ 일반적으로 타입스크립트 같이 사용시 `tsc -b && vite build`

#### 빌드 테스트
vite로 빌드시 `dist` 폴더가 생기지만 CORS 오류로 인해 로컬 테스트 불가능
- 대신 `vite preview` 명령어로 빌드 결과물을 확인할 수 있음


## 배포
- Dockerfile에 베이스 이미지를 node로 설정하고 `vite preview` 명령어로 리소스를 띄움
- Nginx, Apache와 같은 전용 웹 서버나 클라우드 호스팅 서비스를 사용 후 `dist` 폴더를 배포

💡 어느방법이 맞을까?
> 프로덕션 환경에서는 Nginx, Apache와 같은 전용 웹 서버나 클라우드 호스팅 서비스를 사용하는 것이 보안, 성능, 확장성 측면에서 더 적합. vite preview는 간단한 정적 파일 서버일 뿐이며, 프로덕션 환경에서 필요한 다양한 기능들(로드 밸런싱, HTTPS, 보안 설정 등)을 제공하지 않음

#### preview 배포방법
별도의 gateway가 존재하는 경우 간단하게 배포할 때 사용
```Dockerfile
FROM node:18-alpine
WORKDIR /app
COPY package.json .
RUN npm install
COPY . .
RUN npm run build
EXPOSE 3000
CMD ["npm", "run", "preview"]

# docker build -t my-vite-app .
# docker run -dp 3000:3000 my-vite-app
```

vite에서 React를 사용하는 경우 config를 다음과 같이 수정(React + svg + tailwindcss)
```js
/* vite.config.js */
import react from '@vitejs/plugin-react'
import svgr from 'vite-plugin-svgr'
import tailwindcss from '@tailwindcss/vite'

export default defineConfig({
  plugins: [react(), svgr(), tailwindcss],
  server: {
    host: true,
    strictPort: true,
    port: 3000
  }
})
```

## 참고자료
| TITLE | URL |
| --- | --- |
| VITE 사용이유 | https://ko.vitejs.dev/guide/why |