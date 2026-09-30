# 테스트용 export

## 목차

- [원칙](#원칙)
- [참고자료](#참고자료)

## 원칙

- **비공개 함수는 공개 API를 거쳐 테스트한다.** 내부 함수를 호출하는 공개 함수를 테스트하면 내부 함수도 함께 검증된다.
- **메서드가 아니라 동작을 테스트한다.** 사용자가 보는 동작이 그대로면 구현을 바꿔도 테스트는 바뀌지 않아야 한다.
- **테스트 때문에 공개 범위를 넓히지 않는다.** Guava는 public 선언에 `@VisibleForTesting`을 붙이는 것을 "나쁜 설계를 가리는 무화과 잎(fig leaf)"이라고 부른다. 테스트용으로 열어 둔 함수도 결국 다른 코드가 가져다 쓴다는 이유다.

## 참고자료

| TITLE | URL |
| --- | --- |
| 비공개 함수는 공개 API로 테스트 | https://testing.googleblog.com/2015/01/testing-on-toilet-prefer-testing-public.html |
| 구현을 바꿔도 테스트는 그대로 | https://testing.googleblog.com/2013/08/testing-on-toilet-test-behavior-not.html |
| 메서드 단위가 아닌 동작 단위 테스트 | https://testing.googleblog.com/2014/04/testing-on-toilet-test-behaviors-not.html |
| 테스트용 공개 범위 확대는 나쁜 설계 | https://guava.dev/releases/snapshot-jre/api/docs/com/google/common/annotations/VisibleForTesting.html |
