# helm
쿠버네티스에서 어플리케이션을 배포하기 위해 사용되는 대표적인 패키지 툴  
컨테이너 배포 뿐만 아니라 어플리케이션을 배포하기 위해 필요한 쿠버네티스 리소스를 모두 패키지 형태로 배포

## 설치
```bash
$ sudo yum install helm

또는

$ curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
$ chmod 700 get_helm.sh
$ ./get_helm.sh
```
  
## 차트

헬름은 charts라는 쿠버네티스 리소스를 하나로 묶은 패키지 포멧을 사용한다.  
여러개의 yaml 파일들을 관리하기 쉽게 하기 위한 목적

## 패키지 구조

```
Chart.yaml          # 차트에 대한 정보를 가진 YAML 파일
LICENSE             # 옵션: 차트의 라이센스 정보를 가진 텍스트 파일
README.md           # 옵션: README 파일
values.yaml         # 차트에 대한 기본 환경설정 값들
values.schema.json  # 옵션: values.yaml 파일의 구조를 제약하는 JSON 파일
charts/             # 이 차트에 종속된 차트들을 포함하는 디렉터리
crds/               # 커스텀 자원에 대한 정의
templates/          # values와 결합될 때, 유효한 쿠버네티스 manifest 파일들이 생성될 템플릿들의 디렉터리
templates/NOTES.txt # 옵션: 간단한 사용법을 포함하는 텍스트 파일
```

### Example

```
app
|- charts                           # 의존성 관리(DB 등)
|- Chart.yaml                       # chart명, chart 버전, chart 설명 등을 표기
|- templates                        # yaml 파일들이 위치
    |- builder
        |- builder-conf.yaml        # init.sh 등 스크립트 생성
        |- builder-deploy.yaml      # 볼륨 지정 및 마운트, 배포 전에 실행할 스크립트 지정
        |- builder-svc.yaml         # 포트 매핑
    |- chat
        |- chat-conf.yaml
        |- chat-deploy.yaml
        |- chat-hpa.yaml
        |- chat-svc.yaml
    |- fluentd
    |- init
    |- NOTES.txt
    |- redis
|- values.yaml                      # 변수들을 정의한 파일
```

# 참고자료
| TITLE | URL |
| --- | --- |
| helm 공식문 | https://helm.sh/docs/intro/install/ |
|  |  |