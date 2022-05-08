# [Algorithmic Marketing] Uplift Model


타겟팅을 할 때, 주로 사용하는 uplift 모형에 대한 기본개념을 알아보자.

<!--more-->
예를 들어, 앱 사용자들에게 푸시알람을 보내서 전환율(물건구매라고 생각해도 됨)을 높이고 싶은 상황이라고 생각해보자. 모든 유저에게 푸시알람을 보낼 수 없거나 푸시알람이 비용이 발생하는 경우, 전환율이 높을 것 같은 유저에게 푸시를 보내는 것이 맞을 것이다. 이를 모델링으로 해결하고자 한다면 어떻게 해야 할까?

일단 크게 두 가지를 생각할 수 있다. response model (classification), uplift model 이 있다. response model의 경우 이전에 푸시알람을 보낸 데이터를 이용하여 전환여부를 target값으로 놓고 classification model을 만들고 predict하여 확률값으로 sort하여 상위 %에서 보내는 방법이다. 우리가 살펴볼 uplift model의 경우는 조금 다르다. 실험데이터가 필요하기 때문이다. 두 model 모두 공통적으로 covariates와 target은 있지만 uplift model은 RCT를 이용해 구한 treatment여부가 있는 데이터가 필요하다. 예시에서는 푸시알람이 treatment이다. 그래서 각 고객들 중에서 treatment effect가 큰 고객들에게 먼저 보내려고 하는 것이다. 이에 대해 아래 그림을 참고하자.

<center>
    <img src="https://github.com/minsoo9506/blog/blob/master/static/blog-imgs/uplift01.png?raw=true"  width="500">
</center>

아래 그림에서 uplift model이 찾고자하는 그룹은 persuadables이다. 즉, 푸시알람을 보내야만 구매를 하는 사람에게 푸시알람을 보내고 싶은 것이다. 푸시알람을 보내지 않더라도 어차피 물건을 구매하는 사람에게는 보낼 필요가 없고 푸시알람에 상관없이 구매하지 않을 사람, 푸시알람을 받으면 오히려 역효과가 나는 사람들에게도 보낼 필요가 없기 때문이다. 하지만 멀티버스가 존재하지 않는 한 우리는 특정 고객이 푸시알람을 받아야 구매할지 아닐지 알 수가 없다. 둘 중 하나의 결과만 알 수 있기 때문이다. 따라서 uplift model은 이러한 문제를 해결하기 위한 접근이라고 할 수 있다.

마케팅쪽에서는 uplift model이라고 하고 causality쪽에서는 Heterogeneous treatment effect estimation 이라고 불리는 분야이다. 당연히 이와 관련하여 다양한 모델들이 존재한다. Uber나 Microsoft의 오픈소스들을 참고하면 이해하기 더 좋다.

여기서는 다양한 모델중에서 T-Learner에 대해 간단히 정리해보고자 한다. 실제 데이터를 통해 구한 모델링 과정의 코드는 [여기](https://github.com/minsoo9506/causality-study/blob/master/Applied%20Project/Heterogeneous_treatment_effect_estimation/metaLearner(T-learner).ipynb)서 확인하면 된다.

모델링 과정은 다음과 같다.

1. 데이터는 train, valid로 나눈다.
2. train data에서 treat, control 그룹을 각각 나누어서 모델을 각자 만든다.
    - 이 때 모델의 X에는 treatment여부는 빠지고 target은 전환여부(binary)이고 이를 예측하는 위한 covariate들로 이루어져 있다.
    - 모델은 classification 모델 아무거나 상관없다.
3. 그리고 각 모델에 valid data를 넣어서 prediction한다. 이 때, 우리는 확률을 이용할 것이다.
    - 이 valid data에는 treat, control 그룹 모두 들어있다.
    - 즉, 2번에서 만든 모델을 통해 valid data에 있는 유저들이 treatment를 받았을 때와 그렇지 않았을 때 target이 어떨지 각각 구할 수 있는 것이다. 
4. 위의 확률값을 통해서 uplift score를 구한다.
    - treatment 받았을 때의 전환확률 - treatment 받지 않았을 때의 전환확률
5. uplift score에 따라 sort하여서 10%씩 실제 전환율을 살펴보자.

위의 과정을 통해 구한 결과는 아래 사진과 같다. uplift가 0보다 큰 상위 60%정도까지는 실제 treat 그룹의 response rate가 더 높다. 우리는 uplift score가 높은 고객들, 즉 푸시알람을 받았을 때 effect가 큰 유저들에게 보내도 되겠다는 증거가 되는 것이다.

<center>
    <img src="https://github.com/minsoo9506/blog/blob/master/static/blog-imgs/uplift02.png?raw=true"  width="500">
</center>


## Reference
- https://matheusfacure.github.io/python-causality-handbook/21-Meta-Learners.html
- https://partrita.github.io/posts/python-uplift/
