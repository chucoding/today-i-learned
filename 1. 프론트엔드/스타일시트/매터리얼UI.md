# MaterialUI (MUI)
안드로이드 시스템에서 검증된 높은 수준의 디자인 퀄리티를 자랑하는 CSS 프레임워크

# makeStyles
- material UI에서 사용하는 HOC
- 함수를 호출하여 스타일 객체를 생성
- React 18에서 @mui/styles 대신 @mui/system을 사용해야함. (호환성 문제)

```jsx
import { makeStyles } from '@mui/styles';

const useStyles = makeStyles((theme) => ({
  root: {
    backgroundColor: theme.palette.primary.main,
    padding: '10px',
  },
}));

function MyComponent() {
  const classes = useStyles();
  return <div className={classes.root}>Hello, MUI!</div>;
}
```

# withStyles
- material UI에서 사용하는 HOC
- 컴포넌트를 감싸서 스타일을 적용

```jsx
import { withStyles } from '@mui/styles';

const styles = (theme) => ({
  root: {
    backgroundColor: theme.palette.primary.main,
    padding: '10px',
  },
});

const MyComponent = withStyles(styles)(({ classes }) => (
  <div className={classes.root}>Hello, MUI!</div>
));

export default MyComponent;
```

# Select
Select 컴포넌트 v4에서 v5로 넘어가면서 수정사항이 많이 발생  
v4
```
anchorOrigin: {
	vertical: props.vertical || 'bottom',
	horizontal: props.horizontal || 'left'
}
```

v5
```
anchorOrigin: {
	vertical: props.vertical || 'bottom',
	horizontal: props.horizontal || 'left'
},
transformOrigin: {
	vertical: "top",
	horizontal: "left"
},
```

# Grid

# 참고자료
| TITLE            | URL                                                 |
|------------------|-----------------------------------------------------|
| MUI v4=>v5       | https://velog.io/@dlruddms5619/Migration-MUI-v4-to-v5-makeStyles-%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B0   |
