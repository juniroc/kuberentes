# Stateful-set

### Stateless VS Stateful

- Stateless : App이 여러개 배포될 경우 모두 똑같은 상태로 배포
- Stateful : 각 App 마다의 역할이 존재

![Untitled](Stateful-s%20441ff/Untitled.png)

Ex)

- Nginx 웹서버의 경우 모두 같은 서비스를 각 앱에 배포
- Mongo DB (DB) 의 경우
    - Primary : 메인 DB
    - Secondary : Primary 가 죽을 경우 대체
    - Arbiter : Primary 감시 후 Secondary 로 변경

![Untitled](Stateful-s%20441ff/Untitled%201.png)

- `Stateless` 는 app이 죽으면 App4 같이 숫자와 관계없이 똑같은 것을 복제 또는 띄우면 그만
- `Stateful` 은 죽은 서비스와 같은 App3 를 띄워 상태를 맞춰주어야 함.

![Untitled](Stateful-s%20441ff/Untitled%202.png)

- `Stateless` 는 volume 을 반드시 필요로 하지는 않지만, 필요한 경우 (log 수집) volume 하나에 묶어서 모을 수 있음

![Untitled](Stateful-s%20441ff/Untitled%203.png)

- `Stateful` 은 각각의 역할이 다르므로 역할과 맞는 `Volume`을 연결 시켜주어야 함

![Untitled](Stateful-s%20441ff/Untitled%204.png)

- `Stateless` 는 사용자들이 모든 앱에 골고루 분산되는 형태

![Untitled](Stateful-s%20441ff/Untitled%205.png)

- `Stateful` 은 역할에 따라 연결이 다름

![Untitled](Stateful-s%20441ff/Untitled%206.png)

- `Stateless` 는 ReplicaSet 으로 연결이 되어있고,
- `Stateful` 은 `StatefulSet` 으로 연결

---

### Controller 작동 구조

![Untitled](Stateful-s%20441ff/Untitled%207.png)

- `ReplicaSet`
    - 뒤에 Random 으로 이름 생성
    - `Pod` 들이 **동시에 생성**
    - `Pod` 재생성할 경우 → 새로운 이름으로 생성
    - `Pod` 삭제시에도 동시 삭제

- `StatefulSet`
    - 순차적인 `Index` 이름으로 생성
    - 순차적으로 생성
    - 기존의 이름으로 생성
    - `index`가 높은 순으로 삭제
    

![Untitled](Stateful-s%20441ff/Untitled%208.png)

![Untitled](Stateful-s%20441ff/Untitled%209.png)

- 이때 `StatefulSet` 의 스케일 다운의 좋은 점 - `Pod`가 제거될 지 예상 가능

---

### PV/PVC 연결 구조

![Untitled](Stateful-s%20441ff/Untitled%2010.png)

- `ReplicaSet` 은 생성된 `Pod` 들이 하나의 `PVC` 를 통해 하나의 `PV` 에 연결
    - 이때 `Pod` 와 `PV` 의 `Node` 가 동일해야만 함 
    → ex) `Pod-3` : `Node-2`,  `PV-3` : `Node-1` 인 경우 연결이 안됨

![Untitled](Stateful-s%20441ff/Untitled%2011.png)

- `StatefulSet` 은 `Volume Claim Templete` 을 통해 `PVC` 를 동적 생성
→ `Pod` **각각의 다른** `PVC` 생성해 `PV` 연결 (`pod` 각자의 데이터를 따로 저장 가능)
- `VCT` 를 통해 `Pod` 와 `PV` 가 각각 같은 `Node` 에 만들어져 `Node`를 맞춰야하는 복잡함을 덜 수 있음

![Untitled](Stateful-s%20441ff/Untitled%2012.png)

- 만약 `Pod A-2` 가 삭제되었다가 재생성되는 경우 `PVC A-2` 에  연결됨
    - (이때 `Pod A-2` 는 알아서 `PVC A-2` 와 같은 **Node**에 생성됨)

- `StatefulSet` 은 `Pod` 를 삭제할 경우 `PVC` 는 삭제되지 않고 보존됨
→ 따로 지워주어야 함.
    - `ReplicaSet` 은 정책 설정에 따라 다름 (`Retain` / `Delete` / `Recycle`)

---

---

### Practice

`Test_ReplicaSet.yml`

```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: replicaSet-test
spec:
  replicas: 1
  selector:
    matchLabels:
      type: web
  template:
    metadata:
      labels:
        type: web
    spec:
      containers:
      - name: container
        image: kubetm/app
      terminationGracePeriodSeconds: 10
```

`Test_StatefulSet.yml`

```yaml
apiVersion: apps/v1
kind: StatefulSet-test
metadata:
  name: stateful-test
spec:
  replicas: 1
	serviceName: "statefulset-test"
  selector:
    matchLabels:
      type: db
  template:
    metadata:
      labels:
        type: db
    spec:
      containers:
      - name: container
        image: kubetm/app
      terminationGracePeriodSeconds: 10
```

```bash
# ReplicaSet Pod 생성
kubectl apply -f Test_ReplicaSet.yml

# StatefulSet Pod 생성
kubectl apply -f Test_StatefulSet.yml

# Pod 출력
kubectl get pod
```

![Untitled](Stateful-s%20441ff/Untitled%2013.png)

- `Replicaset` : random 한 문자열 
`StatefulSet` : 순차적인 index

![Untitled](Stateful-s%20441ff/Untitled%2014.png)

![Untitled](Stateful-s%20441ff/Untitled%2015.png)

- `Test_ReplicaSet.yml` 의 `Replicas` : 3 으로 변경
- `Test_StatefulSet.yml` 의 `Replicas` : 3 으로 변경

```bash
# 변경사항 적용
kubectl apply -f test_replicaSet.yml

kubectl apply -f test_StatefulSet.yml

kubectl get pod
```

![Untitled](Stateful-s%20441ff/Untitled%2016.png)

- `Replicaset` 은 동시에 2개가 추가로 생성되고, 이름이 Random하게 부여(AGE 부분을 확인해 보면 됨)
- `StatefulSet` 은 순차적으로 생성되며, 생성 순서대로 index 가 부여됨.

- 각각의 `pod`를 제거해보면

```bash
# replicaset pod 삭제
kubectl delete pod replicaset-test-25qrv

# statefulset pod 삭제
kubectl delete pod stateful-test-0

kubectl get pod
```

![Untitled](Stateful-s%20441ff/Untitled%2017.png)

- `25qrv` 가 사라지고 `4629c` 가 새로 생성됨을 알 수 있음
- `stateful-test-0` 이라는 **이름 그대로 재생성**됨을 알 수 있음

![Untitled](Stateful-s%20441ff/Untitled%2018.png)

![Untitled](Stateful-s%20441ff/Untitled%2019.png)

- `Replicas:0` 으로 줄일 경우

![Untitled](Stateful-s%20441ff/Untitled%2020.png)

![Untitled](Stateful-s%20441ff/Untitled%2021.png)

- `Replicaset-test` 는 동시에 삭제
- `StatefulSet-test` 는 역순으로 삭제

`pvc_test.yml`

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: replica-pvc1
spec:
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
      storage: 1G
```

- 동적으로 pvc 생성

![Untitled](Stateful-s%20441ff/Untitled%2022.png)

- default 에 붙음

![Untitled](Stateful-s%20441ff/Untitled%2023.png)

`test_ReplicaSet_2.yml`

```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: replica-pvc
spec:
  replicas: 1
  selector:
    matchLabels:
      type: web2
  template:
    metadata:
      labels:
        type: web2
    spec:
      nodeSelector:
        kubernetes.io/hostname: zerooneai-p210908-6
      containers:
      - name: container
        image: kubetm/init
        volumeMounts:
        - name: storageos
          mountPath: /applog
      volumes:
      - name: storageos
        persistentVolumeClaim:
          claimName: replica-pvc1
      terminationGracePeriodSeconds: 10
```

- 이때 `NodeSelector` 부분을 넣어준 이유는 `ReplicaSet` 에서 `node` 가 `PV` 가 다른 곳에 있으면 연동이 안되므로 작성해주어야함 
→ `PV` 는 먼저 생성된 `Pod`가 있는 `NODE`에 붙음

```yaml
kubectl apply -f test_replicaset_2.yml

kubectl get pod
```

![Untitled](Stateful-s%20441ff/Untitled%2024.png)

```yaml
# 해당 파드로 접속
kubectl exec -it replica-pvc-tfhwk -- bash

# hello_modu.log 라는 파일 생성
touch /applog/hello_modu.log
```

![Untitled](Stateful-s%20441ff/Untitled%2025.png)

![Untitled](Stateful-s%20441ff/Untitled%2026.png)

- `Replicas : 3` 으로 변경

![Untitled](Stateful-s%20441ff/Untitled%2027.png)

- 새로 생성된 `67m5c` 파드 내부에도 `hello_modu.log` 라는 파일 확인
    
    → 하나의 PVC 에 연결됨을 알 수 있음
    

`test_Statefulset_2.yml`

```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: stateful-pvc
spec:
  replicas: 1
  selector:
    matchLabels:
      type: db2
  serviceName: "stateful-test"
  template: 
    metadata:
      labels:
        type: db2
    spec:
      containers:
      - name: container
        image: kubetm/app
        volumeMounts:
        - name: volume
          mountPath: /applog
      terminationGracePeriodSeconds: 10
  volumeClaimTemplates:
  - metadata:
      name: volume
    spec:
      accessModes:
      - ReadWriteOnce
      resources:
        requests:
          storage: 1G
```

- 여기서 중요한건 `VolumeClaimTemplates`
    - 해당 내용을 통해서 `PVC` 가 **동적으로 생성**됨
- `metadata.name` == `spec.volumeMounts.name` 일치해야함

```yaml
kubectl apply -f test_statefulset_2.yml

kubectl get pod
```

![Untitled](Stateful-s%20441ff/Untitled%2028.png)

`kubectl get pv,pvc | grep stateful`

![Untitled](Stateful-s%20441ff/Untitled%2029.png)

- `pv` 와 `pvc` 가 모두 생성되었고 Bound 됨을 확인

`replicas: 3` 으로 변경

![Untitled](Stateful-s%20441ff/Untitled%2030.png)

`kubectl get pv,pvc |grep state`  

![Untitled](Stateful-s%20441ff/Untitled%2031.png)

- 3개의 PV,PVC 가 생성됨을 알 수 있음

```yaml
kubectl exec -it stateful-pvc-0 -- bash

touch /applog/hello-modu-stateful.log

ls /applog
```

![Untitled](Stateful-s%20441ff/Untitled%2032.png)

- `stateful-pvc-0` 에 접속해 로그 파일 생성

```yaml
kubectl exec -it stateful-pvc-1 -- bash

ls /applog
```

![Untitled](Stateful-s%20441ff/Untitled%2033.png)

- 비어있음 → `PVC` 가 분리되어있음을 알 수 있음