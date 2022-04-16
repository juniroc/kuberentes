# Pod

### 1. Container

![Untitled](Pod%20b05ee/Untitled.png)

- Pod 생성시 기본적으로 `Cluster IP` 생성됨
→ 재생성될 경우 변경
→ 외부에서 접근 안됨
→ 클러스터 내에서만 접근 가능
- `Pod` 내부 컨테이너 사이에서는 `Port` 가 겹칠 수 없음

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-1
spec:
  containers:
  - name: container1
    image: kubetm/p8000
    ports:
    - containerPort: 8000   ## 해당 부분으로 포트 연결 가능 
  - name: container2
    image: kubetm/p8080
    ports:
    - containerPort: 8080   ## 해당 부분으로 포트 연결 가능 
```

### 2. Label

![Untitled](Pod%20b05ee/Untitled%201.png)

- key : value 쌍
- 여기서는 type / lo 로 나눴는데,
    - type : 서비스와 그 외에 환경들
    - lo : 개발환경과 실제 상용환경

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-2
  labels:
    type: web
    lo: dev
spec:
  containers:
  - name: container
    image: kubetm/init
```

- 위 내용을 서비스에 붙일 경우

```yaml
apiVersion: v1
kind: Service
metadata:
  name: svc-1. 
spec:
  selector:   ## 다른 resource 의 label 과 일치시켜줌
    type: web  ## 라벨과 일치시켜줌
  ports:
  - port: 8080
```

### Node Scheduler

![Untitled](Pod%20b05ee/Untitled%202.png)

- `Pod 를 생성시` **Node 를 지정**해주고 싶을 때
- `노드`에 직접 `label` 을 달아 지정해줌
- 이를 위해 나온것이
1. nodeSelector

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-3
spec:
  nodeSelector:  ### 그때 나온게 nodeSelector
    kubernetes.io/hostname: k8s-node1
  containers:
  - name: container
    image: kubetm/init
```

1. resources 크기

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-4
spec:
  containers:
  - name: container
    image: kubetm/init
    resources:
      requests:
        memory: 2Gi
      limits:
        memory: 3Gi
```

- 파드가 `필요한 자원의 크기`를 넣어주고, 최대 `허용 제한할 크기`도 지정
→ 지정해주지 않으면 무한한 자원을 사용하려함
- Memory 는 초과시 Pod 를 종료, Cpu 는 초과시 request 로 낮춰 종료되지 않음.
→ 그래서 `limits.memory` 를 줌
- K8s 가 Node 할당에 알아서 우선순위를 정해주는데 이때 영향을 미치는게 자원 할당량 같은 것들