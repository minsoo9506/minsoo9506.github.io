# [k8s] 기본개념


k8s에 대한 기본적인 내용을 알아보자.

<!--more-->

- container 수동배포의 문제점은 아래와 같은 경우 대처하기 어려울 수 있다.
  - container간의 추돌이 있거나 교체해야하는 경우 발생
  - 트래픽이 늘어서 컨테이너를 더 띄워야하는 경우 발생
  - 트래픽이 균등하게 분산되야 하는 경우 발생

## k8s란?

- container deployment를 orchestrating하는 오프소스 시스템

### k8s obeject(리소스) 이해하기

- 모든 리소스를 object로 관리한다.
- 유저는 다양한 k8s objects들을 코드를 통해 만들고 k8s에서 object들을 이용해 각 object들이 하는 일을 진행한다.
- object는 두 가지 방법으로 만들어진다: 명령적(imperatively), 선언적(declaratively)

## k8s 동작원리

### k8s에서 container 동작 flow

1. docker hub에 image를 업로드한다.
2. kubectl을 이용하여 master node의 REST API Server에 요청을 보낸다.
3. 요청이 적절하면 etcd의 정보를 참고하여 master node의 Scheduler에서 pod를 띄우는 스케쥴링을 한다.
4. worker node의 kubelet에서 이를 확인하여 pod에 container를 띄운다.

### k8s component

- master node (control-plane)
  - etcd: key-value 타입의 저장소, 리소스들에 대한 정보 등을 저장
  - kube api server: k8s api를 사용하도록 요청을 받고 요청이 유효한지 검사
  - kube scheduler: pod를 실행할 worker node 선택
  - kube controller: pod를 관찰하며 개수를 보장
- worker node
  - kubelet: 모든 노드에서 실행되는 k8s 에이전트
  - kube proxy: k8s의 network 동작을 관리
  - container runtime: container를 실행하는 엔진

이 이외에도 다양한 component들이 있고 추가적으로 설치해서 사용할 수 있는 것들도 많다.

## kubectl command

- `kubectl get nodes -o wide`
  - node들에 대해 `kubectl get nodes`보다 더 많은 내용 볼 수 있음
- `kubectl get node node이름 -o yaml`
  - yaml 형태로 보여준다.

```bash
NAME       STATUS   ROLES           AGE   VERSION   INTERNAL-IP    EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION      CONTAINER-RUNTIME
minikube   Ready    control-plane   6d    v1.25.3   192.168.49.2   <none>        Ubuntu 20.04.5 LTS   5.15.0-56-generic   docker://20.10.20
```

- `kubectl describe node node이름`
  - 해당 node에 대한 자세한 정보를 확인할 수 있다.
- `kubectl run webserver --image=nginx:1.14 --port=80`
  - `nginx:1.14` 이미지를 다운받아서 `webserver`라는 이름의 pod를 만든다.
  - `run`은 하나의 pod 실행시에만 사용한다.
- `kubectl create deployment mainui --image=httpd --replicas=3`
  - `mainui`로 시작하는 pod를 3개 만든다.
  - `kubectl get pods` 명령어를 통해서 아래와 같이 확인할 수 있다.

```bash
NAME                      READY   STATUS    RESTARTS   AGE
mainui-5965ddf7c8-j6rcx   1/1     Running   0          17s
mainui-5965ddf7c8-m9l4t   1/1     Running   0          17s
mainui-5965ddf7c8-svf89   1/1     Running   0          17s
webserver                 1/1     Running   0          5m4s
```

- `kubectl exec webserver -it -- bash`
  - `webserver` pod의 컨테이너에 bash 명령어를 전달한다.
- `kubectl port-forward webserver 8080:80`
  - 외부에서 8080으로 접속해서 80으로 port forward한다.
- `kubectl edit deployments.apps mainui`
  - 현재 실행되는 `mainui` node 리소스를 수정할 수 있다.
- `kubectl run webserver --image=nginx:1.14 --dry-run -o yaml`
  - 해당 pod를 띄우기 위한 yaml 파일을 보여준다. 실제로 띄우지는 않는다.
  - `> 이름.yaml` 명령어를 추가해서 yaml 파일을 만들고 수정하면 편하다.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: webserver
spec:
  containers:
  - image: nginx:1.14
    name: webserver
    restartPolicy: Always
```

- `kubectl create -f yaml파일이름.yaml`
  - 위와 같은 yaml 파일로 pod를 실행할 수 있다.

## namespace
- k8s api중 하나
- 클러스터 하나를 여러 개의 논리적인 단위로 나눠서 사용

### namespace 생성

```bash
$ kubectl get namespace

NAME                   STATUS   AGE
default                Active   6d17h
kube-node-lease        Active   6d17h
kube-public            Active   6d17h
kube-system            Active   6d17h
kubernetes-dashboard   Active   6d17h
```

- 특정 namespace를 지정하지 않으면 default namespace로 만들어진다.

```bash
# CLI로 namespace 만들기
kubectl create namespace namespace이름
# yaml로 namespace 만들기
kubectl create -f 파일이름.yaml
# 특정 namespace에서 pod 띄우기
kubectl create -f 파일이름.yaml -n namespace이름
# 특정 namespace의 pods 보여준다
kubectl get pods -n namespace이름
```

- namespace 만드는 예시 yaml 파일

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: minsoo_namespace
```

- pod를 만들때도 namespace를 지정할 수 있다.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
  namspace: minsoo_namespace
```

### namespace switch하기

default namespace를 원하는 namespace로 바꾸는 것을 의미한다.

```bash
$ kubectl config view

apiVersion: v1
clusters:
... 생략
contexts:
- context:
    cluster: minikube
    extensions:
    - extension:
        last-update: Mon, 26 Dec 2022 16:09:22 KST
        provider: minikube.sigs.k8s.io
        version: v1.28.0
      name: context_info
    namespace: default
current-context: minikube
... 생략
```

- 아래의 과정을 거치면 `-n` 지정없이 CLI 사용시 적용되는 base namespace가 달라진다.

```bash
# context 생성
kubectl config set-context minsoo_name --cluster=minikube --user=kubernetes-admin --namespace=minsoo
# 현재 context 보기
kubectl config current-context
# context 지정(바꾸기)
kubectl config user-context minsoo_name
```

### namespace 삭제하기
- `kubectl delete namespace namespace이름`을 통해서 삭제할 수 있다.

## yaml template

### yaml
- 기본 문법
  - 들여쓰기 시 tab이 아닌 space bar를 사용
  - 가독성이 좋아 설정 파일에 적합한 형식
  - `:`을 기준으로 key:value 설정
  - `-` 문자로 여러개 나열

### k8s에서 yaml 파일
k8s에서 YAML 파일은 일반적으로 `apiVersion`, `kind`, `metadata`, `spec` 네 가지 항목으로 구성되어있다.
- `apiVersion`: YAML 파일에서 정의한 object의 API 버전
- `kind`: 해당 리소스의 종류 , ex) pod
- `metadata`: 라벨, 주석, 이름 등과 같은 리소스의 부가 정보
- `spec`: 리소스를 생성하기 위한 정보들

## Reference

- Docker & Kubernetes: 실전 가이드 -2022년판 (udemy 강의)
- 시작하세요 도커/쿠버네티스 , 용찬호 지음
- 따배쿠, 쿠버네티스 시리즈
