# [Generative Model] LSGAN


LSGAN 논문을 간단하게 리뷰하였다.

<!--more-->

## Least Squares Generative Adversarial Networks (2017)

GAN과 거의 똑같은 형태를 갖는다. 달라진 점은 loss function! 이라고 할 수 있다. 이를 통해 (vanilla) GAN이 갖고 있는 문제점을 어느 정도 해결하였고 더 좋은 성능을 보였다고 한다.

- GAN은 disciminator에서 sigmoid cross entropy loss function을 사용한다. 이는 fake data가 discriminator에 의해 real로 판단된 경우 문제가 생긴다. fake data가 정말 real해서 판단되면 상관이 없지만 real 차이가 많이 나는 경우에도 불구하고 real이라고 discriminator가 판단하는 경우가 있는 것이다. 이렇게 되면 generator를 학습할 때 해당하는 data를 통해 학습할 수 없게 된다. (gradient vanishing)

그래서 LSGAN은 위와 같은 data도 학습에 사용된다면 더 좋은 결과가 나오지 않을까? 라는 생각을 했다. 이를 위해 새로운 loss function을 제안한 것이다.

### LSGAN
- $a,b$는 각각 fake, real data labels
- $c$는 the value that $G$ wants to $D$ to believe for fake data

$$\min_D V(D)=\frac{1}{2}E_{x\sim p_{data}(x)}[(D(x)-b)^2]+\frac{1}{2}E_{z\sim p(z)}[(D(G(z))-a)^2]$$

$$\min_G V(G)=\frac{1}{2}E_{z\sim p(z)}[(D(G(z))-c)^2]$$

위와 같이 loss function을 바꾸면 LSGAN의 원하는대로 더 학습을 잘 시킬 수 있게 된다.

### Benefits of LSGANs
- 위에서 설명한대로 이미 잘 속인 경우에도 penalty를 주어서 최대한 real과 비슷하게 만들도록 한다. generator를 학습시킬 때, discriminator는 fixed시킨다. 즉, decision boundary가 고정된 상태에서 decision boundary쪽으로 data가 최대한 가까워지도록 만드는 것이다. 이 의미는 결국 최대한 긴가민가한 data를 만들겠다는 의도로 이해할 수 있다.
- (위의 같은 이유 덕에) 덤으로 LSGAN은 학습과정이 더 stable하다.

### Relation to f-divergence
- optimal discriminator $D$ for a fixed $G$ : 

$$D^* (x)=\frac{bp_d (x)+ap_g(x)}{p_d(x)+p_g(x)}$$

- Peason $\chi^2$ divergence
  - if we set $b-c=1,\\;b-a=2$
  - $\min_D V(G)=\frac{1}{2}E_{x\sim p_{data}(x)}[(D(x)-c)^2]+\frac{1}{2}E_{z\sim p(z)}[(D(G(z))-c)^2]$ : 이를 최소화하는 것은 Peason $\chi^2$ divergence between $p_d + p_g$ and $2p_g$ 를 최소화하는 것과 같다.

$$2C(G)=E_{x\sim p_{data}(x)}[(D^* (x)-c)^2]+E_{z\sim p(z)}[(D^* (G(z))-c)^2]$$

$$ =\chi^2 \text{ Pearson} (p_d + p_g || 2p_g)$$

