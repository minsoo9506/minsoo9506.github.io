# [PRML] Bayesian Optimization


Bayesian Optimization으로 모델의 성능을 올려보자.
<!--more-->

## Bayesian Optimization with Gaussian Process
어떤 sequence of experiments를 한다고 생각해보자. 다음은 몇 가지 가정사항이다.
- experiment result should be maximized(or minimized)
- 우리는 experiments result를 만드는 underlying function을 모른다.
- input은 우리가 다 알고 조절할 수 있다.
- results and input are continuous
- result have a stochastic element

이런 경우 이전에는
- Grid search
  - no learning of underlying function
- Binary search
  - learning of constraints, not the function

와 같은 방법들이 많이 사용되었다. 이제는 
- learning underlying function
- selecting the next sampling input

을 모두 잡을 수 있는 Bayesian Optimization을 사용하고자 한다.

- GP는 모든 data point에서 predicted mean, predicted std를 알려준다.
- input을 넣고 underlying function을 만든다. 그 후에 mean과 variance를 통해 exploitation or exploration를 결정하여 next sampling input을 결정한다. (그리고 다시 underlying function을 만든다. 이를 반복한다.) 이에 대한 판단 기준이 필요한데 acquisition function을 이용한다.
  - Exploitation : result값이 높은 곳(underlying function mean이 큰) 탐색
  - Exploration : 관측지가 적은 곳(variance가 큰) 탐색

### Acquisition Function : Maximum probability of improvement
Acquisition Function은 다양하다. 그중 MPI에 대해 알아보자.
- Maximum probability of improvement
  - 현재 optimized value $y_{max}$를 어떤 margin m 이상으로 올려줄 확률이 가장 높은 input을 sampling한다. (grid search처럼 value를 계산하는 것이 아니라 확률만 계산하여 진행)
  - $D$는 기존 data, 이를 통해 GP를 만들수 있겠다.
  - $y \sim N(\mu, \sigma^2)$ 이는 GP로 만들어진 것

$$MPI(x|D) = argmax_x P(y \ge (1+m)y_{max} | x, D),\;y\sim N(\mu, \sigma^2) \\=argmax_x P(\frac{y-\mu}{\sigma} \ge \frac{(1+m)y_{max}-\mu}{\sigma})\\=argmax_x \Phi (\frac{\mu - (1+m)y_{max}}{\sigma})$$
