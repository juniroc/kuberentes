# Headless, Endpoint, ExternalName

![Untitled](Headless,%20%20d8b5d/Untitled.png)

- 클러스터 내에 있는 노드들은 Cluster IP 를 통해 service 에 접근이 가능하지만 
→ 그렇지 않은 경우 : `NodePort` 를 열어주어서 해당 포드로 클러스터 내 노드에 접근해  service 에 접근하는 방식을 이용
- 또는 그외의 플랫폼 등을 이용했을 경우

![Untitled](Headless,%20%20d8b5d/Untitled%201.png)

- 위와같이 `NodeBalancer` 를 이용해 접근

---

- 위 까지는 이전에 다루었던 범위

### Pod 의 입장에서 원하는 Pod 및 Service 에 접근하는 방식

![Untitled](Headless,%20%20d8b5d/Untitled%202.png)

- **사용자 입장에서 생각할 것**
    - 특정 Pod 에서 클러스터 내부 모든 Pod 나 Service 에 요청을 보낼 경우
    - **Service 를 통하지 않고, Headless 만 생성**
- 위와 같이 동시에 배포될 경우 Pod 가 동시에 할당되므로 `Pod A` 는 `Pod B` 에 대한 IP 정보를 알 수 없음
+ `Pod B` 가 죽으면 재생성되며 IP 가 변경
- 그래서 필요한 게 `Headless` 와 `DNS Server`

![Untitled](Headless,%20%20d8b5d/Untitled%203.png)

- 만약 특정 사이트에서 정보를 가져와야 하는 상황에서 경로를 바꾸면 재배포해야하는지?
→ `ExternalName` 으로 해결
→ 내부의 연결을 재배포 없이 변경 가능

### Pod 에서 cluster 바깥의 노드나 외부 네트워크 연결 할 경우

![Untitled](Headless,%20%20d8b5d/Untitled%204.png)

- DNS Server 를 통해 찾아감
→ 해당 DNS 에 없으면 상위 DNS 를 타고 들어가는 방식

![Untitled](Headless,%20%20d8b5d/Untitled%205.png)

- headless 를 이용하게 될 경우 DNS 에 `Pod1.service2` , `Pod2.service2` 와 같이 생성되어 상위 DNS 를 타고 들어가는 방식으로 찾음
    - Pod1 과 Pod2 의 연결은 자연스럽게 해결
    → IP 주소 필요없이 해당 도메인 이름으로 찾을 수 있음

![Untitled](Headless,%20%20d8b5d/Untitled%206.png)

- `ExternalName` 을 등록해 놓으면 Pod 는 위와같이 `Service3` 에 접근해 해당 네트워크의 정보를 가져올 수 있음

![Untitled](Headless,%20%20d8b5d/Untitled%207.png)

- 기존 ClusterIP

![Untitled](Headless,%20%20d8b5d/Untitled%208.png)

- 자세히 보면 위와같이 DNS 에 생성되고
- 이를 `**FQDN : Fully Qualified Domain Name**` 이라 부름
    - Pod 이름이 아닌 **Pod 의 IP** 로 DNS 에 생성됨

![Untitled](Headless,%20%20d8b5d/Untitled%209.png)

- Headless 방식
- Service 에서 `ClusterIP : None` 으로 체크
- Pod 를 만들때 `hostname` 에 **Pod 이름과** 과 `subdomain` 에 **Service name** 을 넣어주어야함

![Untitled](Headless,%20%20d8b5d/Untitled%2010.png)

- Pod 생성시 정의한 이름과 일치하는 headless 를 위한 정보가 DNS 에 추가됨

### Endpoint

![Untitled](Headless,%20%20d8b5d/Untitled%2011.png)

- more detail

![Untitled](Headless,%20%20d8b5d/Untitled%2012.png)

- 사용자 입장에서는 Pod와 Service 의 label 을 일치시켜주는 방식으로 연결을 하지만, 
→ K8s 입장에서는 Endpoint를 생성하는 방식으로 연결시켜줌
    - 해당 규칙을 알면 Label 와 selector 를 안만들어도 직접 연결이 가능

- Service1 의 이름을 갖는 EndPoint 에는 Pod Ip 들의 정보가 들어있음

![Untitled](Headless,%20%20d8b5d/Untitled%2013.png)

- 이는 label 과 selector 없이
    
    → 직접 Endpoint 를 만들어 주면 됨
    

- 위 `EndPoint` 방법을 이용한 것이 `ExternalName`

![Untitled](Headless,%20%20d8b5d/Untitled%2014.png)

- Service 에 `ExternalName` 을 달아서 만들어줌
→ `DNS cache` 가 내부/외부 DNS 를 찾아서 IP 를 알아냄
    - 그래서 Pod 에서 Service 를 가리키고만 있으면 Service 에서 필요시마다 해당 필요한 도메인 주소를 변경할 수 있어서 Pod 는 가리키고만 있으면 됨
    → 애초에 `ExternalName` 의 목적은 다른 도메인 주소에서 데이터를 가져오거나 할 경우에 사용되므로

---

# Practice

### Headless

`Headless.yml`

```yaml
apiVersion: v1
kind: Service
metadata:
  name: headless1
spec:
  selector:
    svc: headless
  ports:
    - port: 80
      targetPort: 8080    
  clusterIP: None  ## 이부분만 추가해주면됨
```

- 똑같은 service yaml 파일에  `clusterIP : None` 으로 설정해주면 **Headless 로 생성**됨

- 여기서 label 들(selector, metadata)은 서로 일치시켜주어야함 (pod 와 headless 사이에서)

`pod_headless.yml`

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod4
  labels:
    svc: headless
spec:
  hostname: pod-a
  subdomain: headless1
  containers:
  - name: container
    image: kubetm/app
```

- `hostname` , `subdomain` 을 설정해주어야 함
    - `hostname` 은 DNS 에 설정될 이름
    - `subdomain` 은 위에서 설정한 headless 이름

- 아래 명령어를 통해 DNS 확인 가능
    - `nslookup headless1`
    - `nslookup pod-a.headless1`
    - `nslookup pod-b.headless1`
- 해당 pod 내에서 접근
    - curl pod-a.headless1:8080/hostname
    - curl pod-b.headless1:8080/hostname

---

### EndPoint

`service_ep.yml`

```yaml
apiVersion: v1
kind: Service
metadata:
  name: endpoint1
spec:
  selector:
    svc: endpoint
  ports:
  - port: 8080
```

- endpoint라는 이름의 service 생성

`pod_ep.yml`

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod7
  labels:
    svc: endpoint
spec:
  containers:
  - name: container
    image: kubetm/app
```

- 위 Endpoint 에 연결하는 Pod 생성

`kubectl describe endpoints endpoint1` 을 통해 해당 엔드포인트의 데한 정보를 알 수 있음
→ 어떤 `Pod 가 연결`되어있는지, `Port` 등 조회 가능

- 즉, 위와 같이 Service 를 만들면 **EndPoint 가 자동으로 생성**됨

- 그렇다면 `Endpoint` 직접 생성하는 방법?

`service_ep_2.yml`

```yaml
apiVersion: v1
kind: Service
metadata:
  name: endpoint2
spec:
  ports:
  - port: 8080
```

- 기타 라벨 없이 생성

`pod_ep_2.yml`

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod9
spec:
  containers:
  - name: container
    image: kubetm/app
```

- Pod 또한 라벨을 넣지 않고 만듬

`endpoint_for_2.yml`

```yaml
apiVersion: v1
kind: Endpoints
metadata:
  name: endpoint2
subsets:
 - addresses:
   - ip: <Pod IP> # ex) 20.109.5.12
   ports:
   - port: 8080
```

- Endpoint 를 따로 생성해주는데,
- 이름은 Service 이름과 동일하게 생성
- Ip 는 Pod 의 Ip 확인 후 넣어줌

- 이후 Service 에 요청을 날리면 파드에 요청이 닿는것을 확인 가능

`curl endpoint2:8080/hostname`

- 여기서 해당 Endpoint Ip 에 외부 링크를 넣어주면 해당 IP 에 접근이 가능

---

### ExternalName

`external_svc.yml`

```yaml
apiVersion: v1
kind: Service
metadata:
 name: externalname1
spec:
 type: ExternalName
 externalName: github.github.io
```

- `externalname` 에 연결하려는 Domain 주소를 넣어주면 됨

![Untitled](Headless,%20%20d8b5d/Untitled%2015.png)

![Untitled](Headless,%20%20d8b5d/Untitled%2016.png)