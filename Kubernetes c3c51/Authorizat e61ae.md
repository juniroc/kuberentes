# Authorization

[Authorization](https://kubetm.github.io/k8s/07-intermediate-basic-resource/authorization/)

### RBAC

![Untitled](Authorizat%20e61ae/Untitled.png)

- 역할 기반 권한 부여하는 방식
- Cluster 단위 또는 Namespace 단위로 관리
- SA 에서 `NameSpace` 단위로 접근하고 싶은 경우
    - RoleBinding 은 Role 은 단 1개만 선택, SA 는 여러개 선택 가능
        - `Role` : 해당 Pod 나 Svc 에 대한 특정 권한
        - 해당 RoleBinding 이 부여된 Service Account 는 해당 권한 부여받음
- SA 에서 `Cluster` 단위로 접근하고 싶은 경우
    - `ClusterRoleBinding` 과 `ClusterRole` 이 있어야 함
    - `ClusterRole` 과 NameSpace에서의 `Role` 의 기능은 같음
        - `ClusterRoleBinding` 을 `SA` 에 부여하면 `Cluster`에 대한 권한도 갖게됨
- `RoleBinding` 을 `ClusterRole` 에 연동을 해주면 해당 `RoleBinding` 이 생성된 `NameSpace` 만 접근이 가능
→ 이 경우는 보통 ns가 많아 전체 Role을 컨트롤하기 힘든 경우에 이용

![Untitled](Authorizat%20e61ae/Untitled%201.png)

---

### Practice

![Untitled](Authorizat%20e61ae/Untitled%202.png)

- 위 사진과 같이 ns 를 만들고 Role 과 RoleBinding 만들기
- 이때 sa 는 따로 만들지 않으면, default 로 생성됨

`role.yml`

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: r-01
  namespace: nm-01
rules:
- apiGroups: [""]
  verbs: ["get", "list"]
  resources: ["pods"]
```

- Role 은 위 처럼 apiVersion 및 rules 생성
- `rbac.authorization.k8s.io` 이 해당 API Group
- Pods 가 core API Group 에 속하기 때문에 apiGroups 는 비워둬도 됨
- `verbs` 의 조건들은 아래 링크에서 참고 가능
• [https://kubernetes.io/docs/reference/access-authn-authz/authorization/#review-your-request-attributes](https://kubernetes.io/docs/reference/access-authn-authz/authorization/#review-your-request-attributes)

`RoleBinding.yml`

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: rb-01
  namespace: nm-01
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: r-01
subjects:
- kind: ServiceAccount
  name: default
  namespace: nm-01
```

- RoleBinding 은 roleRef 와 일치하는 ServiceAccount 를 subjects 로 생성

- 이후 요청시

```yaml
curl -k -H "Authorization: Bearer <TOKEN>" https://192.168.56.30:6443/api/v1/namespaces/nm-01/pods/
```

- Token 은 secret 에서 복사해올 수 있음

---

### ClusterRoleBinding

![Untitled](Authorizat%20e61ae/Untitled%203.png)

`Namespace_crb.yml`

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: nm-02
```

`sa_crb.yml`

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: sa-02
  namespace: nm-02
```

`clusterRole_crb.yml`

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: cr-02
rules:
- apiGroups: ["*"]
  verbs: ["*"]
  resources: ["*"]
```

`ClusterRoleBinding_crb.yml`

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: rb-02
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cr-02
subjects:
- kind: ServiceAccount
  name: sa-02
  namespace: nm-02
```