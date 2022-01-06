# [Algorithmic Marketing] 생존분석 기본개념


생존분석 방법론의 기본적인 개념을 알아보자. 

<!--more-->

생존분석은 주로 의학통계에서 많이 사용해왔다. 마케팅쪽에서는 주로 고객이탈(churn)과 관련하여 방법론을 가져와서 사용하기도 한다. 그외에도 특정한 treatment를 취한 뒤에 시간의 흐름에 따른 변화를 보기위해서도 많이 사용된다. 이름이 생존분석이라 엄청 어려워보이지만 실제로는 새로운 개념 몇가지만 이해한다면 regression처럼 어렵지 않게 이해할 수 있다.

## 생존분석
- 자료의 형태: 어떤 사건이 발생할 때까지 걸리는 시간 (time until an event occurs)
    - 일반적인 regression 문제 아닌가? censored data가 존재하기 때문에 생존분석 접근이 필요하다.

여기서 *censored data*라는 것은 중도절단된 데이터라고 할 수 있다. 예를 들어, 신약을 두 환자그룹에 투여하고 사망까지 걸리는 시간을 측정하고자 한다. 그런데 실험대상이던 홛자가 외국으로 떠나서 연락이 두절되었다. 그러면 그 환자는 실제로 사망한 것은 아니지만 실제로 살았는지 죽었는지 알 방법이 없고 데이터를 더 이상 얻을 수 없는 시점을 그 환자의 수명으로 정할 수 밖에 없다. 이처럼 우리가 기대하는 이벤트(여기서는 사망)가 아닌 다른 이유로 해당 정보를 얻을 수 없는 경우 censored data라고 한다.

- 자료의 예
    - 사건: 사망, 질병발생, 구독취소
    - 시간: 년, 달, 주
- 분석예시: 전립선 암 치료제 효과 비교
    - 사건: 전립선 암으로 인한 사망
    - 시간: randomization시점부터 사망까지의 시간
    - 분석목적: A라는 약을 먹는 집단이 그렇지 않은 집단보다 생존 시간이 더 긴가?

### 생존분석의 특징
- 관심자료가 nonnegative
    - time until an event occurs
- 생존시간의 분포는 주로 skewed
    - log transformation으로 해결되지 않을 정도
- 생존자료가 중도절단(censored)되어 관측
    - 아직 이벤트가 발생하지 않았지만 연구자가 멈추는 경우
    - 우리가 보고 싶은 이벤트 때문이 아니라 다른 이유로 더 이상 관찰을 못하는 경우

### Notation
- $T$: survival time
- $C$: censoring time

$$Y=min(T,C)$$

- status indicator:

$$\delta =
\begin{cases}
1,\\;if\\;T \le C \\\
0,\\;if\\;T > C
\end{cases}$$

- 우리가 관찰하는 dataset은 $(Y,\delta)$ pair의 형태이다.

## 생존분석에서 관심사
- 생존시간의 분포 파악 (집단 1개)
    - 생존 함수 추정: 주로 Kaplan-Meier 추정법
- 집단 간 생존 분포 비교 (집단 2개 이상)
    - 생존 함수 비교: 주로 log-rank 검정
- 생존시간에 대한 변수들의 효과 파악
    - Cox 회귀 모형

## 생존함수(Survival function)
$$S(t)=P(T>t)=1-F(t)$$
- 특정 시점 $t$보다 더 오래 살 확률

### KM 추정법
위의 생존함수를 추정하는 방법은 다양하지만 주로 Kaplan-Meier 추정값을 많이 사용한다. 간단하면서도 유용한 방법이기 때문이다.

$$S_t = \frac{n_t - d_t}{n_t}$$
- $n_t$ : 시간 $t$에 이벤트를 경험할 위험에 놓인 개체의 수
- $d_t$ : 시간 $t$에 이벤트를 경험한 개체의 수

$$\hat{S}(t) = \prod_{i \le t} S_i = \prod_{i \le t} (1 - \frac{d_i}{n_i})$$

위의 식을 Kaplan-Meier 추정값이라고 한다. (MLE이다)
- 표준오차를 통해서 신뢰구간도 알 수 있다.
- 비모수적 생존함수 추정법
- survival curve 를 항상 그려보자. [(코드예시)](https://github.com/minsoo9506/algorithmic-marketing/blob/master/survival_analysis/survival_analysis_example.ipynb)

## 두 집단 생존함수 비교
위와 같은 방법으로 생존함수를 구했다면 집단간의 차이가 있는지 궁금할 수 있다. 예를 들어, 신약을 먹은 집단과 신약을 먹지 않은 집단간의 생존함수가 차이가 있는지 검정하고 싶은 경우를 생각해 볼 수 있다. 신약의 효과에 대해서 검정할 수 있는 것이다. (censored data도 없고 less skewed되어 있다면 그냥 t-test같은 방법도 가능할 것이다)

- 두 집단의 생존함수가 같은지 비교, 검정한다.
- 특정 시점에서의 비교가 아니라 모든 시점을 비교하는 것이다.

$$H_0: S_1(t) = S_2(t), \forall t$$

### log-rank test
- 일관적인 차이를 검출한다.
    - 여기서 일반적인이란 두 그룹의 survival curve를 그렸을 때, 서로 교차하지 않는 것을 의미한다.
    - 교차하게 되면 검정할 수 없다.
- 시점별 다른 가중치를 고려할 수 있다.
- 세 집단 이상도 비교 가능하다.
- 디테일한 유도과정은 생략 [(코드예시)](https://github.com/minsoo9506/algorithmic-marketing/blob/master/survival_analysis/survival_analysis_example.ipynb)

## 위험도함수(hazard function)
$$h(t) = \lim_{\vartriangle t \rightarrow 0} \frac{P(t < T \le t + \vartriangle t | T > t)}{\vartriangle t}$$
- 특정 시점 $t$까지 살아남았을 때, 시간 단위당 순간 사건 발생률
- 조건부가 존재하기에 확률은 아니다.
- 왜 위험도함수가 필요할까?
    - propotional hazards model의 기반이 된다.
    - censored data로 인해 바로 접근하기 어렵기 때문에 새로운 개념인 위험도함수를 통해서 (생존함수도 마찬가지) 접근하면 문제를 더 쉽게 해결할 수 있기 때문이다.

## 생존분석에서 식들의 관계
$$h(t) = \lim_{\vartriangle t \rightarrow 0} \frac{P(t < T \le t + \vartriangle t | T > t)}{\vartriangle t}$$
$$= \lim_{\vartriangle t \rightarrow 0} \frac{P( (t < T \le t + \vartriangle t ) \cap (T > t))}{P(T > t) \vartriangle t}$$
$$=\lim_{\vartriangle t \rightarrow 0} \frac{P(t < T \le t + \vartriangle t )/\vartriangle t}{P(T > t) }=\frac{f(t)}{S(t)}$$
- where $f(t) = \lim_{\vartriangle t \rightarrow 0} \frac{P(t < T \le t + \vartriangle t )}{\vartriangle t}$ : probability density function associated with $T$

## Proportional Hazards Model
survival time을 여러 covariates로 모델링을 하고 싶은 경우 아래와 같이 hazard function의 형태를 가정하여 접근한다. [(코드예시)](https://github.com/minsoo9506/algorithmic-marketing/blob/master/survival_analysis/survival_analysis_example.ipynb)

$$h(t|x_i) = h_0 (t) \exp (\sum_{j=1}^p x_{ij}\beta_j)$$

- where $h_0(t) \ge 0$ is an unspecified function (baseline hazard)
    - functional form에 대한 가정이 없기 때문에 flexible하게 모델링 가능
- 물론 proportional한 가정자체가 이미 한계가 존재하는 것은 사실이다.
- 이외에도 survival forest와 같은 접근법도 존재한다.

위의 내용을 proportional hazard assumption 이라고 한다.

### Partial Likelihood
- 그런데 baseline hazard의 형태를 모르기 때문에 parameter ($\beta$)를 추정할 수 없다.
- 하지만 cox's proportional hazards model에서는 가능하다.

아래의 과정을 보자.

- 가정
    - 일단 이벤트가 발생한 시간이 no ties 를 가정한다.
    - $\delta_i = 1$, 따라서 $t_i = y_i$
- total hazard at time $y_i$ for the at risk observations:
$$\sum_{i' : y_{i'} \ge y_i}h_0(y_i) \exp (\sum_{j=1}^p x_{i'j}\beta_j)$$
- the probability that the *i*th observation is the one to fail at time $y_i$:
$$\frac{h_0(y_i) \exp (\sum_{j=1}^p x_{ij}\beta_j)}{\sum_{i' : y_{i'} \ge y_i}h_0(y_i) \exp (\sum_{j=1}^p x_{i'j}\beta_j)} = \frac{ \exp (\sum_{j=1}^p x_{ij}\beta_j)}{\sum_{i' : y_{i'} \ge y_i} \exp (\sum_{j=1}^p x_{i'j}\beta_j)}$$

위의 식을 이용하여 $\delta_i = 1$인 경우만 고려하여 partial likelihood를 구하면

$$PL(\beta) = \prod_{i:\delta_i = 1} \frac{ \exp (\sum_{j=1}^p x_{ij}\beta_j)}{\sum_{i' : y_{i'} \ge y_i} \exp (\sum_{j=1}^p x_{i'j}\beta_j)}$$

- logistic regression 처럼 iterative하게 parameter를 추정한다.
- $\beta$를 추정하면 일반적인 회귀방법론들처럼 계수에 대한 p-value, confidence interval을 구할 수 있다.
- 맨처음 가정이 깨지는 경우 (당연히 깨지는 경우가 더 많다), 이를 해결하는 방법들이 있다.

## Reference
- ISLR 2nd edition
- 연세대학교 응용통계학과 강상욱 교수님 특강자료
- 알고리즘 마케팅
