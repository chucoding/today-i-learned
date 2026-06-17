# AG Grid
Data Grid 오픈소스 라이브러리

https://www.ag-grid.com/react-data-grid/getting-started/

## 설치방법
### 1. 패키지 설치
```shell
npm install ag-grid-react
```
> ag-grid-react 설치 시 ag-grid-community도 함께 설치됨.

### 2. 모듈 등록
> [모듈 등록 가이드문서](https://www.ag-grid.com/javascript-data-grid/modules/)

```tsx
import { AllCommunityModule, ModuleRegistry } from 'ag-grid-community';

ModuleRegistry.registerModules([AllCommunityModule]);
```
> ❗️ 주의할 점
> - 번들 사이즈 줄이려면 AllCommunityModule 대신 사용하고자 하는 모듈들을 직접 등록
> - 개발용 ValidationModule이 사이즈가 큼

## 기능
||AllCommunityModule|AllEnterpriseModule||
|---|---|---|---|
|정렬|O(단, 소계,합계 커스텀 구현시 사용 불가)|O||
|필터|O|O||
|페이징|O|O||
|총계|X|O||
|소계(그룹 합계)|X|O||
|순번|X|O||
|엑셀 다운로드 기능|X(CSV만 무료 지원)|O||
|체크박스|O|O|https://www.ag-grid.com/react-data-grid/row-selection-multi-row/|
|아코디언|X|O|https://www.ag-grid.com/react-data-grid/grouping-opening-groups/|
