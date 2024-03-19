### 1. Introduction
- 목적: 이 프로젝트의 주요 목적은 기존의 Collaborative Filtering 방식을 보완하여 개인화된 POI(Point of Interest) 추천 시스템을 구축하는 것입니다. Collaborative Filtering은 유사한 행동 패턴을 보이는 다른 사용자의 인기 아이템을 추천하는 데 사용되지만, 이러한 방식은 사용자의 취향을 충분히 반영하지 못할 수 있습니다.

  따라서 이 프로젝트는 POI의 다양한 컨텐츠 정보를 활용하여 추천의 정확성과 설명 가능성을 향상시키는 데 주력합니다. 평점, 리뷰, 사진, 지리적 거리 등의 컨텐츠 정보를 활용하여 개인 사용자의 취향을 더 정확하게 파악하고, 이를 기반으로 추천된 POI에 대한 설명을 제공합니다. 이를 통해 사용자는 왜 특정 POI가 추천되었는지 이해하고, 자신의 관심사와 일치하는지를 확인할 수 있습니다. 따라서 보다 의미 있는 추천을 제공함으로써 사용자 만족도를 높이고, 서비스의 품질을 향상시키는 것이 이 프로젝트의 목표입니다.

- 용어 정의 (정의, 두문자어, 약어)
	- POI: Points-Of-Interest, 사용자들이 관심을 가질 수 있는 장소 또는 위치를 나타냅니다.
	- MLP: Multi-Layer Perceptron, 심층 인공 신경망 중 하나로, 비선형 함수 근사에 사용됩니다.
	- BPR: Bayesian Personalized Ranking, 사용자의 암묵적 피드백을 기반으로한 개인화된 순위 학습 알고리즘입니다.

- 참조
	- 이 문서에서 언급된 용어나 개념에 대한 참조는 필요한 경우 해당 문서 또는 논문으로 제공될 것입니다.



### 2. Overall Description
제품 관점에서의 소프트웨어 동작이나 소프트웨어의 주 기능
명확한 요구사항을 작성하기 보단, 요구사항에 대한 전반적인 내용 기술 
요구사항을 자세히 적은 Specific Requirement 의 이해를 돕기 위한 배경 같은 내용

이 프로젝트는 컨텐츠 정보를 활용하여 개인화된 Points-Of-Interest (POI) 추천 시스템을 구축합니다. 이 시스템은 Light GCN(Graph Convolution Network), Graph Sage, GAT 등의 그래프 기반 모델을 기반으로하여 User와 POI 간, 그리고 POI와 POI 간의 관계를 학습합니다. 또한, Bert 모델을 활용하여 Review Text Data를 감정으로 분류하여 추천에 활용하고, Self Attention 등의 기법을 사용하여 추천 결과의 정확도를 높입니다.


### 3. System Feature(시스템 특징)
#### 개인화 POI 추천:
사용자의 이전 활동과 취향을 고려하여 개인화된 POI 추천을 제공합니다.

#### 다양한 추천 알고리즘: 
Collaborative Filtering과 BPR을 기본으로 하며, Graph Attention Network, Document Embedding, 언어 모델 등의 기술을 선택적으로 활용할 수 있습니다.

#### 컨텐츠 정보 활용: 
사용자의 리뷰, POI의 지리 정보 등의 컨텐츠 정보를 활용하여 추천의 정확성을 높입니다.

#### 설명 가능한 추천: 
추천된 POI에 대해 왜 해당 지역이 추천되었는지 설명하는 기능을 제공합니다.




### 4. External Interface Requirement (외부 인터페이스 요구사항)
- **운영 환경:** 웹 또는 앱 형태로 제공되어야 하며, 다양한 운영 체제에서 사용 가능해야 합니다.
- **데이터셋 활용:** Yelp 등의 오픈 데이터셋을 사용하여 성능을 평가합니다.


### 5. 기능 이외의 요구사항 (Other Nonfunctional Requirements
- **성능:** 대용량 데이터 처리 및 실시간 추천 기능을 제공해야 합니다.
- **보안:** 사용자의 개인 정보 및 활동 데이터 보호를 위한 보안 기능을 갖추어야 합니다.
- **확장성:** 새로운 알고리즘 및 기능의 추가가 용이해야 합니다.
- **사용자 경험:** 직관적이고 사용하기 쉬운 사용자 인터페이스를 제공해야 합니다.







참고)
https://velog.io/@bagt/%EC%82%AC%EC%9A%A9%EC%9E%90-%EC%9A%94%EA%B5%AC%EC%82%AC%ED%95%AD-%EC%A0%95%EC%9D%98%EC%84%9C-SRS%EB%9E%80

https://velog.io/@angel_eugnen/SRSSoftware-requirements-specification

