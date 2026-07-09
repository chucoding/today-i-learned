# model
FSD Segment의 한 종류로 schema, interfaces, store, business logic 등의 애플리케이션 도메인 모델을 포함.

## 기본값(fallback)
API 통신 후 기본값 지정이나 fallback 값은 모델 하위에 파일을 만들어 관리
> 단, 주의할 점은 config, const 등의 기술 역할명은 사용하면 안됨. (도메인 관련 명칭으로 사용. ex.`user.ts`)