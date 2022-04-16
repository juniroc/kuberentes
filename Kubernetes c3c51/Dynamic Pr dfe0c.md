# Dynamic Provisioning, Storage Class, Status, ReclaimPolicy

![Untitled](Dynamic%20Pr%20dfe0c/Untitled.png)

- 실제 데이터는 K8s Cluster 와 분리되어 사용됨
→ **K8S 와 Volume 이 분리**됨

### Dynamic Provisioning

- 사용자가 PVC 를 만들면 자동으로 PV 를 생성해 Volume 과 연결
- 각각의 Status 및 Policy 가 존재

![Untitled](Dynamic%20Pr%20dfe0c/Untitled%201.png)

![Untitled](Dynamic%20Pr%20dfe0c/Untitled%202.png)

- 위 사진처럼 StorageClass 이름을 정의하고
    
    → PVC 를 만들 때 위에서 정의한 StorageClassName 을 넣어주면 해당 StorageClass 와 일치하는 Volume 의 PV 생성
    
    → Default 를 정의해주면 안넣어줘도 일치하는 PV 생성
    

![Untitled](Dynamic%20Pr%20dfe0c/Untitled%203.png)

- Pod - PVC - PV 가 연결되어 있을 때
Pod 가 삭제되어도 PVC 는 삭제되지 않아서 `PVC-PV : Bound` 로 유지
- PVC 를 삭제해주어야 PV 가 Released 상태로 바뀜
→ 이때 ReclaimPolicy 에 따라 PV가
    - `Released` 상태
    - `Delete`
    - `Available` 상태가 됨

---

### Practice

[Volume](https://kubetm.github.io/k8s/07-intermediate-basic-resource/volume/)

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: default
  annotations:
    # Default StorageClass로 선택 
    storageclass.kubernetes.io/is-default-class: "true" 
# 동적으로 PV생성시 PersistentVolumeReclaimPolicy 선택 (Default:Delete)
reclaimPolicy: Retain, Delete, Recycle  # 정책 설정
provisioner: kubernetes.io/storageos
# provisioner 종류에 따라 parameters의 하위 내용 다름 
parameters:
```

`StorageClass.yml`

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: default
  annotations: 
    storageclass.kubernetes.io/is-default-class: "true"
provisioner: kubernetes.io/storageos
parameters:
  adminSecretName: storageos-api
  adminSecretNamespace: storageos-operator
  fsType: ext4
  pool: default
```