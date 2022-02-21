# PV/PVC

### PV (Persistent Volume)

- 관리자에 의해 생성된 볼륨

### PVC (Persistent Volume Claim)

- 사용자가 볼륨을 사용하기 위해 PV에 하는 요청

![Untitled](PV%20PVC%206ba5a/Untitled.png)

- 컨테이너의 `/var/log/test.log` 디렉토리를 워커노드의 `/tmp/log_backup` 경로에 `PVC` 와 `PV` 설정을 하면 위와 같은 형태

### PV 와 PVC 의 LifeCycle

1. 프로비저닝(Provisioning)
2. 바인딩(Binding)
3. 사용(Using)
4. 회수(Reclaiming)

### 1. 프로비저닝(Provisioning)

- `정적(static)` 또는 `동적(dynamic)`의 `pv`를 **생성**하는 단계 (`pv` 생성이라는 단어를 `프로비저닝`이라 불러도 같은 의미)
- `PV생성`이 정상적으로 처리되면 `Available` 상태가 됨
- `정적 프로비저닝(Static Provisioning)` : 매니페스트 파일 등을 통해 특정 용량을 가진 `PV`를 **미리 생성해**, **요청이 있을 시** 미리 생성한 `PV를 할당`해 사용
- `동적 프로비저닝(Dynamic Provisioning)` :  사용자가 **요청할 때** `PV`**를 생성**하여 할당하고, **사용자는 원하는 만큼의 용량을 생성해** 자유롭게 **사용**

### 2. 바인딩(Binding)

- `PV`를 `PVC`에 연결하는 단계
- `PVC` 는 사용자가 요청하는 볼륨을 `PV`에 요청하고 `PV`는 그에 맞는 볼륨이 있으면 할당.
→ 만약 `PVC`가 요청하는 볼륨이 `PV`에 없다면 해당 요청은 무한정 남아있음.
→ 해당 요청 볼륨이 `PV`에 생성되면 그 요청은 받아들여져 할당

### 3. 사용(Using)

- `Pod` 은 `PVC` 를 볼륨으로 사용
→ 클러스터는 `PVC` 를 확인하여 바인딩된 `PV` 를 찾고 해당 볼륨을 `Pod` 에서 사용할 수 있도록 함
- `Pod` 가 사용중인 `PVC` 를 삭제하려고 하면 `Storage Object in Use Protection` 에 의해 삭제되지 않음.
→ 삭제 요청을 하였다면 `Pod`가 `PVC` 사용하지 않을때까지 삭제 요청이 연기됨

### 4. 회수(Reclaiming)

- `PV` 는 기존에 사용했던 `PVC` 가 아니더라도 다른 `PVC` 로 재활용이 가능
- 사용 종료된 `PVC` 를 삭제할 때, 사용했던 `PV` 의 **데이터를 어떻게 처리할지 설정**
    - `Retain` : `PV` 의 데이터를 그대로 보존
    - `Recycle` : 재사용하게될 경우 기존의 `PV` 데이터들을 모두 삭제 후 재사용
    - `Delete` : 사용이 종료되면 해당 볼륨을 삭제

`test_pv.yml`

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: dev-pv
spec:
  capacity:
    storage: 2Gi
  volumeMode: Filesystem
  accessModes:
  - ReadWriteOnce
  storageClassName: manual
  persistentVolumeReclaimPolicy: Retain
  hostPath:
    path: /home/zerooneai/220111_test_pv
```

- `spec.capacity.storage` : 사용할 용량 설정
- `spec.volumeMode` : 볼륨을 `Filesystem` 으로 사용
- `spec.accessModes` : 특정 접근 모드 선택
    - `ReadWriteOnce` : 하나의 Pod 노드에서만 읽고 쓸 수 있음
    - `ReadOnlyMany` : 여러개의 Pod 노드에서 읽을 수 있음
    - `ReadWriteMany` : 여러개의 Pod 노드에서 읽고 쓸 수 있음.
- `spec.storageClassName` : 스토리지 클래스를 지정하고 해당 클래스에 맞는 `PVC`와 연결 할 수 있음
- `spec.persistentVolumeReclaimPolicy` : 회수 단계에서 설명한 설정
- `hostPath.path` : 노드 저장되는 디렉토리 설정

```bash
kubectl apply -f test_pv.yml

### PV확인
kubectl get pv
```

![Untitled](PV%20PVC%206ba5a/Untitled%201.png)

- `Available` 상태임을 알 수 있음

`test_pvc.yml`

```bash
kind: PersistentVolumeClaim
apiVersion: v1 
metadata:
  name: dev-pvc
spec:
  accessModes: 
  - ReadWriteOnce
  volumeMode: Filesystem
  resources:
    requests:
      storage: 2Gi
  storageClassName: manual
```

`kubectl apply -f test_pvc.yml`

- 위 `yml` 파일 실행

`kubectl get pv,pvc`

![Untitled](PV%20PVC%206ba5a/Untitled%202.png)

- `Bound` 됨을 알 수 있음

`pvc_deploy.yml`

```bash
apiVersion: apps/v1
kind: Deployment
metadata:
  name: test-deployment
  labels:
    app: test-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: test-deployment
  template:
    metadata:
      labels:
        app: test-deployment
    spec:
      containers:
      - name: test-deployment
        image: nginx
        ports:
        - containerPort: 8080
        volumeMounts:
        - mountPath: "/var/log/test.log"
          name: dev-volume
      volumes:
      - name: dev-volume
        persistentVolumeClaim:
          claimName: dev-pvc
```

- `spec.template.spec.containers.volumeMounts.mountPath` : 볼륨 마운트할 **`컨테이너` 안의 경로**
- `spec.template.spec.containers.volumeMounts.name` : 경로를 저장한 **볼륨 마운트 이름**
- `spec.template.spec.volumes.name` : `컨테이너`에서 사용할 **볼륨 마운트 이름**
- `spec.template.spec.volumes.persistentVolumeClaim.claimName` : 연결 요청을 보낼 **pvc 이름**

`kubectl apply -f pvc_deploy.yml`

`kubectl get deployment`

![Untitled](PV%20PVC%206ba5a/Untitled%203.png)

`kubectl get pod`

![Untitled](PV%20PVC%206ba5a/Untitled%204.png)

`kubectl exec -it test-deployment-55f656f99-fvj82 -- bash`

![Untitled](PV%20PVC%206ba5a/Untitled%205.png)

`/var/log/test.log` `directory` 확인

![Untitled](PV%20PVC%206ba5a/Untitled%206.png)

- 해당 `test.log` `directory` 안에 `test_220111` 파일 생성

![Untitled](PV%20PVC%206ba5a/Untitled%207.png)

- 이때 생성된 `Pod` 의 정보를 보면 `Node` 가 `3번`서버에 할당 된것을 알 수 있음

- `3번` 서버에 접속해서 확인

![Untitled](PV%20PVC%206ba5a/Untitled%208.png)

`kubectl get pod test-deployment-55f656f99-fvj82 -o wide`

- `-o` : output
- `wide` : 어디로 할당 되었는지
- `yaml` : yaml 내용 보고싶은 경우

![Untitled](PV%20PVC%206ba5a/Untitled%209.png)

---

### PV 없이 스토리지 할당하는 법

`기존pvc.yml` 

```bash
kind: PersistentVolumeClaim
apiVersion: v1 
metadata:
  name: dev-pvc
spec:
  accessModes: 
  - ReadWriteOnce
  volumeMode: Filesystem
  resources:
    requests:
      storage: 2Gi
  storageClassName: manual
```

`변경된 pvc.yml`

```bash
kind: PersistentVolumeClaim
apiVersion: v1 
metadata:
  name: dev-pvc
spec:
  accessModes: 
  - ReadWriteOnce
  volumeMode: Filesystem
  resources:
    requests:
      storage: 2Gi
```

- `storageClassName` 만 제거해주면 아래와 같이 6번 서버에 할당됨

`NFS 서버의 디렉토리`

![Untitled](PV%20PVC%206ba5a/Untitled%2010.png)

- `<namespace>-<pvc name>-<pv name>`