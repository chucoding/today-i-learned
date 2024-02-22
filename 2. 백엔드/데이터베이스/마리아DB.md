# MariaDB
Mysql과 비슷한 데이터베이스

### 설치
아래 링크를 통해 zip 파일을 다운받고 압축해제 한다.

https://mariadb.org/download
- v10.5.24 : 한국 Mirror 서버가 없어서 일본 Osaka 통해서 다운 받음

### 데이터베이스 초기화
```cmd
.\bin\mariadb-install-db.exe
    --datadir=C:\Work\mariadb-10.5.8-winx64\data       # 저장소 지정
    --service=mariaDB                          # 윈도우 서비스명 지정
    --port=3306                                      # 포트번호 지정
    --password=test1357                              # 패스워드 지정
```
❗ 관리자 권한으로 CMD 실행해야함.(powershell 까진 아니어도됨.) 안그러면 OpenSCManager failed 오류 발생
❗ user home 디렉토리 아래에 설치파일X 실행 오류남.
❗ 저장소 지정시 data는 없어야 함.

### 시작, 종료, 상태 명령어
```sh
$ mysql.server.start, mysql.server.stop, mysql.server.status
```

### 접속
```
mysql -u root -p
```

### 데이터베이스 조회
```
show databases;
```