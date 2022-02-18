# [Causality] Propensity Score


Propensity Score에 대해 알아보자.

<!--more-->
회사에서 업무를 하다가 Inverse Propensity Weight 방법론을 사용하게 되었는데 관련 내용을 찾다가 pycon에서 2019년에 올라온 [영상](https://www.youtube.com/watch?v=gaUgW7NWai8)을 찾았고 이를 간단히 정리하였다.

## Propensity Score Matching
- A Non-experimental Approach to Causal Inference

### Propensity Score Mathcing
- RCT가 불가능 할 떄 사용한다.
- group들이 balanced되도록 만든다.
- selection bias를 해결하기 위해 사용한다.

어떤 treatment의 effect가 궁금하다. 그런데 RCT가 불가능하고 observational data만 존재한다. 따라서 confounder들의 영향이 존재하고 causal effect를 측정하기 위해서는 이를 없애야 한다. Pearl이 말하는 back-door problem이라고도 할 수 있다. 이를 해결하기 위해 Propensity Score Matching을 이용한다.

- step
1. Calculate the propensity
2. Use the propensity score to create a control group matched to the exposed group
3. Check that the exposed group and matched control group are similar
4. Estimate effect of exposure on the outcome of interset

### Propensity score
- probability of being exposed given a set preditors

$$Pr(T = 1| X)$$

- 이를 구할 떄 주로 Logistic regression을 사용한다.
    - $X$는 현재 갖고 있는 covariate들이고 
    - $y$ label은 treatment group(1)인지 control group(0)
- 여러 covariate들이 있는데 이들을 하나의 숫자(해당 instance가 treatment group에 속할 확률)로 압축해서 사용하는 것으로 볼수도 있다.

### Selecting Covariates
- covariate들은 treatment group의 selection process와 관련이 있어야 한다.
    - true confounders!, avoid instrumental variable
- 즉, covariate는 treatment에도 영향을 주고 target에도 영향을 주는 변수여야 한다.

### Matching algorithm
Matching에도 다양한 방법론들이 존재하는데 크게 아래처럼 나누어서 생각할 수 있다.
- Greediness: greedy vs optimal
- Distance: caliper vs nearest neighbor
- Symmetry: one-to-one vs one-to-many

### Checking for Balance
matching을 하고나서 항상 matching이 잘 됐는지 확인해야 한다. 이 또한 다양한 방법론이 존재한다.
- Cohen's $d$:
    - mean diffence와 pooled SD를 이용하여 아래처럼 계산
    - 각 covariate마다 계산해서 mean과 sd를 계산하여 $d$값을 구하고 match 전, 후를 비교
    - 상황마다 다르지만 절댓값 0.1 정도면 good

$$d = \frac{M_2 - M_1}{\sqrt{\frac{SD_{1}^2 + SD_{2}^2}{2}}}$$

### Incorporate the propensity score
propensity score를 이용하는 다른 방법론에 대핸 간략히 설명했다.
- Regression adjustment
    - effect model을 만들 때 propensity score를 covariate로 이용
- Stratified Analysis
    - Estimate $T$ effect within propensity score strata
- Inverse Probability of Treatment Weighting


## Why Propensity Scores Should Noe Be Used For Matching
- [Gary King's Lecture](https://www.youtube.com/watch?v=rBv39pK1iEs)

1시간짜리 영상인데 꽤 재밌었다. Propensity Score를 이용하여 matching을 진행할 때, 발생할 수 있는 문제들에 대해 알려준다. 시뮬레이션 데이터를 통해 설명해서 이해가 잘된다. 결론만 정리했다.
- Why propensity scores should not be used for matching
    - Low Standards: 때로는 좋은 결과를 낼 수 있지만 최선의 결과를 찾는 과정은 아니다.
    - The PSM Paradox: 오히려 더 안 좋아질 수 도 있다.

그 이외에 기억에 남는 것들
- matching을 통해 model dependence를 줄인다. matching을 하지 않으면 모델에 따라 데이터에 대한 해석이 달라질 수 있다. (특히 causal 부분에서)
- RCT보다 fully blocked가 더 좋다.
    - RCT는 observed, unobserved 모두 평균적으로 balance한데, fully blocked는 observed에서는 exact하기에 더 좋다.
- 결국 PS는 covariate를 하나의 수로 요약하는 것인데 이는 정보를 잃는 것으로 볼 수도 있고 따라서 효과가 그리 좋지 않을 수도 있다.
