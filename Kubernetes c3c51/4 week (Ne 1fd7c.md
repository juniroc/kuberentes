# 4 week (Network, Ingress, LoadBalancer, livenessProbe, readinessProbe)

![Untitled](4%20week%20(Ne%201fd7c/Untitled.png)

- `FQDN`은 '**절대 도메인 네임**' 또는 '**전체 도메인 네임**' 
→ `도메인 전체 이름을 표기`하는 방식

![Untitled](4%20week%20(Ne%201fd7c/Untitled%201.png)

- `K8s` 의 자체 `DNS`

![Untitled](4%20week%20(Ne%201fd7c/Untitled%202.png)

![Untitled](4%20week%20(Ne%201fd7c/Untitled%203.png)

- `pod` 안에서 접근시도하면 결과가 나옴

- `Pod` 끼리는 `K8s` 내부에서는 모두 접근 가능 (서로의 IP 를 알고 있음)

![Untitled](4%20week%20(Ne%201fd7c/Untitled%204.png)

- `service name` . `namespace name` . `scv.cluster.local`
- 위의 경우를 따져보면 `http:// svc-node`. `default` . `svc.cluster.local` 이렇게 작성 가능
    - service name 까지만
    - namespace 까지만
    - svc 까지만 써도 가능한데
    - 그 이후로는 안됨

![Untitled](4%20week%20(Ne%201fd7c/Untitled%205.png)

![Untitled](4%20week%20(Ne%201fd7c/Untitled%206.png)

### Service

![Untitled](4%20week%20(Ne%201fd7c/Untitled%207.png)

- `app=node-web` 이라는 포멧으로 나옴
- `Endpoints` : replicas로 인해 생성된 Pod의 IP
→ 실제로 일하는 것은 Endpoints
- `service`가 연결해주는건 실시간은 아님

![Untitled](4%20week%20(Ne%201fd7c/Untitled%208.png)

- `Endpoint` 도 yaml 을 통해 생성할 수 있음
    
    → `IP` 와 `ports.port` 만 주면 됨
    
- 그러나 잘 안쓰임 (Endpoints 도 하나의 리소스 라는 것으로 알면 될 것)
    
    → IP 열어주면 외부에서 접근도 가능
    
- `service` 와 `Endpoints` 명칭 일치 필요

![Untitled](4%20week%20(Ne%201fd7c/Untitled%209.png)

- 생성된 모든 `Pod` 에 연결하고 싶은 경우 
→ `같은 Service` 에 묶인 `다른 Pod` 와 `통신`할 경우 (모든 주소를 알기 어려우므로)
- `CLUSTER-IP` : `None` 으로 출력됨

- 그러면 어떻게 호출하는가??
    
    → `FQDN`
    

![Untitled](4%20week%20(Ne%201fd7c/Untitled%2010.png)

- `nslookup` 이 들어있는 파드를 띄우고 `headless` 검색

### Ingress

- 실제서비스는 `Ingress` 를 이용해서 진행함

![Untitled](4%20week%20(Ne%201fd7c/Untitled%2011.png)

![Untitled](4%20week%20(Ne%201fd7c/Untitled%2012.png)

- `하나의 IP 주소`로 `수십 개의 서비스에 접근`이 가능하도록 지원해줌

---

![Untitled](4%20week%20(Ne%201fd7c/Untitled%2013.png)

- `Service` 를 `배분`해주는 역할을 한다 정도로 알면 됨
- `Core 기술`은 **아니어서** 설치에 따라서 없는 경우가 있음

![Untitled](4%20week%20(Ne%201fd7c/Untitled%2014.png)

![Untitled](4%20week%20(Ne%201fd7c/Untitled%2015.png)

![Untitled](4%20week%20(Ne%201fd7c/Untitled%2016.png)

![Untitled](4%20week%20(Ne%201fd7c/Untitled%2017.png)

![Untitled](4%20week%20(Ne%201fd7c/Untitled%2018.png)

![Untitled](4%20week%20(Ne%201fd7c/Untitled%2019.png)

![Untitled](4%20week%20(Ne%201fd7c/Untitled%2020.png)

![Untitled](4%20week%20(Ne%201fd7c/Untitled%2021.png)

![Untitled](4%20week%20(Ne%201fd7c/Untitled%2022.png)

- `ip:port/foo` 면 왼쪽 `ip:port/bar` 면 오른쪽

![Untitled](4%20week%20(Ne%201fd7c/Untitled%2023.png)

![Untitled](4%20week%20(Ne%201fd7c/Untitled%2024.png)

- 위에서 나온 `ingress ip:port` 와 `/path` 를 입력하면 접근 가능

### Node Port / LoadBalancer / Ingress 는 동급인가?

→ 아니다. `Ingress` 는 `NodePort` 나 `LoadBalancer` 를 이용해 **외부와의 연결고리**를 만드는 것

---

<break time>

---

`Loadbalancer`는 서비스에 대한 `네트워크 트래픽을 분산`해 **서버의 부하를 경감**

![Untitled](4%20week%20(Ne%201fd7c/Untitled%2025.png)

![Untitled](4%20week%20(Ne%201fd7c/Untitled%2026.png)

### MetalLB - Loadbalancer

![Untitled](4%20week%20(Ne%201fd7c/Untitled%2027.png)

![Untitled](4%20week%20(Ne%201fd7c/Untitled%2028.png)

- metalLB 가 IP 할당해줌

![Untitled](4%20week%20(Ne%201fd7c/Untitled%2029.png)

![Untitled](4%20week%20(Ne%201fd7c/Untitled%2030.png)

![Untitled](4%20week%20(Ne%201fd7c/Untitled%2031.png)

![Untitled](4%20week%20(Ne%201fd7c/Untitled%2032.png)

![Untitled](4%20week%20(Ne%201fd7c/Untitled%2033.png)

![Untitled](4%20week%20(Ne%201fd7c/Untitled%2034.png)

- `MetalLB` 가 쓰고싶은 `IP 대역대를 설정`해주면 됨
- 만약 `10 개정도의 대역대`만 주었는데 `11개 이상의 서비스`를 요청하면 `에러` 발생함

---

`Protocol`에 따라 종류 나눠씀

- `Layer 2 configuration`  보통 이걸로 쓰면 편함 `L2`
- `BGP configuration`
- `Advanced configuration`

![Untitled](4%20week%20(Ne%201fd7c/Untitled%2035.png)

- `type` 에 `LoadBalancer` 띄우고 하면 됨
- 자동으로 대역대에 있는 아이피를 `Exeternal-ip` 로 할당함

![Untitled](4%20week%20(Ne%201fd7c/Untitled%2036.png)

- `같은 IP` 로 접근했는데 `다른 pod` 가 **반응**한다.
    
    → 세션이 유지가 안될 수 있음
    → 계속 하나의 Pod 가 반응하도록 하는 것이 `SessionAffinity`
    

![Untitled](4%20week%20(Ne%201fd7c/Untitled%2037.png)

- 뒤에 sessionAffinity 를 붙여주면 됨 (3600초 동안 유지)
→ 뒤에 config 없으면 default 따로 있긴 함.

![Untitled](4%20week%20(Ne%201fd7c/Untitled%2038.png)

![Untitled](4%20week%20(Ne%201fd7c/Untitled%2039.png)

`ExternalTrafficPolicy` : `Local`

- 특정 Node로 들어오게 되면, 해당 Node 에 있는 Pod를 선택하도록 함
→ 그러나 해당 Node 에 파드가 없으면 다른 Node 로 접근

![Untitled](4%20week%20(Ne%201fd7c/Untitled%2040.png)

- Loadbalancer 가 균등하게 배분하지 않을 수 있음

![Untitled](4%20week%20(Ne%201fd7c/Untitled%2041.png)

- Pod가 잘 살아있는지 어떻게 검사하지?
    
    → `Pod가 죽은 것`과 `서비스 기능을 못하는 것`은 **다른 의미** 
    
    - 그러나 **K8s** 는 이것을 어떻게 알 수 있을까
- 그래서 나온 것이 `livenessProbe`
- 상용 서비스에서 엄청 쓰임
- `httpGet` : http로 접근해서 return 주는 것으로 체크할 것이다

![Untitled](4%20week%20(Ne%201fd7c/Untitled%2042.png)

![Untitled](4%20week%20(Ne%201fd7c/Untitled%2043.png)

![Untitled](4%20week%20(Ne%201fd7c/Untitled%2044.png)

- 서비스를 제대로하고 있는지 확인하기 위해
    
    → [`github.com/status`](http://github.com/status) 식으로 생성해서 만들어 둠
    
    → Pod 가 아닌 service 의 정상 작동 여부를 보기 위해
    

![Untitled](4%20week%20(Ne%201fd7c/Untitled%2045.png)

![Untitled](4%20week%20(Ne%201fd7c/Untitled%2046.png)

`readinessProbe`

- service 할 준비 되었다는 것을 알려주는 `Probe`
- 응답이 실패해도 `Pod` 를 `restart` 하지 않음

![Untitled](4%20week%20(Ne%201fd7c/Untitled%2047.png)

- 쓸때는 `Containers` 내부에 써야함.
- 여기서는 `exec` 를 이용함

![Untitled](4%20week%20(Ne%201fd7c/Untitled%2048.png)