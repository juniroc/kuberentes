# 7 week : RBAC

[쿠버네티스 인가(Authorization) - RBAC, Role, RoleBinding](https://velog.io/@younge/%EC%BF%A0%EB%B2%84%EB%84%A4%ED%8B%B0%EC%8A%A4-%EC%9D%B8%EA%B0%80Authorization-RBAC-Role-RoleBinding)

# Authentication & Authorization

### RBAC : 누가(주체), 무엇을(동사), 어디에(네임스페이스) 실행할 수 있는지 결정하는 권한 또는 템플릿 집합을 수반하는 Identity 및 액세스 관리 형식

![Untitled](7%20week%20RBA%20a1616/Untitled.png)

- `Authentication` 과 `Authorization` 이 구분이 되어야함
- `Authentication` : 구글 OAuth 정도로 생각
→ 이것을 쓰겠다는 것은 기본 권한을 다른 플랫폼에 위임하겠다는 뜻
- `Authorization` : 게시판 작성 및 삭제 권한은 바로 주어야할까?
→ 이럴 때 쓰이는 권한 관리가 `Authorization`
    - 내부의 권한 관리는 `Authorization` 으로 따로 관리
- 즉, 올바른 유저라는 것은 `Authentication` 으로 관리, 그 이후 내부 권한 관리는 `Authorization`
- `Admission control plugin` : 요청된 리소스 확인 및 수정 (모든 요청서를 가지고 옴, 일부만 채워져 있으면 , 플러그인 내부에서 디폴트값을 주거나, 제대로 된 양식을 만들어주는 역할
→ 기본적으로 채워야하는부분 default 로 채우거나 해서 요청을 완성시켜줌

![Untitled](7%20week%20RBA%20a1616/Untitled%201.png)

1. `Authentication` 을 우선적으로 확인
2. 확인 된 경우 `Authorization` 을 확인
3. 확인 된 경우 요청을 넣고
4. `Admission controllers` 요청 허가

![Untitled](7%20week%20RBA%20a1616/Untitled%202.png)

- 실제 사용자의 계정이 아닌, `Service Account` 만드는 과정

### RBAC

- 누가(주체), 무엇을(동사), 어디에(네임스페이스) 실행할 수 있는지 결정하는 권한 또는 템플릿 집합을 수반하는 Identity 및 액세스 관리 형식

### Service Account

![Untitled](7%20week%20RBA%20a1616/Untitled%203.png)

- 기본적으로 `namespace` 기준으로 움직임
- `Pod` 는 같은 `namespace` 의 `ServiceAccount` 만 사용 가능
- **1개** `pod(리소스)` 는 **1개**의 `ServiceAccount` 만 할당 가능
- 다른 `namespace` 라면 `SA`가 같은 이름을 가져도 상관없음
- 각 `SA`에는 각각의 `Secret` 이 생성됨

![Untitled](7%20week%20RBA%20a1616/Untitled%204.png)

- `SA` 하나당 하나의 `Secret` 을 `Mount` 함

![Untitled](7%20week%20RBA%20a1616/Untitled%205.png)

- `namespace` 를 지정해주어서 만들어야 함

### Role

![Untitled](7%20week%20RBA%20a1616/Untitled%206.png)

- **Role** : **누가 무엇을** 할건데?

![Untitled](7%20week%20RBA%20a1616/Untitled%207.png)

- `apiGroups` : “” 하면 `core API` 를 쓴다는 뜻
→ ex) `apiVersions/v1`  이런거 모두 다 쓰려면 `“*”`
→ `apiGroups` : `Extensions`, `apps`
- `resources` : pod 나 dp 등
→ 모두 다 쓰고싶으면 이거 쓰면됨 `“*”`

![Untitled](7%20week%20RBA%20a1616/Untitled%208.png)

- api-resources 를 이용해 모든 resources를 보여줌
- `namespace` 단위로 노는 애들은 `True`

![Untitled](7%20week%20RBA%20a1616/Untitled%209.png)

- `Role Yaml`

![Untitled](7%20week%20RBA%20a1616/Untitled%2010.png)

- `Role` 정의하고 `SA`와 바인딩하는 것은 `RoleBinding`
- `PV/PVC` 와 비슷

![Untitled](7%20week%20RBA%20a1616/Untitled%2011.png)

- 즉, `Role` 을 정의해두면 `Rolebinding` 을 통해 `SA 와 Biding`

![Untitled](7%20week%20RBA%20a1616/Untitled%2012.png)

![Untitled](7%20week%20RBA%20a1616/Untitled%2013.png)

![Untitled](7%20week%20RBA%20a1616/Untitled%2014.png)

- `apiGroups` : `Extensions`, `apps`

 

![Untitled](7%20week%20RBA%20a1616/Untitled%2015.png)

![Untitled](7%20week%20RBA%20a1616/Untitled%2016.png)

- `—insecure`

![Untitled](7%20week%20RBA%20a1616/Untitled%2017.png)

- 위의 문제를 해결하기 위해 Token 값을 가져오면 됨
- 그냥 가져다 쓰면 `encoding` 된 값이 오기 때문에  `decode` 해서 써먹으면 됨

![Untitled](7%20week%20RBA%20a1616/Untitled%2018.png)

### ClusterRole

![Untitled](7%20week%20RBA%20a1616/Untitled%2019.png)

- `ClusterRole` : `Cluster` 기반 권한 부여
- `ClusterRole` 은 `RoleBinding` 으로도 되긴 함
- `Namespace - Role` , `Cluster - ClusterRole` 로 바인딩하는게 좋음

![Untitled](7%20week%20RBA%20a1616/Untitled%2020.png)

![Untitled](7%20week%20RBA%20a1616/Untitled%2021.png)

- 즉, `Namespace` 와 `user`를 같은 개념으로 보면 될 것
    
    → 그래서 `Namespace` 에 `SA` 를 바인딩시켜줌 
    

![Untitled](7%20week%20RBA%20a1616/Untitled%2022.png)

![Untitled](7%20week%20RBA%20a1616/Untitled%2023.png)

- `imagePullSecret` : 에 정보를 같이 넣어주면 Secret image 임에도 Pull 받을 수 있음

![Untitled](7%20week%20RBA%20a1616/Untitled%2024.png)

- 하지만 매번 `Pod`에서 `secret` 을 지정하지 말고, `dockerhub-account` 만들어서 위와 같이 연결해 줄 수 있음

### Pod ~ API-Server

![Untitled](7%20week%20RBA%20a1616/Untitled%2025.png)

![Untitled](7%20week%20RBA%20a1616/Untitled%2026.png)

### Pod 안에서 API-Server 접근하는 법

![Untitled](7%20week%20RBA%20a1616/Untitled%2027.png)

![Untitled](7%20week%20RBA%20a1616/Untitled%2028.png)

- `pod` 안으로 들어가서 실행
- `CURL_CA_BUNDLE` 환경변수로 인증서 위치 설정해주면 자동으로 참조함

![Untitled](7%20week%20RBA%20a1616/Untitled%2029.png)

- `TOKEN` 파일을 `Cat` 한것을 넣어주면 됨

![Untitled](7%20week%20RBA%20a1616/Untitled%2030.png)