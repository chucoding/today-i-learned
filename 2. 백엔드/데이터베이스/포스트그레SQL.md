# Postgres
대표적인 NoSQL기반 무료 데이터베이스

# 타입
|타입|설명|
|---|---|
|serial|자동 증가하는 정수 값|
|text|가변 길이 텍스트|
|varchat(n)|최대 길이가 n인 가변 길이 문자열|
|integer|4바이트 정수|
|timestamp|날짜와 시간|
|date|날짜만 저장|
|boolean|참/거짓|
|json|json|
|jsonb|이진 형태로 인코딩된 json|

### json vs jsonb
#### json  
토큰 사이의 의미없는 공백도 모두 저장
- 순서 보장
- 중복키 보장

#### jsonb  
의미단위(key-value 기준)로 선처리를 해서 저장
- 인덱싱 지원 
- 공백 보존 X
- 순서 보장 X
- 중복키 x (마지막 키만 들어감)

### text vs varchar
mysql과 달리 postgres에서는 TOAST 저장소를 활용하기 때문에 큰 차이 없음.

# 권한
슈퍼유저 생성
```postgresql
CREATE USER <계정명> PASSWORD '<비밀번호>' SUPERUSER;
```

# 설치

### ubuntu
1. 설치된 패키지가 있는지 확인(이미 설치되어있는데 다시 설치하는것을 방지)
```
pg_lsclusters
apt list --installed | grep postgres
```

2. 공식 홈페이지를 통해 postgresql 다운로드
https://www.postgresql.org/download/linux/debian/

3. 초기화 및 클러스터 생성
```
sudo pg_ctlcluster 13 main start
```

4. postgres 재시작
```
sudo service postgresql restart
```

### centos

1. 설치된 패키지가 있는지 확인(이미 설치되어있는데 다시 설치하는것을 방지)
```
yum list installed | grep postgresql
yum list postgresql*
```

2. 만약 원하는 버전이 없다면 PostgreSQL 공식 저장소 추가
```
yum install https://download.postgresql.org/pub/repos/yum/reporpms/EL-7-x86_64/pgdg-redhat-repo-latest.noarch.rpm
```
💡 이 때 보이는 패키지들은 아직 설치가 안된 패키지들. (TODO 디스크를 어떻게 사용하는지 공부해보자)

3. postgresql 설치 (13버전 기준)
```
yum install postgresql13-server postgresql13-contrib
```

4. 초기화 및 클러스터 생성
```
/usr/pgsql-13/bin/postgresql-13-setup initdb
```

5. postgresql 서비스 시작 및 재부팅했을 때 PostgreSQL DB 자동 실행 활성화
```
systemctl start postgresql-13
systemctl enable postgresql-13
```


### 서비스 확인
#### 명령 실행
```shell
systemctl list-units --type=service
```

#### 출력 결과
- postgresql.service # PostgreSQL RDBMS (서버의 기본 인스턴스)
- postgresql@13-main.service # PostgreSQL Cluster 13-main ( 메인 클러스터 )

### 클러스터 재기동
```
sudo -u postgres pg_ctlcluster 13 main start
```
# 데이터베이스 접속 및 비밀번호 초기화

1. postgres 접속
```
sudo -u postgres psql
```

2. 데이터베이스 생성
```
CREATE DATABASE mydb;
```

3. 비밀번호 변경
```
ALTER USER postgres WITH PASSWORD '새로운_비밀번호';
# 또는
\password postgres
```


# 패스워드 자동화 사용
.pgpass 파일을 만들어서 아래와 같이 입력한다. 단, .pgpass파일은 해당 계정 소유권한으로 만들어야함(root X)
```
localhost:5432:mydatabase:postgres:konan415
```

# 외부 접속 허용
1. sudo vi /etc/postgresql/13/main/postgresql.conf 수정
```
#listen_addresses = 'localhost'  => listen_addresses = '*' 
```
     
2. sudo vi /etc/postgresql/13/main/pg_hba.conf 에 아래 항목 추가
```
host    all   all  0.0.0.0/0   md5
```
※ HBA(Host-Based Authentication) - 특정 IP 주소만 허용할 수 있도록 할 수 있음.

# 보안 강화
1. 포트번호 변경(*기본포트 사용시 멀웨어 등 악성바이러스가 침투할 수 있으므로 반드시 변경하기)  
sudo vi /etc/postgres/13/main/postgresql.conf 수정
```
port = 65432
```
서비스 재기동 후 변경된 포트로 클라이언트 접속 테스트  
```
sudo -u postgres -p 65432 psql
```

2. 특정 IP만 접근 허용  
sudo vi /etc/postgresql/13/main/pg_hba.conf 아래와 같이 수정 후 서비스 재기동
```
host    all             all             IP주소/0        md5
```

# 저장소 변경 방법
```
vi /usr/lib/systemd/system/postgresql-13.service
Environment=PGDATA=/path/data #참고로 이 data 폴더는 postgres 접근권한 있어야함.
```
변경 후 초기화 및 클러스터 생성부터 다시

# 포스트그레 서비스 조회
```
systemctl list-units --type=service
sudo journalctl -xeu postgresql@13-main.service
```

# 클러스터 재기동
모델 서버를 재기동하거나 예상치 못하게 장애가 발생하는 경우 클러스터는 postgres 계정으로 기동해야되기 때문에 다음과 같이 수동으로 재기동 명령어를 실행해야 한다.
```
sudo -u postgres pg_ctlcluster 13 main start
```

# 데이터 백업
서버 이관작업이 예정되어 있거나 예기치 못한 장애 발생을 대비해 다음과 같이 백업을 진행할것.

덤프
```
sudo -u postgres pg_dump -U postgres -d postgres -f konanllm_dump.sql
```

덤프 복원
```
sudo -u postgres createdb -U postgres konanllm
sudo -u postgres psql -U postgres -d konanllm -p 65432 -f konanllm_dump.sql
```

# 참고자료
|title|url|
|---|---|
|우분투 psql 설치 공식|https://www.postgresql.org/download/linux/debian/|
|포스트그레 sql 완전 삭제방법|https://sunnys-note.tistory.com/5|
|Text vs varchar|https://dev-scratch.tistory.com/172|