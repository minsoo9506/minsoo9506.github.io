---
layout: post
mathjax : true
date: 2021-02-13
title: "Introduction to Boosting"
---

boosting에 대해 간략하게 이해해보자. 

이번에 정리할 내용은 아래와 같다.
- Background
  - Function estimation
  - Numerical optimization
  - Optimization in function space
- Boosting
- Gradient Boosting
- Convergence of Boosting

## Function estimation
우리에게 주어진 dataset을 이용하여 function estimation을 해야한다. 즉, 우리가 모르는 어떤 function $f$를 정해진 loss function $L(y,f)$를 이용하여 estimate or approximation $\hat{f}(\textbf{x})$를 구한다.

$$\hat{f}(\textbf{x})=\arg\min_{f(\textbf{x})} E_{y,\textbf{x}}L(y,f(\textbf{x}))\\=\arg\min_{f(\textbf{x})} E_\textbf{x}[E_{y,\textbf{x}}L(y,f(\textbf{x}))|\textbf{x}]$$

여기서 estimate의 형태나 loss function은 다양한 것이 올 수 있을 것이다. 우리는 이 문제를 풀기 위해 function search space를 parametric family of functions $f(\textbf{x};\theta)$로 제약을 둘 것이다. 즉 function opimization 문제에서 parameter estimation 문제로 바꾸는 것이다.

$$\hat{\theta} = \arg\min_{\theta}E_\textbf{x}[E_y L(y,f(\textbf{x};\theta))|x]$$

closed-form의 형태로 풀리면 쉽게 풀 수 있을 것이고 그렇지 않다면 iterative한 numerical optimization이 필요할 것이다.

## Numerical optimization
가장 흔히 사용되는 steepest gradient descent방법론으로 접근해보자.

$$\theta^{(t)} = \theta^{(t-1)}-\alpha \frac{\partial L}{\partial \theta^{(t-1)}}$$

위의 과정을 수렴할 때까지 반복하면 우리가 찾는 특정한 $\theta^{T}$을 얻을 수 있다. 따라서 구하고자 하는 parameters는 아래와 같은 successive increments(*steps* or *boosts*)로 나타낼 수 있다.

$$\theta^{(T)} = \sum_{t=0}^{T-1} (\theta_0 - \alpha_t \frac{\partial L}{\partial \theta^{(t)}}) \approx \sum_{t=0}^{T-1}(-\alpha_t \frac{\partial L}{\partial \theta^{(t)}})$$

이제 steepest gradient descent의 과정을 하나씩 지나가 보자.
- 시점 $t$의 gradient $\textbf{g}^{(t)}$는

$$\textbf{g}^{(t)}=\{ g_j^{(t)} \}=\{ [\frac{\partial L(\theta)}{\partial \theta_j}]_{\theta=\theta^{(t-1)}} \}$$

- 그 다음 나아가야할 step은 $-\alpha_t \textbf{g}^{(t)}$ 이다.

$$\theta^{(t)} = \theta^{(t-1)} -\alpha_t \textbf{g}^{(t)} \\ \text{where}\;\alpha_t = \arg\min_{\alpha} L(y, \theta^{(t-1)} - \alpha \textbf{g}^{(t)})$$

- negative gradient $-\textbf{g}^{(t)}$는 *steepest-descent* 방향을 정의하고 $\alpha_t = \arg\min_{\alpha} L(y, \theta^{(t-1)} - \alpha \textbf{g}^{(t)})$을 구하는 것을 그 방향을 따르는 *line search* 라고 한다.

## Optimization in function space
function space에도 numerical optimization을 적용해보자. function or classifier $\hat{f}$를 parameter처럼 생각해보자. 즉 nonparametric function을 estimate하기 위해 numerical optimization으로 접근하는 것이다. 

$$\hat{f}^{(T)} = \sum_{t=0}^{T-1} (\hat{f}_0 - \alpha_t \frac{\partial L}{\partial \hat{f}^{(t)}}) \approx \sum_{t=0}^{T-1}(-\alpha_t \frac{\partial L}{\partial \hat{f}^{(t)}})$$

$$\textbf{g}^{(t)}=\{ g_j^{(t)} \}=\{ [\frac{\partial L(\hat{f})}{\partial \hat{f}_j}]_{\hat{f}=\hat{f}^{(t-1)}} \}$$

$$\hat{f}^{(t)} = \hat{f}^{(t-1)} -\alpha_t \textbf{g}^{(t)} \\ \text{where}\;\alpha_t = \arg\min_{\alpha} L(y, \hat{f}^{(t-1)} - \alpha \textbf{g}^{(t)})$$

## Boosting

Boosting 알고리즘은 sum of weak models이다. random보다는 좋은 weak models (weak model은 tree, linear model등 다양한 model로 접근할 수 있다)들을 합해서 좋은 성능을 갖는 model을 만드는 것이 목표인 것이다. 그 모습은 아래와 같다.
- 위에서 정리한 것을 이용하자면
  - functional approach를 feasible하게 하려 parameter를 갖는 *base-learner(weak model)* 을 이용한다. 즉, 위에서 $\hat{f}$를 base-learner로 approximation한다고 이해할 수 있다.

$$\hat{F}_H = \sum_{h=1}^{H} w_h \hat{f}_h (x;\theta_h)$$

$w_h$는 각 model의 weight라고 할 수 있고 $\hat{f}_h(x;\theta_h)$는 weak model을 의미한다. 우리가 구해야하는 parameter는 $w_h,\theta_h$들이다. 정해진 loss function을 통해 parameter를 구해야 한다.

$$\arg\min_{\Theta,\textbf{x}} \sum_{i=1}^{N}L(y_i, \hat{F}_H(x_i))\\=\arg\min_{\Theta, \textbf{w}}\sum_{i=1}^{N}\sum_{i=1}^N L(y_i, \sum_{h=1}^H w_h \hat{f}_h (x;\theta_h))$$

근데 우리는 위의 동시에 parameter들의 optimal value를 구할 수 없다. 따라서 이를 approximation하는 *Forward Stagewise Additive Modeling* 방법론을 이용한다. 한번에 구할 수 없으니 순차적으로 parameter를 구하는 방법으로 이해할 수 있다.

### Forward Stagewise Additive Modeling
- input : $(\textbf{x}_1, y_1),(\textbf{x}_2, y_2),...,(\textbf{x}_N, y_N)$
- output : Ensemble classifier $\hat{F}_H$
- Intialize $\hat{f}_0 (\textbf{x}) = 0$
- for $h=1$ to $H$ do
  - Compute $(\beta_h, \theta_h) = \arg\min_{\beta, \theta} \sum_{i=1}^N L(y_i, \hat{f}_{h-1} + \beta k(\textbf{x};\theta))$
  - Set $\hat{f} _h(\textbf{x}) =  \hat{f} _{h-1} + \beta_h k(\textbf{x};\theta_h)$
- $\hat{F}_H (\textbf{x}) = \hat{f}_h (\textbf{x})$

## Gradient Boosting Machine
- Intialize $\hat{f}_0 (\textbf{x}) = 0$
- for $h=1$ to $H$ do
  - Compute gradient $\textbf{g}^{(t)} (\textbf{x}) = \{ g_j^{(t)}(\textbf{x}) \} = \{ [ \frac{\partial L(y, f(\textbf{x}))}{\partial f(\textbf{x})}] _{f(\textbf{x}) = \hat{F} _{h-1} (\textbf{x})} \}$
  - Compute $\theta_h = \arg\min_{\theta} \sum_{i=1}^N (-\textbf{g}^{(t)}(\textbf{x}_i) - k(\textbf{x}_i;\theta))^2$
  - Compute $\beta_h = \arg\min_{\beta} \sum_{i=1}^N L(y_i, \hat{f}_{h-1} + \beta k(\textbf{x}_i;\theta_h))$
  - Set $\hat{f} _h(\textbf{x}) =  \hat{f} _{h-1} + \beta_h k(\textbf{x};\theta_h)$
- $\hat{F}_H (\textbf{x}) = \hat{f}_h (\textbf{x})$

Gradient Boosting을 regression 예시를 통해 이해해보자.

$$ \text{loss}\;L(y,f) = \frac{1}{2}(y-f)^2 \rightarrow \frac{\partial L}{\partial f} = -(y-f)$$

이를 이용하여 차례대로 진행해보자. 먼저, $F_1(\textbf{x})$를 구하면
- $\theta_1 = \arg\min_{\theta} \sum_{i=1}^N (-\frac{\partial L}{\partial F_0}-k(\textbf{x}_i;\theta))^2$
- $\theta_1 = \arg\min_{\theta} \sum_{i=1}^N ((y_i-F_0(\textbf{x}_i))-k(\textbf{x}_i;\theta))^2$
  - residual에 fitting하는 것으로 볼수도 있다.
- $\beta_1 = \arg\min_{\beta} \sum_{i=1}^N ((y_i-F_0(\textbf{x}_i))-\beta k(\textbf{x}_i;\theta_1))^2$
- $F_1(\textbf{x}) = F_0(\textbf{x})+\beta_1 k(\textbf{x};\theta_1)$

$F_2(\textbf{x})$부터 위의 과정을 똑같이 반복하면 된다.

## Convergence of Boosting
시간이 없어서 설명은 나중에 정리하고자 한다 :)
- 결론은 Boosting 알고리즘을 iteration이 반복(weak model을 많~이 만들면)한다면 train error가 0에 수렴한다.