# QoS, Node Scheduling

[Node Scheduling](https://kubetm.github.io/k8s/06-intermediate-pod/nodescheduling/)

### QoS classes

![Untitled](QoS,%20Node%20%209beaa/Untitled.png)

- 위와 같은 경우 어떤것을 내리고 어떤것을 올려야하나?
→ 그러한 우선순위를 정해주는 것이 `QoS`
    1. Guaranteed 
    2. Burstable
    3. BestEffort 
    - 1번이 제일 마지막에 다운됨 (제일 중요하다 판단)

![Untitled](QoS,%20Node%20%209beaa/Untitled%201.png)

- Guaranteed 의 조건
    - 모든 Container 에 `Request` 와 `Limit` 가 설정
    - 그 `Request` 와 `Limit` 에 `Memory` 와 `CPU` 가 설정
    - 각 `Container` 내에 `Memory` 와 `CPU` 의 `Request` 와 `Limit` 값이 같음

- Burstable
    - 일부 Container 에만 설정 되어있거나
    - 설정되어있어도 설정 값이 다른 경우
    - 이때, Burstable Pod 내부 Container 사이에서도 메모리 사용 비중에 따라 우선적으로 삭제되는 것이 다름

![Untitled](QoS,%20Node%20%209beaa/Untitled%202.png)

![Untitled](QoS,%20Node%20%209beaa/Untitled%203.png)

- 위 그림에서 같은 memory 를 사용해도 Request 가 더 작은 쪽이 먼저 삭제됨
→ 즉 Usage % 가 더 높은 Container가 우선적으로 삭제됨

- BestEffort
    - 모든 Container 에 Request 와 Limit 미설정
    

---

### Node Scheduling

![Untitled](QoS,%20Node%20%209beaa/Untitled%204.png)

- NodeName, NodeSelector, NodeAffinity 는 모두 Pod 가 특정 노드에 올라가도록 설정하는 값
    - NodeName : 실제로 노드가 추가 및 삭제 될 경우 노드 이름이 바뀌는 경우도 있어 사실상 많이 쓰이진 않음
    - NodeSelector : key-value 에 매칭되는 것에만 할당
    - NodeAffinity : 조건을 주고 자원이 많은 곳에 할당
    

![Untitled](QoS,%20Node%20%209beaa/Untitled%205.png)

- Pod Affinity : 만약 PV 를 HostPath (사실 Local을 의미하는 듯)로 할 경우 같은 Node 내부에 할당이 되어야 함
- Anti-Affinity : 만약 Master 와 Slave 가 따로 있을 경우, 각각 다른 노드에 있어야 백업이 의미가 있으므로
- Toleration / Taint : 특정 노드에는 아무파드나 할당되지 않도록 하기 위해 설정
ex) GPU 서버
    - 이를 쓰기위해서는 Node를 직접 정의해도 안되며, `Toleration`을 달고 요청해야 가능

![Untitled](QoS,%20Node%20%209beaa/Untitled%206.png)

![Untitled](QoS,%20Node%20%209beaa/Untitled%207.png)

![Untitled](QoS,%20Node%20%209beaa/Untitled%208.png)

- 

![Untitled](QoS,%20Node%20%209beaa/Untitled%209.png)

- `Taint` 는 `Node` 에 달아줌
- `Toleration` 을 `Pod` 에 달아주고 `Key:value` 형식으로 포멧을 맞춰줌
    - 단, 이때 `pod` 가 해당 `node` 에 스케쥴링 되도록 `NodeSelector` 를 설정해주는 건 별도
- `NoSchedule` : 일치하는 `Taint` 가 없을 경우 스케쥴이 할당되지 않음
    - 대신 `사전에 Pod` 가 할당된 `Node` 에 `Taint`를 달면 기존 파드는 무시되지 않음
- `PreferNoSchedule` :
- `NoExecute` :  NoSchedule 과 다르게 `사전에 할당된 Pod 도 삭제`됨
    - 하지만 `Toleration` 에 `NoExecute` 가 있으면 삭제가 안되고
    - `tolerationSeconds` 가 있으면 그 시간만큼 지난 후 삭제됨