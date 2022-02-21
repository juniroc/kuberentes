# 5 week : volume - EmptyDir, Hostpath, PV/PVC

### Volume

![Untitled](5%20week%20vol%20f282c/Untitled.png)

 

## EmptyDir

![Untitled](5%20week%20vol%20f282c/Untitled%201.png)

- Pod 안에서 생성된 것이므로 다른 Pod / Node 에서는 참고할 수 없음

![Untitled](5%20week%20vol%20f282c/Untitled%202.png)

- spec 을보면 `Container` 내부에 마운트정보를 입력 한다보면 됨

![Untitled](5%20week%20vol%20f282c/Untitled%203.png)

- 각 컨테이너 내부에 생성된 것을 확인 할 수 있음

![Untitled](5%20week%20vol%20f282c/Untitled%204.png)

- 1번 Container 에서 만든 것도 2번 Container 에서 확인 가능

![Untitled](5%20week%20vol%20f282c/Untitled%205.png)

- `Pod`를 죽이면 `Restart`되고 이때 파일 내용이 모두 사라짐을 알 수 있음

---

## HostPath

![Untitled](5%20week%20vol%20f282c/Untitled%206.png)

- `Node Level` 에서 만든다고 보면 됨
→ 다른 Node 에서는 확인을 못함
- pod 재시작되어도 내용 유지

![Untitled](5%20week%20vol%20f282c/Untitled%207.png)

- `Directory` 없으면 생성하는 등 다양한 조건을 줄 수 있음

![Untitled](5%20week%20vol%20f282c/Untitled%208.png)

- `Container` 는 `host` 와 공유하는게 많아서 Container 는 보안이 취약한 편(host 가 뚫릴 가능성이 높음)
- `Container` 요즘 트렌드 : `Rootless` → **Root 권한**이 **없는 Container**

![Untitled](5%20week%20vol%20f282c/Untitled%209.png)

![Untitled](5%20week%20vol%20f282c/Untitled%2010.png)

![Untitled](5%20week%20vol%20f282c/Untitled%2011.png)

![Untitled](5%20week%20vol%20f282c/Untitled%2012.png)

## PV / PVC

![Untitled](5%20week%20vol%20f282c/Untitled%2013.png)

- PVC : 일종의 주문서 라고 보면됨
- PV : 요리
- H/W storage : 식재료
    
    → 즉 PVC 의 요청을 통해 H/W storage 를 PV 로 생성해줌
    

![Untitled](5%20week%20vol%20f282c/Untitled%2014.png)

- **PVC 로 요청**을 넣으면 **기존의 PV 가 있을 경우 연결**해줌
- 물리적인 **Storage 와 PV 는 관리자만** 알면되고, **손님(사용자)은 PVC 까지만** 알면 됨
    - 그래서 위에 그림보면 `Admin` 과 `User` 를 분리

---

## PV / PVC - 1. Static Provisioning

![Untitled](5%20week%20vol%20f282c/Untitled%2015.png)

- 물리적인 스토리지가 있으면 PV 를 미리 만들어 놓고 PVC 가지고 주문하면 POD 에 붙여준다.

### 그럼 해야할 것은 무엇인가?

- `PV`를 먼저 만들어놔야 함.

![Untitled](5%20week%20vol%20f282c/Untitled%2016.png)

- `storageClassName` 은 공백으로 놔도됨
- `PersistentVolumeReclaimPolicy` : `Retain` / `Delete` / `Recycle` → 삭제할 때의 정책
- `accessModes` : 노드별 정책

- `PVC` 생성하는 방법

![Untitled](5%20week%20vol%20f282c/Untitled%2017.png)

- `accessModes` 를 맞춰주어야함
- 조건에 맞는 `PV`가 존재할 경우 `Bounding`
- 근데 50Mi을 요청했는데 100Mi 용량짜리가 있어서 그것이 Matching 될 경우 → **낭비 가능성있음**

![Untitled](5%20week%20vol%20f282c/Untitled%2018.png)

![Untitled](5%20week%20vol%20f282c/Untitled%2019.png)

- 용량 그런거 설정 안하고 **`입맛에 맞는 PVC`** 있으면 그것만 선택해서 생성하면됨

---

## PV / PVC - 2. Dynamic Provisioning

- 가장 중요
- **근데 위에서 보면 `PV` 를 미리 만들어 놓는 것도 상당한 Cost 낭비!**
    
    → 그래서 나온게 주문할 경우 그때 만들어 주는 것
    

![Untitled](5%20week%20vol%20f282c/Untitled%2020.png)

- `물리적인 저장공간` 만들기

![Untitled](5%20week%20vol%20f282c/Untitled%2021.png)

- 위에서 `VolumeBindingMode` : 정책 설정
    - `waitForFirstConsumer` : 실제 소비할 때까지 기다리기
- local storage 인 경우 : `no-provisioner` 로 설정

- `PV` 만들기

![Untitled](5%20week%20vol%20f282c/Untitled%2022.png)

- `hostname` 이 `worker1` 인 경우에만 노드 생성하도록 함

- `PVC` 만들기

![Untitled](5%20week%20vol%20f282c/Untitled%2023.png)

- PVC 가 Pending 인 이유 :  `VolumeBindingMode` 정책으로 인해 Pending 상태

![Untitled](5%20week%20vol%20f282c/Untitled%2024.png)

- 이후 **실제로 Volume을 쓸 경우**에 그때 **Bounding 됨
→ Pod 생성이나 그럴 때**

---

# Config

![Untitled](5%20week%20vol%20f282c/Untitled%2025.png)

- 값을 전달하는 방법 중 하나가 `Arguments`로 보내주는 것
→ `Environment Variables`
- 명령어를 내릴 경우는 `command` + `args`

![Untitled](5%20week%20vol%20f282c/Untitled%2026.png)

- `Docker` 에서의 `Arguments`

![Untitled](5%20week%20vol%20f282c/Untitled%2027.png)

- 도커 파일에서는 10초단위로 sleep을 주었는데
→ 다시 `args: [”5”]` 를 통해 5초단위 sleep 으로 덮어씀
→ `CMD` 가 덮어쓰여짐

![Untitled](5%20week%20vol%20f282c/Untitled%2028.png)

---

### ConfigMap

![Untitled](5%20week%20vol%20f282c/Untitled%2029.png)

### configMap 의 3가지 타입

![Untitled](5%20week%20vol%20f282c/Untitled%2030.png)

- 생성 방법 3가지 : `directory`, `file`, `literal`
- 설정 방법 3가지 : `env`, `envFrom`, `volumes`

![Untitled](5%20week%20vol%20f282c/Untitled%2031.png)

- 왼쪽 위 파일을 
→ `envFrom` : 환경변수로 가져오는 방법
→ envFrom 을 통해 받아옴

![Untitled](5%20week%20vol%20f282c/Untitled%2032.png)

![Untitled](5%20week%20vol%20f282c/Untitled%2033.png)

---

### Secret

- 보안을 위한 `Configmap`

![Untitled](5%20week%20vol%20f282c/Untitled%2034.png)

- 저장 방법 : `Password` , `OAuth Token` , `SSH Key`
- `etcd` 에 암호화되어 저장됨

![Untitled](5%20week%20vol%20f282c/Untitled%2035.png)

- Secret 설정 방법 : `env` , `volumes` , `imagePullSecrets`
- Secret 생성 방법 : `from directory` , `from file` , `from literal`

![Untitled](5%20week%20vol%20f282c/Untitled%2036.png)

- 위 Secret 은 `API Server` 와 통신하기 위해 존재

![Untitled](5%20week%20vol%20f282c/Untitled%2037.png)

- `Kubectl run` 을 하면 Pod 를 하나 만듬(간단하게 실험할 경우 이용)

![Untitled](5%20week%20vol%20f282c/Untitled%2038.png)

- `Private` 로 도커 허브에 업로드

![Untitled](5%20week%20vol%20f282c/Untitled%2039.png)

- `Private` 로 만들어서 다음과 같은 에러가 뜸

![Untitled](5%20week%20vol%20f282c/Untitled%2040.png)

- `Private` 파일을 내려받기 위해 Secret 생성하여 정보 넣어줌

![Untitled](5%20week%20vol%20f282c/Untitled%2041.png)

- Secret_type

![Untitled](5%20week%20vol%20f282c/Untitled%2042.png)

- `SideCar Pattern`
    - 기존 기능을 확장 또는 강화 용도로 컨테이너를 추가하는 패턴
    - `기존 컨테이너`는 `원래 목적 기능만` 충실 + `부가 기능`은 `사이드카 컨테이너`를 추가해 사용

![Untitled](5%20week%20vol%20f282c/Untitled%2043.png)

- `ssh` 를 처음에 접속할 경우 `known_host` 에 등록 여부 물어봄
→ 이 과정에서 에러 발생 방지를 위해 미리 등록해 둠.

![Untitled](5%20week%20vol%20f282c/Untitled%2044.png)

![Untitled](5%20week%20vol%20f282c/Untitled%2045.png)

---

### DownwardAPI

![Untitled](5%20week%20vol%20f282c/Untitled%2046.png)

![Untitled](5%20week%20vol%20f282c/Untitled%2047.png)

![Untitled](5%20week%20vol%20f282c/Untitled%2048.png)