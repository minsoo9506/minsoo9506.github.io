# [Generative Model] GAN


GAN의 논문과 youtube에서 본 내용을 간단히 정리하였다.

<!--more-->

## Main problem and solution
- problem
  - complex/high dimensional training distribution에서 sampling하고 싶다
  - 근데 어렵다
- solution
  - Gan에서는 2-step으로 이 문제를 해결
  - sample from a simple distribution
  - learn a transformation(by NN) to the training distribution

## GAN 
- do not work with any explicit density function
  - take a game-theoreric approach

- generator network
  - directly produces samples $\tilde{\textbf{x}} = G(\textbf{z};\theta_g)$
  - $\textbf{z}$는 random noise
- discriminator network
  - train data와 generator가 만든 sample을 구분

## Training objective function

$$\min_{\theta_G} \max_{\theta_D} [E_{x\sim p_{data}}\log D_{\theta_d}(x)+E_{z\sim p(z)}\log (1-D_{\theta_d}(G_{\theta_G}(z)))]$$

- 실제 학습을 진행할 때는 두 네트워크를 동시에 학습시키지 않고 따로따로 업데이트를 한다. D를 학습시킬 때는 G를 고정한 상태에서, G를 학습시킬 때는 D를 고정한 상태에서 진행한다. 먼저, D를 학습하기 위해서는 G의 parameter를 고정한 상태에서 batch size의 수만큼의 data를 넣어서 V를 높이는 방향으로 parameter를 업데이트한다.

- discriminator : maximize $J_D$ wrt $\theta_d$

$$J_D = E_{x\sim p_{data}}\log D_{\theta_d}(x)+E_{z\sim p(z)}\log (1-D_{\theta_D}(G_{\theta_g}(z)))$$
$$ \approx\frac{1}{m}\sum_{i=1}^m\log D_{\theta_D}(x_i)+\frac{1}{m}\sum_{i=1}^m\log (1-D_{\theta_d}(G_{\theta_g}(z_i)))$$ 

- generator : minimize $J_G$ wrt $\theta_g$

$$J_G =E_{z\sim p(z)}\log (1-D_{\theta_d}(G_{\theta_g}(z)))\\ \approx \frac{1}{m}\sum_{i=1}^m\log (1-D_{\theta_d}(G_{\theta_g}(z_i)))$$ 

- gradient ascent on discriminator, gradient descent on generator
- $\log (1-D(G(z)))$의 경우 $D(G(z))=0$에서 기울기가 거의 0 이다. 반대로 $D(G(z))=1$에서는 기울기가 크다. 즉 fake에 가까울수록 gradient가 커서 학습이 잘되야 하는데 반대의 경우가 되버리는 것이다. 이러면 특히 학습초반에 fake image가 잘 만들어지지 않을 때 문제가 생기게 되는 것이다.
    - 그래서 generator의 objective function을 바꾼다. 이제 generator도 gradient ascent가 된다.

$$\max_{\theta_G}E_{z\sim p(z)}\log (D_{\theta_D}(G_{\theta_g}(z)))$$

## Each gradient update
1. discriminator update (repeat k times per iteration)
   - discriminator가 generator보다 앞서서 학습해야 더 잘된다고 함
   - real data m개, fake data m개 -> 총 2m개
   - gradient ascent
2. generator update(once per iteration)
    - backward gradient from discriminator output
    - but update $\theta_G$ only

## Performance evaluation
- entropy
  - high entropy = high randomness, less predictable
- GAN이 원하는 결과는?
  - quality
    - low entropy (high predictable) conditional label distribution $p(y\|x)$
    - 어떤 label인지 바로 맞출수있게!
  - diversity
    - high entropy (less predictable) $p(y)=\int p(y\|x=G(z))dz$
    - 다양한 data가 generate가 가능하게!

- Inception score
  - 높은수록 좋다.
  - quality와 diversity 둘 다 고려
$$IS(G)=\exp (E_{x\sim G} [D_{KL}(p(y|x)|| p(y))])$$

- FID
    - 낮을수록 좋다.
$$FID(x_r, x_f) = ||\mu_r - \mu_f ||^2 + Tr(\Sigma_r + \Sigma_f - 2(\Sigma_r \Sigma_f)^{1/2})$$

- precision, recall, F1 score 

## GAN의 발전
- depth and convolution
  - **DCGAN** (2015) : deep하고 convnet사용
- class-conditional generation
  - **Conditional GAN** (2014) : 진짜같지 않으니 label을 알려주자.
  - **AC-GAN*** (2016) : ensemble of specialized classifiers, 100개의 generator를 만들어서 각 10개의 label을 만들었다.
- spectral normalization
  - **SN-GAN** (2018) : 하나의 generator로 모든 label을 만들었다, spectral normalization 
  - Spectral normalization
    - 각 layer의 weight들을 SVD해서 가장 큰 singular value로 weight들을 나눠준다.
    - 그렇게하면 learning이 상당히 stable해진다. 그래서 GAN을 죽이지 않고 오래동안 학습이 가능해졌다고 한다.
- two-timescale update rule
  - **TTUR (Two-timescale update rule)** : 이론적으로 generator의 learning rate가 discriminator 것보다 더 빠르게 줄어들면 convergence에 도움을 된다는 것을 밝힘, 실제로는 그냥 서로 다른 learning rate를 쓰면 충분하다고 한다.
- self-attention
  - **non-local neural nets** (2017) : self-attention과 non-local filtering operation을 연결시킴
  - **SAGAN** (2018) : non-local neural net을 layer로 사용, generation을 할 때 symmetric을 이용하자(eg, 눈), unusually shaped도 잘 만드려고 했다

## Challenge
- Mode collapse (sample의 variety 부족)
- Gan은 VAE보다 Sharp한 결과를 만듬  

왜 이런 결과가 나오는 걸까? 중명은 생략하고 결과만 보자. 
- $p:p_{data},q:p_{model}$이라고 가정하자.
- $D_{KL}(p\|\|q)$
  - name : maximum likelihood
  - key property : mean-seeking
  - high $q(x)$ anywhere $p(x)$ is high
  - lower quality but more diverse samples
- $D_{KL}(q\|\|p)$
  - name : reverse KL
  - key property : mode-seeking
  - rarely high $q(x)$ anywhere $p(x)$ is low
  - higher quality but less diverse samples

그렇다면 GAN은?
- Jensen-Shannon divergence
  - symmetric
  - penalizes poor images badly : revese KL과 비슷

$$D_{JS}(p||q)=\frac{1}{2}D_{KL}(p||\frac{p+q}{2})+\frac{1}{2}D_{KL}(q||\frac{p+q}{2})$$

- GAN : optimizing generator = minimizing JS divergence
  - 2014 GAN 논문에서 discriminator가 optimal한 상태라면 generator의 objective function은 아래처럼 JS-divergence를 최소화하는 것이다라고 했다.

$$J(D^*, G)=2D_{JS}(p_{data}|| p_{model})-\log 4$$

- 최적의 D가 전제되었다면 gan의 목적함수를 최적화하는 것은 $p_{data}$와 $p_{g}$사이의 Jensen-Shannon divergence를 최소화하는 것과 같다. 즉, data의 분포와 G가 생성하는 분포 사이의 차이를 줄인다고 할 수 있다. 따라서 최적의 결과는 $p_{data}=p_{g}$이다. 

$$\min_{G}V(D^* , G)=E_{x\sim p_{data}(x)}[\log D^* (\textbf{x})]+E_{z\sim p_{g}(z)}\[\log (1-D^*(\textbf{x})) \]$$

$$=E_{x\sim p_{data}(x)}\left[\log\frac{P_{data}(\textbf{x})}{P_{data}(\textbf{x})+P_{g}(\textbf{x})}\right]+E_{x\sim p_{g}(z)}\left[\log\frac{P_{g}(\textbf{x})}{P_{data}(\textbf{x})+P_{g}(\textbf{x})}\right]$$

$$=\int_{x}p_{data}(x)\log\left[\frac{P_{data}(\textbf{x})}{P_{data}(\textbf{x})+P_{g}(\textbf{x})}\right]dx+\int_{x}p_{g}(x)\log\left[\frac{P_{g}(\textbf{x})}{P_{data}(\textbf{x})+P_{g}(\textbf{x})}\right]dx$$

$$=-\log4+\int_{x}p_{data}(x)\log\left[\frac{p_{data}(x)}{\frac{p_{data}(x)+p_{g}(x)}{2}}\right]dx+\int_{x}p_{g}(x)\log\left[\frac{P_{g}(\textbf{x})}{\frac{P_{data}(\textbf{x})+P_{g}(\textbf{x})}{2}}\right]dx$$

$$=-\log4+KL\left(p_{data}(x)||\frac{p_{data}(x)+p_{g}(x)}{2}\right)+KL\left(p_{g}(x)||\frac{p_{data}(x)+p_{g}(x)}{2}\right)$$

$$=-\log4+2*JS\left(p_{data}(x)||p_{g}(x)\right)\\; JS(\cdot)=0\;\text{if}\;p_{data}=P_{g}$$

그래서 이전에는 GAN의 sharp/real 한 이미지를 만드는 이유가 reverse KL 과 비슷한 JS divergence때문이라고 생각했다. 근데 최근에는 해당하는 이유는 아니라고 밝혀졌다.
- GAN을 원래와 다른 $D_{KL}$로 학습해봤더니 그래도 비슷한 결과가 나오더라.
- reverse KL이 특정한 mode를 선호한다는 근거가 부족하다.
- mode collapsing은 divergence보다는 training의 어려움 때문에 발생할 것이다.
- generate한 image가 sharp한 이유는 아직까지도 not clear 하다.

## GAN training challenges
- non-convergence
  - 두 model을 동시에 훈련하다보니까 문제 발생
- mode collapse
  - 서로 다른 input을 generator에 넣었음에도 동일한 output이 나오는 문제 발생
  - maxmin solution과 minmax solution이 달라서가 아닌가? 라는 의문 존재
  - 이를 해결하기 위한 접근
    - augmented/different objective function
      - unrolled GAN, DRAGAN, EBGAN
    - architecture chage
      - MAD-GAN, MRGAN
    - minibatch discrimination
      - progressive GAN
- diminished gradient
  - discriminator가 너무 성능이 좋아지면 generator gradient vanishing이 발생한다고 한다.
- generator/discriminator unbalance
  - cause overfitting
- hyperparmeter sensitivity

## Tips and Tricks
- train with labels
- one-sided label smoothing
  - real label을 $1-\epsilon$으로 바꿔서 regularization
- virtual batch normalization
  - GAN에서는 BN을 사용하지 않는다.
  - 그래서 다른 trick으로 BN사용
- balancing generator/discriminator
  - D가 살짝 앞서가도록 한다.
  - D가 G보다 layers/filter를 더 많이 갖게 한다.
  - 근데 너무 D가 앞서가면 안된다.

## Advantages and disadvantages
논문에서 말하는 장단점은 아래와 같다.

- advantage
  -  No need of Markov chain only backprop with gradient.
  -  No need of inference during training.
  -  Wide variety of functions can be incorporated into the model.
- disadvantage
  - There is no explicit rep[resentation of $p_{g}(\textbf{x})$.
  - D must be synchronized well with G during training.
