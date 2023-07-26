# Cloud Functions 
서버 관리나 프로비저닝 필요 없이 비즈니스 로직을 실행할 수 있는 서비스    
※ 프로비저닝 : IT 인프라를 생성하고 설정하는 프로세스

클라우드 환경에서 코드가 실행된 만큼만 과금되기 때문에 효율적인 운영이 가능하며, 갑작스러운 요청에도 유연하게 대처할 수 있다.  

## 용어
|이름|설명|
|---|---|
|네임스페이스|Cloud Functions 사용 신청(이용 약관 동의)시 할당되는 고유의 영역|
|Action|JavaScript, Java, Python 등의 코드 스니펫을 의미|
|Package|단일 또는 다수의 액션을 분류, 관리하는 목적의 단위|
|트리거|외부 서비스의 이벤트를 통해 액션을 실행할 수 있는 이벤트 전달 객체<br>(ex. GitHub 코드에 변경 사항이 발생했을 때)|

## Action 생성방법

1. 기본 정보 생성

    ![Cloud Functions 기본 정보](https://github.com/chucoding/today-i-learned/assets/56211193/7215aefd-6208-4ec2-abdd-8b4e57eb4989)  

    |타입|설명|
    |---|---|
    |일반 액션|하나의 액션만 실행 (기본 JSON 응답)|
    |웹 액션|다양한 REST API 형태의 요청 가능(GET, POST, PUT, DELETE)|
    |시퀀스 액션|여러개의 액션을 순차적으로 실행|
    |시퀀스 웹 액션|여러개의 액션을 순차적으로 실행|

2. 소스코드

    Github에서 소스 내려받아서 올리기

3. 옵션 설정

    - 액션 메모리는 최대한 작게
    - timeout은 최대 시간인 300000으로 설정  

4. 외부 연결 주소 생성

    ![Cloud Function 액션 - 외부 연결 주소 생성](https://github.com/chucoding/today-i-learned/assets/56211193/7b7c209f-5c93-4285-b1d1-a05dd4ad4734)