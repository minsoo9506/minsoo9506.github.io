# [Docker] docker-compose


docker compose에 대해 알아보자.

<!--more-->

## compose

이전에 docker network를 이용하여 다수의 컨테이너를 다뤘었다. 하지만 하나씩 하지 않고 docker-compose를 이용하면 더 편하다. 여러개의 이미지를 빌드할 수 있고 여러개의 컨테이너를 띄울 수 있게한다.

- (참고) 리눅스에서는 도커 컴포즈를 따로 다운받아야한다.

### docker-compose.yaml 작성

- 설명이 잘 되어있음: https://meetup.toast.com/posts/277
- `docker-compose.yaml` 파일을 만든다.
  - 2개의 공백을 사용

```yaml
version: "3.8"
services:
  mongodb:
    image: "mongo"
    # container_name: mongodb
    volumes:
      - data:/data/db
    # environment:
    # MONGO_INITDB_ROOT_USERNAME: root
    # MONGO_INITDB_ROOT_PASSWORD: secret
    # - MONGO_INITDB_ROOT_USERNAME=root
    env_file:
      - ./env/mongo.env
  backend:
    build: ./backend
    # build:
    #   context: ./backend
    #   dockerfile: Dockerfile
    #   args:
    #     some-args: 80
    ports:
      - "80:80"
    volumes:
      - logs:/app/logs # named volume
      - ./backend:/app # bind mount, compose에서는 상대경로 이용
      - /app/node_modules # anonymous volume
    env_file:
      - ./env/backend.env
    depends_on:
      - mongodb
  frontend:
    build: ./frontend
    ports:
      - "3000:3000"
    volumes: -./frontend/src:/app/src
    stdin_open: true # -i
    tty: true # -t
    depends_on:
      - backend

volumes:
  data:
  logs:
```

- `version`: docker-compose.yaml의 버전을 의미한다.
- `services`: 컨테이너들을 묶어놓은 단위이다.
- 위에서 `mongodb`, `backend`, `front` 는 컨테이너를 의미한다.
  - 나중에 도커컴포즈로 컨테이너를 띄우면 해당 이름이 컨테이너 이름의 일부로 들어가 있는 것을 알 수 있다.
  - 물론 이름을 정하고 싶으면 `container_name`을 이용하면 된다.
- `environment`부분에서 보면 `~: ~` 이 아니라 `=`을 사용하고 싶으면 맨 앞에 `-`을 붙여야 한다.
- network를 따로 추가하지 않아도 docker-compose가 모든 서비스를 디폴트 네트워크에 자동으로 추가해준다.
  - 물론 원하는 network를 추가해서 사용할 수 있다.
- `volumes`: service에 있는 컨테이너에서 명시한 volume을 작성한다.
  - named volume만 작성이 필요하다. (anonymous, bind mount는 불필요)
  - 위에서 named volume은 `data`이고 `data:` 처럼 작성하면 된다. (`:`뒤에 아무것도 없는게 정상)
  - 동일한 volume을 여러개의 컨테이너가 공유할 수도 있다.
- `build`: 해당 위치에 있는 Dockerfile 이용하여 이미지를 빌드하고 컨테이너를 띄운다.
- `depends_on`: 의존성을 나타내고 아래에 해당하는 컨테이너들이 먼저 생성된다.

### 실행

- `docker-compose up`으로 실행할 수 있다.
  - `-d`을 추가하면 detach모드
  - `-p 프로젝트이름`을 추가하면 동일한 docker-compose.yaml로 이름이 다른 여러 개의 프로젝트를 생성할 수 있다.
- `docker-compose down`하면 volume을 제외하고 컨테이너들을 모두 내리고 삭제한다.
  - `docker-compose down -v`하면 volume도 삭제된다.
- `docker-compose up --build`를 하면 강제로 이미지를 다시 빌드해서 컨테이너를 띄우게 한다. (위에서 backend, frontend처럼 build하는 과정이 있는 경우)
  - `--build` 옵션이 없고 이미지가 이미있다면 이미지를 빌드하지 않고 기존의 이미지를 통해서 컨테이너를 띄운다.
  - `docker-compose build`를 하면 이미지만 빌드하고 컨테이너를 띄우지는 않는다.

## Reference

- Docker & Kubernetes: 실전 가이드 -2022년판 (udemy 강의)
- 시작하세요 도커/쿠버네티스 , 용찬호 지음

