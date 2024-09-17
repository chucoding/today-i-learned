# JavaScript 패키지 매니저
프로젝트의 종속성을 관리하는 도구

### 1. npm
- Node.js의 기본 패키지 매니저로, Node.js와 함께 설치됨
- 2010년에 등장해서, JavaScript 커뮤니티에서 널리 사용됨
- package-lock.json 파일을 사용하여 버전 관리

```
npm install <package-name>
```

### 2. yarn 
- Facebook이 2016년에 개발하여 공개
- NPM의 속도를 해결하기 위해 만들어짐 (병렬처리 및 로컬캐시 최초 고안)
- yarn.lock 파일을 사용하여 버전 관리

```
yarn add <package-name>
```

### npm vs yarn
- 옛날에는 yarn을 사용하면 되지만 요즘은 속도나 보안면에서 큰 차이가 없음. (npm 5버전 이상부터 npm이 yarn을 벤치마킹)
- yarn은 오히려 next vercel 배포시 오류가 발생하므로 npm 사용하는것이 더 나음


# 참고자료
| TITLE | URL |
|---|---|
| npm vs yarn | https://velog.io/@jma1020/npm-yarn-%EB%AC%B4%EC%97%87%EC%9D%B4%EA%B3%A0-%EC%96%B4%EB%96%A4-%EC%B0%A8%EC%9D%B4 |