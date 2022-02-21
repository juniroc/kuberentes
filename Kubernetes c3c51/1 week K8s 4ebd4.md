# 1 week : K8s overview

![Untitled](1%20week%20K8s%204ebd4/Untitled.png)

![Untitled](1%20week%20K8s%204ebd4/Untitled%201.png)

- `OpenShift` - RedHat 계열이며 k8s 와 비슷한 기능을 가짐

![Untitled](1%20week%20K8s%204ebd4/Untitled%202.png)

![Untitled](1%20week%20K8s%204ebd4/Untitled%203.png)

- 위 표에서
    - `Bare Metal` : 생 서버라 생각하면됨
    - `Openstack` : VM 으로 생각하면됨

![Untitled](1%20week%20K8s%204ebd4/Untitled%204.png)

![Untitled](1%20week%20K8s%204ebd4/Untitled%205.png)

- `Managed` : `GKE`, `EKS` 같은거

![Untitled](1%20week%20K8s%204ebd4/Untitled%206.png)

- `v1.22` 이전과 이후 차이를 알아야함

![Untitled](1%20week%20K8s%204ebd4/Untitled%207.png)

- cgroups 이란 무엇인지

![Untitled](1%20week%20K8s%204ebd4/Untitled%208.png)

- 여기서 `namespace` 는 Linux kernel 상에서의 `namespace` 를 말함
- 옆 부서 또는 클러스터간의 `Linux kernel` 을 파티셔닝 하는 것
→ `resources` 를 파티셔닝하는 것은 아님 (Naming 정도)

![Untitled](1%20week%20K8s%204ebd4/Untitled%209.png)

![Untitled](1%20week%20K8s%204ebd4/Untitled%2010.png)

- Overlay2 기반(왼쪽아래사진)
→ 새 파일이나 업로드가 되면 새로운 레이어를 쌓아서 둠
→ 예전꺼로 돌아갈 수 있음
→ snap history 가 많게되면 용량이 엄청커질 수 있음.

![Untitled](1%20week%20K8s%204ebd4/Untitled%2011.png)

- 공통되는 부분이 많으면 위 방식이 맞을 수 있음

![Untitled](1%20week%20K8s%204ebd4/Untitled%2012.png)

- 네트워크 모델도 여러개가 있음
→ 컨테이너 레벨에서는 거의 다 비슷함
→ K8s 까지가게되면 Router 등 까지가게되어, Istio를 배우는 것
- `Endpoint` 는 그냥 container라고 생각하면 됨.

![Untitled](1%20week%20K8s%204ebd4/Untitled%2013.png)

- `Docker CLI` : 명령어를 날리는 곳
- `Daemon` : 명령어를 받는 곳
- `Protocol (Docker CLI - Docker Daemon 사이)` : REST API
- `Container Daemon` : 컨테이너 명령어를 받는 곳
- `Protocol (Docker Daemon - Container Daemon 사이)` : GRPC
- `성능 차이` : 압축된 내용으로 전달할 수 있다 → **(리소스를 효율적으로 사용)**
- **REST 는 주로 사용자가 자주 수정해야하는 경우에만 쓰이고 요즘은 GRPC 가 대세**

→ 좀 더 search 가 필요할 것

- `shim` : Container 를 생성시켜줌
- `runc` : Container 를 실행

![Untitled](1%20week%20K8s%204ebd4/Untitled%2014.png)

- `Container` 에서 `Images` 로 업데이트할 수 있음 - `Commit`

![Untitled](1%20week%20K8s%204ebd4/Untitled%2015.png)

![Untitled](1%20week%20K8s%204ebd4/Untitled%2016.png)

- `alpine` : 경량화된 `OS` 정도로 생각하면됨

![Untitled](1%20week%20K8s%204ebd4/Untitled%2017.png)

![Untitled](1%20week%20K8s%204ebd4/Untitled%2018.png)

- Ubuntu 로 인스턴스 이미지 하나 만들어 둘 것

![Untitled](1%20week%20K8s%204ebd4/Untitled%2019.png)

![Untitled](1%20week%20K8s%204ebd4/Untitled%2020.png)

![Untitled](1%20week%20K8s%204ebd4/Untitled%2021.png)

![Untitled](1%20week%20K8s%204ebd4/Untitled%2022.png)

![Untitled](1%20week%20K8s%204ebd4/Untitled%2023.png)

![Untitled](1%20week%20K8s%204ebd4/Untitled%2024.png)

- 환경이 어려울 경우
- learn 을 누르고 다양한 실습환경 경험해볼 수 있음

![Untitled](1%20week%20K8s%204ebd4/Untitled%2025.png)

![Untitled](1%20week%20K8s%204ebd4/Untitled%2026.png)

![Untitled](1%20week%20K8s%204ebd4/Untitled%2027.png)

![Untitled](1%20week%20K8s%204ebd4/Untitled%2028.png)

- `K8s` 의 러닝커브가 큰편
→ `Managed service` 가 많이 도입되는 편
→ 그럼에도 알아야함.

![Untitled](1%20week%20K8s%204ebd4/Untitled%2029.png)

![Untitled](1%20week%20K8s%204ebd4/Untitled%2030.png)

- 보통 `server` 하나를 `Node` 라고 부름

- 다음주 시작하자마자 클러스터 하나 생성하는거 하고 시작

![Untitled](1%20week%20K8s%204ebd4/Untitled%2031.png)

- CNCF 에 들어오면 금전적 지원등을 받게됨
- 유명한건 대부분 졸업했음
→ 유명한 것들 위주로 공부하면 됨

![Untitled](1%20week%20K8s%204ebd4/Untitled%2032.png)

![Untitled](1%20week%20K8s%204ebd4/Untitled%2033.png)