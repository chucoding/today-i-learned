# HyperCLOVA
## HyperCLOVA 기술
- Domain에 맞는 대규모 사용자 데이터 수집에 대한 어려움을 HyperCLOVA를 활용하여 손 쉽게 대규모 데이터 생성 (Few-shot Learning을 통한 Dialogue Generation과 QA Generation)
- Generator만으로 서비스를 만드는 것의 난점인 높은 Inference 비용(GPU-Heavy) 문제를 Retriever를 중심으로 한 모델링으로 변경하여 해결(Retriever + Generator)

## Pipeline
![image](https://github.com/chucoding/today-i-learned/assets/56211193/593c9fe4-7b14-4983-befc-462822ee9cba)

### Example
많은 양의 대화를 직접 작성하기에는 개인정보 이슈가 있고 대화의 범위도 너무 넓은 문제가 있다. 따라서 Chatbot과 User의 대화 상황을 가정하고 대화 예시를 작성한다.

### HyperCLOVA 1-shot Generation
대화 상황에 대한 간단한 prompt와 대화 예시를 이용해 대화씬을 생성  
※ HyperCLOVA를 이용한 데이터 수집의 장점 : 빠르고 안전하게 원하는 대화만 수집 가능