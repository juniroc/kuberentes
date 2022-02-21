# Service

- Pod 자체 IP를 통해 다른 Pod 과 통신할 수 있지만, **쉽게 사라지고 쉽게 생성**
→ **직접 통신하는 방법은 권장하지 않음**
- 즉, Pod 직접 통신하는 방법 대신, **별도의 고정된 IP를 가진 Svc** 를 만들고 **그 서비스를 통해 Pod 에 접근하는 방식** 사용

![Untitled](Service%20a30bd/Untitled.png)

### 노출 범위에 따른 타입 분류

1. **ClusterIP**
2. **NodePort**
3. **LoadBalancer**

### 1. ClusterIP

- **Cluster 내부에 새로운 IP 할당**하고, 여러 개의 Pod을 바라보는 로드밸런서 기능을 제공
→ 서비스 이름을 내부 도메인 서버에 등록해 Pod 간 서비스 이름으로 통신 가능

![Untitled](Service%20a30bd/Untitled.png)

`counter-redis-svc.yaml`

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: redis
spec:
  selector:
    matchLabels:
      app: counter
      tier: db
  template:
    metadata:
      labels:
        app: counter
        tier: db
    spec:
      containers:
        - name: redis
          image: redis
          ports:
            - containerPort: 6379
              protocol: TCP

---
apiVersion: v1
kind: Service
metadata:
  name: redis
spec:
  ports:
    - port: 6379
      protocol: TCP
  selector:
    app: counter
    tier: db
```

- 하나의 `yaml` 파일에서 여러 개의 리소스 정의할 때 `---` 구분자 이용
- `Redis` 의 `Deployment` 와 `Service` 를 생성

- 위 `yaml` 파일을 실행하면

`kubectl apply -f counter-redis-svc.yml`

→

![Untitled](Service%20a30bd/Untitled%201.png)

![Untitled](Service%20a30bd/Untitled%202.png)

- 위와 같이 생성
- 같은 클러스터에 생성된 Pod 는 `redis` 라는 도메인으로 `redis Pod` 에 접근 가능

---

- `spec.ports.port` : 서비스가 생성할 Port
- `spec.ports.targetPort` : 서비스가 접근할 Pod의 Port (default : port 와 동일)(
- `spec.selector` : 서비스가 접근할 Pod의 label 조건

---

- 여기서 `Service 의 selector` 는 `Deployment에 정의한 label` 을 사용해 해당 `Pod` 을 가리킴

`counter-app.yaml`

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: counter
spec:
  selector:
    matchLabels:
      app: counter
      tier: app
  template:
    metadata:
      labels:
        app: counter
        tier: app
    spec:
      containers:
        - name: counter
          image: ghcr.io/subicura/counter:latest
          env:
            - name: REDIS_HOST
              value: "redis"
            - name: REDIS_PORT
              value: "6379"
```

- `redis`에 접근할 `counter Deployment` 생성

`kubectl get po`

→

![Untitled](Service%20a30bd/Untitled%203.png)

`kubectl exec -it counter-7b76fd9bc6-zqbm6 -- sh` 를 통해 접속 후

```yaml
apk add curl busybox-extras

curl localhost:3000
-> 1

curl localhost:3000
-> 2

```

![Untitled](Service%20a30bd/Untitled%204.png)

- service 를 통해 Pod 와 연결

### Service 생성 흐름

![Untitled](Service%20a30bd/Untitled%205.png)

- `Endpoint Controller`
    - `Service` 와 `Pod` 을 감시하면서 조건 맞는 Pod IP 수집
    - 수집한 IP 가지고 Endpoint 생성
- `Kube-Proxy`
    - `Endpoint` 변화를 감시
    - 노드의 `iptables` 설정
- `CoreDNS`
    - `Service` 감시하고 서비스 이름과 IP를 `CoreDNS` 에 추가

`iptables` : 커널 레벨의 네트워크 도구

→ 여러 IP 에 트래픽 전달

→ 규칙이 많아지면 성능이 느려져, `ipvs` 를 사용하는 옵션 존재

`CoreDNS` : 클러스터 내부용 DNS

→ Ip 대신 도메인이름 사용

→ 클러스터 내 호환성을 위해 `kube-dns` 라는 이름으로 생성

`EndPoint` : 서비스의 접속 정보를 가지고 있음

→ `kubectl get endpoints (or ep)`

![Untitled](Service%20a30bd/Untitled%206.png)

→ `kubectl describe ep`

![Untitled](Service%20a30bd/Untitled%207.png)

- redis pod 의 IP 가 보임 (replicas 여러개면 여러 IP 가 보임

---

### Service(NodePort)

- ClusterIP 는 클러스터 내부에서만 접근 가능
    
    → `NodePort` 는 클러스터 외부(노드)에서 접근 가능
    

`counter-nodeport.yml`

```yaml
apiVersion: v1
kind: Service
metadata:
  name: counter-np
spec:
  type: NodePort
  ports:
    - port: 3000
      protocol: TCP
      nodePort: 31000  ## 노드에 오플할 Port
  selector:
    app: counter
    tier: app
```

---

- `spec.ports.nodePort` : 노드에 오픈할 Port (미지정시 30000~32768 중 자동 할당)

`kubectl apply -f counter-nodeport.yml`

`kubvctl get svc`

→ 

![Untitled](Service%20a30bd/Untitled%208.png)

![Untitled](Service%20a30bd/Untitled%209.png)

![Untitled](Service%20a30bd/Untitled%2010.png)

- `curl <k8s IP>:<Port>` 로 연결 확인

- `k8s IP` 는 `kubectl describe services` 를 통해 확인

![Untitled](Service%20a30bd/Untitled%2011.png)

![Untitled](Service%20a30bd/Untitled%2012.png)

![Untitled](Service%20a30bd/Untitled%2013.png)

![Untitled](Service%20a30bd/Untitled%2014.png)

- `NodePort` 는 `ClusterIP` 의 기능을 기본으로 포함

---

## Service (LoadBalancer)

![Untitled](Service%20a30bd/Untitled%2015.png)

- 노드가 사라졌을 때 자동으로 다른 노드를 통해 접근이 불가능한 `NodePort`의 단점
    
    → 3개의 노드가 있을 때 3개중 아무 노드로 접근해도 `NodePort`로 연결할 수 있지만 `**어떤 노드가 살아있는지 알 수 없음**`
    
- **자동으로 살아있는 노드에 접근하기 위한 `Load Balancer` 가 필요
→ 즉, 직접 `NodePort` 에 요청하는 것이 아닌, `Load Balancer` 가 알아서 살아 있는 노드에 접근하도록**

`counter-lb.yml`

```yaml
apiVersion: v1
kind: Service
metadata:
  name: counter-lb
spec:
  type: LoadBalancer
  ports:
    - port: 30000 
      targetPort: 3000
      protocol: TCP
  selector:
    app: counter
    tier: app

```

`kubecdtl get svc`

![Untitled](Service%20a30bd/Untitled%2016.png)

- 보통은 `EXTERNAL-IP` 가 `Pending` 상태일 것 (온프레미스 상태일 경우)
    
    → `metallb`를 통해 `LoadBalancer` 구현 가능 (External IP 할당해주는 툴 정도로 생각)