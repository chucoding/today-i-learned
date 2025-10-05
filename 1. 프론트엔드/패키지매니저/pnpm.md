# pnpm
빠르고, 효율적인 패키지 매니저

## 동기
패키지 설치시 콘텐츠 주소 저장소(CAS: Content-Addressable Store)에 저장하여 디스크 공간은 프로젝트와 의존성의 수에 비례하여 더 많이 절약되고 더 빠르게 설치 가능

## 설치방법
```shell
# macOS (Homebrew)
brew install pnpm

# 또는 npm 사용
npm install -g pnpm
```

## 명령어
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