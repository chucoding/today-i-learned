# ajax caching

cache의 default 옵션은 datatype=”script” 또는 “jsonp”를 제외한 모든 데이터 유형에 대해서 cache:true

그러나 브라우저(특히 IE)마다 Ajax 요청 캐싱에 대한 기본 정책이 다르므로 주의

따라서 Ajax 요청에 명시적으로 cache=false를 설정해야한다.

❗IE에서 cache=false를 설정안하면 동일한 URL에 대해 POST 요청을 할때 캐시된 응답을 불러온다.

> 💡{cache=false}
URL에 타임스탬프를 추가하여 브라우저가 자체 캐시를 무시하도록 속이는 역할 (cache: true 설정시 브라우저가 파일을 캐시함 - url에 언더바("_")가 나타나지 않는다) 

### Example

```jsx
$.ajax('/ajax_handler.php', {cache:false});
// ajax_handler.php?_=1323308900002 (url 뒤에 이런식으로 붙음)
```

❗GET 요청시에만 사용되기 때문에 cache:false 옵션 사용 대신 POST로 바꾸는 방법도 있지만 RESTful하지 않기 때문에 사용을 권장하지는 않는다.)

## ajax 호출시 브라우저 cache 방지 방법

### 1. 전역 설정

```jsx
$.ajaxSetup({cache: false})
```

jQuery 메소드 ($.get, $.ajax 등)에 관계없이 이후의 모든 AJAX 요청이 캐시되지 않도록 함

### 2. ajax 설정

```jsx
$.ajax({
	method: "GET",
	url: "/some/path",
	data: { param: value },
	cache: false,
	success: function(result) {...}
});
```

❗$.getJSON 사용하는 경우에는 cache:false 옵션이 없다. (전역 설정 또는 $.ajax로 변경해서 사용)
   

### 3. 캐시 헤더 설정

```jsx
response.setHeader("Pragma", "no-cache"); // HTTP 1.0 스펙
response.setHeader("Cache-Control", "no-cache"); // HTTP 1.1 스펙
response.setDateHeader("Expires",0);
```

💡Pragma, Cache-Control 둘 중 하나만 써도 되지만 혹시 모를 1.0에 대비해 둘다 설정

### 4. HTML 파일 캐시 방지

```html
<meta http-equiv='cache-control' content='no-cache'>
<meta http-equiv='expires' content='0'>
<meta http-equiv='pragma' content='no-cache'>
```

| http-equiv | 설명 |
| --- | --- |
| cache-control | no-cache : 캐시를 사용하기 전에 재검증을 위한 요청을 강제함.
no-store : 클라이언트의 요청, 서버의 응답 등을 일체 저장하지 않음
must-revalidate : 캐시를 사용하기 전에 만료된 것인지를 검증

💡콤마(,)로 여러개 설정 가능 |
| expires | 리소스가 validate 하지 않다고 판단할 시간을 설정
💡conten=’0’: 리소스 만료 |
| pragma | HTTP 1.0 버전에서 HTTP 1.1의 Cache-Control 헤더와 같은 역할을 함 ex) Pragma: no-cache |

# jQuery UI

jQuery JavaScript 라이브러리 위에 구현된 이펙트, 위젯, 캘린더 등의 사용자들이 직접 만들어서 올려놓은 라이브러리

# 참고자료

| 제목 | url |
| --- | --- |
| IE 11 Ajax Caching 끄기 | https://effortguy.tistory.com/36 |
| jQuery에서 getJSON에 대해 캐시를 false로 설정하는 방법 | https://stackoverflow.com/questions/13391563/how-to-set-cache-false-for-getjson-in-jquery |
| jqueryU | [https://jqueryui.com](https://jqueryui.com/dialog/#modal-form) |