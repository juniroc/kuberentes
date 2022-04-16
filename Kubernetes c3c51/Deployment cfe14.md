# Deployment

### Deployment Update 방법

![Untitled](Deployment%20cfe14/Untitled.png)

- `B/G 방식`과 `Canary 방식`의 차이는 둘다 동시에 운영하냐 안하냐 차이

![Untitled](Deployment%20cfe14/Untitled%201.png)

- 카나리는 둘다 운영하다가 한쪽의 service 를 제거

### ReCreate

![Untitled](Deployment%20cfe14/Untitled%202.png)

![Untitled](Deployment%20cfe14/Untitled%203.png)

- `v1` 이 사라진 시간인 Downtime 이 존재
- `revisionHistoryLimit` 을 1로하면 위에서 또 다른 버전을 만들었을 경우
→ 맨처음 ReplicaSet 은 삭제됨
    - 즉, 여분의 `ReplicaSet` 을 몇개 남겨둘지 정의

![Untitled](Deployment%20cfe14/Untitled%204.png)

![Untitled](Deployment%20cfe14/Untitled%205.png)