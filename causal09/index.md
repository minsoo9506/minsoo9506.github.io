# [인과추론] Difference-in-Difference


DID 방법론에 대해 알아보자.

<!--more-->
## ATT
이전에 ATE를 위해 가정했던 것과는 다르게

$$Y(0) \perp T$$

이것만 가정하면 (ATE보다 약한 가정) 우리는 *average treatment effect on the treated* (ATT)를 구할 수 있다.

- ATT

$$E[Y(1)-Y(0)|T=1]$$

$$E[Y(1)-Y(0)|T=1] = E[Y(1)|T=1] - E[Y(0)|T=1] \\\ = E[Y|T=1] - E[Y(0)|T=1] \\\ = E[Y|T=1] - E[Y(0)|T=0] \\\ = E[Y|T=1] - E[Y|T=0]$$

## Introducing Time
지금부터는 time(시간)을 추가해서 고민해보자. 지금까지 동일하게 treatment, control group이 있다. 특정 시간 이후에 treatment group에게만 treatment가 행해진다. 특정 시간 ($\tau$)에서 treatment $t$가 행해진 potential outcome을 나타나는 random variable은 $Y_{\tau}(t)$로 표시하자. 시간을 고려하면서 우리가 알고 싶은 것은 treatment group에 treatment가 적용된 causal estimand이다 : 

$$E[Y_1 (1) - Y_1 (0) | T=1]$$

이제 이 값을 identification하고 estimation하는 방법까지 알아보자.

## Identification

### Assumptions
- Consistency (extended to Time)

$$\forall \tau , \; T=t \Rightarrow Y_{\tau} = Y_{\tau}(t)$$

consistency 가정으로 causal estimand $E[T_{\tau}(1)|T=1]$와 statistical estimand $E[T_{\tau}|T=1]$가 같다고 할 수 있다.

- Parallel Trends
  - treatment group이 treatment를 받지 않았을 때, 시간에 따른 trend가 control group과 같다
  - 해당하는 assumption이 있어야 treat를 받지 않은 treatment의 값(counterfactual)을 알 수 있다.

$$E[Y_1 (0) - Y_0 (0) | T=1] = E[Y_1 (0) - Y_0 (0)|T=0]$$
$$(Y_1(0) - Y_0(0)) \perp T$$

- No Pretreatment Effect
  - treatment가 이행되기 전에는 treatment group에 영향을 주지 않는다

$$E[Y_0 (1) - Y_0 (0) | T=1] = 0$$

### Result and Proof
- DID identification
  - Given consistency,
parallel trends, and no pretreatment effect, we have the following:

$$E[Y_1 (1) - Y_1 (0) | T=1] \\\ = (E[Y_1|T=1]-E[Y_0 | T=1]) - (E[Y_1|T=0] - E[Y_0|T=0])$$

- (proof)

$$E[Y_1 (1) - Y_1 (0) | T=1] = E[Y_1 (1) | T=1] - E[Y_1 (0) | T=1] \\\ =E[Y_1 | T=1] - E[Y_1 (0) | T=1] \\; \text{(consistency)}$$

위의 식에서 두 번째 항 $E[Y_1 (0) \| T=1]$은 counterfactual이고 이를 알 수 없지만 parallel trends assumption으로 구할 수 있다.

$$E[Y_1 (0) | T=1] = E[Y_0 (0) | T=1] + E[Y_1 (0)|T=0] - E[Y_0 (0) | T=0]$$

consistency를 이용하면

$$= E[Y_0 (0) | T=1] + E[Y_1 |T=0] - E[Y_0 | T=0] $$

그런데 정리한 식에서도 counterfactual이 존재한다. 이제 pretreatment effect assumption을 이용하면

$$=E[Y_0 (1) | T=1] + E[Y_1 |T=0] - E[Y_0 | T=0]$$

다시 consistency를 이용하면

$$=E[Y_0 | T=1] + E[Y_1 |T=0] - E[Y_0 | T=0]$$

드디어 $E[Y_1 (0) \| T=1]$를 identify했다. 이제 식을 정리하면 최종 결과가 나온다.

<center>
    <img src="https://github.com/minsoo9506/blog/blob/master/static/blog-imgs/Lec_09_01.PNG?raw=true"  width="300">
</center>

## Problem
parallel trends assumption이 깨질 수 있다. confounder때문이라면 controlled parallel trends assumption으로 어느 정도 해결 할 수 있다.

- Controlled Parallel Trends

$$E[Y_1(0) - Y_0(0)|T=1,W] = E[Y_1(0)-Y_0(0)|T=0,W]$$

그런데 만약 시간과 treatment 사이에 interaction이 있다면 parallel trends를 기대하기 어렵다. 또한 parallel trends assumption은 scale-specific하다. parallel trends가 성립해도 $Y$를 변환시킨다면 trends가 유지된다고 할 수 없다는 의미이다. (parallel trends assumptions isn't nonparametric)

## Reference
- [Brady Neal - Causal Inference](https://www.youtube.com/watch?v=2nDgrNP7XSE&list=PLoazKTcS0RzZ1SUgeOgc6SWt51gfT80N0&index=10)
