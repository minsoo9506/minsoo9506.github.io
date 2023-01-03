# [k8s] Pod


k8s를 구성하는 object중에서 Pod에 대해 알아보자.

<!--more-->

## Pod

- container를 표현하는 k8s api의 최소 단위
- 하나의 pod에는 하나 또는 여러개의 container가 포함될 수 있다.
- 완전한 어플리케이션의 역할을 할 수 있다.
- pod는 docker volume같이 shared resource도 가지고 있다.
- pod끼리 또는 외부와 통신이 가능하다.
  - 디폴트로 pod는 내부적으로 cluster ip를 갖고 있다.
  - 하나의 pod에 있는 container들끼리는 localhost를 통해 서로 통신할 수 있다.
- pod는 지속되지 않는다.
  - pod가 교체(제거)되면 디폴트로 모든 리소스는 사라진다. 물론 이들을 날아가지 않도록 할 수 있다.

### pod 생성하기

- kubectl run CLI로 생성
  - `kubectl run webserver --image=nginx:1.14`
- pod yaml파일을 이용하여 생성
  - `kubectl create -f pod_nginx.yaml`

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - name: nginx-container
    image: nginx:1.14
    ports:
    - containerPort: 80
```

### multicontainer pod 생성하기

- `containers`에 2개 이상의 container에 대해 작성하면 된다.
- container들의 pod명과 ip는 동일하다.
  - 따라서 centos-container에 들어가서 `curl locahost:80`이 작동하는 것이다.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: multipod
spec:
  containers:
  - name: nginx-container
    image: nginx:1.14
    ports:
    - containerPort: 80
  - name: centos-container
    image: centos:7
```

```
NAME                      READY   STATUS             RESTARTS      AGE
mainui-5965ddf7c8-fft27   1/1     Running            0             20m
multipod                  2/2     Running            3 (29s ago)   84s
```

- `kubectl exec pod이름 -it -c container이름 -- bash`
  - pod안의 container에서 `bash` 명령어 실행
- `kubectl logs pod이름 -c container이름`
  - pod안의 container의 log 출력
- pod에 하나의 container만 있으면 그냥 pod만 작성하면 된다.


### Pod 동작 flow
pod를 생성시키면 `Pending` 상태였다가 worker node를 할당하고 `ContainerCreating` 상태가 되고 성공적으로 만들어지면 `Running` 상태가 된다.

### `livenessProbe`를 이용하여 self-healing pod 만들기
pod가 계속 실행될 수 있음을 보장한다.

- pod의 `spec`에 정의되어 있다.
- `80` port와 `/` path로 k8s가 접속해서 문제가 없는지 확인한다.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - name: nginx-container
    image: nginx:1.14
    ports:
    - containerPort: 80
    livenessProbe:
      httpGet:
        path: /
        port: 80
      initialDelaySeconds: 15
      periodSeconds: 20
      timeoutSeconds: 1
      successThreshold: 1
      failureThreshold: 3
```

#### livenessProbe 종류
- `httpGet` probe
  - 지정한 ip, port, path에 http get 요청을 보내서 container가 응답하는지를 확인한다.
  - 200이 아닌 값이 나오면 기존 container는 죽이고 container를 다시 다운받고 시작한다.
- `tcpSocket` probe
  - 지정된 port에 TCP 연결을 시도한다.
  - 연결되지 않으면 container를 다시 시작한다.
- `exec` probe
  - `exec` 명령을 전달한다.
  - 명령의 종료코드가 0이 아니면 container를 다시 시작한다.

### init container를 적용한 pod
기존 목적인 main container를 실행하기 전에 init container를 실행해서 성공한 경우 main container를 실행한다. main container가 실행되기 전에 사전 작업이 필요한 경우에도 init container를 사용할 수 있다.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - name: nginx-container
    image: nginx:1.14
    ports:
    - containerPort: 80
  initContainers:
  - name: init-myService
    image: sample:1.28
```

### infra container(pause) 이해하기
pod를 만들면 pause라는 container가 자동으로 만들어진다. pod에 대한 infra를 관리할 뿐이고 우리가 `get pod`등으로 모니터링 할 때는 보이지 않는다.

### static pod 만들기 
일반적인 pod와 다르게 API에게 요청을 보내지 않는다. worker node에 있는 특정경로(주로 `etc/kubernetes/manifests/`)에 yaml파일을 전달하면 kubelet이 해당 내용을 토대로 pod를 띄운다. 이처럼 kubelet demon에 의해 실행되는 pod를 static pod라고 부른다.

- 특정 worker node에 접속해서 `cat /var/lib/kubelet/config.yaml` 명령어를 치면 `staticPodPath`가 있다. 해당 디렉토리에 pod yaml을 넣으면 된다.
- `config.yaml`에서 해당 디렉토리를 원하는대로 수정할 수도 있다.

### pod에 resource(cpu, memory) 할당하기
원래는 scheduler가 알아서 worker node들의 resource들을 확인하고 pod를 할당해준다. 그런데 유저가 요청할 때, resource를 정해서 master node에 요청할 수 있다.

- resource request: pod를 실행하기 위한 최소 resource 양 요청
- resource limits: 반대로 최대 resource 양 제한, memory limit을 초과해서 사용하는 pod는 종료되며 다시 스케쥴링 된다.
- resource 여유가 없으면 pod는 pending 상태가 된다.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - name: nginx-container
    image: nginx:1.14
    ports:
    - containerPort: 80
    resource:
      requests:
        cpu: 200m
        memory: 250Mi
      limits:
        cpu: 1
        memory: 500Mi
```

### pod 환경변수 설정
- 환경변수 (`env`)
  - pod내의 container가 실행될 때 필요로 하는 변수
  - container 제작시 미리 정의된 변수들이 있는데 pod 실행 시 이들을 변경할 수도 있다.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - name: nginx-container
    image: nginx:1.14
    ports:
    - containerPort: 80
    env:
    - name: MYVAR
      value: "test"
```

## Reference

- Docker & Kubernetes: 실전 가이드 -2022년판 (udemy 강의)
- 시작하세요 도커/쿠버네티스 , 용찬호 지음
- 따배쿠, 쿠버네티스 시리즈
