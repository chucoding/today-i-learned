# 네트워크 기초

### 네트워크 어댑터
- 컴퓨터에 있는 하드웨어 구성요소
- 컴퓨터가 네트워크를 통해 다른 컴퓨터 장치와 통신할 수 있도록 하는 것


### IP
```shell
::1/128 # IPv6 주소
127.0.0.1/32 # IPv4 주소
```
※ 서브넷 마스크 - 네트워크 주소와 호스트 주소를 구분하는 역할  
/0: 모든 주소를 하나의 대규모 네트워크로 간주하는 것 (서브넷 마스크를 명시하지 않으면 기본적으로 /32, /128 이 적용됨)

### NAT(Network Address Translation) - 네트워크 주소 변환
- IP 주소 절약 : 하나의 공인 IP주소를 사용하여 여러 대의 호스트가 인터넷에 접속할 수 있음
- 보안 : 외부로 트래픽이 나갈 때는 사설 IP가 공인 IP 주소로 바뀌므로 최종 목적지로의 공격이 어려워짐

### ALB와 NLB 차이점
- ALB : Application Load Balancer
    - L7단의 로드 밸런서를 지원
    - HTTP/HTTPS 프로토콜의 헤더를 보고 적절한 패킷으로 전송 
    - IP주소 + 포트번호 + 패킷 내용을 보고 스위칭
    - IP 주소가 변동되기 때문에 Client에서 Access 할 ELB의 DNS Name을 이용해야 합니다.
    - L7단을 지원하기 때문에 SSL 적용이 가능합니다.
- NLB : Network Load Balancer
    - L4단의 로드 밸런서를 지원
    - TCP/IP 프로토콜의 헤더를 보고 적절한 패킷으로 전송
    - IP + 포트번호를 보고 스위칭합니다.
    - 할당한 Elastic IP를 Static IP로 사용이 가능하여 DNS Name과 IP주소 모두 사용이 가능합니다.
    - SSL 적용이 인프라 단에서 불가능하여 애플리케이션에서 따로 적용해 주어야 합니다.

※ alb 인그레스 를 ip를 고정 하는 방법?
AWS ALB는 NLB -> ALB -> SVC 이런식으로 앞에 고정 IP가 되는 NLB를 두고 사용하는 편법이 있는데, 네이버 ALB는 고정 IP가 있다. (aws도 지금은 고정 IP 지원)

"싱가포르 리전에서 Network를 Public IP로 선택한 경우, 고정 Public IP를 지정할 수 있습니다."
https://guide.ncloud-docs.com/docs/loadbalancer-application-vpc


# 참고자료
ALB와 NLB의 차이점 https://no-easy-dev.tistory.com/entry/AWS-ALB%EC%99%80-NLB-%EC%B0%A8%EC%9D%B4%EC%A0%90