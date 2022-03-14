# K8s_Network

### 기본 Network 개념 (OSI 7 Layer - 2, 3 layer 계층)

![Untitled](OSI_7_Laye%2025cdb/Untitled%201.png)

- 동일 네트워크 내에서 데이터 전송

![Untitled](OSI_7_Laye%2025cdb/Untitled%202.png)

- `Frame` 으로 통신하고, 3계층으로 가서는 `Packet` 형태로 통신

![Untitled](OSI_7_Laye%2025cdb/Untitled%203.png)

![Untitled](OSI_7_Laye%2025cdb/Untitled%204.png)

![Untitled](OSI_7_Laye%2025cdb/Untitled%205.png)

![Untitled](OSI_7_Laye%2025cdb/Untitled%206.png)

- `ENQ-ACK` 방법

![Untitled](OSI_7_Laye%2025cdb/Untitled%207.png)

- `Polling` 방법

![Untitled](OSI_7_Laye%2025cdb/Untitled%208.png)

- 중복되지 않도록 Sequence number 를 바꿔서 보냄

![Untitled](OSI_7_Laye%2025cdb/Untitled%209.png)

![Untitled](OSI_7_Laye%2025cdb/Untitled%2010.png)

- 즉, 정해진 시간내에 `ACK` 가 안오거나 `NAK` 이 오면 재전송

![Untitled](OSI_7_Laye%2025cdb/Untitled%2011.png)

- 손상된 3번이 포함된 345 재전송 → 이는 비효율적
→ 그래서 나온게 selective Repeat ARQ

![Untitled](OSI_7_Laye%2025cdb/Untitled%2012.png)

- `FCS(Frame Check Sequence)` : 에러 체크

---

![Untitled](OSI_7_Laye%2025cdb/Untitled%2013.png)

![Untitled](OSI_7_Laye%2025cdb/Untitled%2014.png)

![Untitled](OSI_7_Laye%2025cdb/Untitled%2015.png)

![Untitled](OSI_7_Laye%2025cdb/Untitled%2016.png)

![Untitled](OSI_7_Laye%2025cdb/Untitled%2017.png)

![Untitled](OSI_7_Laye%2025cdb/Untitled%2018.png)

 

![Untitled](OSI_7_Laye%2025cdb/Untitled%2019.png)

![Untitled](OSI_7_Laye%2025cdb/Untitled%2020.png)

![Untitled](OSI_7_Laye%2025cdb/Untitled%2021.png)

![Untitled](OSI_7_Laye%2025cdb/Untitled%2022.png)

![Untitled](OSI_7_Laye%2025cdb/Untitled%2023.png)

- `브로드캐스트 스위칭` 영역은 `민수 - 철수` 정도의 범위라 생각

![Untitled](OSI_7_Laye%2025cdb/Untitled%2024.png)

---

## 라우터 & 서브넷

![Untitled](OSI_7_Laye%2025cdb/Untitled%2025.png)

- **라우터 : 목적지 IP 주소를 확인하고 경로를 찾아 패킷을 전송해주는 장비
→ 이때 라우팅 테이블을 참고해 경로를 찾아냄**

![Untitled](OSI_7_Laye%2025cdb/Untitled%2026.png)

![Untitled](OSI_7_Laye%2025cdb/Untitled%2027.png)

- 네트워크가 얼굴이라 치면 호스트는 이마나 얼굴, 입술 정도로 생각하면 됨
→ 서브넷 : 네트워크와 호스트를 구분하기 위한 것, 부분망

```python
성형외과에서 코수술을 한다고 했을때, 환자얼굴위에 수술 마스크를 덮습니다. 
해당 마스크는 코만 보이고 눈,입,볼 등 코를 제외한 부분은 전부 가려줍니다. 
의사가 코에만 집중할 수 있게 말이죠... 
이와 비슷한 개념입니다. 

어느 한 네트워크에서 1~100까지 IP를 할당받을 수 있고 우리가 필요한 IP가 20개정도라고 가정할 때, 
굳이 1~100까지 IP를 줄 필요가 없습니다. 최소 1~20까지만 IP를 주면 되겠죠...
그럼 나머지 21~100은 사용자가 신경쓰지 않게 끔, **서브넷마스크로 가려버린다** 라고 생각

ex)
IP주소에서 만약 192.168.0.1/24 라 하면 이는 **C클래스**

기본 디폴트 마스크는 255.255.255.0 이고
11111111.11111111.11111111.00000000 로 표현 가능

여기서 **1은 네트워크 영역**으로 사용하겠다는 뜻이고 **0은 호스트** **IP

즉, 사용자에게 0이 표현된 부분만 호스트 IP를 할당할 수 있게 만들겠다는 뜻**
```

![Untitled](OSI_7_Laye%2025cdb/Untitled%2028.png)

## Static 라우팅

![Untitled](OSI_7_Laye%2025cdb/Untitled%2029.png)

![Untitled](OSI_7_Laye%2025cdb/Untitled%2030.png)

![Untitled](OSI_7_Laye%2025cdb/Untitled%2031.png)

![Untitled](OSI_7_Laye%2025cdb/Untitled%2032.png)

- 위는 `Bob의 IP` 설정

![Untitled](OSI_7_Laye%2025cdb/Untitled%2033.png)

![Untitled](OSI_7_Laye%2025cdb/Untitled%2034.png)

![Untitled](OSI_7_Laye%2025cdb/Untitled%2035.png)

![Untitled](OSI_7_Laye%2025cdb/Untitled%2036.png)

![Untitled](OSI_7_Laye%2025cdb/Untitled%2037.png)

![Untitled](OSI_7_Laye%2025cdb/Untitled%2038.png)

![Untitled](OSI_7_Laye%2025cdb/Untitled%2039.png)

---

### ETC

- Table 3대장
    - Routing Table : 라우팅할 경우 참고하는 경로 테이블
    - ARP Table : IP 와 MAC 주소를 일대일 매칭하여 목적지를 제대로 찾아가도록 도움 (**IP 와 MAC Address 정보** - Switch에 존재)
    - MAC Table : 스위치에 연결된 사용자들의 MAC Address를 저장한 것 (**Mac Address와 Port 정보**)
- LAN : ARP Request 패킷이 도달하는 범위의 네트워크
- ARP는 요청을 보내 Switch 에서 IP 에 대응하는 MAC address를 가져옴
- ARP Table 에서 Next Hop 에 대한 MAC Address가 등록되어 있는지 확인 후 등록되어 있지 않으면 Switch에게 ARP Request 메시지를 보내 MAC Address를 알아 옴.
- ARP : 주소 프로토콜 정도로 알면 됨
→ IP를 알고있는 상태에서 MAC 주소를 알아내는 과정

---

### 오리뎅이님 자료

![Untitled](K8s_Networ%20a4c3f/Untitled.png)

![Untitled](K8s_Networ%20a4c3f/Untitled%201.png)

- Switch 를 통해 직접 통신

![Untitled](K8s_Networ%20a4c3f/Untitled%202.png)

- L3 통신 - Routing 통해 간접 통신

![Untitled](K8s_Networ%20a4c3f/Untitled%203.png)

![Untitled](K8s_Networ%20a4c3f/Untitled%204.png)

![Untitled](K8s_Networ%20a4c3f/Untitled%205.png)

- 서브넷 마스크가 다른 경우 - L3 (Subnet Mask 가 /32 이므로 다를 수 밖에 없을 것)

![Untitled](K8s_Networ%20a4c3f/Untitled%206.png)