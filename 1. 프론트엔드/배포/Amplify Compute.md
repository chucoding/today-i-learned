# Amplify Compute

Amplify SSR 실행 환경의 한도

- [응답 크기 한도](#응답-크기-한도)
- [한도가 생기는 구조](#한도가-생기는-구조)
- [한도를 넘는 작업의 선택지](#한도를-넘는-작업의-선택지)
- [참고자료](#참고자료)

> 💡 Amplify Hosting compute란?
> Next.js 12 이후 앱의 서버 사이드 렌더링(SSR)을 Amplify가 대신 실행해 주는 관리형 실행 환경. 서버를 직접 띄우지 않는 대신 플랫폼이 정한 한도 안에서만 동작함.

## 응답 크기 한도

- 응답 최대 5.72MB. 넘으면 본문 없이 504 반환.
- 설정으로 늘릴 수 없는 고정 상한.

> "Currently, the maximum response size that Amplify supports for Next.js 12 and later apps using the Web Compute platform is 5.72 MB. Responses over that limit return 504 errors with no content to clients."
>
> 출처: [Troubleshooting server-side rendered applications - AWS Amplify Hosting](https://docs.aws.amazon.com/amplify/latest/userguide/troubleshooting-SSR.html)

## 한도가 생기는 구조

- 요청마다 함수가 실행되고, 응답을 다 만든 뒤 한 번에 돌려주는 모델
  - 응답 전체를 메모리에 모으므로 크기 상한이 필요함.
  - Amplify는 Next.js streaming을 지원하지 않는 기능으로 명시함.
    - AWS: Amplify support for Next.js의 Unsupported features 목록.
- 여러 고객이 함께 쓰는 관리형 인프라라 한 요청이 자원을 독점하지 못하게 막음.
- Lambda 동기 호출의 버퍼 응답 한도(요청, 응답 각 6MB)와 같은 성격의 상한
  - Amplify가 내부 구현을 Lambda라고 밝히지는 않았으므로 비교로만 볼 것.
  - AWS: Lambda quotas의 Invocation payload 행(동기 요청, 응답 각 6MB, 비동기 1MB).
- 대비: 상주 서버(EC2의 Tomcat 등)는 연결을 잡고 만드는 대로 흘려보내므로 플랫폼 차원의 응답 크기 한도 없음.

## 한도를 넘는 작업의 선택지

| 상황 | 선택지 | 비고 |
|---|---|---|
| 결과가 큼 | 결과를 S3에 저장하고 서명 URL만 응답 | 응답은 URL 한 줄로 줄어듦 |
| 처리가 오래 걸림 | 작업 ID를 먼저 돌려주고 폴링하는 비동기 작업 | Lambda 비동기 호출 페이로드는 1MB라 큰 입력도 S3에 둠 |
| 둘 다 | 컨테이너(ECS, EKS)나 EC2의 상주 서버로 이전 | 앞단 로드밸런서 유휴 시간 제한(ALB 기본 60초, 1~4000초)은 따로 조정 |

- Lambda를 직접 쓸 수 있으면 응답 스트리밍으로 최대 200MB까지 반환 가능
  - 처음 6MB 이후는 최대 2MBps로 전송됨.

> "Additionally, response streaming functions can return payloads up to 200 MB, compared to the 6 MB maximum for buffered responses."
>
> 출처: [Response streaming for Lambda Event Functions](https://docs.aws.amazon.com/lambda/latest/dg/configuration-response-streaming.html)

> "By default, Elastic Load Balancing sets the idle timeout value for your load balancer to 60 seconds."
>
> 출처: [Edit attributes for your Application Load Balancer](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/edit-load-balancer-attributes.html)

## 참고자료

| TITLE | URL |
|---|---|
| Amplify SSR 응답 5.72MB 한도 | [https://docs.aws.amazon.com/amplify/latest/userguide/troubleshooting-SSR.html](https://docs.aws.amazon.com/amplify/latest/userguide/troubleshooting-SSR.html) |
| Amplify의 Next.js streaming 미지원 | [https://docs.aws.amazon.com/amplify/latest/userguide/ssr-amplify-support.html](https://docs.aws.amazon.com/amplify/latest/userguide/ssr-amplify-support.html) |
| Lambda 동기 6MB와 비동기 1MB 페이로드 | [https://docs.aws.amazon.com/lambda/latest/dg/gettingstarted-limits.html](https://docs.aws.amazon.com/lambda/latest/dg/gettingstarted-limits.html) |
| Lambda 응답 스트리밍 200MB | [https://docs.aws.amazon.com/lambda/latest/dg/configuration-response-streaming.html](https://docs.aws.amazon.com/lambda/latest/dg/configuration-response-streaming.html) |
| ALB 유휴 시간 제한 기본값과 범위 | [https://docs.aws.amazon.com/elasticloadbalancing/latest/application/edit-load-balancer-attributes.html](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/edit-load-balancer-attributes.html) |
