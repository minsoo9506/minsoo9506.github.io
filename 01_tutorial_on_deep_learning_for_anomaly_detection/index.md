# [Survey] Deep Learning for Anomaly Detection (WSDM'21 tutorial)


Deep Learning for Anomaly Detection - Challenges, Methods tutorial 정리

<!--more-->

- Anomalies: points that are significantly different from most of the data

# Part1: challenges

### Problem variations
- Binary output vs scoring
- mulitple ways to define what makes an anomaly different 3 common types of anomalies:
  - point anomalies
  - conditional anomalies (contextual anomalies)
  - group anomlies

### application-specifi complexities
- Heterogeneity
  - different anomalies may exhibit different expression
  - anomalies도 다같은 anoamlies가 아니다
- Application-specific methodologies
- Unknown Nature (unsupervised setting)
  - anomalies는 발생하기 전까지 그게 있는지 조차도 모름
- Coverage
  - 모든 anomalies를 모으는 것도 힘들다

### Key Challenges
- Low anomaly detection accuracy
- Contextual and high-dimensional data
- sample-efficient learning
  - building generalized detection models with a limited amount of labeled anomaly data
- Noise-Resilient anomaly detection
- complex anomalies
- anomaly explanation

### Traditional (Shallow) methods and Disadvantages
- statistical/probabilistic-based approaches
  - statistical-test, depth-based, deviation-based
- proximity-based approach
  - distance-based, density-based, clustering-based
- shallow ML models
  - unsupervised ML model (one-class svm, pca)
- others
  - information-theoretic, subspace method

- weakness
  - weak capability of capturing intricate relationships
  - lots of hand-crafting of algorithms and features
  - Ad hoc nature make it difficult to incorporate supervision

### Advantages of Deep Learning
- Integrates feature learning and anomaly scoring
  - generates newly learned feature space
  - end-to-end learning
  - diverse neural architectures
  - unified detection and localization of anomalies
    - localization을 통해 anomalies에 대한 해석을 더 쉽게 할 수 있다
  - anomaly-informed models with improved accuracy

### 3 principal categories
- Deep learning for Feature Extraction
  - DL을 이용해서 feature를 뽑아내고 이를 다른 모델에 넣어서 찾는 방법
- Learning Feature Representations of Normality (가장 많이 연구됨)
- End-to-End Anomaly Score Learning

### Categorization Based on Supervision
- Unsupervised approach
  - anomaly-contaminated unlabeled data; no manually labeled training data
- Semi-supervised approach (가장 많이 연구됨)
  - assuming the availability of a set of manually labeled normal training data
- Weakly-supervised approach
  - assuming have some labels for anomaly classes
  - yet the class labels are partial, inexact, inaccurate

# Part2-1: methods (The modeling perspective) 

### Deep learning for feature extraction
- assumption
  - extracted features preserve the discriminative information that helps separate anomalies from normal instances
- 방법
  - pre-trained model을 사용
    - pre-trained model에서 feature를 추출한 뒤 다른 classifier를 학습시켜서 anomaly score를 구한다
    - ex) (paper) unmasking the abnormal events in video
  - training deep feature extraction models
    - 주로 autoencoder를 이용해서 feature extract한 뒤에 다른 분퓨 모델을 이용한다
#### summary
- 장점
  - 구현하기 쉽다
  - linear model보다 dimensionaliy reduction이 성능이 좋다
  - 다양한 sota 모델을 활용할 수 있다
- 단점
  - feature extraction이 anomaly soring이 disjointing한 과정이라서 유용한 정보가 추출되지 않을수도 있다
  - pre-trained model은 데이터의 종류가 제한적이다

### Learning feature representation of normality
크게 두가지로 구분 할 수 있다
- Generic normality feature learning
- Anomaly measure-dependent feature learning

#### Generic normality feature learning
- Autoencoders
  - assumption
    - normal instances can be better reconstructed from compressed feature space than anomalies
  - gerneral framewok
    - 1. Bottleneck architecture + reconstruction loss
    - 2. The larger reconstruction errors the more abnormal
- GAN
  - assumption
    - Normal data instances can be better generated than anomalies from the latent feature space of the generative network in GANs
  - general framework
    - 1. Train a GAN-based model
    - 2. Calculate anomaly scores by looking into the difference bewteen an input instance and its counterpart generated from the latent space of the generator
  - 종류
    - AnoGAN, EBGAN ...
- Predictability modeling
  - assumption
    - Normal instances are temporally more predictable than anomalies
  - general framework
    - 1. Train a current/future instance prediction network
    - 2. Calculate the difference between the predicted instance and the actual instance as anomaly score
  - 종류
    - Future frame prediction
- Self-supervised classification
  - assumption
    - Normal instances are more consistent to self-supervised classifiers than anomalies
  - general framework
    - 1. Apply different augmentation operations to the data
    - 2. Learn a multi-class classification model using instances
    - 3. Calculate the inconsistency of the instance to the model as anomaly score

#### summary    
- 장점
  - Deep learning for feature extraction 보다 효율적이다
  - 다양한 모델을 활용할 수 있다
- 단점
  - GAN 같은 경우 훈련이 쉽지 않다
  - unsupervised setting 이기 떄문에 anomaly contamination에 취약하다
    
#### Anomaly measure-dependent feature learning
- Distance-based measures
  - assumption
    - Anomalies are distributed far from their closest neighbors while normal instances are located in dense neighborhoods
  - general framework
    - 1. orginal data를 새로운 representation space로 map하는 feature mapping function $\pi$를 만든다
    - 2. feature representation을 anomalies가 특정 reference instances와 거리가 더 커지도록 optimize한다.
    - 3. 그렇게 만들어진 space에서 거리를 측정하여 anomaly score로 이용한다
  - 종류
    - REPEN
- One-class classification measure
  - assumption
    - All normal instances come from a single (abstract) class andn can be summarized by a compact model, to which anomalies do not conform
  - general framework
    - 1. orginal data를 새로운 representation space로 map하는 feature mapping function $\pi$를 만든다
    - 2. one-class classification loss를 이용하여 feature representation을 optimize한다
    - 3. 그렇게 만들어진 space에서 one-class classification model을 통해 anomaly score를 구한다
  - 종류
    - Deep SVDD
- Cluster-based measure
  - assumption
    - Normal isntances have stronger adherence to clusters than anomalies
  - general framework
    - 1. orginal data를 새로운 representation space로 map하는 feature mapping function $\pi$를 만든다
    - 2. cluster-based loss를 이용하여 feature representation을 optimize한다
    - 3. 그렇게 만들어진 space에서 cluster-based model을 통해 anomaly score를 구한다
  - 종류
    - DAGMM
#### summary
- 장점
  - 전통적인 방법론들이라 비교적 연구가 탄탄하다
  - 특정 anomaly measure를 기준으로 잡고 representation을 만들기 때문에 해당 measure에 알맞는 data를 만나면 효과가 좋다
- 단점
  - 성능이 anomaly measure에 heavily dependent하다
  - clustering 과정에 있어서 contaminated anomalies가 training data에 있는 경우 biased 될 수 있다

### End-to-end anomaly score learning
- anomaly score가 있는 상태에서 학습을 하는 (지도학습) 방법이다
- 크게 4가지로 구분한다
  - Ranking models
  - Prior-driven models
  - Softmax likelihood models
  - End-to-End one-class classfication

#### Ranking models
- assumption
  - There exists an observable ordinal variable that captures some data abnormality
- general framework
  - 1. Definen the (synthtic) ordinal variable
  - 2. Use the variable to define a surrogate loss functions for anomaly ranking and train the detection model
  - 3. Given a test instance, the model firectly gives its anomaly score
- 종류
  - SDOR(Deep ordinal regression), PReNet(Pairwise relation prediction)

#### Prior-driven models
- assumption
  - The imposed prior captures the underlying (ab)normality of the dataset
- general framework
  - 1. Impose a prior over the weight parameters of a network-based anomaly scoring measure, or over the expected anomaly scores
  - 2. Optimize the anomaly ranking/classification with the prior
  - 3. Given a test instance, the model directly gives its anomaly score
- 종류
  - DevNet

#### Sotfmax likelihood models
- assumption
  - Anomalies and normal instances are respectively low- and high-probability events
- general framework
  - 1. The probability of an event is modeled using a softmax function $p(x;\theta) = \frac{\exp (\tau(x;\theta))}{\sum_x \exp (\tau(x;\theta))}$
  - 2. The parameters are then learned by a maximum likelihood function
  - 3. Given a test instance, the model directly gives its anomaly score by the event probability
- 종류
  - APE

#### End-to-End one-class classification
- assumption
  - Data instances that are approximated to anomalies can be effectively synthesized
  - All normal instances can be summarized by a discriminative one-class model
- general framework
  - 1. Generate artificial outliers
  - 2. Train a GAN to discriminate whether a given instance is normal or an artificial outlier
- 종류
  - Fence GAN, OCAN

#### summary
- 장점
  - anomaly scoring/ranking/classification의 과정이 end-to-end로 이뤄지기에 더 효율적일 수 있다
  - anomly measures에 depend하지 않는다
- 단점
  - 어느정도의 labeled/synthetic anomalies가 필요하다
  - unseen anomalies에 대해서 성능이 떨어질 수 있다

# Part2-2: methods (The supervision information perspective)
### Unsupervised approach
- Training on anomaly-contaminated unlabeled data
- 종류
  - outlier-aware autoencoders
    - robust deep autoencoders
  - one-class method
    - Deep SVDD
  - pseudo labeling
    - Deep distance-based method
    - Deep ordinal regressioin
  - augmented deep clustering
    - DAGMM
### Weakly-supervised approach
- A limited number of partially labeled anomalies and large unlabeled data
- 종류
  - Contrastive feature learning
    - Deep distance-based method
  - Prior-driven method
    - Deviation network
  - Surrogate learning
    - Pairwise relation prediction
  - Multiple instance learning
### Semi-supervised approach
- Training on a large labeled normal dataset

# Part3: Conclusions and future opportunities
## six possible directions for future research
### 1. Exploring anomaly-supervisory signals
### 2. Deep weakly-supervised anomaly detection
### 3. Large-scale normality learning
### 4. Deep detection of complex anomalies
- deep models for conditional/group anomalies
- multimodal anomaly detection
### 5. Interpretable and actionable deep anomaly detection
- Interpretable deep anomaly detection
- Quantifyiing the impact of detected anomalies and mitigation actions
### 6. Novel applications and settings

## Reference
- WSDM 2021 Tutorial on Deep Learning for Anomaly Detection
