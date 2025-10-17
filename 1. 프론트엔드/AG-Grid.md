# AG Grid
Data Grid 오픈소스 라이브러리(React, Angular, Vue, JavaScript 지원)

https://www.ag-grid.com/react-data-grid/getting-started/

## 설치방법
### 1. 패키지 설치
```shell
npm install ag-grid-react
```
ag-grid-react 설치 시 ag-grid-community도 함께 설치됨.

### 2. 모듈 등록
```tsx
import { AllCommunityModule, ModuleRegistry } from 'ag-grid-community';

ModuleRegistry.registerModules([AllCommunityModule]);
```
번들 사이즈 줄이려면 AllCommunityModule 대신 사용하고자 하는 모듈들을 등록하면됨. (개발용 ValidationModule이 사이즈가 큼)

https://www.ag-grid.com/javascript-data-grid/modules/

> AllCommunityModule vs AllEnterpriseModule
> - 제공 기능(무료)
>   - 정렬/필터/페이징/셀 렌더링 등 핵심 테이블 기능 충분
> - 미제공 기능(유료)
>   - 엑셀 다운로드 기능 (csv만 무료 지원)
>   - 대용량/온디멘드 로딩