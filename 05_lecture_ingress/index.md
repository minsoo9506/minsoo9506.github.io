# [k8s] Ingress


외부의 요청과 k8s 내부를 연결해주는 Ingress에 대해 알아보자.

<!--more-->

## Ingress
- HTTP, HTTPS를 통해 cluster 내부의 서비스를 외부로 노출
- 기능
    - Service에 외부 URL 제공
    - 트래픽을 로드밸런싱
    - SSL 인증서 처리
    - Virtual hosting을 지정
- 이런 기능과 관련한 규칙들을 정의해둔 것이 Ingress 자체이고 실제로 동작시키는 것은 Ingress Controller이다.
- 아래의 yaml 파일로 Ingress를 생성해도 아무 일도 일어나지 않는다. Ingress는 단지 Ingress 규칙을 정의하는 선언적인 object일 뿐, 외부 요청을 받는 서버가 아니다. Ingress Controller가 외부 요청을 수신했을 때, Ingress 규칙에 기반해 이 요청을 어떻게 처리할지를 결정한다.

### Ingress 설치
[다양한 Ingress controller 구현체](https://kubernetes.io/docs/concepts/services-networking/ingress-controllers/)가 있고 원하는 것을 선택해서 설치해서 사용하면 된다. minikube를 사용하고 있어서 `minikube addons enable ingress` 명령어로 ingress-nginx를 설치했다.

- `kubectl get svc -n ingress-nginx`
```bash
NAME                                 TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
ingress-nginx-controller             NodePort    10.108.58.44    <none>        80:31542/TCP,443:30202/TCP   4m54s
ingress-nginx-controller-admission   ClusterIP   10.108.234.41   <none>        443/TCP                      4m54s
```
- `kubectl  get pod -n ingress-nginx`
```bash
NAME                                        READY   STATUS      RESTARTS      AGE
ingress-nginx-admission-create-l6fcc        0/1     Completed   0             21h
ingress-nginx-admission-patch-6lp2h         0/1     Completed   0             21h
ingress-nginx-controller-5959f988fd-q2jn2   0/1     Running     1 (48s ago)   21h
```

### Ingress 사용하는 방법
1. 위처럼 ingress controller를 생성한다.
2. 외부로 노출하고 싶은 어플리케이션을 생성한다.
3. 아래와 같은 yaml 파일을 통해 ingress object를 생성한다.
4. 클라이언트에서의 요청이 ingress controller에게 전달되고 규칙에 따라 적절한 pod(endpoint)로 전달된다.

```yaml
apiVersion: extensions/v1beta1 # 지금 버전은 다를수도
kind: Ingress
metadata:
  name: marvel-ingress
spec:
  rules:
  - http:
    paths:
    - path: /
      backend:
        serviceName: marvel-service
        servicePort: 80
    - path: /pay
      backend:
        serviceName: pay-service
        servicePort: 80
```

- `/`경로로 들어온 요청을 `marvel0-service`라는 서비스의 80 port로 전달한다.

## Reference

- Docker & Kubernetes: 실전 가이드 -2022년판 (udemy 강의)
- 시작하세요 도커/쿠버네티스 , 용찬호 지음
- 따배쿠, 쿠버네티스 시리즈
