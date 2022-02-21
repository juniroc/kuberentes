# 2 week : Pod, Namespace

- `MiniKube` 로 실습을 하지 않는 추세
→ 노드 하나만 있으므로, 어떻게든 2개이 상의 노드를 이용
- `master` node 에 꼭 설치되어야하는 것들
→ `APIServer` , `ETCD` , `Kube-scheduler` 등..

![Untitled](2%20week%20Pod%2053700/Untitled.png)

---

### Env

![Untitled](2%20week%20Pod%2053700/Untitled%201.png)

![Untitled](2%20week%20Pod%2053700/Untitled%202.png)

- `yaml` 같은 파일로 `Template`, `Script` 등으로 설정을 지정해둔 방식
- `Teraform` 이 나오기 전에는 Vagrant 가 많이 쓰였음

### `Ansible` , `Teraform` 알아보기

- `Ansible` : Ansible은 오픈소스 IT 자동화 툴로서, `프로비저닝`, `구성 관리`, `애플리케이션 배포`, `오케스트레이션`, 기타 여러 가지 수동 IT 프로세스를 자동화
- `Teraform` :
    - 테라폼은 프로그램 **코드를 통해 인프라 서버를 구축/운영** 할 수 있게 해주는 **오픈 소스 소프트웨어** 입니다.
    - 코드형 인프라를 뜻하는 `IaC(Infra as a code)` 도구(Tool)

![Untitled](2%20week%20Pod%2053700/Untitled%203.png)

![Untitled](2%20week%20Pod%2053700/Untitled%204.png)

- 내꺼 공유기

![Untitled](2%20week%20Pod%2053700/Untitled%205.png)

![Untitled](2%20week%20Pod%2053700/Untitled%206.png)

![Untitled](2%20week%20Pod%2053700/Untitled%207.png)

![Untitled](2%20week%20Pod%2053700/Untitled%208.png)

![Untitled](2%20week%20Pod%2053700/Untitled%209.png)

- `etcd` 가 죽으면 `k8s` 가 제대로 작동하지 않음
- `etcd` : 분산된 시스템 또는 클러스터의 설정 공유, 서비스 검색 및 스케줄러 조정을 위한 일관된 오픈소스, 분산형 키-값 저장소 (**k8s 기본데이터 저장소 정도로 생각**) `etcd`는 더욱 안전한 자동 업데이트를 지원하고, 호스트에 스케줄링되는 작업을 조정하며, 컨테이너에 대한 오버레이 네트워킹 설정을 지원

![Untitled](2%20week%20Pod%2053700/Untitled%2010.png)

---

### POD

- `Pod` : 쿠버네티스 최소 관리 단위이므로 `Container`가 아닌 `Pod` 단위로 관리
    - 보통 1개가 권장 사항인데 → n 개가 붙는 경우도 있음 (log 쌓는 용도 등)
    - 리소스(pod or ns 등) 생성은 yaml, json 등을 이용
- K8s 에서 가장 중요한 것은 `API Server`
- `Spec` : 상세 정보를 넣어주는 것

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: node-web
  labels:
    creation_method: manual
    env: stage
spec:
  containers:
  - image: whatwant/node-web:1.0
    name: node-web
    ports:
    - containerPort: 8080
      protocol: TCP
```

- `Containers`, `Ports` 등 s 를 붙여 복수 처리해주어야 함 (2개 이상 올 수 있음)

![Untitled](2%20week%20Pod%2053700/Untitled%2011.png)

![Untitled](2%20week%20Pod%2053700/Untitled%2012.png)

- `Label` 은 중요하지만 `Option` 임

![Untitled](2%20week%20Pod%2053700/Untitled%2013.png)

![Untitled](2%20week%20Pod%2053700/Untitled%2014.png)

![Untitled](2%20week%20Pod%2053700/Untitled%2015.png)