# Typescript
정적 타입의 컴파일 언어

### 특징
- 타입스크립트 컴파일러 또는 바벨(Babel)을 통해 자바스크립트 코드로 변환됨.
- 코드 작성 단계에서 타입을 체크해 오류를 확인할 수 있고 미리 타입을 결정하기 때문에 실행 속도가 매우 빠르다.(?)

### 셋팅방법
1. 컴파일러 설치
```powershell
npm install typescript
tsc --version
```

2. tsconfig.json 파일 생성
```powershell
npx tsc --init 
```

❗ tsconfig.json 파일 생성 안하면 function duplicate오류남

### 컴파일 방법
js 자동변환
```powershell
tsc -w
```

js 수동변환
```powershell
# main.js 변환
npx tsc ./src/main.ts
```

### 타입 추론
타입스크립트가 타입을 추론하는 경우
- 초기화된 변수
- 기본값이 설정된 매개 변수
- 반환 값이 있는 함수

### 목차
[1. 설정](./1.설정.md)  
[2. 문법](./2.문법.md)
