# Storybook

UI 컴포넌트를 독립적인 환경에서 그려볼 수 있는 툴

## 실행

```bash
pnpm run storybook
```

## 장점

1. 결과물을 쉽고 빠르게 확인할 수 있음
2. 리팩토링을 강제함

## 사이드바 구성

**Category > Folder > Component > Docs / Story**

### 구성 방식


| 방식              | 쓰는 법                                                                    | 언제                      |
| --------------- | ----------------------------------------------------------------------- | ----------------------- |
| 명시              | `title: 'Design System/Atoms/Checkbox'`                                 | 파일 위치와 사이드바를 따로 두고 싶을 때 |
| 암묵 (auto-title) | `title` 생략 + `main.ts` 의 `stories: [{ directory, files, titlePrefix }]` | 기본. 파일을 옮기면 사이드바도 따라옴   |


CSF3 에서 `title` 은 선택이다. 손으로 쓰면 파일을 옮길 때마다 어긋나므로 패키지 단위 디자인시스템에서는 auto-title 쪽이 우세하다.

> 💡 [CSF](https://storybook.js.org/docs/api/csf/index) 가 뭐지?
>
> - Component Story Format. 스토리 파일을 쓰는 형식 규격이고 뒤의 숫자는 버전임. 지금 설치하면 받는 게 CSF3 임. (6.4 등장, 7.0부터 기본)
> - CSF2 는 스토리가 함수라 `Template.bind({})` 로 만들었지만, CSF3 은 객체임. `component` 만 있으면 렌더 함수를 스토리북이 만들어 줌.
> - 그 연장선에서 `title` 도 필수가 아니게 됨. 생략하면 파일 경로에서 자동으로 만듦.

### 예제


| 패턴            | 값                                                 | 채택                            |
| ------------- | ------------------------------------------------- | ----------------------------- |
| Atomic Design | Atoms / Molecules / Organisms / Templates / Pages | Codecademy, The Guardian(일부만) |
| 기능별           | Buttons / Forms / Layout / Navigation             | Spotify Backstage, Grommet    |
| 성숙도별          | Ready / Experimental / Deprecated                 | IBM Carbon, Workday Canvas    |


### 컴포넌트 구성요소

- **Docs** : autodocs 가 만든 문서 노드. 컴포넌트의 첫 자식이라 스토리들이 그 아래에 선다
  - `tags: ['autodocs']` 켜기
  - `tags: ['!autodocs']` 끄기
- **Default** : `export const Default` 의 표시 이름(export 이름이 start case 로 바뀐다. `PrimaryLarge` => `Primary Large` )

문서 이름과 모드는 `main.ts` 에서 바꾼다.

```tsx
docs: {
  defaultName: '문서',   // 사이드바의 'Docs' 라벨
  docsMode: false,      // true 면 스토리를 감추고 문서만 보임. 문서 사이트로 배포할 때
}
```

자동 생성 Docs 를 직접 쓴 글로 바꾸려면 MDX 를 붙인다.

```mdx
import { Meta } from '@storybook/addon-docs/blocks';
import * as ButtonStories from './Button.stories';

<Meta of={ButtonStories} />   {/* attached. 해당 컴포넌트의 Docs 자리를 대체 */}
<Meta title="Guides/소개" />   {/* unattached. 스토리 없는 독립 문서 노드 */}
```

`<Meta>` 를 아예 빼면 파일의 물리적 위치로 자리가 정해진다.

### 컴포넌트 내부 순서

공식블로그가 권하는 순서는 다음과 같다.

**Overview(문서) → Playground(모든 prop 을 컨트롤로 노출) → 기능별 변형 → 실제 조합 예시**

### 겉모습

`manager.ts` 에서 바꾼다.

```tsx
import startCase from 'lodash/startCase.js';

addons.setConfig({
  sidebar: {
    showRoots: false,  // 최상위를 섹션이 아니라 폴더로 표시
    renderLabel: ({ name, type }) => (type === 'story' ? name : startCase(name)),
  },
})
```

## 컨트롤

스토리의 `args` 를 화면에서 바꿔 보게 해 주는 애드온. (@storybook/addon-controls)

### Canvas

Canvas 패널은 `compact` 로 그려진다.

`compact` 면 Description 과 Default 셀을 **아예 만들지 않는다.**

### Docs

`compact` 가 아니기 때문에 Name, Description, Default, Control이 다 보인다.

## 참고자료


| TITLE                                  | URL                                                                                                                                                                                                                                                                                                  |
| -------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 스토리북 작성을 통해 얻게 되는 리팩토링 효과              | [https://fe-developers.kakaoent.com/2022/220609-storybookwise-component-refactoring/](https://fe-developers.kakaoent.com/2022/220609-storybookwise-component-refactoring/)                                                                                                                           |
| 스토리북 설치 및 셋팅                           | [https://yamoo9.github.io/react-master/lecture/sb-install.html#storybook-가이드-파일](https://yamoo9.github.io/react-master/lecture/sb-install.html#storybook-%E1%84%80%E1%85%A1%E1%84%8B%E1%85%B5%E1%84%83%E1%85%B3-%E1%84%91%E1%85%A1%E1%84%8B%E1%85%B5%E1%86%AF)                                       |
| 스토리북 패키지 구조 다시잡기                       | [https://www.howdy-mj.me/storybook/writing-stories](https://www.howdy-mj.me/storybook/writing-stories)                                                                                                                                                                                               |
| 스토리북 발표자료                              | [https://docs.google.com/presentation/d/1HwlpIaUrZBN-WRpjuA1B9U2nQA6apN0R/edit?usp=share_link&ouid=107042004582488103761&rtpof=true&sd=true](https://docs.google.com/presentation/d/1HwlpIaUrZBN-WRpjuA1B9U2nQA6apN0R/edit?usp=share_link&ouid=107042004582488103761&rtpof=true&sd=true) |
| Naming components and hierarchy        | [https://storybook.js.org/docs/writing-stories/naming-components-and-hierarchy](https://storybook.js.org/docs/writing-stories/naming-components-and-hierarchy)                                                                                                                                       |
| Sidebar & URLs                     | [https://storybook.js.org/docs/configure/user-interface/sidebar-and-urls](https://storybook.js.org/docs/configure/user-interface/sidebar-and-urls)                                                                                                                                                   |
| Autodocs                               | [https://storybook.js.org/docs/writing-docs/autodocs](https://storybook.js.org/docs/writing-docs/autodocs)                                                                                                                                                                                           |
| MDX                                    | [https://storybook.js.org/docs/writing-docs/mdx](https://storybook.js.org/docs/writing-docs/mdx)                                                                                                                                                                                                     |
| Configure story loading                | [https://storybook.js.org/docs/configure/](https://storybook.js.org/docs/configure/)                                                                                                                                                                                                                 |
| Structuring your Storybook             | [https://storybook.js.org/blog/structuring-your-storybook/](https://storybook.js.org/blog/structuring-your-storybook/)                                                                                                                                                                               |
| Controls                               | [https://storybook.js.org/docs/essentials/controls](https://storybook.js.org/docs/essentials/controls)                                                                                                                                                                                               |
| ArgTypes                               | [https://storybook.js.org/docs/api/arg-types](https://storybook.js.org/docs/api/arg-types)                                                                                                                                                                                                           |


