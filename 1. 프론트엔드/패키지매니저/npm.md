# npm
Node Package Manager

## 특징
- NodeJS 설치시 자동으로 같이 설치됨.
- Corepack으로 사실상 대체 가능하지만 왠만하면 안지우는게 좋음.
    - 어차피 Node 업데이트시 다시 설치되기도 하고 Corepack 재설치 할때도 npm이 필요함.

## 명령어

### 설치
npm 레지스트리에서 필요한 모듈들을 내 PC 또는 프로젝트로 설치하는 방법 정리

#### 로컬 설치
프로젝트에 필요한 라이브러리 의존성 추가시 사용
```shell
npm i ...
```

#### 글로벌 설치
firebase-tools 등 초기 프로젝트 셋팅 용 CLI 도구 추가시 유용하게 사용(자동으로 환경변수 PATH에 경로가 셋팅되어 편리)
```
npm i -g ...
```

CI/배포용 "엄격 설치" 모드
```shell
npm ci
```
- node_modules를 지우고 다시 설치
- package-lock.json과 완벽히 일치하는 버전만 설치하고, lock을 절대 수정하지 않음.
- lock이 소스랑 안 맞으면 즉시 실패. 재현성/속도가 좋아서 CI에 많이 씀.

> 💡 npm 버전과 관계없이 package.json에 버전 명시가 없으면 설치할 모듈들은 무조건 최신 버전을 받아온다. (만약 PC에 설치된 Node 버전이랑 맞지 않으면 오류 발생할 수도 있음.)

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