# MaterialUI (MUI)
안드로이드 시스템에서 검증된 높은 수준의 디자인 퀄리티를 자랑하는 CSS 프레임워크

# Custom CSS

### Emotion or Styled API
- Material-UI v5 부터는 Emotion API 또는 @mui/system 라이브러리의 syled API 사용 권장(MUI v4의 makeStyles, withStyles 대체)

# Icon

### IconButton
기본적으로 `Ripple` 효과가 적용되어 있음. 

#### 사용 예시
다음과 같이 아이콘을 Wrapping하여 사용 가능
```jsx
import { IconButton } from '@material-ui/core';
import MoreVertIcon from '@material-ui/icons/MoreVert';

<IconButton onClick={onClick}>
    <MoreVertIcon fontSize='large' />
</IconButton>
```

>💡 Ripple 효과   
Android 5.0 (21) 머터리얼 디자인에 소개되었으며, 버튼을 클릭하였을때, 물결이 퍼지듯이 효과가 나오는 기능

# 프로젝트 셋팅
MUI 프로젝트 셋팅시 유용한 컴포넌트

## CssBaseline
브라우저 간의 일관된 스타일링을 위해 기본 CSS 리셋을 제공(normalize.css와 유사한 역할 수행)

#### 사용 예시
```jsx
import { CssBaseline } from '@material-ui/core';

<CssBaseline />
```

> 💡 사용시 적용되는 주요 스타일   
margin제거, 기본배경색(다크모드포함), 폰트 

> 💡 사용시 주의사항   
ThemeProvider 컴포넌트 내부에서 사용해야함.

## ThemeProvider
MUI 테마를 적용하는 컴포넌트 (폰트 적용시 유용)

#### 사용 예시
```jsx
import { ThemeProvider } from '@material-ui/core/styles';
import { createTheme as createMuiTheme } from '@material-ui/core/styles';

const theme = createMuiTheme({
    typography: {
        fontFamily: 'Pretendard',
    },
});
```

# 참고자료
| TITLE            | URL                                                 |
|------------------|-----------------------------------------------------|
| MUI v4=>v5       | https://velog.io/@dlruddms5619/Migration-MUI-v4-to-v5-makeStyles-%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B0   |
| ripple 효과      | https://velog.io/@vov3616/Android-Ripple-%ED%9A%A8%EA%B3%BC-%EA%B5%AC%ED%98%84%ED%95%98%EA%B8%B0|
