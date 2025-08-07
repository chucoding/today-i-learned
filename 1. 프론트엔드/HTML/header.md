# `<head>`
문자 인코딩, 타이틀, 설명, 스타일시트, 스크립트 등
문서 정보(메타데이터)에 관련된 다양한 요소를 모아둔 태그

```html
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8"> <!-- 문자 인코딩 -->
        <title>이 페이지의 타이틀</title> <!-- 문서의 타이틀 -->
        <meta name="description" content="이 페이지의 짧은 설명입니다."> <!-- 문서의 설명 -->
        <link rel="stylesheet" href="style.css"> <!-- 스타일시트 -->
        <script src="script.js"></script> <!-- 스크립트 -->
    </head>
    <body>
        <!-- 문서의 내용 -->
    </body>
</html>
```

# `<header>`
웹 페이지의 제목, 로고, 주요 네비게이션 메뉴 등 포함.

```html
<header>
    <!-- 섹션의 목차 -->
    <nav>
        <ul>
            <li><a href="#section1">섹션 1</a></li>
            <li><a href="#section2">섹션 2</a></li>
            <li><a href="#section3">섹션 3</a></li>
        </ul>
    </nav>
    
    <!-- 검색 양식 -->
    <form action="/search" method="get">
        <label for="search">검색어</label>
        <input id="search" type="text" name="q" placeholder="검색어를 입력하세요">
        <button type="submit">검색</button>
    </form>
    
    <!-- 로고 -->
    <img src="logo.png" alt="웹 페이지 로고">
</header>
```