## Main Quest 02. 가랏, 몬스터볼! 전설의 포켓몬 찾아 삼만리

| 평가문항  | 상세기준 | 
| :--- | :--- | 
| 1. 충분한 EDA를 진행하였는가? | EDA(탐색적 데이터 분석), 데이터 전처리, 검증 데이터 분리 과정을 수행 하였는가? | 
| 2. 머신러닝 모델을 3개 이상 사용하였는가? | 머신러닝 모델을 3가지 이상 사용하여 결과를 확인해 보았는가? |   
| 3. 회고를 잘 정리했는가? | 머신러닝과 관련한 질문들을 정리하여 회고 작성하기 | 

### 목차

1. **포켓몬, 그 데이터는 어디서 구할까?** Pokemon 데이터 가져와서 준비하기  
2. **전설의 포켓몬? 먼저 샅샅이 살펴보자!** 전체 데이터셋 탐색  
3. **전설의 포켓몬과 일반 포켓몬, 그 차이는?** target의 특징 살펴보기  
4. **모델에 넣기 위해! 데이터 전처리하기** 데이터 전처리  
5. **가랏, 몬스터볼!** 베이스라인 모델을 학습시키고, 데이터 성능 올리기  

### 학습 전제

* `numpy`, `pandas`를 활용해서 표 데이터를 다뤄본 적이 있고, 이에 대한 기본적인 활용을 할 수 있습니다.
* `pandas의 문법과 다양한 메서드`를 활용해 본 적이 있고, 코드를 보면 어느 정도 이해할 수 있습니다.
* `matplotlib`을 활용해서 데이터 시각화를 해본 적이 있고, 코드를 보면 어느 정도 이해할 수 있습니다.
* 데이터셋을 `train/test 데이터셋`으로 나누어서 모델을 학습 및 검증해본 경험이 있습니다.

### 학습 목표

* 다양한 피처가 있는 데이터셋을 밑바닥부터 샅샅이 뜯어보고, 전설의 포켓몬을 분류하기 위한 피처에는 무엇이 있는지 생각해 봅시다.
* 모델 학습을 시작하기 전 모든 컬럼에 대해 `그래프 시각화`, `피벗 테이블` 등을 활용하며 다양한 방법으로 충분한 EDA를 진행합니다.
* 모델 학습에 넣기 위해서 전처리가 필요한 범주형/문자열 데이터에 대한 전처리를 `원-핫 인코딩` 등으로 적절하게 진행합니다.
* 전체 데이터셋을 `train/test 데이터셋`으로 나누고, 적절한 분류 모델(Decision Tree)을 선택해 학습시키며 베이스라인과 비교합니다.

### Retrospect

>머신러닝의 다양한 분류 모델을 사용하여 포켓몬스터 데이터를 학습-예측-검증하는 과정을 실습하였다. 머신러닝과 관련하여 지금까지 학습한 내용들을 복습할 수 있어서 유익했다. 개인적으로 각 모델의 특징과 관련하여 특정 모델을 선택하는 이유와 검증 결과값을 해석하는 것에 대한 이해가 부족하다는 것을 느꼈고, 복습을 통해 분류와 회귀 모델에 대한 이해가 더 필요하다는 것을 확인할 수 있었다. EDA와 데이터 전처리 과정에서 궁금했던 부분들이 다수 있었으며, 자세한 내용은 아래와 같이 정리하였다.  

**Q. 정규식은 모든 프로그래밍 언어에서 동일한가?**  

```
정규식의 기본 원칙과 구문은 상당히 일관되지만, 다양한 프로그래밍 언어와 도구에서 구현되고 지원되는 기능에는 차이와 변형이 있을 수 있음.  
```

**Q. 파이썬에서 사용하는 regex와 다른 프로그래밍 언어에서 사용하는 regex 간의 차이점**  

```
1. 문자열 표현: 파이썬에서는 정규 표현식 패턴을 나타내는 문자열 앞에 'r'을 붙여 raw 문자열로 처리,  
    이렇게 하면 백슬래시(\)를 이스케이프 문자로 취급하지 않고 그대로 문자열로 인식.  

2. 모듈 사용: 파이썬에서는 정규 표현식을 다루기 위해 re 모듈을 사용,  
    다른 언어는 각각의 표준 라이브러리나 외부 패키지를 사용.  

3. 함수 및 메서드: 파이썬의 re 모듈은 정규 표현식과 관련된 다양한 함수와 메서드를 제공,  
    다른 언어의 정규 표현식 라이브러리에서는 이러한 함수와 메서드의 이름과 사용법이 다를 수 있음.  

4. 플래그: 정규 표현식에서 사용하는 플래그 (예: 대소문자 구분 여부, 다중 행 모드 등)는 다른 언어에서도 지원,  
    플래그의 이름과 설정 방법은 언어에 따라 다름.  

5. 유니코드 지원: 파이썬의 정규 표현식은 기본적으로 유니코드를 지원하며, 유니코드 문자 클래스 및 속성을 사용.  
``` 

**Q. ML 및 통계 분야에서 분류 문제의 성능을 평가하기 위해 사용되는 주요 행렬(matrix)의 유형**  

```
1. `혼동 행렬 (Confusion Matrix)`: 가장 일반적으로 사용되는 행렬,  
    모델의 예측 결과와 실제 결과 간의 관계를 보여줌. 혼동 행렬은 True Positives (TP), True Negatives (TN),  
    False Positives (FP), 그리고 False Negatives (FN) 네 가지 주요 요소를 포함.  

2. `오차 행렬 (Error Matrix)`: 혼동 행렬의 다른 이름으로 사용, 분류 모델의 오류를 설명하는 데 사용.  

3. `등급 행렬 (Rating Matrix)`: 다중 클래스 분류 문제에서 사용, 모든 클래스 간의 혼동을 보여줌.  
    각 클래스에 대한 True Positives, True Negatives, False Positives 및 False Negatives를 포함.  

4. `ROC 곡선 (Receiver Operating Characteristic Curve)`: 분류 모델의 성능을 평가하기 위한 그래프.  
    ROC 곡선은 True Positive Rate (재현율) 대비 False Positive Rate를 그린 그래프로,  
    모델의 분류 임계값을 조정하여 분류 성능을 시각화하는 데 사용.  

5. `AUC (Area Under the ROC Curve)`: ROC 곡선 아래 영역,  
    모델의 전반적인 성능을 요약하는 데 사용. 값은 0에서 1 사이에 있으며,  
    1에 가까울수록 모델의 성능이 좋음을 나타냄.  

6. `혼동 매트릭스 히트맵 (Confusion Matrix Heatmap)`: 혼동 행렬을 시각적으로 표현한 히트맵,  
    클래스 간의 관계를 쉽게 파악할 수 있도록 도와줌.  

7. `G-Measure`: 정밀도와 재현율의 조화 평균으로 모델의 성능을 평가하는 지표 중 하나.  

8. `카파 통계량 (Cohen's Kappa)`: 모델의 예측 결과가 무작위 예측과 비교하여 얼마나 더 나은지를 나타내는 지표.
```

**Q. K-Fold 기법과 Stratified-KFold 기법의 정의 및 차이점**  

```
`K-Fold Cross-Validation`  
    * 데이터를 무작위로 K개의 폴드(부분 집합)로 분할. 일반적으로 K는 5 또는 10으로 선택.  
    * 모델은 K번의 반복 실험을 수행, 각 실험에서 하나의 폴드를 테스트 세트로 사용하고 나머지 (K-1)개의 폴드를 학습 세트로 사용.  
    * 각 폴드가 한 번씩 테스트 세트로 사용되므로 K번의 모델 학습 및 평가가 수행.  
    * 주로 고정된 분할을 사용, 데이터 분포의 특성을 고려하지 않음. 따라서 각 폴드 내의 클래스 분포가 불균형.  

`Stratified K-Fold Cross-Validation`  
    * 클래스 분포를 유지하기 위해 데이터를 분할. 이는 불균형한 클래스 분포를 갖는 데이터 세트에 유용.  
    * 데이터가 여러 클래스로 나뉘어 있을 때, 각 폴드에는 각 클래스의 비율이 고려되도록 분할.  
    * Stratified K-Fold는 모델의 일반화 성능을 더 정확하게 평가, 특히 클래스 불균형이 있는 분류 문제에서 사용.  

주요 차이점?  
    * K-Fold는 각 폴드를 단순히 데이터의 임의 분할로 생성하는 반면, Stratified K-Fold는 각 폴드에서 클래스의 분포를 유지.  
    * Stratified K-Fold는 주로 분류 문제에 사용, 클래스 불균형 문제에 특히 유용.  
    * K-Fold는 데이터 분포의 특성을 무시하고 단순한 무작위 분할을 사용하는 반면, Stratified K-Fold는 각 폴드에서 클래스 분포의 유사성을 유지.  
``` 

**Q. 비정형 데이터를 분석하기 위해 적합한 머신러닝 모델은? *텍스트 분석(Text Analysis)을 중심으로***

```
텍스트 데이터를 분석하려면 자연어 처리 (NLP) 기술과 관련 모델을 사용.  

* `백 오브 워즈 (Bag of Words)`: 단어 등의 텍스트 요소를 특성으로 사용하는 모델, 간단한 텍스트 분류에 사용.  
* `TF-IDF (Term Frequency-Inverse Document Frequency)`: 단어의 중요도를 나타내는 특성을 생성하여 텍스트 분류에 사용.  
* `Word Embeddings (Word2Vec, GloVe)`: 단어를 밀집 벡터로 표현하는 방법, 딥러닝 모델과 연계하여 텍스트 분류 및 유사성 분석에 사용.  

**Q. `countplot`과 `scatterplot`**  

1. 목적:  
    - countplot: 범주형 데이터의 빈도를 시각화하는 데 사용. 주로 각 범주가 데이터셋에 얼마나 자주 나타나는지 보여줌.  
    - scatterplot: 연속형 변수 사이의 관계를 시각화하는 데 사용. 두 변수 간의 관계, 패턴, 이상치를 확인하는 데 유용.  

2. 데이터 유형:  
    - countplot: 주로 범주형 데이터를 다루며, 각 범주의 빈도를 막대 그래프로 표현.  
    - scatterplot: 연속형 데이터를 다루며, 두 변수 간의 관계를 산점도로 나타냄.  

3. 그래프 형태:  
    - countplot: 주로 막대 그래프 형태, 각 범주에 해당하는 막대가 그려짐.  
    - scatterplot: 점 형태의 그래프, 데이터 포인트가 두 변수에 따라 위치.  

4. 데이터 표현:  
    - countplot: 각 범주의 빈도를 표현하여 범주 간 비교를 할 수 있음.  
    - scatterplot: 두 연속형 변수 사이의 관계를 표현하여 변수 간 상관성 및 패턴을 파악.  

5. 예시:  
    - countplot: 성별, 학력 수준, 도시, 카테고리 등과 같은 범주형 변수의 빈도를 시각화할 때 사용.  
    - scatterplot: 나이와 소득, 온도와 판매량, 시간과 주가 등과 같은 연속형 변수의 관계를 시각화하고 분석할 때 사용.  

6. 사용 사례:  
    - countplot: 범주형 데이터의 분포 및 클래스 불균형을 확인하거나 범주 간 비교를 할 때 사용.  
    - scatterplot: 연속형 변수 사이의 상관성을 확인하고, 데이터 분포와 이상치를 식별하는 데 사용.  

요약하면, `countplot`은 범주형 데이터의 빈도를 시각화하는 데 사용되고, `scatterplot`은 연속형 변수의 관계와 패턴을 시각화하는 데 사용.
```