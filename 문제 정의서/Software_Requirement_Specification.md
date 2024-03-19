## 1. Introduction
- 소개: 현재의 개인화 POI(Point of Interest) 추천 시스템은 주로 Collaborative Filtering을 활용하여 유사하게 행동한 다른 사용자에게 인기가 많은 아이템을 추천하는 경향이 있습니다. 이러한 시스템은 뛰어난 성능으로 인해 많은 주목을 받고 있지만, 평점, 리뷰, 사진, 지리적 거리 등과 같은 POI의 컨텐츠 정보를 활용하면 더 정확한 추천이 가능합니다. 더불어, 컨텐츠 정보를 이용하면 왜 해당 POI가 추천되었는지에 대해 사용자에게 설명할 수 있으며, 여러 관점에서 사용자의 취향을 분석할 수도 있습니다.

  이에 본 프로젝트는 이러한 컨텐츠 정보를 활용하여 개인화된 POI 추천 시스템을 개발하는 데 초점을 맞추고 있습니다. 이를 통해 사용자가 보다 의미 있는 추천을 받을 수 있으며, 추천된 POI에 대한 이해도와 만족도를 높일 수 있습니다.

- 목적: 개인화된 POI 추천 시스템의 목적은 Collaborative Filtering 방식을 보완하여 더 정확하고 설명 가능한 추천을 제공하는 것입니다. 이를 위해 POI의 다양한 컨텐츠 정보를 활용하여 사용자의 취향을 더욱 정확하게 파악하고, 이를 기반으로 추천의 이유를 설명할 수 있도록 합니다.

- 범위
	- 사용자 등록 및 프로필 관리 기능을 포함한 사용자 관리 시스템 구축
	- POI 데이터 수집 및 전처리
	- Collaborative Filtering 및 콘텐츠 기반 추천 알고리즘 구현
	- 추천된 POI에 대한 설명 생성 기능 추가

- 용어 정의 (정의, 두문자어, 약어)
	- POI (Point of Interest): 지도나 여행 앱에서 사용자에게 특별히 관심 있는 장소나 명소를 나타내는 용어입니다.
	- SRS (System Requirements Specification): 소프트웨어 개발에서 시스템의 요구사항을 문서화하는 과정을 의미합니다.
	- Collaborative Filtering: 유사한 사용자들이 선호하는 항목을 기반으로 추천을 제공하는 기술입니다. (옵션: Graph Attention Network: 그래프 구조에서의 관계를 학습하여 추천하는 기술)
	- Document Embedding: 텍스트 문서를 고정 차원의 벡터로 변환하는 기술입니다. (옵션: Sentence to Vector Model: 문장을 벡터로 변환하는 모델, Language Model: 언어 모델을 활용한 문장 벡터화)
	- Classifier: 입력 데이터를 여러 범주 중 하나로 분류하는 모델이나 알고리즘입니다. 다층 퍼셉트론(MLP)은 이에 해당하는 대표적인 모델입니다.

- 참조
	- [BPR] Steffen Rendle, Christoph Freudenthaler, Zeno Gantner, and Lars
Schmidt-Thieme. 2009. BPR: Bayesian personalized ranking from implicit feedback.
In Proceedings of the Twenty-Fifth Conference on Uncertainty in Artificial
Intelligence (UAI '09). AUAI Press, Arlington, Virginia, USA, 452~461.
	- [Distance] Mao Ye, Peifeng Yin, Wang-Chien Lee, and Dik-Lun Lee. 2011.
Exploiting geographical influence for collaborative point-of-interest
recommendation. In Proceedings of the 34th international ACM SIGIR conference
on Research and development in Information Retrieval (SIGIR '11). Association for
Computing Machinery, New York, NY, USA, 325?334.
https://doi.org/10.1145/2009916.2009962
	- [Review] Ramesh Baral, XiaoLong Zhu, S. S. Iyengar, and Tao Li. 2018. ReEL:
Review Aware Explanation of Location Recommendation. In Proceedings of the
26th Conference on User Modeling, Adaptation and Personalization (UMAP '18).
Association for Computing Machinery, New York, NY, USA, 23?32.
https://doi.org/10.1145/3209219.3209237
	- [Sentence Embedding] Matteo Pagliardini, Prakhar Gupta, and Martin Jaggi. 2018.
Unsupervised Learning of Sentence Embeddings Using Compositional n-Gram
Features. In Proceedings of the 2018 Conference of the North American Chapter
of the Association for Computational Linguistics: Human Language Technologies,
Volume 1 (Long Papers), pages 528?540, New Orleans, Louisiana. Association for
Computational Linguistics.
	- [Visual] Ruining He and Julian McAuley. 2016. VBPR: visual Bayesian Personalized
Ranking from implicit feedback. In Proceedings of the Thirtieth AAAI Conference
on Artificial Intelligence (AAAI'16). AAAI Press, 144?150.
	- [Counterfactual Learning] Li, S., Guo, D., Liu, K., Hong, R., & Xue, F. (2023). Multimodal Counterfactual Learning Network for Multimedia-based Recommendation. In SIGIR '23: Proceedings of the 46th International ACM SIGIR Conference on Research and Development in Information Retrieval (pp. 1539–1548). July 2023. https://doi.org/10.1145/3539618.3591739


<br><br><br>

## 2. Overall Description
이 프로젝트는 컨텐츠 정보를 활용하여 개인화된 Points-Of-Interest (POI) 추천 시스템을 구축합니다. 이 시스템은 Light GCN(Graph Convolution Network), Graph Sage, GAT 등의 그래프 기반 모델을 기반으로하여 User와 POI 간, 그리고 POI와 POI 간의 관계를 학습합니다. 또한, Bert 모델을 활용하여 Review Text Data를 감정으로 분류하여 추천에 활용하고, Self Attention 등의 기법을 사용하여 추천 결과의 정확도를 높입니다.
<br><br>

이 프로젝트에서는 Neo4j Graph Database(GDB)를 도입하여 영속성과 확장성을 고려합니다. Neo4j는 그래프 데이터베이스로, 그래프 형태의 데이터를 효율적으로 저장하고 관리할 수 있습니다. 이를 통해 사용자와 POI 간의 관계를 효과적으로 저장하고, 그래프 기반 모델의 학습 과정에 활용할 수 있습니다. 또한 Neo4j는 분산 시스템으로 확장이 가능하므로, 대규모 데이터 처리에 적합합니다.
<><br>

POI 추천 시스템을 학습하기 위해 다음 정보를 사용합니다.
- USER
	- user의 이름, 주소, 인적 정보, 나이
- Review
	- 방문한 POI에 대한 Text Review
- POI
	- POI 명, 주소, 위도, 경도


<br><br><br>

## 3. System Feature(시스템 특징)
### 개인화 POI 추천:
사용자의 이전 활동과 취향을 고려하여 개인화된 POI 추천을 제공합니다.
<br><br>

### 다양한 추천 알고리즘: 
Collaborative Filtering과 BPR을 기본으로 하며, Graph Attention Network, Document Embedding, 언어 모델 등의 기술을 선택적으로 활용할 수 있습니다.
<br><br>

### 컨텐츠 정보 활용: 
사용자의 리뷰, POI의 지리 정보 등의 컨텐츠 정보를 활용하여 추천의 정확성을 높입니다.
<br><br>

### 설명 가능한 추천: 
추천된 POI에 대해 왜 해당 지역이 추천되었는지 설명하는 기능을 제공합니다.
<br><br>

<br><br><br>


## 4. External Interface Requirement (외부 인터페이스 요구사항)
- **운영 환경:** 웹 또는 앱 형태로 제공되어야 하며, 다양한 운영 체제에서 사용 가능해야 합니다.
- **데이터셋 활용:** Yelp 등의 오픈 데이터셋을 사용하여 성능을 평가합니다.


<br><br><br>


## 5. 기능 이외의 요구사항 (Other Nonfunctional Requirements
- **성능:** 대용량 데이터 처리 및 실시간 추천 기능을 제공해야 합니다.
- **보안:** 사용자의 개인 정보 및 활동 데이터 보호를 위한 보안 기능을 갖추어야 합니다.
- **확장성:** 새로운 알고리즘 및 기능의 추가가 용이해야 합니다.
- **사용자 경험:** 직관적이고 사용하기 쉬운 사용자 인터페이스를 제공해야 합니다.



<br><br><br>



참고)
https://velog.io/@bagt/%EC%82%AC%EC%9A%A9%EC%9E%90-%EC%9A%94%EA%B5%AC%EC%82%AC%ED%95%AD-%EC%A0%95%EC%9D%98%EC%84%9C-SRS%EB%9E%80

https://velog.io/@angel_eugnen/SRSSoftware-requirements-specification

