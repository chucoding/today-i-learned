# standalone 모드

> - [next.config.js: output](https://nextjs.org/docs/app/api-reference/config/next-config-js/output) (문서 기준 v16.3.5)
> - [vercel/next.js examples/with-docker/Dockerfile](https://github.com/vercel/next.js/blob/canary/examples/with-docker/Dockerfile)

배포에 필요한 파일만 추려 `.next/standalone` 한 폴더에 복사해 주는 빌드 모드.

```js
// next.config.js
module.exports = { output: 'standalone' }
```

- `next build`는 원래도 `@vercel/nft`로 필요한 파일을 정적 분석해 `.next/*.nft.json`에 목록을 남긴다
- 그 목록을 읽어 **실제로 복사하는 일은 배포하는 쪽 몫**이었고, `standalone`은 그 복사까지 대신 한다
- 결과물은 추적된 `node_modules` 일부 + 최소 `server.js` + `package.json`

## 왜 쓰나

`node_modules`를 통째로 옮기지 않기 위해서다. 컨테이너 이미지로 말아 올리는 self-hosting이 본진이고, 관리형 플랫폼(Vercel, Amplify 등)이나 정적 사이트(`output: 'export'`)에는 쓸 일이 없다.

