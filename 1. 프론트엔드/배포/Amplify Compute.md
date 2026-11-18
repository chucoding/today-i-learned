# Amplify Compute

Amplify SSR 실행 환경의 응답 크기 한도

- [응답 크기 한도](#응답-크기-한도)
- [한도가 생기는 구조](#한도가-생기는-구조)
- [한도를 넘는 응답의 선택지](#한도를-넘는-응답의-선택지)
- [참고자료](#참고자료)

> 💡 Amplify Hosting compute란?
> Next.js 12 이후 앱의 서버 사이드 렌더링(SSR)을 Amplify가 대신 실행해 주는 관리형 실행 환경. 서버를 직접 띄우지 않는 대신 플랫폼이 정한 한도 안에서만 동작함.

## 응답 크기 한도

- 응답 최대 5.72MB. 넘으면 본문 없이 504 반환.

> "Currently, the maximum response size that Amplify supports for Next.js 12 and later apps using the Web Compute platform is 5.72 MB. Responses over that limit return 504 errors with no content to clients."
>
> 출처: [Troubleshooting server-side rendered applications - AWS Amplify Hosting](https://docs.aws.amazon.com/amplify/latest/userguide/troubleshooting-SSR.html)

## 한도가 생기는 구조

- 응답을 흘려보내지 못하고 한 번에 돌려주는 환경
  - Amplify는 Next.js streaming을 지원하지 않는 기능으로 명시함.
    - AWS: Amplify support for Next.js의 Unsupported features 목록.
  - 응답 전체를 모아 돌려주므로 크기 상한이 생기는 것으로 추정. AWS가 이유를 설명한 문서는 없음.
- Lambda 동기 호출의 버퍼 응답 한도(요청, 응답 각 6MB)와 비슷한 크기
  - Amplify가 내부 구현을 Lambda라고 밝히지는 않았으므로 비교로만 볼 것.
  - AWS: Lambda quotas의 Invocation payload 행.
- 대비: 직접 운영하는 상주 서버(EC2의 Tomcat 등)는 관리형 플랫폼이 정한 응답 크기 상한을 따르지 않음. 상한은 운영자가 서버와 프록시 설정으로 정함.

## 한도를 넘는 응답의 선택지

- 결과를 S3에 저장하고 서명 URL만 응답
  - 응답 본문이 URL 한 줄로 줄어듦.
- 직접 운영하는 상주 서버(ECS, EKS의 컨테이너나 EC2)로 이전
  - 응답 크기 상한을 서버와 프록시 설정으로 직접 정함.

## 참고자료

| TITLE | URL |
|---|---|
| Amplify SSR 응답 5.72MB 한도 | [https://docs.aws.amazon.com/amplify/latest/userguide/troubleshooting-SSR.html](https://docs.aws.amazon.com/amplify/latest/userguide/troubleshooting-SSR.html) |
| Amplify의 Next.js streaming 미지원 | [https://docs.aws.amazon.com/amplify/latest/userguide/ssr-amplify-support.html](https://docs.aws.amazon.com/amplify/latest/userguide/ssr-amplify-support.html) |
| Lambda 동기 호출 6MB 페이로드 | [https://docs.aws.amazon.com/lambda/latest/dg/gettingstarted-limits.html](https://docs.aws.amazon.com/lambda/latest/dg/gettingstarted-limits.html) |
