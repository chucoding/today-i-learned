# Nginx
nginx에 react프로젝트 배포하기
```
# 1. 80포트 방화벽 전체IP 허용
# 2. 서버 재시작

# 3. nginx 설치
$ sudo apt-get update
$ sudo apt-get install nginx

# 4. listen [::]:80 default_server; 부분 주석처리 또는 default 지우고 새로만들기(myapp.conf)
/etc/nginx/sites-enabled/default

# 4-1. myapp.conf를 만들었다면 심볼릭 링크 생성하기
sudo ln -s /etc/nginx/sites-available/myapp.conf /etc/nginx/sites-enabled/myapp.conf

# 5. 파일질라로 build 폴더 옮기기 

# 6. nginx 재시작
systemctl restart nginx
```

### Nginx 설정 파일
```
cd /var/log/nginx #로그파일
cd /etc/nginx/conf.d #nginx 설정파일
cd /usr/share/nginx/html 기본 인덱스 파일 위치
```

### SSL 설정방법
# default

SSL 설정방법(https)

```
server {
    listen       443 ssl;  #1.15이상은 ssl붙음
    server_name  도메인주소;
    ssl_certificate /root/certs/fullchain.pem;
    ssl_certificate_key /root/certs/privkey.pem;
}
```

### 명령어
```powershell
systemctl restart nginx # nginx 재시작
systemctl start nginx # nginx 시작
systemctl stop nginx # nginx 중지
```

💡 service nginx restart vs systemctl restart nginx  
- service nginx restart : System V(init.d) 초기화 시스템을 사용하는 시스템에서 사용
- systemctl restart nginx : systemd 초기화 시스템을 사용하는 시스템에서 사용.
- systemctl이 더 많은 기능과 세부적인 제어 제공(최신 리눅스 배포판에서 좀 더 일반적으로 사용됨)


# 참고자료
|TITLE|URL|
|---|---|
|centos certbot|https://www.digitalocean.com/community/tutorials/how-to-secure-nginx-with-let-s-encrypt-on-centos-7|
|ubuntu ssl|https://chucoding.tistory.com/130|