# Volume

### 1. EmptyDir

![Untitled](Volume%20fe167/Untitled.png)

- **Pod 내부에서** Volume을 생성해 **Container 끼리 공유 (목적)
→ Pod 에 문제가 생겨 사라지면 데이터도 같이 사라짐
→ 즉, 일시적인 데이터만 넣어줘야함**

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-volume-1
spec:
  containers:
  - name: container1
    image: kubetm/init
    volumeMounts:
    - name: empty-dir
      mountPath: /mount1
  - name: container2
    image: kubetm/init
    volumeMounts:
    - name: empty-dir
      mountPath: /mount2
  volumes:
  - name : empty-dir
    emptyDir: {} 
```

- 위의 경우 `mountPath` 가 다르지만 모두 같은 `volumes.name` 을 공유하므로 **같은 볼륨내부에 다른 Path 가 마운트됨**

### 2. hostPath

![Untitled](Volume%20fe167/Untitled%201.png)

![Untitled](Volume%20fe167/Untitled%202.png)

- `Node` 내부에 `Pod` 와 `Volume` 을 연결
→ `Pod` 가 사라져도 데이터는 사라지지 않음

![Untitled](Volume%20fe167/Untitled%203.png)

- 하지만 `Pod` 가 죽었다가 다시 살아나며 `다른 Node` 에 생성될 경우
→ 기존 Volume 마운트가 어려울 수 있음
→ 사용자가 직접 Node2 에 기존 Volume 에 대한 마운트를 걸어주어야 함
- 각 노드마다 자신을 위해서 사용되는 파일들을 Pod 가 읽어야 할 경우 사용

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-volume-3
spec:
  nodeSelector:
    kubernetes.io/hostname: k8s-node1
  containers:
  - name: container
    image: kubetm/init
    volumeMounts:
    - name: host-path
      mountPath: /mount1
  volumes:
  - name : host-path
    hostPath:
      path: /node-v
      type: DirectoryOrCreate
```

- `node-v` 라는 `directory` 가 없으면 생성함
- 그리고 `NodeSelector` 가 없이 생성하게되면 다른 노드에 생성이 될 수 있음
→ 기존 Volume 에 연결이 안됨

### 3. PV/PVC

![Untitled](Volume%20fe167/Untitled%204.png)

- 각 Volume 종류에 맞는 config 를 PV 에서 정의
- 이후 사용자가 PVC 를 만들면
→ 해당 PVC volume에 맞는 PV 를 연결해줌
- 이 후 Pod 에 PVC 연결해주면
- 이미 있는 Host 의 Volume 을 사용

`**PV**`

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-03
spec:
  capacity:
    storage: 2G
  accessModes:
  - ReadWriteOnce
  local:
    path: /node-v
  nodeAffinity:
    required:
      nodeSelectorTerms:
      - matchExpressions:
        - {key: kubernetes.io/hostname, 
					 operator: In, values: [k8s-node1]}
```

- `Local` type 의 PV를 생성할 경우 
→ `matchExpressions` 에서 정의한 `node` 에 연결
→ 즉, Pod는 위에서 정의한 노드에만 생성이 됨.

`**PVC**`

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pvc-01
spec:
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
      storage: 1G
  storageClassName: ""
```

`**Pod**`

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-volume-3
spec:
  containers:
  - name: container
    image: kubetm/init
    volumeMounts:
    - name: pvc-pv
      mountPath: /mount3
  volumes:
  - name : pvc-pv
    persistentVolumeClaim:
      claimName: pvc-01
```

---

### Practice of PV / PVC

`pv-01`

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-01
spec:
  capacity:
    storage: 1G
  accessModes:
    - ReadWriteOnce
  local:
    path: /node-v
  nodeAffinity:
    required:
      nodeSelectorTerms:
      - matchExpressions:
        - {key: kubernetes.io/hostname, 
						operator: In, 
						values: [minikube]}
```

`pv-02`

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-02
spec:
  capacity:
    storage: 1G
  accessModes:
    - ReadOnlyMany
  local:
    path: /node-v
  nodeAffinity:
    required:
      nodeSelectorTerms:
      - matchExpressions:
        - {key: kubernetes.io/hostname, operator: In, values: [minikube]}
```

- AccessModes 만 바꿈

`pv-03`

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-03
spec:
  capacity:
    storage: 2G
  accessModes:
    - ReadWriteOnce
  local:
    path: /node-v
  nodeAffinity:
    required:
      nodeSelectorTerms:
      - matchExpressions:
        - {key: kubernetes.io/hostname, 
						operator: In, 
						values: [minikube]}
```

- Storage 크기만 바꿈

`pvc.yaml`

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pvc-01
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1G
  storageClassName: ""
```

- `accessModes` , `storage` 를 통해 어느쪽에 연결할지 선택
- `요청한 용량`이 `PV로 생성된 용량`보다 클 경우 생성이 안됨

`Pod`

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-volume-3
spec:
  containers:
  - name: container
    image: tmkube/init
    volumeMounts:
    - name: pvc-pv
      mountPath: /mount3
  volumes:
  - name: pvc-pv
    persistentVolumeClaim:
      claimName: pvc-01
```

- 만약 파드안에 `/mount3` 디렉토리가 없으면 파드 생성이 안됨
- 옵션 `DirectoryOrCreate`

### docker 내부 위치에 /node-v  directory 생성

![Untitled](Volume%20fe167/Untitled%205.png)