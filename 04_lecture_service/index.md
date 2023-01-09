# [k8s] Service


k8s를 구성하는 object중에서 Service들에 대해 알아보자.

<!--more-->

## Service
- k8s 네트워크
- 동일한 서비스를 제공하는 pod 그룹의 단일 진입점을 제공 (하나의 IP 묶어서 관리)
    - 여러개의 pod들을 Virtual IP를 통해 접근할 수 있는 것이다.

### Service Type
- ClusterIP(default)
    - pod 그룹의 단일 진입점(Virtual IP) 생성
- NodePort
    - ClusterIP가 생성된 후 모든 Worker Node에 외부에서 접속가능한 Port를 할당
- LoadBalancer
    - 클리우드(AWS 등)에서만 적용
- ExternalName
    - Cluster 안에서 외부에 접속시 사용할 도메인을 등록해서 사용
    - Cluster 도메인이 실제 외부 domain으로 forward되어 동작

## ClusterIP
- `<ClusterIP>`로 들어온 cluster 내부 트래픽을 해당하는 pod들의 `<pod IP>:<targetPort>`로 넘겨주는 역할
- 오직 cluster 내부에서만 접근 가능
- selector의 label이 동일한 pod들을 그룹핑
- 단일 집입점(Virtual IP)을 생성
- 트래픽을 같은 service의 pod들에서 당연히 밸런스있게 배분
- type 생략 시 default 값으로 10.96.x.x 범위에서 할당

일단 먼저 deployment를 통해서 `selector`가 `app: webui`인 pod(2개 띄움)를 띄운다. 그리고 아래와 같은 yaml 파일로 service를 만든다. 

```yaml
apiVersion: v1
kind: Service
metadata:
  name: clusterip-nginx
spec:
  type: ClusterIP
  clusterIP: 10.100.100.100
  selector:
    app: webui
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80 # pod들의 port, pod에 설정된 containerPort
```

그러면 `kubectl get svc` 명령어로 아래와 같은 결과를 확인할 수 있다.

```bash
NAME              TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)   AGE
clusterip-nginx   ClusterIP   10.100.100.100   <none>        80/TCP    6m13s
```

`kubectl describe svc svc이름` 명령어를 통해서 결과를 보면 service를 적용받는 pod들의 ip(`Endpoints`)를 확인할 수 있다. pod의 수를 수정해도 Service가 알아서 적용된다.

```bash
Name:              clusterip-nginx
Namespace:         default
Labels:            <none>
Annotations:       <none>
Selector:          app=webui
Type:              ClusterIP
IP Family Policy:  SingleStack
IP Families:       IPv4
IP:                10.100.100.100
IPs:               10.100.100.100
Port:              <unset>  80/TCP
TargetPort:        80/TCP
Endpoints:         172.17.0.5:80,172.17.0.6:80
Session Affinity:  None
Events:            <none>
```

이제 cluster내부(예를 들면, 다른 pod)에서 `10.100.100.100:80`으로 curl같은 명령어를 보내면 k8s가 알아서 각 pod에게 해당 트래픽를 할당하고 진행한다. 이때, IP뿐만 아니라 service이름 그 자체로도 접근할 수 있다. 위의 예시에서는 `curl clusterip-nginx:80`처럼 접근이 가능하다. 이는 k8s가 내부 DNS를 구동하고 있고 pod들은 자동으로 이 DNS를 사용하도록 설정되기 때문이다. 하지만 어쨋든 이는 cluster 내부에서만 접근가능하기에 외부에서 접근을 하려면 Nodeport를 사용해야한다.

- endpoint
    - service의 selector와 pod의 label이 연결되면 k8s는 자동으로 endpoint라는 object를 생성
    - `kubectl get endpoints`로 확인가능
    - 어차피 자동생성 해줘서 굳이 깊이 알 필요는 없지만 endpoint 자체로 독립적인 object라는 것만 알고 넘어가자

## NodePort
- 외부에서 node IP의 특정 port(`<NodeIP>:<NodePort>`)로 들어오는 요청을 감지하여 해당 port와 연결된 pod들로 트래픽을 전달
- 모든 node를 대상으로 외부 접속 가능한 port(`<NodePort>`)를 할당
    - ClusterIP는 내부용이고 NodePort는 외부용이라고 이해할 수 있다.
- ClusterIP를 생성 후 NodePort를 할당하기 때문에 당연히 clusterIP의 기능을 포함한다.
- Default NodePort 범위: 30000-32767

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nodeport-nginx
spec:
  type: NodePort
  clusterIP: 10.100.100.200
  selector:
    app: webui
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80 # pod의 port
    nodePort: 30200 # 생략하면 랜덤으로 할당
```

위의 파일로 NodePort를 만들면 아래의 결과를 확인할 수 있다.

```bash
NAME             TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
nodeport-nginx   NodePort    10.100.100.200   <none>        80:30200/TCP   5m22s
```

```bash
Name:                     nodeport-nginx
Namespace:                default
Labels:                   <none>
Annotations:              <none>
Selector:                 app=webui
Type:                     NodePort
IP Family Policy:         SingleStack
IP Families:              IPv4
IP:                       10.100.100.200
IPs:                      10.100.100.200
Port:                     <unset>  80/TCP
TargetPort:               80/TCP
NodePort:                 <unset>  30200/TCP
Endpoints:                172.17.0.5:80,172.17.0.6:80
Session Affinity:         None
External Traffic Policy:  Cluster
Events:                   <none>
```

하지만 실제 운영 환경에서는 Nodeport를 외부에 제공하지는 않는다. 주로 Ingress라는 object에서 간접적으로 사용한다.

## ExternalName
- cluster 내부에서 외부의 도메인을 설정

```yaml
apiVersion: v1
kind: Service
metadata:
  name: externalname-svc
spec:
  type: ExternalName
  externalName: google.com
```

```bash
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
externalname-svc   ExternalName   <none>       google.com    <none>    10s
```

이제 `kubectl run testpod -it --image=centos:7`와 같은 명령어로 임의의 pod를 만들고 해당 pod에 들어가서 `curl externalname-svc.default.svc.cluster.local` 같은 명령어를 치면 google.com의 웹사이트가 결과로 나온다.

## Headless Service
- ClusterIP가 없는 서비스로 단일진입점이 필요 없을 때 사용
- Service와 연결된 Pod의 endpoint에 DNS resolving service 지원
  - Pod의 DNS 주소: `<pod-ip-addr>.<namespace>.pod.cluster.local`, 예를 들어 `10-36-0-1.default.pod.cluster.local`

```yaml
apiVersion: v1
kind: Service
metadata:
  name: headless-service
spec:
  type: ClusterIP
  clusterIP: None
  selector:
    app: webui
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80
```

먼저 deployment로 pod들을 띄운다. 그리고 위의 yaml파일을 이용하여 headless service를 만들 수 있다.

```bash
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
headless-service   ClusterIP      None         <none>        80/TCP    10s
```

## kube-proxy
- 모든 worker node에 하나씩 위치하고 있고 DaemonSet의 형태로 배포되어 있음
- endpoint 연결을 위한 iptables 구성
- nodeport로의 접근과 pod와의 연결을 구현

## Reference

- Docker & Kubernetes: 실전 가이드 -2022년판 (udemy 강의)
- 시작하세요 도커/쿠버네티스 , 용찬호 지음
- 따배쿠, 쿠버네티스 시리즈
