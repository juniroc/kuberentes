# ConfigMap, Secret

### 개발환경과 상용환경

![Untitled](ConfigMap,%208bc43/Untitled.png)

- 위와 같이 개발환경과 상용환경에 각각 다른 SSH 설정, User, Key 값을 사용할 때, 이는 컨테이너 이미지를 각각 관리한다는 것과 같은 원리
    
    → 이는 이미지 크기가 클 경우 큰 자원낭비가 될 수 있음
    
    → 그래서 나온게 `ConfigMap` 과 `Secret`
    

 

![Untitled](ConfigMap,%208bc43/Untitled%201.png)

![Untitled](ConfigMap,%208bc43/Untitled%202.png)

- 즉 각각의 `Config` 와 `Secret` 를 할당해주는 것

---

### 1. Yaml 로 정의

![Untitled](ConfigMap,%208bc43/Untitled%203.png)

- 둘다 `key:value` 로 정의
- 단, `Secret` 은 PW 를 넣을 때 `Base64` 로 **인코딩**해서 넣어주어야함 
→ `Pod` 주입 될 때는 자동으로 `Decoding` 되어 넣어짐
- `ConfigMap` 은 Key:Value 를 무한히 넣을 수 있지만, 
`Secret` 은 1MB 로 제한

`configmap.yml`

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: cm-dev
data:
  SSH: 'false'  ## string 으로 넣어주어야해서 ' 붙여야함
  User: dev
```

`Secret.yml`

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: sec-dev
data:
  Key: MTIzNA==  ## base64 인코딩이 필요해서 옆과 같은 모습으로 넣어줌
```

`Pod.yml`

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-1
spec:
  containers:
  - name: container
    image: kubetm/init
    envFrom:
    - configMapRef:
        name: cm-dev
    - secretRef:
        name: sec-dev
```

- envFrom의 `configMapRef / SecretRef` 에 `metadata.name` 를 일치시켜주면 됨

![Untitled](ConfigMap,%208bc43/Untitled%204.png)

![Untitled](ConfigMap,%208bc43/Untitled%205.png)

---

### 2. File 로 정의

![Untitled](ConfigMap,%208bc43/Untitled%206.png)

`Configmap.file`

```bash
echo "Content" >> file-c.txt
kubectl create configmap cm-file --from-file=./file-c.txt
```

`Secret.file`

```bash
echo "Content" >> file-s.txt
kubectl create secret generic sec-file --from-file=./file-s.txt
```

![Untitled](ConfigMap,%208bc43/Untitled%207.png)

`pod`

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-file
spec:
  containers:
  - name: container
    image: kubetm/init
    env:
    - name: file-c
      valueFrom:
        configMapKeyRef:
          name: cm-file
          key: file-c.txt
    - name: file-s
      valueFrom:
        secretKeyRef:
          name: sec-file
          key: file-s.txt
```

- `env.name` 을 `file-c` or `file-s`로 정의
- 추가적으로 `configMapKeyRef.name` or `secretKeyRef.name` 정의

![Untitled](ConfigMap,%208bc43/Untitled%208.png)

---

### 3. Volume Mount(file)

![Untitled](ConfigMap,%208bc43/Untitled%209.png)

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-mount
spec:
  containers:
  - name: container
    image: kubetm/init
    volumeMounts:
    - name: file-volume
      mountPath: /mount
  volumes:
  - name: file-volume
    configMap:
      name: cm-file
```

- `volume` 마운트를 한 곳에 `configMap` 을 정의한 파일을 저장
- `volumes.configMap.cm-file`

### 2번 방법과 3번 방법의 큰차이

- 2번의 경우 : 수정했을 때, 넣은 이후 파드가 죽었다 다시 살아날 때 Pod 내용 수정
- 3번의 경우 : mount 한 폴더 내부 파일을 수정하면 Pod 내부의 내용도 수정됨