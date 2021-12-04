# [인과추론] Randomized Experiments and Identification


Randomized Experiment에 대해 알아보자.

<!--more-->

## Randomized Experiment

언제나 우리가 관측하지 못한 confounder가 있다. 이를 conditioning하지 못할 것이다. 따라서 RCT (Randomized control trial)이 아주 강력한 것이다. 기업에서 많이들 A/B test를 하는데 이게 RCT이다. treatment, control group을 random하게 나누는 것이다. RCT를 통해 association이 causation이 되는데 이를 각기 다른 3가지 측면에서 살펴보자.

- covariate balance

$$P(X|T=1) = P(X|T=0)$$

- Randomization implies covariate balance
  - treatment를 random하게 적용하기 때문에 covariate $X$들과 독립이다. 이 때 covariate들이 observed or unobserved 인지 상관없이 독립적이다.

$$P(X|T=1) = P(X|T=0) = P(X)$$

- Covariate balance implies association is causation

$$P(y|do(t)) = \sum_x P(y|t,x)P(x) \\\ = \sum_x \frac{P(y|t,x)P(t|x)P(x)}{P(t|x)}\\ = \sum_x \frac{P(y,t,x)}{P(t|x)} \\\ = \sum_x \frac{P(y,t,x)}{P(t)} \\\ = \sum_x P(y,x|t)\\ =P(y|t)$$

- Exchangeability
  - RCT를 exchangeability의 측면에서 살펴보자. RCT를 하면 exchangeability가 성립할 수 밖에 없다. 동전을 던져서 treatment를 할지 말지 결정하기 때문에 당연히 아래의 식이 성립하고 association이 causation이 되는 것이다.

$$E[Y(1)|T=1] = E[Y(1)|T=0] \\ E[Y(0)|T=0] = E[Y(0)|T=1]$$

$$E[Y(1)]-E[Y(0)] = E[Y(1)|T=1] - E[Y(0)|T=0] \\\ =E[Y|T=1] = E[Y|T=0]$$

- No backdoor paths
  - graphical causal model에서도 RCT를 통해 association이 causation이 된다. $T$의 incoming edges가 사라지기 때문이다. 따라서 backdoor path들도 사라지고 association이 causal이 되는 것이다.

## Frontdoor adjustment
아래의 그림처럼 $W$가 unobserved인 경우 backdoor path를 막을 수 없고 따라서 causal association도 구할 수 없게 된다. 그러면 어떻게 할까? Frontdoor adjustment!

<center>
    <img src="https://github.com/minsoo9506/blog/blob/master/static/blog-imgs/Lec_05_01.PNG?raw=true"  width="300">
</center>

위의 그림에서 mediator $M$에 집중하면 된다. 과정은 아래와 같다.

1. Identify the causal effect of $T$ on $M$
2. Identify the causal effect of $M$ on $Y$
3. Combine the above steps to identify the causal effect of $T$ on $Y$

step1에서 $Y$는 $T-M$ path through $W$에 대해 collider이고 이는 backdoor path를 막아준다. 따라서 우리는 아래와 같은 identification이 성립함을 알 수 있다.

$$P(m|do(t)) = P(m|t)$$

step2에서 $T$를 conditioning하면 backdoor path $M \leftarrow T \leftarrow W \rightarrow Y$를 막을 수 있다.

$$P(y|do(m)) = \sum_t P(y|m,t)P(t)$$

이제 위에서 알게된 내용을 합쳐서 $T$의 변화가 $Y$를 얼마나 변하게 하는지 알 수 있게 된다.

$$P(y|do(t))=\sum_m P(m|do(t))P(y|do(m))$$

- proof

위의 사진으로 진행한다. truncated factorization으로

$$P(w,m,y|do(t))=P(w)P(m|t)P(y|w,m)$$

$w,m$에 대해 marginalize하면

$$\sum_m \sum_w P(w,m,y|do(t)) = \sum_m \sum_w P(w)P(m|t)P(y|w,m) \\\ P(y|do(t)) = \sum_m P(m|t) \sum_w P(w)P(y|w,m) $$

그런데 우항에 unobserved $w$가 있기에 marginalization을 진행할 수 없다. 따라서 우리는 $w$를 식에서 없애는 작업이 필요할 것이다.

그림에서 $T$가 주어진다면 $P(w\|t)=P(w\|t,m)$이므로

$$P(y|do(t)) = \sum_m P(m|t) \sum_w P(w)P(y|w,m) \\\ = \sum_m P(m|t) \sum_w P(y|w,m)\sum_{t'}P(w|t')P(t') \\\ = \sum_m P(m|t) \sum_w P(y|w,m)\sum_{t'}P(w|t',m)P(t') \\\ = \sum_m P(m|t) \sum_{t'}P(t') \sum_w P(y|w,m)P(w|t',m) \\\ = \sum_m P(m|t) \sum_{t'}P(t') \sum_w P(y|w,m,t')P(w|t',m) \\\ = \sum_m P(m|t) \sum_{t'}P(t') \sum_w P(y,w|m,t') \\\ =\sum_m P(m|t) \sum_{t'}P(t')P(y|m,t')$$

- Frontdoor Adjustment
  - 만약 $(T,M,Y)$이 the frontdoor
criterion을 만족하고 positivity를 만족하면

$$P(y|do(t))=\sum_m P(m|t) \sum_{t'}P(y|m,t')P(t')$$

- Frontdoor Criterion
  - 아래의 조건이 사실이면 set of variables $M$들이 $T,Y$에 대해 the
frontdoor criterion을 만족한다.
    - $M$ completely mediates the effect of $T$ on $Y$
    - There is no unblocked backdoor path from $T$ to $M$
    - All backdoor paths from $M$ to $Y$ are blocked by $T$

## Pearl's do-calculus
backdoor adjustment가 성립하지 않더라도 frondoor adjustment라는 방법을 통해 identification이 성립할 수 있음을 알게되었다. 그렇다면 둘다 성립하지 않을 때는 어떻게 해야할까? *do-calculus* ! do-calculus는 causal graph에 녹아있는 causal assumptions을 사용하여 causal effects를 identify할 수 있는 tool을 제공한다. 

- notation
  - $G$ : causal graph
  - $G_{\overline{X}}$ : the graph that remove all of the incoming edges to nodes in the set $X$
  - $G_{\underline{X}}$ : the graph that remove all of the outgoing edges to nodes in the set $X$

### Rules of *do*-calculus
Given a causal graph $G$, an associated distribution $P$, and disjoint sets of vairables $Y,T,Z,W$ the following rules hold:

- Rule1 : if $Y \perp_{G_{\overline{T}}} Z \| T,W$

$$P(y|do(t),z,w)=P(y|do(t),w)$$

- Rule2 : if $Y \perp_{G_{\overline{T}, \underline{Z}}} Z \| T,W$

$$P(y|do(t),do(z),w) = P(y|do(t),z,w)$$

- Rule3 : if $Y \perp_{G_{\overline{T}, \overline{Z(W)}}} Z \| T,W$
  - $Z(W)$ : the set of nodes of $Z$ that aren't ancestors of any node of $W$

$$P(y|do(t),do(z),w) = P(y|do(t),w)$$

이에 대한 증명은 'Pearl (1995) Causal diagrams for empirical research'를 읽어보면 될 것이다. 그렇다면 *do*-calculs만 있으면 causal estimands들이 무조건 identifiable할 수 있을까? 2000년대에 나온 논문들이 이를 증명했다고 한다. 즉, 위의 *3가지 rule들이 sufficient to identify all identifiable causal estimands*라는 것을 의미한다.

위의 identification은 nonparametric identification에 속한다. 추후에 parametric identification에 대해서도 살펴볼 것이다.

## Determining identifiability from the graph
- skip

### Reference
- https://www.youtube.com/watch?v=9X4pR4jvKmM&list=PLoazKTcS0RzZ1SUgeOgc6SWt51gfT80N0&index=5

