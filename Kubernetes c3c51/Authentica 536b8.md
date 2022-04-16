# Authentication -  X509 Client Certs, kubeconfig, service account

[Authentication](https://kubetm.github.io/k8s/07-intermediate-basic-resource/authentication/)

### Kubernetes API

![Untitled](Authentica%20536b8/Untitled.png)

- Master 서버에 K8s API Server 가 있음
→ Kubectl 을 통해 명령을 내리는 것도 K8s API Server 에 접근해서 명령어를 보내는 것임
- 만약 외부에서 접근하려면 **인증서**가 있을 경우
→ https 로 접근이 가능하지만
- 그렇지 않은 경우
→ Proxy 를 열어주어서 그쪽으로 http 로 접근 가능
- 또는 Config 를 가지고 있다면 외부에서도 K8s Cluster 에 접근해 명령을 내릴 수 있음

---

**이 윗 부분들을 `USER Account` 라고 부름**

- Pod 입장에서 모든 Pod 가 마음대로 K8s API Server 에 접근이 가능하다면 Pod를 통해 모두가 접근해 보안상 문제가 될 수 있음
**→ 이런 문제를 해결하기 위해 `Service Account` 를 통해 Pod 들이 K8s API Server 에 접근하는 것을 통제할 수 있음**

**이 부분을 `Service Account` 라 부름**

- `Service Account` 도 외부에서 K8s API 에 접근할 수 있도록 하는 방법이 존재
→ `Authentication`
- 특정 Namespace에 접근이 가능해도 필요한 자원을 조회할 수 있는 접근 권한이 존재
→ `Authorization`

---

## Authentication 3가지 방법

- Config 를 가져오는 방법

### 1. X509 Client Certs

![Untitled](Authentica%20536b8/Untitled%201.png)

- K8s 설치시에 이 Cluster에 접근할 수 있는 Config 인증서 파일들이 존재
    - `CA crt`
    - `Client crt`
    - `Client key`
- 순서
    1. CA Key, Client key 생성
    2. 1에서 생성한 key 를 이용해 csr 요청서 작성
    3. 2에서 생성한 csr 을 이용해 `CA crt` 라는 인증서를 만듬
    4. CA key, CA crt, Client csr 을 이용해 `Client crt` 생성
- 또는 Kubectl 에서 kubeconfig 를 직접 Kubectl 에서 사용하도록 복사하는 과정도 있음

### Kubectl

![Untitled](Authentica%20536b8/Untitled%202.png)

- 외부에 kubectl 을 설치에 멀티 Cluster 에 접근하는 방법
- 사전에 생성된 Kubeconfig 파일이 내 local pc 에도 있어야 함

- 해당 내용 확인 및 로컬 pc 에 설정하는 방법
    - `/etc/kubernetes/admin.conf` 를 복사해서 config 로 저장
    - 저장한 config 파일을 `~/.kube/config` 로 저장
    

### Service Account

![Untitled](Authentica%20536b8/Untitled%203.png)