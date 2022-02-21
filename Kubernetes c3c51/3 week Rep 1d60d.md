# 3 week : ReplicaSet, DaemonSet, CronJob, Service - ClusterIP, LodePort, LoadBalancer


---

### ReplicaSet, DaemonSet, CronJob

![Untitled](3%20week%20Rep%201d60d/Untitled.png)

클러스터에서 파드 생성시 레플리케이션 컨트롤러가 감지 후 재생성 해줌

### 레플리케이션 컨트롤러의 요소

- 레이블 셀렉터
- 레플리카 수
- 파드 템플릿

- 레플리카 수, 레이블 셀렉터, 파드 템플릿은 수정 가능, 기존에 생성된 파드에 영향을 주지 않음

`--cascade=orphan` 해주면 컨트롤러에서 관리하는 파드에 영향을 주지 않음.

### 레플리카셋(ReplicaSet)

- 이것의 등장으로 레플리케이션 컨트롤러를 완전히 대체함

### DaemonSet 과 ReplicaSet 차이

![Untitled](3%20week%20Rep%201d60d/Untitled%201.png)

- 레플리카셋은 지정 노드에 파드 갯수 맞추기위해 그곳에 생성
- 대몬셋은 모두 1개씩 맞춰주기위해

![Untitled](3%20week%20Rep%201d60d/Untitled%202.png)

### Job 리소스

- 컨테이너 프로세스가 제대로 실행 후 종료되면, 다시 생성되지 않는 컨테이너 생성 가능

![Untitled](3%20week%20Rep%201d60d/Untitled%203.png)

- restartPolicy 문법은 Job 뿐만 아니라 Pod 에서도 사용하는데,
→ OnFailure 는 실패를 했을 때만,
- 쨋든 `Always` 는 Job 에서는 안쓰임

---

### Cron Job

![Untitled](3%20week%20Rep%201d60d/Untitled%204.png)

![Untitled](3%20week%20Rep%201d60d/Untitled%205.png)

- `Replicas`
- `Selector`
- `template`

![Untitled](3%20week%20Rep%201d60d/Untitled%206.png)

- `Operator` 는 포함하거나 포함하지 않거나 등등

![Untitled](3%20week%20Rep%201d60d/Untitled%207.png)

- metadata 에 이름 지정하면 `rs-labels` 라는 이름 뒤에 `-해시 값`이 붙음

![Untitled](3%20week%20Rep%201d60d/Untitled%208.png)

- 기존꺼를 살리는 것이 아닌 새로운 것을 하나 만들어줌
- 웹서비스로써는 무식한 방법..

K8S 는 `시작 트리거 시점만 보증`하고 생성하거나 제거하는데 걸리는 시간에 따라 순서가 명확하지 않음

→ 컨테이너에 따라 생성되거나 제거되는 속도가 다양하므로 그때그때 다름

![Untitled](3%20week%20Rep%201d60d/Untitled%209.png)

- Replicas 갯수를 수정하고 싶은 경우
- `edid replicaset`명령어 이용해서 접근 후 수정 가능

![Untitled](3%20week%20Rep%201d60d/Untitled%2010.png)

![Untitled](3%20week%20Rep%201d60d/Untitled%2011.png)

- 지우면 유지가 안됨 —> 바로 삭제됨

그러면 유지하고싶으면?

![Untitled](3%20week%20Rep%201d60d/Untitled%2012.png)

- 이전에 False 라고 발표하셨는데 `orphan` 이라고 해야함(False: 는 deprecated 됨)

![Untitled](3%20week%20Rep%201d60d/Untitled%2013.png)

- `ReplicaSet` 은 `Node` 에 골고루 분배되지 않음
→ `DaemonSet` 은 `Node` 에 골고루 분배됨

![Untitled](3%20week%20Rep%201d60d/Untitled%2014.png)

- `apps/v1` 버젼임
- `kind` 에 `DaemonSet` 이라고만 적어주면됨

![Untitled](3%20week%20Rep%201d60d/Untitled%2015.png)

- 이것도 파드들 죽음
→ `--cascate=orphan` 을 달아주면 안죽음

![Untitled](3%20week%20Rep%201d60d/Untitled%2016.png)

- `Job` : 단 한번만 실행하는 용도  
→  ex) log 1회성 코드 가져오기 등
- Dockerfile 에서  Ubuntu 이미지만 띄워놓으면 쿠버네티스가 종료시켜버림
    
    → ENTRYPOINT 등으로 실행시켜두거나 (ex, Sleep 333333 등으로 계속 실행시켜줘야함)
    
    → 그래야 `K8s` 가 안없애고 그대로 놔둠
    

![Untitled](3%20week%20Rep%201d60d/Untitled%2017.png)

![Untitled](3%20week%20Rep%201d60d/Untitled%2018.png)

- 처음엔 10초, 그다음은 20초, 그다음은 40초

![Untitled](3%20week%20Rep%201d60d/Untitled%2019.png)

![Untitled](3%20week%20Rep%201d60d/Untitled%2020.png)

![Untitled](3%20week%20Rep%201d60d/Untitled%2021.png)

- `completions` : 성공 횟수
- `parallelism` : 병렬로 몇개
- `backoffLimit` : 최대 실행 횟수

![Untitled](3%20week%20Rep%201d60d/Untitled%2022.png)

- `parallelism` 이 3 이어서 3개가 뜸

![Untitled](3%20week%20Rep%201d60d/Untitled%2023.png)

- `ex) 아침 6시마다 뭐 보내~` 이런거
- linux 의 `cron tab` 과 비슷

![Untitled](3%20week%20Rep%201d60d/Untitled%2024.png)

- 실패할 경우 `backoffLimit`
- 문법이 어려울 경우 위 사이트 참고할 수 있음 (`https://crontab.guru/`)

![Untitled](3%20week%20Rep%201d60d/Untitled%2025.png)

- `Suspend` 는 직접 볼것

![Untitled](3%20week%20Rep%201d60d/Untitled%2026.png)

![Untitled](3%20week%20Rep%201d60d/Untitled%2027.png)

- 크론잡도 파드는 새로 만듦 (기존거를 사용하지 않음)

![Untitled](3%20week%20Rep%201d60d/Untitled%2028.png)

- 성공한 것 몇개 남길래?
- 실패한 것은 몇개 남길래?
    
    → 모두 지우고 싶은 경우 0 / 0 으로 바꾸면 됨
    

![Untitled](3%20week%20Rep%201d60d/Untitled%2029.png)

![Untitled](3%20week%20Rep%201d60d/Untitled%2030.png)

![Untitled](3%20week%20Rep%201d60d/Untitled%2031.png)

---

### Service

![Untitled](3%20week%20Rep%201d60d/Untitled%2032.png)

![Untitled](3%20week%20Rep%201d60d/Untitled%2033.png)

![Untitled](3%20week%20Rep%201d60d/Untitled%2034.png)

![Untitled](3%20week%20Rep%201d60d/Untitled%2035.png)

---

![Untitled](3%20week%20Rep%201d60d/Untitled%2036.png)

`https://hyojun.me/~k8s-network-1`

`https://hyojun.me/~k8s-network-2`

`https://www.slideshare.net/InfraEngineer/meetup2nd-v12`

![Untitled](3%20week%20Rep%201d60d/Untitled%2037.png)

![Untitled](3%20week%20Rep%201d60d/Untitled%2038.png)

- `LoadBalancer` 는 주로 클라우드 벤더에서 이용
→ 온 프레미스 환경에서는 이용하지 않음

![Untitled](3%20week%20Rep%201d60d/Untitled%2039.png)

![Untitled](3%20week%20Rep%201d60d/Untitled%2040.png)

- `Cluster IP` : 컨테이너를 하나로 묶어 줌

![Untitled](3%20week%20Rep%201d60d/Untitled%2041.png)

- `NodePort` : 외부에서 접근하고 싶은 경우

![Untitled](3%20week%20Rep%201d60d/Untitled%2042.png)

![Untitled](3%20week%20Rep%201d60d/Untitled%2043.png)

---

![Untitled](3%20week%20Rep%201d60d/Untitled%2044.png)

- 일단 위와 같이 띄워줌
- 위의 `IP` 는 파드 아이피
→ 파드끼리는 서로 통신 가능 (k8s 클러스터 내부)

![Untitled](3%20week%20Rep%201d60d/Untitled%2045.png)

- 바깥에서 Node 에 할당된 IP 로 접근할 수 있음
    
    → Pod 로는 접근 불가능 ㅠ
    

![Untitled](3%20week%20Rep%201d60d/Untitled%2046.png)

- 노드에 접속하면, 접근 가능

![Untitled](3%20week%20Rep%201d60d/Untitled%2047.png)

`ports.port:80 / ports.targetPort: 8080`

- 서비스가 바라보는 파드는 8080 밖에서는 80 으로 접근하겠다는 뜻
- 위와같이 서비스 만들면 80으로 접근하면 컨테이너의 8080으로 접근한다는 뜻
- **서비스가 알아서 노드밸런싱하기 때문에 접근하는 파드 이름이 다름**

![Untitled](3%20week%20Rep%201d60d/Untitled%2048.png)

- **파드를 죽여서 IP를 바꿔보아도
→  Service 가 Label 가지고 알아서 매칭해주기 때문에 큰 문제없이 접근 가능**

![Untitled](3%20week%20Rep%201d60d/Untitled%2049.png)

- `Service Describe` 해보면 `ENDPOINT`에 연결된 아이피가 있음

### 그러면 밖에서 접근하려면?

![Untitled](3%20week%20Rep%201d60d/Untitled%2050.png)

- `type` : NodePort 라고만 해주면 됨
- `nodePort` 는 안적어도 알아서 할당해줌
- 밖에서 `30123` 으로 접근하면 내부의 `80` 으로 접근 
→ 이는 다시 컨테이너의 `8080` 으로 접근
- `IP`는 `Node` 의 아무 `IP` 나 써도 상관없음

![Untitled](3%20week%20Rep%201d60d/Untitled%2051.png)

- Pod 안에서 나를 가리키는 서비스 정도는 뭐지?
환경변수에 적용되어있으므로 확인해보면 됨

---

![Untitled](3%20week%20Rep%201d60d/Untitled%2052.png)

- service 3 으로 `google.com` 에 접근 가능

![Untitled](3%20week%20Rep%201d60d/Untitled%2053.png)

![Untitled](3%20week%20Rep%201d60d/Untitled%2054.png)

![Untitled](3%20week%20Rep%201d60d/Untitled%2055.png)
