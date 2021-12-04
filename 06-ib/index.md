# [Generative Model] Deep Variational Information Bottelneck (ICLR 2017)


Deep Variational Information Bottelneck 논문을 간단하게 리뷰하였다.

<!--more-->

- *models trained with the VIB objective outperform those that are trained with other forms of regularization, in terms of generalization performance and robustness to adversarial attack*

deep network에 정보이론은 접목시켜보자. 먼저, encoder $p(\boldsymbol{z}\|\boldsymbol{x};\boldsymbol{\theta})$가 있고 우리는 target Y에 대한 정보를 최대한 담고있는 encoder를 만들고자 한다. 이를 mutual information으로 접근해보자. 아래의 식을 최대화하는 것이 목표이다.

$$I(Z,Y;\theta)=\int dxdyp(z,y|\theta)\log\frac{p(z,y|\theta)}{p(z|\theta)p(y|\theta)}$$

그런데 아무런 제약식이 없으면 위의 식은 Z=X일 때 최대가 된다. 따라서 제약식 $I(X,Z)\le I_{c}$라는 제약식을 넣어으면 최종 objective는 아래의 식이 된다.

$$\max_{\theta}I(Z,Y;\theta)-\beta I(Z,X;\theta)$$

결국 우리는 Y에 대해 최대한 expressive하고 동시에 X에 대해 compressive한 encoding Z를 구하는 것이 목표인 것이다. 이런 접근을 information bottleneck (IB) 이라고 한다. 이제 이를 최적화하는 과정을 수식으로 살펴보자.

- Assume : 

$$p(X,Y,Z)	=p(Z|X,Y)p(Y|X)p(X)=p(Z|X)p(Y|X)p(X)\\\ \rightarrow p(Z|X,Y)=p(Z|X)$$

- $I(Z,Y) :$

$$I(Z,Y) =\int dydzp(y,z)\log\frac{p(y,z)}{p(y)p(z)}\\\
	=\int dydzp(y,z)\log\frac{p(y|z)}{p(y)}p(y|z)\\\
    =\int dxp(x,y|z)
	=\int dx\frac{p(z|x)p(y|x)p(x)}{p(z)}$$
    
$$q(y|z)\text{ : variational approximation to } p(y|z)$$

$$KL\left[p(Y|Z),q(Y|Z)\right] =\int dyp(y|z)\log\frac{p(y|z)}{q(y|z)}\ge 0 \\\ \rightarrow	\int dyp(y|z)\log p(y|z) \ge\int dyp(y|z)\log q(y|z)$$

$$I(Z,Y)	\ge\int dydzp(y,z)\log\frac{q(y|z)}{p(y)}\\\
	=\int dydzp(y,z)\log q(y|z)-\int dyp(y)\log p(y)\\\
	=\int dydzp(y,z)\log q(y|z)+H(Y)$$
    
$$\therefore I(Z,Y)	\ge\int dxdydzp(x,y,z)\log q(y|z)\\\
	=\int dxdydzp(z|x)p(y|x)p(x)\log q(y|z)$$

- $\beta I(Z,X) :$

$$I(Z,X)=\int dzdxp(x,z)\log\frac{p(z|x)}{p(z)}\\\
	=\int dzdxp(x,z)\log p(z|x)-\int dzp(z)\log(z)$$

$$r(x)\text{ : variational approximation to }p(z)$$

$$KL\left[p(Z),r(Z)\right]	=\int dzp(z)\log\frac{p(z)}{r(z)}\ge0 \\\
\rightarrow	\int dzp(z)\log p(z)\ge\int dzp(z)\log r(z)$$

$$\therefore I(Z,X)\le\int dxdzp(x)p(z|x)\log\frac{p(z|x)}{r(z)}$$

• 위에서 구한 결과를 통해

$$I(Z,Y)-\beta I(Z,X) \ge L \\\ = \int dxdydzp(z|x)p(y|x)p(x)\log q(y|z) - \beta\int dxdzp(x)p(z|x)\log\frac{p(z|x)}{r(z)}\\\ \approx\frac{1}{N}\sum_{n=1}^{N}\left[\int dz\\{ p(z|x_{n})\log q(y_{n}|z)-\beta p(z|x_{n})\log\frac{p(z|x_{n})}{r(z)}\\} \right]$$
    
$$\text{encoder of the form } p(z|x)=N(z|f_{e}^{\mu}(x),f_{e}^{\Sigma}(x))\rightarrow \text{ reparameterization trick}$$ 

$$J_{IB}=\frac{1}{N}\sum_{n=1}^{N}E_{\epsilon\sim p(\epsilon)}\left[-\log q(y_{n}|f(x_{n},\epsilon))\right]+\beta KL\left[p(Z|x_{n}),r(Z)\right]$$

지금까지 Deep network를 이용하여 IB를 최적화과정을 알아보았다. 

위의 결과(단 위의 과정은 label을 이용하는 supervised learning)와 과정을 보다보면 VAE 모델과 유사한 부분이 많음을 알 수 있고 논문에서도 둘의 관계를 설명하고 있다. VAE는 generative model이기에 Y 대신에 X를 넣어서 전개하면 된다.

$$I(Z,X)	=\int dxdzp(x,z)\log\frac{p(x,z)}{p(x)p(z)}\\\
	=\int dxdzp(x,z)\log\frac{p(x|z)}{p(x)}\\\
	=\int dzp(z)\int dxp(x|z)\log p(x|z)+H(x)\\\
	\ge\int dzp(z)\int dxp(x|z)\log q(x|z)\\\
	=\int dxp(x)\int dzp(z|x)\log q(x|z)I(Z,i)\\\
    =\frac{1}{N}\sum_{i}\int dzp(z|x_{i})\log\frac{p(z|x_{i})}{p(z)}
	\le\frac{1}{N}\sum_{i}\int dzp(z|x_{i})\log\frac{p(z|x_{i})}{r(z)}$$
    
$$\therefore I(Z,X)-\beta I(Z,i)\le\int dxp(x)\int dzp(z|x)\log q(x|z)-\beta\frac{1}{N}\sum_{i}KL\left[p(Z|x_{i}),r(Z)\right]$$

이는 VAE의 형태이다. 수식은 비슷해보이지만 해석적인 부분에서 차이는 존재한다. VAE는 encoder $p(z\|x)$부분에서 $q(z\|x)$으로 variational approximation하지만 IB에서는 decoder $p(x\|z)$부분을 approximation한다.
