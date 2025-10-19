# pnpm

패키지 설치시 콘텐츠 주소 저장소(CAS: Content-Addressable Store)에 저장하여 디스크 공간은 프로젝트와 의존성의 수에 비례하여 더 많이 절약되고 더 빠르게 설치 가능한 패키지 매니저

## 설치방법
```shell
# macOS (Homebrew)
brew install pnpm

# 또는 npm 사용
npm install -g pnpm
```

## 명령어
### 패키지 설치
```shell
pnpm install --frozen-lockfile
```
- pnpm-lock.yaml과 불일치하면 실패(lock 수정 안 함).

> 💡 pnpm은 npm과 다르게 node_modules를 지우지 않아도됨.
> - node_modules에는 실제 파일을 복사/덮어쓰는 대신 store를 가리키는 심볼릭 링크만 관리되기 때문에 남아있던 쓰레기 파일 때문에 오염될 여지가 적음
> - 단, OS 또는 node 버전 바뀌는 경우 청소 필요
>   - `rm -rf node_modules`
>   - `pnpm store prune`

패키지 최신 버전 업데이트
```shell
# vite update example
pnpm update vite
pnpm update vite@latest
```

패키지 추가/삭제
```shell
pnpm add     # 의존성 추가
pnpm remove  # 의존성 삭제
```

### Filtering
모노레포 상황에서 특정 패키지로 명령을 제한

#### `pnpm -C <path>` 옵션과의 차이점
- `-C` : 위치를 바꿔 실행
- `--filter` : 위치는 그대로 두고 대상만 골라 실행

#### 결정 규칙
- 정책/재사용 스크립트(build, test, lint 등): --filter 권장
- 즉석·단발성·위치보정(특정 폴더에서만 한 번 실행): -C 선호
- 둘 다 가능하지만 대상 여러 개를 다뤄야 함: -r + --filter 조합이 깔끔

### 전체 빌드
```
pnpm -r build
```
`-r`(`--recursive`)옵션 : workspace에 정의된 모든 패키지의 build 스크립트를 실행