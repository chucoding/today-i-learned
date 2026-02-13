# npm
Node Package Manager

## 명령어

### 설치
#### 로컬 설치
```shell
npm i
```

#### 글로벌 설치
```
npm i -g
```

CI/배포용 "엄격 설치" 모드
```shell
npm ci
```
- node_modules를 지우고 다시 설치
- package-lock.json과 완벽히 일치하는 버전만 설치하고, lock을 절대 수정하지 않음.
- lock이 소스랑 안 맞으면 즉시 실패. 재현성/속도가 좋아서 CI에 많이 씀.

### 빌드
```shell
npm run build
```

# npx
npm(Node Package Module)의 5.2.0 버전부터 새로 추가된 도구

```
npm = Package Manager (관리)
npx = Package Runner (실행)
```

1. 기본적으로 실행되어야할 패키지가 경로에 있는지 먼저 확인

2. 경로에 제대로 있다면, 그대로 실행

3. 없으면 npx가 최신 버전의 패키지를 설치를 한 후에 실행

💡 원래는 cra 설치시 global(-g 옵션)으로 해야했으나 npx가 나온뒤로는 글로벌 설치 안해도 이용할 수 있음(node online registry에서 가져와서 사용)

**npx create-react-app 장점**

1. Disk Space낭비 X
2. 최신 버전을 받아옴(다른 버전의 노드 사용가능)