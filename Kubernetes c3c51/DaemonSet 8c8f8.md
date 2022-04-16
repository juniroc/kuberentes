# DaemonSet

![Untitled](DaemonSet%208c8f8/Untitled.png)

- **모든 노드에 Pod 가 하나씩 생성**
    
    Ex) 
    
    - **성능 수집할 경우 : Prometheus**
    - **Log 수집할 경우 : fluentd**
    - **Storage 시스템 구축할 경우 : GlusterFS**

![Untitled](DaemonSet%208c8f8/Untitled%201.png)

- Job 과 ReplicaSet 의 차이
    - ReplicaSet 의 경우 : 서비스가 계속 운영되어야 하는 경우
    - Job 의 경우 : 프로세스가 일하지 않으면 Pod 가 종료 (사라지는 것이 아닌 자원 사용을 하지 않은 상태)
    - CronJob : 스케쥴링 해주는 것 (특정 시간 반복적 사용)
    
    Ex)
    
    - Backup
    - Checking
    - Messaging

### DaemonSet

![Untitled](DaemonSet%208c8f8/Untitled%202.png)

- 위 사진처럼 원하는 노드만 daemonSet 을 설정하고 싶은 경우 **nodeSelector 에 라벨**을 달아서 제한걸 수 있음

![Untitled](DaemonSet%208c8f8/Untitled%203.png)

- 위 externalTrafficPolicy : Local 과 같이 hostPort 를 이용해 노드포트(18080) 를 통해 내부 컨테이너 포트(8080) 으로 접근 가능

`DaemonSet.yml`

```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: daemonset-2
spec:
  selector:
    matchLabels:
      type: app
  template:
    metadata:
      labels:
        type: app
    spec:
      nodeSelector:
        os: centos
      containers:
      - name: container
        image: kubetm/app
        ports:
        - containerPort: 8080
```

### Job

![Untitled](DaemonSet%208c8f8/Untitled%204.png)

- completions : 해당 횟수 만큼 Pod 의 작업이 끝나야 Job 이 종료
- parallelism : 병렬로 수행
- activeDeadlineSeconds: 해당 시간이 지나면 종료 후 제거
→ 만약 10초 짜리 작업인데 30 초 이상 해결되지 않으면 종료 후 제거

![Untitled](DaemonSet%208c8f8/Untitled%205.png)

- completions : 해당 횟수 만큼 Pod 의 작업이 끝나야 Job 이 종료
- parallelism : 병렬로 수행
- activeDeadlineSeconds: 해당 시간이 지나면 종료 후 제거
→ 만약 10초 짜리 작업인데 30 초 이상 해결되지 않으면 종료 후 제거

---

### CronJob

![Untitled](DaemonSet%208c8f8/Untitled%206.png)

![Untitled](DaemonSet%208c8f8/Untitled%207.png)

- Allow : 해당 잡이 끝나지 않아도 계속 생성해서 처리
- Forbid : 해당 잡이 다음 실행시간까지 끝나지 않으면 다음 실행은 Skip
- Replace : 해당 잡이 다음 실행시간까지 끝나지 않으면 다음 실행에 잡이 붙어서 Pod 을 실행

![Untitled](DaemonSet%208c8f8/Untitled%208.png)

- Schedule 과 ConcurrencyPolicy 부분을 체크해줄 수 있음

---