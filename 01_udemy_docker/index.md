# [Docker] Image와 Container


Docker의 가장 기본적인 개념 (Image, Container)

<!--more-->

### Image

- templates for containers, Read-Only
- 이미지를 기반으로 컨테이너 띄워서 사용한다.

- `docker run imageName`
  - `run` 명령어를 치면 이미지가 없는 경우 도커허브에서 다운받고 컨테이너를 띄운다'
- `docker run -it node`
  - 바로 컨테이너안에서 명령어를 칠 수 있게 된다 (컨테이너가 대화형 셀을 제공하는 경우)
- `docker ps`
  - 지금 돌아가고 있는 컨테이너를 알려준다.
  - `docker ps -a`로 `-a`를 붙이면 모든 컨테이너를 보여준다 (정지된 컨테이너도 포함하여)

### Image layer

- 이미지는 layer 기반이라고 할 수 있다. (각 명령어는 layer라고 할 수 있다)
- 한번 build한 이미지는 다시 build하면 엄청 빠르게 진행된다. 이는 캐시되었기 때문이다.
- 특정 명령어 부분의 코드가 바뀌면 그 뒤의 모든 명령어(layer)들도 전부 캐시를 이용하지 않고 처음 build하듯이 살펴본다.
- 그래서 명령어의 순서도 적절하기 배치하는 것이 build를 최적화하는 방법중 하나이다.

### Dockerfile

- Dockerfile을 통해서 이미지를 빌드할 수 있다.
- 우리가 원하는대로 커스텀 이미지를 만들 수 있는 것이다.
- Dockerfile 예시
  - `FROM node`: 기본 이미지 다운로드
  - `WORKDIR /app`: 이미지에서 명령어가 실행될 기본 디렉토리 설정
  - `COPY . ./`: 첫번째가 지금 위치, 두번째가 이미지 내부 위치를 의미, 지금 위치의 파일을 복사해서 이미지 내부의 위치에 복사
  - `RUN npm install`: 이미지 내부에서 명령어 실행
  - `EXPOSE 80`: 호스트와 연결할 포트 번호를 설정, 하지만 document같은 역할일 뿐 실제로 포트를 연결하기 위해서는 컨테이너를 띄울때 명령어를 통해 진행해야 한다
  - `CMD ["node", "server.js"]`: 이미지를 기반으로 컨테이너가 시작될 때 실행된다, 배열형식으로 명령어를 작성

```Dockerfile
FROM node

WORKDIR /app

COPY . /app

RUN npm install

EXPOSE 80

CMD ["node", "server.js"]
```

- 위의 Dockerfile이 있는 위치에서 `docker build .` 명령어를 통해서 이미지를 빌드할 수 있다.
- 포트를 연결하기 위해서는 `docker run -p 3000:80 imageID` 같은 명령어가 필요하다: `-p 로컬포트:container포트`
- `docker stop containerName`으로 컨테이너를 멈출 수 있다.

## image, container 관리

### 컨테이너 재시작

- `docker start containerID`: 기존의 containerID 컨테이너 재시작 (container 이름으로도 당연히 가능하다)

### attached, detached

- `docker run`은 attached 모드가 default, `docker start`는 detached 모드가 default이다.
- detached 모드는 컨테이너를 백그라운드에서 동작하게 한다.
- detached 모드를 하고 싶으면 `docker run -d imageID`하면 된다.
- attaced모드로 꺼져있는 컨테이너를 시작하고 싶으면 `docker start -a containerID`하면 된다.
- 실행중인 컨테이너에 attach하고 싶으면 `docker attach containerID`하면 된다.

### interactive 모드

- `-i` 옵션을 통해서 입력이 가능하게하고 `-t` 옵션을 통해서 컨테이너의 터미널을 사용할 수 있게 된다. 그래서 이미지로 컨테이너를 띄울 때, 두 가지 옵션 `-it`을 자주 같이 사용한다.
- 컨테이너 재시작에서도 `docker start -a -i containerID`로 사용하기도 한다.

### 컨테이너에서 나오기

- `exit`, `Ctrl + D` 입력하면 컨테이너 빠져나오면서 동시에 컨테이너를 정지시킨다.
- 컨테이너를 정지시키지 않고 나오고 싶으면 `Ctrl + P, Q`를 이용하자.

### exec

- `docker exec`를 통해서 실행중인 컨테이너에 명령어를 사용할 수 있다.
  - 예를 들어, `docker exec -it containerName npm init`
- 컨테이너를 띄울 때, 이미지의 이름 뒤에 명령어를 쓰면 기존의 Dockerfile에 있던 `CMD`에 작성한 명령어를 덮어쓴다.
- `ENTRYPOINT`는 이미지의 이름 뒤 명령어가 이어져서 사용된다.

```Dockerfile
CMD ["npm", "start"]
ENTRYPOINT ["npm"]
```

- `docker run node init` 이런식의 명령어를 사용하면 `CMD`는 무시되고 `npm init`이 실행된다.

### 삭제

- `docker rm containerID`는 컨테이너를 삭제할 수 있다
  - 한번에 여러개도 할 수 있다. 그냥 주르륵 쓰면 된다.
- `docker images`는 image들을 보여준다.
- `docker rmi imageID`는 이미지를 삭제할 수 있다.
  - 모든 이미지를 항상 삭제할 수 있는 것은 아니고 중지된 컨테이너들만 존재할 때 삭제할 수 있다.
- `docker image prune`는 현재 실행되지 않는 컨테이너의 이미지들을 모두 삭제한다. (사용하지 말자 ㅎㅎ)
- `docker rum --rm containerID`에서 `--rm`은 컨테이너가 중지되면 자동으로 삭제되는 옵션이다.

### 이미지, 컨테이너 살펴보기

- `docker image inspect imageID`를 통해 이미지에 대한 정보를 알 수 있다. (컨테이너도 가능)
  - OS, 만들어진 날짜 등등 생각보다 많은 정보를 알 수 있다.

### 컨테이너 파일 복사

- 컨테이너로 파일을 복사할 수 있고 반대로 컨테이너에서 파일을 복사할 수도 있다. (실행중인 컨테이너도 가능)
- `docker cp folder/test.txt containerName:/test/test.txt`는 로컬 파일경로 `folder/test.txt`를 `containerName`이라는 컨테이너의 `/test/test.txt`위치에 복사한다. (`:`도 꼭 해줘야한다)
- `docker cp containerName:/test/test.txt folder/test.txt`는 위와 반대의 과정을 진행한다.

### 컨테이너의 이름, 이미지의 태그 지정

- `docker run --name 원하는컨테이너이름 imageID`처럼 `--name`으로 원하는 컨테이너의 이름을 지정할 수 있다.

- 이미지의 이름은 아래처럼 두 가지로 이루어져있다.
  - `repository:tag`
  - `repository`는 이미지들을 그룹핑 할 수 있는 것 또는 이름으로 이해할 수 있고 `tag`는 버전같이 더 해당그룹의 작은 단위의 특징을 나타낼 수 있다.
- `docker build -t repository:tag .` 이런식으로 image의 만들면 된다.
- `docker tag 이전이름 새이름` repository:tag를 바꿀 수도 있다.

### Dockerhub에 이미지 푸시하기

- Docker Hub를 이용할 수도 있고 Private Registry에 이미지를 푸시할 수 있다.
- Docker Hub
  - Docker Hub에서 repository를 먼저 만들어준다.
  - 그리고 `push` 를 이용해서 올릴 수 있다.
  - `docker push hubID/repositoryName:tagName`
    - 따라서 로컬에 있는 이미지의 이름도 위와 같아야한다.
    - 그리고 권한 제한이 뜰 수도 있는데 이 때는 `docker login`을 해서 인증을 해야한다.

## Reference

- Docker & Kubernetes: 실전 가이드 -2022년판 (udemy 강의)
- 시작하세요 도커/쿠버네티스 , 용찬호 지음

