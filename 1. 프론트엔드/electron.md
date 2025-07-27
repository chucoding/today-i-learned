# electron
일렉트론 이벤트 정리

### before-quit
```javascript
app.whenReady().then(()=> {
    // 앱 구동시 로직 
});
```

### window-all-closed
```javascript
app.on('before-quit', async (event) => { 
    // 앱 종료 전 로직
});
```


# 참고자료
https://www.electronjs.org/docs/latest/api/app