# Readinessprobe, Livenessprobe - Pod

## Pod 의 LifeCycle

![Untitled](Readinessp%200ccab/Untitled.png)

### Pod - Lifecycle

![Untitled](Readinessp%200ccab/Untitled%201.png)

 

![Untitled](Readinessp%200ccab/Untitled%202.png)

### Overview

![Untitled](Readinessp%200ccab/Untitled%203.png)

### 1.

![Untitled](Readinessp%200ccab/Untitled%204.png)

- **PodScheduled**  : Pod 가 지정한 Node 위에 제대로 올라간 경우 `True`
- **Initialized :** initContainers 에 Command 가 존재해 그것이 컨테이너가 실행되기 전에 성공적으로 실행되거나 없는 경우  `True` 로 나옴 / 실패하면 `False`

![Untitled](Readinessp%200ccab/Untitled%205.png)

- 만약 아래의 Running 이 아니고 Waiting - CrashLoopBackOff 인 경우 Running 의 ContainerReady, Ready : `False` 가 됨
- 단 Pod 는 Running 상태를 유지할 수 있음
→ Pod 뿐만 아니라 Pod 내부 컨테이너를 잘 확인해야함.

  

![Untitled](Readinessp%200ccab/Untitled%206.png)

### Pod

![Untitled](Readinessp%200ccab/Untitled%207.png)

- Pod 가 삭제되었다가 재구동시 App 이 Booting 하는 과정이 존재하는데 이때 트래픽이 전달되면 실패하게 됨
→ 이를 방지하기 위해 **ReadinessProbe** 를 설정해줌 (확실하게 구동이 되거나 또는 일정 시간 이후에 트래픽이 전달되도록)
→ App 이 실행도중 Down 이 되면, **LivenessProbe** 가 App 을 재가동 시켜줌

![Untitled](Readinessp%200ccab/Untitled%208.png)

- Exec 를 사용해 해당 `ready.txt` 파일이 조회가 되면 성공
→ 이때 조건을 주어서 일정 시간 대기 및 유지 시간, 성공횟수 가 넘어가면 `ContainerReady/Ready` 가 `True` 로 바뀜

![Untitled](Readinessp%200ccab/Untitled%209.png)

- `initialDelaySeconds` : n 초 후에 LivenessProbe 체크 시작
- `periodSeconds` : n 초 마다 LivenessProbe 체크
- `timeoutSeconds` : 프로브 제한시간
- `successThreshold` : 실패 후 프로브가 성공해야 하는 횟수
- `failureThreshold` : 3번 실패시 Pod Restart

![Untitled](Readinessp%200ccab/Untitled%2010.png)

- 위에서 정의한 조건에 따라 실패시 Pod 재실행

---