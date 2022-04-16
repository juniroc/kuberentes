# 6 week : Deployment, StatefulSet

### Deployment

![Untitled](6%20week%20Dep%20e495f/Untitled.png)

- `Deployment` 를 생성하면 자동으로 `ReplicaSet` 은 자동으로 생성
→ `ReplicaSet` 사용하면 `Deployment`를 굳이 사용하지 않아도됨
- 그럼 왜 따로 구분해 두었나?
→ 업데이트 방식에 따른 다양한 조작이 가능하기 때문
ex) 카나리, 블루/그린, 롤링업데이트 등

![Untitled](6%20week%20Dep%20e495f/Untitled%201.png)

- `deployment` 도 `apps/v1` 임
→ replicaSet 과 같음
- 이름 뒤에 해시 값이 붙음
- `deployment` 뒤에 `ReplicaSet` 에서 붙이는 해쉬 (`위의 78~`)가 붙고
→ 다시 `파드`가 생성될때 여기 뒤에 각각의 해쉬값이 붙음

![Untitled](6%20week%20Dep%20e495f/Untitled%202.png)

- 각 파드에 랜덤으로 접속이 됨 (그때그때 해쉬값이 다름을 알 수 있음)

![Untitled](6%20week%20Dep%20e495f/Untitled%203.png)

- 버전 업데이트의 3가지 방법

![Untitled](6%20week%20Dep%20e495f/Untitled%204.png)

- `Deployment.yml` 이나 `ReplicaSet.yml` 을 새로운 버전으로 수정하고 apply 한 상태라고 보면 됨

![Untitled](6%20week%20Dep%20e495f/Untitled%205.png)

- Blue-Green 방식이라고 부름
- 좋지만 위험한 방식
    1. 순식간에 바뀔 때 대기시간이 발생해 서비스가 중단될 수 있음
    2. 어떤 사용자가 접속중에 갑자기 끊길 수 있음 (겟앰에서 서버 갑자기 종료되는거 생각)
    3. 서버 리소스가 2배로 많아야 함 (두 버전이 모두 공존하는 순간이 있으므로)
    4. 한 순간이라도 두가지가 공존하면 절대 안됨 (겟앰 생각)
    

![Untitled](6%20week%20Dep%20e495f/Untitled%206.png)

- K8s 에서 권장하는 업데이트 방법
- 단계적으로 교체 1개씩
- `Deployment` 가 자동으로 알아서 해줌

![Untitled](6%20week%20Dep%20e495f/Untitled%207.png)

- `apply` 는 기존것을 덮어쓰기
- `edit` 도 많이 쓰임
- `set image` : rolling update 에 쓰이는 방식이라 생각하면 됨

![Untitled](6%20week%20Dep%20e495f/Untitled%208.png)

- 새로운 `ReplicaSet` 을 만들어 거기에 파드를 붙이는 방식임
- `yaml` 에 `v1` → `v2` 라 작성해주면 됨

![Untitled](6%20week%20Dep%20e495f/Untitled%209.png)

- `dp-web` 이라는 `deployment` 에 `node-web=~:2.0` 로 올려라
- 왼쪽 사진을 보면 ver2.0 중간에도 예전 버전이 접속되는 경우도 있음

![Untitled](6%20week%20Dep%20e495f/Untitled%2010.png)

- `--record=true` 를 입력해주면 history 가 기록이 됨
→ 안그러면 이력이 날라갈 수 있음
    - 아직은 `record` 옵션을 주어야 하지만 없어진다는 얘기가 나옴

![Untitled](6%20week%20Dep%20e495f/Untitled%2011.png)

- Rolling Update 속도 제어
- `MaxSurge` : 레플리카 수 보다 **몇개의** **여분을** 더 만들 수 있는지?
- `MaxUnavailable` : 사용할 수 없는 파드 인스턴수 수

![Untitled](6%20week%20Dep%20e495f/Untitled%2012.png)

- **`MaxSurge`** 가 **1개**이므로 **1개를 띄우고 1개를 지우는 방식**으로 진행 (왼쪽 사진 확인)
- `**MaxUnavailable**` 은 서비스의 안정성이라 생각하면 됨 
→ 오른쪽 사진을 보면 MaxUnavailable이 1 이므로 3개중 2개만 운용으로 돌리고 나머지2개를 변경 가능

![Untitled](6%20week%20Dep%20e495f/Untitled%2013.png)

- `minReadySeconds` : 새로 배포된 컨테이너가 준비되기까지 시간 (pod 가 status 가 Ready 될 때까지의 최소 대기 시간)
- `progressDeadlineSeconds` : 총 소요 시간이 넘어가면 중단 (일정 시간이 지나가면 관리자가 원하지 않은 이슈가 존재할 수 있다는 판단하에 설정)
→ 당연히 `minReadSeconds` 보다는 커야함
- `revisionHistoryLimit` : 되돌릴 수 있는 Revision 개수

---

![Untitled](6%20week%20Dep%20e495f/Untitled%2014.png)

![Untitled](6%20week%20Dep%20e495f/Untitled%2015.png)

![Untitled](6%20week%20Dep%20e495f/Untitled%2016.png)

![Untitled](6%20week%20Dep%20e495f/Untitled%2017.png)

![Untitled](6%20week%20Dep%20e495f/Untitled%2018.png)

![Untitled](6%20week%20Dep%20e495f/Untitled%2019.png)

![Untitled](6%20week%20Dep%20e495f/Untitled%2020.png)

![Untitled](6%20week%20Dep%20e495f/Untitled%2021.png)

![Untitled](6%20week%20Dep%20e495f/Untitled%2022.png)