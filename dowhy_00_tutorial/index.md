# [DoWhy] Tutorial


MS의 causal inference를 공개한 library DoWhy에 대해 알아보자

<!--more-->
DoWhy는 causality에 관련하여 다양한 기능을 제공한다. 이를 사용하는 tutorial을 진행하면서 DoWhy에 대해 이해하고 observational data를 이용한 causal inference의 과정에 대해 이해하고자 한다.

먼저, DoWhy에서 제공(제안)하는 causal inference의 단계는 다음과 같다.
- 1. Modeling: Create a causal graph to encode assumptions
- 2. Identification: Formulate what to estimate
- 3. Estimation: Compute the estimate
- 4. Refutation: Validate the assumptions

DoWhy에서는 기본적으로 causal graph를 이용하여 inference를 진행한다. 실제 데이터에서 causal graph를 알아내기는 쉽지 않다. domain 지식을 이용해야한다. 물론 DoWhy에서 causal discovery의 기능도 제공한다.

causal graph를 찾았으면 이제 identification을 할 차례이다. 이 과정은 causal graph를 이용하여 causality를 추정할 수 있는지 확인하는 과정이라고 할 수 있다. 다양한 방법들이 있다.
- Graphical constraint-based methods
  - RCT
  - Backdoor criterion
  - Frontdoor criterion
  - Mediation
- Non-graphical constraints methods
  - Instrumental variables
  - Regression discontinuity
  - Difference-in-difference

identification이 잘 됐다면 우리는 estimation할 수 있다. estimation에도 다양한 방법들이 있다.
- conditioning
  - Matching
  - Stratification
- Propensity Score-Based
  - Propensity Mathching
  - Inverse Propensity Weighting
- Synthetic Control
- Outcome-Based
  - Double ML
  - T-learner
  - X-learner
- Loss-Based
  - R-learner
- Threshold-based
  - DID

마지막으로 estimation으로 causal inference를 완료하였다면 이를 validation하는 과정도 필요하다. 하지만 causality의 경우 정답지가 없기 때문에 우리가 잘했는지 확인하기 어렵다. 대신에 여러가지 가정들을 확인하면서 틀렸는지는 확인해볼 수 있다.
- Unit test
  - Model
    - Conditional independence test
  - Identify
    - D-seperatio test
  - Estimate
    - Bootstrap refuter
    - data subset refuter
- Integration test
  - Placebo treatment refuter
  - Dummy outcome refuter
  - Random common cause refuter
  - sensitivity analysis
  - simulated outcome refuter

## Practice
[전체코드](https://github.com/minsoo9506/causality-study/blob/master/DoWhy/00_tutorial.ipynb)는 github에 올려놓았다. 공홈을 거의 그대로 따라했다.

### data
먼저 data를 생성해보자. `beta`가 true causal effect이다.

```python
data = dowhy.datasets.linear_dataset(beta=10,
        num_common_causes=5,
        num_instruments = 2,
        num_effect_modifiers=1,
        num_samples=5000,
        treatment_is_binary=True,
        stddev_treatment_noise=10,
        num_discrete_common_causes=1)
df = data["df"]
```

`data`에 들어있는 값들은 아래와 같다. (`df`제외, `df`는 위의 조건대로 만들어진 `pandas.DataFrame`)

```
'treatment_name': ['v0'],
'outcome_name': 'y',
'common_causes_names': ['W0', 'W1', 'W2', 'W3', 'W4'],
'instrument_names': ['Z0', 'Z1'],
'effect_modifier_names': ['X0'],
'frontdoor_variables_names': [],
'dot_graph': 'digraph {v0->y;W0-> v0; W1-> v0; W2-> v0; W3-> v0; W4-> v0;Z0-> v0; Z1-> v0;W0-> y; W1-> y; W2-> y; W3-> y; W4-> y;X0-> y;}',
'gml_graph': 'graph[directed 1node[ id "y" label "y"]node[ id "W0" label "W0"] node[ id "W1" label "W1"] node[ id "W2" label "W2"] node[ id "W3" label "W3"] node[ id "W4" label "W4"]node[ id "Z0" label "Z0"] node[ id "Z1" label "Z1"]node[ id "v0" label "v0"]edge[source "v0" target "y"]edge[ source "W0" target "v0"] edge[ source "W1" target "v0"] edge[ source "W2" target "v0"] edge[ source "W3" target "v0"] edge[ source "W4" target "v0"]edge[ source "Z0" target "v0"] edge[ source "Z1" target "v0"]edge[ source "W0" target "y"] edge[ source "W1" target "y"] edge[ source "W2" target "y"] edge[ source "W3" target "y"] edge[ source "W4" target "y"]node[ id "X0" label "X0"] edge[ source "X0" target "y"]]',
 'ate': 12.852576193620925}
```

### Model causal mechanism
위에서 만든 `data`를 이용하여 causal graph를 생성한다.
```python
model=CausalModel(
        data = df,
        treatment=data["treatment_name"],
        outcome=data["outcome_name"],
        graph=data["gml_graph"]
        )
```

생성한 데이터가 아닌 다른 데이터를 이용하려면 위와 같은 format으로 만들면 된다. 갖고 있는 데이터에 대해 저절로 찾아주는게 아니라 사용자가 알려줘야한다.

```python
# 예시
causal_graph = """
digraph {
High_limit;
Churn;
Income_Category;
Education_Level;
Customer_Age;
U[label="Unobserved Confounders"];
Customer_Age -> Education_Level; Customer_Age -> Income_Category;
Education_Level -> Income_Category; Income_Category->High_limit;
U->Income_Category;U->High_limit;U->Churn;
High_limit->Churn; Income_Category -> Churn;
}
"""

model= CausalModel(
        data = training, # dataframe
        graph=causal_graph.replace("\n", " "),
        treatment='High_limit',
        outcome='Churn')
```

그러면 어떤 causal graph가 만들어졌는지 확인해보자.
```python
model.view_model()

from IPython.display import Image, display
display(Image(filename="causal_model.png"))
```

<center>
    <img src="https://github.com/minsoo9506/blog/blob/master/static/blog-imgs/DoWhy_00.png?raw=true"  width="400">
</center>

각 node가 의미하는 바는 위의 `data` 설명을 참고하자.

### Identify the target estimand
```python
identified_estimand = model.identify_effect(proceed_when_unidentifiable=True)
# proceed_when_unidentifiable. It needs to be set to True
# to convey the assumption that we are ignoring any unobserved confounding

print(identified_estimand)
```

결과로 아래와 같이 identify하는 방법들에 대한 설명이 나온다. 귀엽게 수식도 나오고 가정도 알려준다. 나중에 시간이 되면 소스코드를 보고 싶다. 이런 오픈소스를 사용하다보면 겸손해진다.

```
Estimand type: nonparametric-ate

### Estimand : 1
Estimand name: backdoor
Estimand expression:
  d                       
─────(E[y|W3,W1,W2,W4,W0])
d[v₀]                     
Estimand assumption 1, Unconfoundedness: If U→{v0} and U→y then P(y|v0,W3,W1,W2,W4,W0,U) = P(y|v0,W3,W1,W2,W4,W0)

### Estimand : 2
Estimand name: iv
Estimand expression:
 ⎡                              -1⎤
 ⎢    d        ⎛    d          ⎞  ⎥
E⎢─────────(y)⋅⎜─────────([v₀])⎟  ⎥
 ⎣d[Z₁  Z₀]    ⎝d[Z₁  Z₀]      ⎠  ⎦
Estimand assumption 1, As-if-random: If U→→y then ¬(U →→{Z1,Z0})
Estimand assumption 2, Exclusion: If we remove {Z1,Z0}→{v0}, then ¬({Z1,Z0}→y)

### Estimand : 3
Estimand name: frontdoor
No such variable(s) found!
```

### Estimate causal effect
```python
causal_estimate = model.estimate_effect(identified_estimand,
        method_name="backdoor.propensity_score_stratification")
print(causal_estimate)
```

backdoor criterion과 propensity score를 이용하여 causal effect(지금은 ATE)를 구하면 아래와 같은 결과가 나온다. true값과 거의 비슷하게 나왔다.

```
propensity_score_stratification
*** Causal Estimate ***

## Identified estimand
Estimand type: nonparametric-ate

### Estimand : 1
Estimand name: backdoor
Estimand expression:
  d                       
─────(E[y|W3,W1,W2,W4,W0])
d[v₀]                     
Estimand assumption 1, Unconfoundedness: If U→{v0} and U→y then P(y|v0,W3,W1,W2,W4,W0,U) = P(y|v0,W3,W1,W2,W4,W0)

## Realized estimand
b: y~v0+W3+W1+W2+W4+W0
Target units: ate

## Estimate
Mean value: 10.257618758277024
```

### Refute estimate
refute의 방법에는 여러가지가 있다. 그 중 두가지만 알아보자. 

먼저 placebo방법은 treatment를 random하게 만들고 causal effect를 구해보는 것이다. 아래 결과의 `New effect`가 그 결과이다. 거의 0에 가까워야한다.

```python
# Replacing treatment with a random (placebo) variable
res_placebo=model.refute_estimate(identified_estimand, causal_estimate,
        method_name="placebo_treatment_refuter", placebo_type="permute")
print(res_placebo)
```
```
Refute: Use a Placebo Treatment
Estimated effect:10.257618758277024
New effect:0.004448677029791578
p value:0.49
```

다음은 data의 subset을 random하게 없애고 causal effect를 구하는 것이다. 일부를 없애도 `New effect`는 기존의 effect와 동일하게 나와야한다.

```python
# Removing a random subset of the data
res_subset=model.refute_estimate(identified_estimand, causal_estimate,
        method_name="data_subset_refuter", subset_fraction=0.9)
print(res_subset)
```

```
Refute: Use a subset of data
Estimated effect:10.257618758277024
New effect:10.220266223065927
p value:0.37
```

## Reference
- [Getting started with DoWhy: A simple example](https://microsoft.github.io/dowhy/example_notebooks/dowhy_simple_example.html)
- [Dowhy: An end-to-end library for causal inference slide](https://www2.slideshare.net/AmitSharma315/dowhy-an-endtoend-library-for-causal-inference)
