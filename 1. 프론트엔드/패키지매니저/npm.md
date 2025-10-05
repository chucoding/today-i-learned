# npm

## 명령어
CI/배포용 "엄격 설치" 모드
```shell
npm ci
```
- node_modules를 지우고 다시 설치
- package-lock.json과 완벽히 일치하는 버전만 설치하고, lock을 절대 수정하지 않음.
- lock이 소스랑 안 맞으면 즉시 실패. 재현성/속도가 좋아서 CI에 많이 씀.