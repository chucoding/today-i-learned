# overflow

overflow hidden과 clip 비교

## 목차

- [hidden과 clip 차이](#hidden과-clip-차이)
- [선택 기준](#선택-기준)
- [참고자료](#참고자료)

## hidden과 clip 차이


| 항목                     | hidden                                 | clip                                      |
| ---------------------- | -------------------------------------- | ----------------------------------------- |
| 상자 밖 내용                | 가림                                     | 가림                                        |
| 스크롤 컨테이너               | 만듦 (`scrollTop`, `scrollTo()`로 스크롤 가능) | 만들지 않음 (프로그램 스크롤 불가)                      |
| 포커스 이동으로 안쪽이 밀려남       | 있음                                     | 없음                                        |
| 한 축만 지정 시 다른 축         | `visible`이 `auto`로 계산됨                 | `visible`과 짝이면 그대로 `visible`              |
| 새 BFC                  | 만듦 (float 감싸기, margin 상쇄 방지)           | 만들지 않음                                    |
| `overflow-clip-margin` | 무시됨                                    | 적용됨 (Chrome은 두 축 모두 clip일 때만, Safari 미지원) |
| 지원                     | 전 브라우저                                 | Chrome 90, Firefox 81, Safari 16부터        |

> 💡 BFC(Block Formatting Context, 블록 서식 맥락)란?
> - 블록 상자가 배치되고 float가 주변 요소와 상호작용하는 독립된 레이아웃 영역
> - 새 BFC를 만든 요소의 효과
>   - 안쪽 float를 감쌈. (float 자식만 있어도 부모 높이가 0이 되지 않음)
>   - 바깥 float를 피해 배치됨.
>   - 자식과의 margin 상쇄(겹침)가 일어나지 않음.
> - BFC만 필요하면 `overflow: hidden` 대신 `display: flow-root` 권장 (잘라 내기 같은 부작용 없음)

## 선택 기준

- 판단 질문 : 가리기만 하면 되는가, 스크롤 컨테이너가 필요한가
- clip
  - 장식이나 애니메이션이 상자 밖으로 삐져나오는 것만 자를 때
  - 한 축만 자르고 다른 축은 그대로 둘 때
  - 코드나 포커스 이동으로 안쪽이 스크롤되면 안 될 때
- hidden
  - 코드로 `scrollTop`을 다루는 커스텀 스크롤, 캐러셀
  - 잘라 내기와 BFC가 함께 필요할 때 (BFC만 필요하면 `display: flow-root`)
  - Safari 16 미만 지원이 필요할 때

## 참고자료


| TITLE                                     | URL                                                                                                                                                                                            |
| ----------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| hidden과 clip의 스크롤 컨테이너, BFC, 한 축 지정 시 계산값 | [https://developer.mozilla.org/en-US/docs/Web/CSS/overflow](https://developer.mozilla.org/en-US/docs/Web/CSS/overflow)                                                                         |
| BFC 정의와 flow-root로 만드는 방법 | [https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_display/Block_formatting_context](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_display/Block_formatting_context) |
| overflow-clip-margin은 clip에만 적용           | [https://developer.mozilla.org/en-US/docs/Web/CSS/overflow-clip-margin](https://developer.mozilla.org/en-US/docs/Web/CSS/overflow-clip-margin)                                                 |
| clip 값 브라우저 지원 버전                         | [https://github.com/mdn/browser-compat-data/blob/main/css/properties/overflow.json](https://github.com/mdn/browser-compat-data/blob/main/css/properties/overflow.json)                         |
| overflow-clip-margin 브라우저 지원과 Chrome 제약   | [https://github.com/mdn/browser-compat-data/blob/main/css/properties/overflow-clip-margin.json](https://github.com/mdn/browser-compat-data/blob/main/css/properties/overflow-clip-margin.json) |
