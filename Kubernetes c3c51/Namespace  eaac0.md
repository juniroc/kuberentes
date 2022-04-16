# Namespace - ResourceQuota, LimitRange

![Untitled](Namespace%20%20eaac0/Untitled.png)

- 하나의 namespace 가 많은 자원을 사용하면 다른 namespace 는 사용할 수 없음
→ `ResourceQuota` 설정으로 최대 자원을 설정
→ 즉, 해당 namespace 에서 자원이 부족할 수는 있어도, 다른 곳은 영향을 끼치지 못함
- 추가적으로 `LimitRange` 를 이용해 `namespace` 에 들어오는 `Pod` 크기도 제한할 수 있음

### Namespace

![Untitled](Namespace%20%20eaac0/Untitled%201.png)

- 하나의 `Namespace` 에 같은 이름의 `Pod` 를 생성할 수 없음
- 타 namespace 와 분리됨
- namespace 를 지울 경우 그 안의 자원들도 모두 삭제됨

`namespace.yml`

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: nm-1
```

- ns 을 만들 때는 위와 같이 이름외에 따로 지정해줄 것은 없음

ex)

![Untitled](Namespace%20%20eaac0/Untitled%202.png)

- 위와 같은 경우 `pod` 와 `svc` 의 `label` 과 `selector` 가 일치하더라도 `ns` 가 달라서 연결되지 않음

### ResourceQuota

- namespace 에 제한을 거는 느낌

![Untitled](Namespace%20%20eaac0/Untitled%203.png)

- 해당 ns 의 총 자원은 3Gi 로 하고, memory 는 6Gi 로 설정
    - 이후 2Gi 의 자원을 사용하는 Pod가 존재하고 이후에 2Gi 의 자원을 쓰는 Pod 를 생성하려할 경우 
    → 생성되지 않음

![Untitled](Namespace%20%20eaac0/Untitled%204.png)

- 오브젝트에 관련해서도 제한할 수 있음

### LimitRange

- ns 에 생성되는 Pod(container) 에 제한을 거는 방법

![Untitled](Namespace%20%20eaac0/Untitled%205.png)

- `maxLimitRequestRaio` : Request 값과 limit 값의 비율이 n 배 만큼 넘으면 안됨
- `Pod1` 의 경우 limits 이 넘어서 안됨
- `Pod2` 의 경우 Ratio 가 3 이 넘어서 안됨
- Default 를 설정해 놓으면 `Pod3` 과 같이 자동으로 주어짐

![Untitled](Namespace%20%20eaac0/Untitled%206.png)

---

## Practice

`namespace.yaml`

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: nm-1
```

![Untitled](Namespace%20%20eaac0/Untitled%207.png)

`pod.yaml`

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-1
  namespace: nm-1 # 이것을 지정해주어야 함
  labels:
    app: pod
spec:
  containers:
  - name: container
    image: kubetm/app
    ports:
    - containerPort: 8080
```

- namespace 지정

`service.yaml`

```yaml
apiVersion: v1
kind: Service
metadata:
  name: svc-1
  namespace: nm-1
spec:
  selector:
    app: pod
  ports:
  - port: 9000
    targetPort: 8080
```

- `namespace` 와 `pod selector` 지정

![Untitled](Namespace%20%20eaac0/Untitled%208.png)

![Untitled](Namespace%20%20eaac0/Untitled%209.png)

![Untitled](Namespace%20%20eaac0/Untitled%2010.png)

- 두개 만들었을 경우

`namespace_2.yaml`

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: nm-2
```

`pod_2.yaml`

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-1
  namespace: nm-2
  labels:
    app: pod
spec:
  containers:
  - name: container
    image: kubetm/init
    ports:
    - containerPort: 8080
```

- 만약 hostpath 를 통해 mount 시킨 경우 내용이 중복될 수 있음

---

### ResourceQuota

`ns-3.yaml`

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: nm-3
```

`ResourceQuota.yaml`

```yaml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: rq-1
  namespace: nm-3
spec:
  hard:
    requests.memory: 1Gi
    limits.memory: 1Gi
```

`kubectl describe resourcequotas --namespace**=**nm-3`

- 해당 명령어를 통해 잘 만들어졌는지 확인 가능

![Untitled](Namespace%20%20eaac0/Untitled%2011.png)

`ns3_pod.yaml`

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-2
  namespace: nm-3
spec:
  containers:
  - name: container
    image: kubetm/app
```

- 해당 Pod 를 만들려하면, 아래와 같이 반드시 Rsc_Quota 를 명시하라고 에러 나옴

![Untitled](Namespace%20%20eaac0/Untitled%2012.png)

`ns3_pod_2.yml`

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-3
  namespace: nm-3
spec:
  containers:
  - name: container
    image: kubetm/app
    resources:
      requests:
        memory: 0.5Gi
      limits:
        memory: 0.5Gi
```

- 그래서 위와 같이 만들어야함

`ns3_pod_3.yaml`

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-4
  namespace: nm-3
spec:
  containers:
  - name: container
    image: kubetm/app
    resources:
      requests:
        memory: 0.8Gi
      limits:
        memory: 0.5Gi
```

- 위와 같이 추가로 만들면

- 다음과 같이 초과 리소스를 사용했다는 에러 발생

![Untitled](Namespace%20%20eaac0/Untitled%2013.png)

```yaml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: rq-2
  namespace: nm-3
spec:
  hard:
    pods: 2
```

- 위와 같이 `pod 갯수` 제한도 가능

**주의 : ResourceQuota 없이 Pod 를 만들어 놓은 상태에서 ResourceQuota 를 만들면 기존에 존재하던 Pod 가 지워지지 않고 유지됨.**

**→ 대신 새로 만들려할 때는 제한 받음
→ ResourceQuota 를 만들 때, 기존 Pod가 존재하는지 확인 후 만드는 것이 좋음**

---

### LimitRange

![Untitled](Namespace%20%20eaac0/Untitled%2014.png)

`limit_range.yml`

```yaml
apiVersion: v1
kind: LimitRange
metadata:
  name: lr-1
  namespace: nm-4
spec:
  limits:
  - type: Container
    min:
      memory: 0.1Gi
    max:
      memory: 0.4Gi
    maxLimitRequestRatio:
      memory: 3
    defaultRequest:
      memory: 0.1Gi
    default:
      memory: 0.2Gi
```

`k describe limitranges -n nm-4`

![Untitled](Namespace%20%20eaac0/Untitled%2015.png)

- `limitrange` 가 2개 만들어진 경우
→ **조건이 더 강한**(ex, max 5, max 3 인 경우 3) 을 **따름**