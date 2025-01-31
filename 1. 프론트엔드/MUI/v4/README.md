# MaterialUI (MUI)
안드로이드 시스템에서 검증된 높은 수준의 디자인 퀄리티를 자랑하는 CSS 프레임워크

# Custom CSS
### makeStyles
- material UI v4에서 사용하는 HOC
- 함수를 호출하여 스타일 객체를 생성 (함수 컴포넌트에서 사용하기 적합)
- React Hooks와 함께 사용할 수 있어서 더 현대적인 접근 방법
- 성능면에서 withStyles보다 효율적
- React 18에서 @mui/styles 대신 @mui/system 라이브러리의 syled API 을 사용하거나 emotion API를 사용해야함. (호환성 문제)

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

### withStyles
- material UI v4에서 사용하는 HOC
- 컴포넌트를 감싸서 스타일을 적용
- 클래스 컴포넌트와 함수 컴포넌트 모두에서 사용 가능

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