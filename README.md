# 여성 고객의 스탠다드는 무엇인가?

> **리뷰 데이터 감성 분석을 통한 무신사 추천과 실제 판매 상품의 갭 진단 및 세그먼트별 개인화 추천 제안**
---

## 프로젝트 개요

무신사 스탠다드 우먼 카테고리에서 추천순과 판매금액순 상위 50개 상품을 비교하고,
실제 판매 상품의 리뷰를 분석해 고객 세그먼트를 도출한 뒤 추천 구조 개선 방향을 제안한다.

- **보고 대상** : 무신사 스탠다드 우먼 제품팀·브랜드팀
- **분석 기준** : 최근 1개월 판매금액순 TOP 50 / 추천순 TOP 50
- **수집 기간** : 2023-03 ~ 2026-04 (최신순 기준 최대 1,000건)

---

## 분석 질문 및 가설

### 분석 질문

| | 질문 |
|---|---|
| RQ1 | 추천순 상위 상품과 판매금액순 상위 상품은 어떤 점에서 다른가? |
| RQ2 | 실제 판매된 상품의 리뷰는 어떤 의미 군집으로 나뉘는가? |
| RQ3 | 그 군집은 어떤 고객 세그먼트를 반영하며, 추천 구조는 이를 균형 있게 반영하고 있는가? |
| RQ4 | 세그먼트 관점에서 추천순은 어떻게 개선될 수 있는가? |

### 가설 검정 결과

| | 가설 | 결과 |
|---|---|---|
| H1 | 추천순과 판매금액순은 서로 다른 상품 특성을 반영할 것이다 | ✅ 채택 |
| H2 | 판매금액순 리뷰는 의미 있는 언어 군집으로 구분될 수 있다 | ✅ 채택 |
| H3 | 추천순은 고객 세그먼트를 균형 있게 반영하지 못할 수 있다 | ✅ 채택 |

---

## 데이터

| 구분 | 상품 수 | 수집 리뷰 수 | 수집 기간 |
|---|---|---|---|
| 판매금액순 TOP50 | 50개 | 28,216건 | 2023-03-14 ~ 2026-04-20 |
| 추천순 TOP50 | 50개 | 21,295건 | 2023-04-25 ~ 2026-04-21 |

> 📁 [Google Drive 데이터 폴더](https://drive.google.com/drive/folders/1mjYZzHkTQgJ_u-PrbhI_G52jvdMWuFpE?hl=ko)

---

## 분석 기법

| 기법 | 목적 | 라이브러리 |
|---|---|---|
| REST API 크롤링 | 무신사 상품정보 및 리뷰 수집 | ![requests](https://img.shields.io/badge/requests-2CA5E0?style=flat) |
| 정규표현식 | 텍스트 클리닝 및 파싱 | ![Python](https://img.shields.io/badge/Python-3776AB?style=flat&logo=python&logoColor=white) |
| BERT (`korean_sentiment`) | 리뷰 긍정/부정 감성 분류 | ![HuggingFace](https://img.shields.io/badge/HuggingFace-FFD21E?style=flat&logo=huggingface&logoColor=black) |
| 문장 임베딩 (`ko-sroberta-multitask`) | 리뷰 의미 벡터화 | ![HuggingFace](https://img.shields.io/badge/HuggingFace-FFD21E?style=flat&logo=huggingface&logoColor=black) |
| K-Means 클러스터링 | 리뷰 군집화 (K=4) | ![Scikit-learn](https://img.shields.io/badge/Scikit--learn-F7931E?style=flat&logo=scikit-learn&logoColor=white) |
| TF-IDF | 군집별 핵심 키워드 추출 | ![Scikit-learn](https://img.shields.io/badge/Scikit--learn-F7931E?style=flat&logo=scikit-learn&logoColor=white) |
| UMAP | 군집 2D 시각화 | ![umap-learn](https://img.shields.io/badge/umap--learn-6B4FBB?style=flat) |
| Mann-Whitney U 검정 | 판매순/추천순 속성 차이 통계 검증 | ![SciPy](https://img.shields.io/badge/SciPy-8CAAE6?style=flat&logo=scipy&logoColor=white) |

---

## 주요 발견

- **Jaccard 유사도 17.6%** : 추천순과 판매금액순은 실질적으로 다른 상품군을 반영
- **리뷰 수 격차** : 판매단독 중앙값 691건 vs 추천단독 118건 (p=0.0103)
- **평점 격차** : 판매단독 중앙값 98 vs 추천단독 96 (p=0.0003)
- **4개 고객 세그먼트 도출**

| 세그먼트 | 비중 | 부정 감성 비율 |
|---|---|---|
| 검증 구매형 | 26.8% | 3.7% |
| 소재 목적형 | 22.1% | 4.2% |
| 핏 기준형 | 26.6% | **10.7%** |
| 경험 만족형 | 24.5% | 0.4% |

- **핏 기준형** : 전체 부정 리뷰 1,309건 중 **58.3% 집중**
- **추천순 편향** : 추천순에서 핏 기준형 비율 +4.3%p 증가 (26.6% → 30.9%)

---

## 레포지토리 구조
```
musinsa-standard-analysis/
├── README.md
│
├── data/
│   ├── raw/                                    # 크롤링 원본 데이터
│   │   ├── musinsastandard_woman_top50.csv
│   │   ├── musinsastandard_woman_recommend_top50.csv
│   │   ├── musinsastandard_reviews_raw.csv
│   │   └── musinsastandard_recommend_reviews_raw.csv
│   │
│   ├── processed/                              # 전처리 완료 데이터
│   │   ├── reviews_merged.csv
│   │   └── recommend_reviews_merged.csv
│   │
│   └── analyzed/                              # 분석 결과 데이터
│       ├── musinsa_review_analysis.csv
│       ├── musinsa_review_reco_analysis.csv
│       └── qualitative_analysis.xlsx
│
├── notebooks/
│   ├── 01_crawling.ipynb                      # 판매금액순/추천순 TOP50 상품정보 및 리뷰 수집
│   ├── 02_preprocessing_sales.ipynb           # 판매금액순 리뷰 전처리
│   ├── 03_preprocessing_recommend.ipynb       # 추천순 리뷰 전처리
│   ├── 04_analysis_sales.ipynb                # 판매금액순 감성분석·군집화·키워드 추출
│   ├── 05_analysis_recommend.ipynb            # 추천순 감성분석·군집화·키워드 추출
│   └── 06_visualization.ipynb                # 판매금액순 vs 추천순 비교 차트·시각화
│
└── presentation/
    └── musinsa_presentation.pdf                      # 최종 발표자료
    └── musinsa_standard_woman_report.pdf 
```
---

## 팀 구성 및 기여

> 이신민, 엄주환 불참
> 
### 기여 영역별 요약

| 기여 영역 | 김예원 | 조수경 | 최홍주 |
|---|---|---|---|
| 데이터 수집 | ✅ | | |
| 데이터 전처리 | ✅ | | |
| 분석 설계·기법 적용 | | | ✅ |
| 분석 실행·결과 도출 | | | ✅ |
| 인사이트 도출 | | ✅ | |
| 전략 연결·기획 | | ✅ | |
| 발표자료 제작 | | ✅ | |

### 팀원별 상세 기여

**김예원**
데이터 수집(`REST API 크롤링`, `JSON 파싱`), 데이터 전처리(`정규표현식`, `결측치 처리`, `중복 제거`, `파생변수 생성`, `데이터 병합`), 시각화(`벤다이어그램`, `막대그래프`, `포지셔닝맵`), 파일 통합 및 정리(`GitHub`, `Google Drive 관리`)

**조수경**
프로젝트 총괄(분석 전략 수립 및 프레임워크 설계), 보고서 작성, 발표자료 제작(PPT), 발표

**최홍주**
자연어 처리(`형태소 분석`, `TF-IDF`, `문장 임베딩`, `감성분석`), 군집 분석(`K-Means 클러스터링`, `엘보우/실루엣/데이비스-볼딘 지수 검정`, `UMAP`), 시각화(`UMAP 산점도`, `워드클라우드`, `막대그래프`)
