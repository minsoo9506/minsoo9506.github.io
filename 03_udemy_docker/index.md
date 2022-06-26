# [Docker] Network


도커의 네트워크에 대해 알아보자.

<!--more-->

## Network

docker에서 네트워크 통신은 3가지로 생각할 수 있다.

1. 컨테이너와 외부의 웹사이트와 통신

- 외부의 웹사이트와 통신은 주소만 잘 맞춰주면 잘 된다. (언어나 라이브러리별로 통신하는 코드만 잘 작성하면 됨)

2. 컨테이너와 Host Machine간의 통신

- 예를 들어, 로컬에 깔려있는 DB와 데이터를 주고 받는 경우
- host machine의 주소에서 `localhost`를 `host.docker.internal`로 바꾸면 잘 된다.

3. 컨테이너 간의 통신

### 컨테이너 간의 통신

컨테이너 간의 통신은 IP주소를 수동으로 찾거나 Network를 이용한다.

- `docker container inspect 컨테이너이름`해서 나오는 정보중에서 `IPAdress`를 찾을 수 있다.
- 해당하는 IP주소를 통신하려고 하는 어플리케이션 컨테이너에 넣으면 (하드코딩) 통신이 가능하다. (사용하는 언어, 라이브러리마다 방법은 달라진다)
- 하지만 예를 들어, 통신하려고 하는 컨테이너의 IP가 바뀌면 매번 코드를 수정해야한다.
- Container Networks (Network)는 `docker run`에서 `--network`를 추가하면 된다.
  - 먼저 network를 생성해야하는데 `docker network creat 네트워크이름`으로 네트워크를 생성한다.
  - `docker run -d --name 컨테이너1 --network 네트워크이름 이미지이름`로 컨테이너를 띄운다.
  - 통신을 하려고 하는 두 개이상의 컨테이너도 위처럼 띄우면 된다. 하지만 위에서 띄운 컨테이너1과 통신을 하기 위해서는 주소는 컨테이너이름(여기서는 컨테이너1)을 주소에 추가하면 된다.
  - 예를 들어,`minsooMongo`라는 컨테이너가 있으면 `mongodb://minsooMongo:27017/minsoo` 이런식으로 주소가 만들어 질 것이다.
- 위의 과정만 잘 지키면 도커가 알아서 다수의 컨테이너끼리 통신을 할 수 있게 해주는 편리한 기능이다.

### 다중 어플리케이션 구축 프로젝트

- 크게 3가지 파트로 이루어져있다.
  - Database(MongoDB), Backend(NodeJS REST API), Frontend(React SPA)
- 해당 웹의 역할은 어떤 목표를 작성하면 이것이 DB에 저장이 되고 웹페이지에 나타난다. 이를 클릭하면 지울 수 있다.
- 또한 Database의 데이터는 컨테이너가 삭제되어도 남아있어야한다. Backend의 로그데이터도 그렇다. 또한 Back, Frontend 모두 코드가 수정되면 live update가 되어야한다.
- [코드](https://github.com/minsoo9506/udemy-docker-practice)

```bash
# backend image build
docker build -t goals-node .

# frontend imaeg build
docker build -t goals-react .

# creat network
docker network creat goals-net

# db 컨테이너 (image download)
docker run \
--name mongodb \
-v data:/data/db \
--rm \
-d \
--network goals-net \
-e MONGO_INITDB_ROOT_USERNAME=root \
-e MONGO_INITDB_ROOT_PASSWORD=secret mongo

# backend 컨테이너
docker run \
--name goals-backend \
-v /home/minsoo/Workspace/docker-practice/backend:/app \
-v logs:/app/logs \
-v /app/node_modules \
-d \
--rm \
-p 80:80 \
--network goals-net \
goals-node

# frontend 컨테이너
docker run \
--name goals-frontend \
-v /home/minsoo/Workspace/docker-practice/frontend/src:/app/src \
--rm \
-p 3000:3000 \
goals-react

```

- backend에서 volume, bind mount
  - mongodb의 데이터를 컨테이너가 사라져도 남기기 위해 named volume을 사용한다.
  - backend도 `/app/logs`에 있는 로그데이터를 named volume을 통해 저장한다.
  - 또한 `/app` 폴더의 코드를 로컬에서 수정하면 컨테이너에 반영되도록하기 위해 bind mount도 진행한다
  - 이때, named volume으로 지정한 더 자세한 경로인 `/app/logs` 는 로컬호스트머신에서 `/app/logs`를 수정해도 영향을 받지 않는다.
  - 마찬가지로 `/app/node_modules`는 anonymous volume로 하면 로컬에서의 수정에 영향을 받지 않는다.
- 아이디,비번
  - mongodb에 아무나 접근하지 못하게 아이디, 비번을 지정했다. 따라서 backend에서 접근할 때, 해당값 필요하다.
  - backend의 Dockerfile에 `ENV`를 이용하면 된다. ([Dockerfile](https://github.com/minsoo9506/udemy-docker-practice/blob/master/backend/Dockerfile))
  - backend에서는 주소를 `mongodb://${process.env.MONGODB_USERNAME}:${process.env.MONGODB_PASSWORD}@mongodb:27017/course-goals?authSource=admin`

## Reference

- Docker & Kubernetes: 실전 가이드 -2022년판 (udemy 강의)
- 시작하세요 도커/쿠버네티스 , 용찬호 지음

