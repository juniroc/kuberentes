# Volume (local)

- 컨테이너 디렉토리를 외부 저장소와 연결해, 스토리지로 별도 설정
→ MySQL 같은 DB는 데이터가 유실되지 않도록 별도 저장소에 데이터를 저장해 Container를 만들때 이전 데이터 가져와야 함

`PV/PVC`

```yaml
데이터 저장이 필요한 경우에 흔히 Persistent Volume(PV),Persistent Volume Claim(PVC)를 사용
```

### empty-dir

- Pod 안에 속한 컨테이너 간 디렉토리 공유 방법
- 주로 `Side-car` 라는 패턴에서 사용, 특정 컨테이너에서 생성되는 로그 파일을 별도의 컨테이너(사이드카)가 수집하는 구조

![Untitled](Volume%20(lo%20958a1/Untitled.png)

`empty-dir.yml`

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: sidecar
spec:
  containers:
    - name: app      ## app 컨테이너
      image: busybox
      args:
        - /bin/sh
        - -c
        - >
          while true;
          do
            echo "$(date)\n" >> /var/log/example.log;
            sleep 1;
          done
      volumeMounts:
        - name: varlog
          mountPath: /var/log
    - name: sidecar     ## 사이드카 컨테이너
      image: busybox
      args: [/bin/sh, -c, "tail -f /var/log/example.log"]
      volumeMounts:
        - name: varlog
          mountPath: /var/log
  volumes:
    - name: varlog
      emptyDir: {}
```

- `app` 컨테이너는 `/var/log/example.log` 에 로그 파일 만들고, `sidecar` 컨테이너는 해당 로그 파일 처리

`sidecar` 로그 확인

```yaml
kubectl apply -f empty-dir.yml

# sidecar 로그 확인
kubectl logs -f sidecar -c sidecar
```

![Untitled](Volume%20(lo%20958a1/Untitled%201.png)

- `app` 컨테이너 로그파일을 `sidecar` 컨테이너에서 처리하는 모습

### Hostpath

- `Host_dir` 를 `container_dir` 에 연결하는 방법

![Untitled](Volume%20(lo%20958a1/Untitled%202.png)

`hostpath.yml`

```yaml
apiVersion: v1
kind: Pod
metadata:
	name: host-log
spec:
  containers:
    - name: log
      image: busybox
      args: ["/bin/sh", "-c", "sleep infinity"]
      volumeMounts:
        - name: varlog
          mountPath: /host/var/log
  volumes:
    - name: varlog
      hostPath:
        path: /var/log
```

- `/var/log` 를 컨테이너의 `/host/var/log` 디렉토리로 마운트

```yaml
kubectl apply -f hostpath.yml

kubectl exec -it host-log --sh
```

`ls /host/var/log 검색결과`

![Untitled](Volume%20(lo%20958a1/Untitled%203.png)

`7번 서버 내부 디렉토리`

![Untitled](Volume%20(lo%20958a1/Untitled%204.png)

### `local directory` 와 연결됨.