# ncloud server

[ncloud 서버 생성 - 어떤 서버를 선택해야할까??](https://chucoding.tistory.com/98)

## **1세대 vs 2세대**  

VPC 환경에서 제공되는 2세대(g2) 서버는 Classic 환경에서 제공되는 1세대 서버 대비 CPU와 네트워크 등에서 성능이 개선되고 안정성이 향상된 버전

2세대 서버의 경우 Standard, High Memory, High CPU 간 타입 변경이 가능하나 1세대 서버의 경우 Compact와 Standard간 타입 변경만 가능 (타입 변경시 IP주소가 변경됨)


# 추가 요금 정보

서버 이미지, 스토리지, 공인 IP, 로드밸런서는 시간 단위로 요금이 청구  
네트워크 사용료는 서버와 파일 스토리지에서 발생한 트래픽 양에 따라 과금

**공인 IP**
- 1개당, 시간당 5.6원 
- 한 달(30일 기준) ⇒ 4,032원

**Private Subnet 요금**
- 1개당 시간당 28원
- 한 달(30일 기준) ⇒ 20,160원

**네트워크 사용료**
- 인바운드 트래픽 - 무료
- 아웃바운드 트래픽

| 네트워크 구간 | 단위 | 사용량 구간 | 종량 요금제 |
| --- | --- | --- | --- |
| 인터넷을 이용한 트래픽
(다른 리전으로 공인 IP를 이용한 트래픽 포함) | GB | 20GB 이하 | 무료 |
| 인터넷을 이용한 트래픽
(다른 리전으로 공인 IP를 이용한 트래픽 포함) | GB | 20GB 초과 ~ 5TB 이하 | 100원 |
| 다른 존(Zone)에서 공인 IP를 이용한 트래픽 | GB | 전 구간 동일 | 10원 |
| 같은 존(Zone)에서 공인IP를 이용한 트래픽 | GB | 전 구간 동일 | 10원 |
| 다른 존(Zone)에서 비공인 IP를 이용한 트래픽 | GB | 전 구간 동일 | 10원 |
| 같은 존(Zone)에서 비공인 IP를 이용한 트래픽 | GB | 전 구간 동일 | 무료 |

💡**서버 정지시 요금 발생?**

Classic 환경의 Compact, Standard 타입, VPC 환경의 Standard, High CPU, High Memory 타입 서버에 한해 운영체제 설치를 위한 기본 디스크 요금만 부과

1회 최대 90일, 12개월 누적 180일 이상 서버를 정지할 수 없으며 서버 정지 가능 기간을 초과한 서버는 고객에게 통보한 후 서버를 반납 처리

# 서버 생성 후 추가 작업


1. 관리자 비밀번호 변경 ⇒ [https://blog.huny.dev/naver-cloud-platform-server-setting/](https://blog.huny.dev/naver-cloud-platform-server-setting/)
2. 서버 생성 후 포트포워딩 설정

💡 **리눅스에서 루트 암호를 변경하는 방법?**

리눅스 계정 생성  
[https://mungiyo.tistory.com/14](https://mungiyo.tistory.com/14)  
서버 내용은 반납하지 않는 이상 유지가능

# 참고자료

| TITLE | URL |
| --- | --- |
| Ncloud Classic  vs VPC  | https://docs.3rdeyesys.com/management/ncloud_management_classic_vs_vpc_guide.html#classic-%ED%99%98%EA%B2%BD-%EC%9E%A5%EC%A0%90-%EC%83%81%EC%84%B8 |
| Ncloud VPC | https://blog.naver.com/n_cloudplatform/222292617371 |
| Ncloud GPU | https://www.ncloud.com/product/compute/gpuServer |
| Ncloud Server, 요금 | https://www.ncloud.com/product/compute/server |
| 1세대(g1) vs 2세대(g2) | https://guide.ncloud-docs.com/docs/compute-server-virtualmachineserver-overview |
| KR-2 , KR-1 서버 위치 | https://api.ncloud-docs.com/beta/docs/compute-server-getzonelist |
| KR-2, KR-1 환경별 제공 서비스 | https://guide.ncloud-docs.com/docs/environment-environmentproducts |
| acg 및 acg 설정 예제 | https://www.ncloud.com/product/security/acg |
| 서버 생성 가이드 | https://www.ncloud.com/guideCenter/guide/1 |
| 패스워드 변경 | https://www.psychz.net/client/question/ko/how-to-change-root-password-in-linux.html |