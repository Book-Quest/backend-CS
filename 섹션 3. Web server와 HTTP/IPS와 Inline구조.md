## 1. **네트워크 보안 솔루션 종류별 대응 계층**

### **OSI 7 Layer**

| 계층 | 주요 프로토콜/기능 | 보안 솔루션 |
|------|-------------------|------------|
| L7. 응용 (Application) | HTTP, FTP, DNS | WAF, 애플리케이션 방화벽 |
| L6. 표현 (Presentation) | 데이터 암호화, 압축 | 데이터 암호화 솔루션 |
| L5. 세션 (Session) | SSL/TLS와 같은 세션 관리 기능 | VPN |
| L4. 전송 (Transport) | TCP, UDP | L4 스위치, 방화벽 |
| L3. 네트워크 (Network) | IP, ICMP | L3 스위치, 라우터, IPS |
| L2. 데이터 링크 (Data Link) | MAC 주소 및 프레임 전송 | L2 스위치 |
| L1. 물리 (Physical) | 물리적 연결 | 물리적 보안 장치 |

## 2. **네트워크 장비 및 역할**

### **스위치 (Switch)**

<img src="https://github.com/user-attachments/assets/3c504402-f765-4153-8b01-e04709df2105" width="500"/>

- 기능: 네트워크 회선과 서버 컴퓨터를 연결하는 장비로, **MAC 주소**를 기반으로 트래픽을 전달
- 특징:
    - 한 포트로 들어오는 패킷을 자신과 연결된 장치에게 전달하는 점에서 허브와 유사한 역할을 하나, 위 그림처럼 신호(패킷)를 모든 포트로 전달하는 허브에 비해 스위치는 신호를 필요로 하는 포트로만 전달
    - 따라서 스위치에서는 불필요한 트래픽이 감소하게 되며, 데이터 전송 속도가 향상됨
    - 이를 위해 스위치에서는 자신과 연결된 장비들의 MAC 주소와 그 장비가 연결된 포트를 기억해 두었다가, 자신이 아는 MAC 주소로 트래픽의 목적지가 설정되어 있으면 해당 포트로 트래픽을 전달

### **L3, L4 스위치**

- L3 스위치:
    - L2 스위치는 MAC 주소까지만 알았다면, L3 스위치는 MAC 주소뿐만 아니라 IP 주소까지 알고 있는 **라우팅 기능**이 추가된 스위치로, IP 주소를 기반으로 패킷을 전송하고 **네트워크 간 트래픽 라우팅**을 수행
    - 예시: AP 장비(공유기), 고성능 기업용 L3 스위치.
- L4 스위치:
    - 외부에서 들어오는 모든 요청을 먼저 받아 내부의 서버들에게 적절히 분산시킴
    - 즉, **부하 분산**(로드 밸런싱)을 통해 여러 서버에 트래픽을 분배
    - IP 주소뿐만 아니라 포트 정보도 참고하여 애플리케이션 별로 우선순위를 두어 스위칭을 하며, **서비스의 가용성**을 높이는 **Active-Active** 또는 **Active-Standby** 기능도 제공
    - 그러나, 설정이 복잡할 수 있다는 단점 존재

## 3. **네트워크 보안 솔루션**

### **Inline 구조**
<img src="https://github.com/user-attachments/assets/8401fb5e-29d3-4c39-922f-d7cd218c7ad1" width="500"/>

- 정의: 라우터와 같은 장치나 IPS와 같은 보안 장치들이 있을 때 패킷 단위로 Bypass or Drop 하는 장치
    - 특징: 각각의 네트워크 장비들이 일렬로 늘어선 상태 (공기 청정기 필터와 같은 구조)
    - 예시: 라우터, 공유기, IPS(침입 방지 시스템), 방화벽
### **Out-of-Path 구조**

- 정의: 패킷을 복제하여 Read-Only 방식으로 읽고 분석하여  Port Mirroring한 내용을 여러개의 인터페이스들로 전송
    - 특징: 실시간 차단이나 개입 없이 패킷을 고속도로에서 과속 감시 카메라처럼 **모니터링**하거나 **감지**하는 센서들이 이에 해당
    - 예시: IDS(침입 탐지 시스템), 네트워크 분석 장비

---

### **IPS (Intrusion Prevention System, 침입 방지 시스템)**

- 기능: 악성 트래픽을 실시간으로 **탐지하고 차단**하는 보안 장비
- 동작 원리: 네트워크를 통해 들어오는 트래픽을 **Inline**으로 모니터링하고, 악성 트래픽이 발견되면 이를 차단하거나 보안 정책에 맞는 조치를 취함
- 주요 역할: 트래픽에 대한 취약성 검사를 수행하고, 악용 가능성이 있는 패킷을 차단하여 네트워크 보호

### **WAF (Web Application Firewall, 웹 애플리케이션 방화벽)**

- 기능: 웹 애플리케이션을 대상으로 한 **악성 트래픽**을 차단하는 장비
- 동작 원리: **L7**에서 작동하며, **HTTP** 및 **HTTPS** 트래픽을 분석하여 **웹 공격**(SQL 인젝션, XSS 등)을 탐지하고 차단
- 특징:
    - **SSL termination**을 통해 암호화된 트래픽을 복호화하고 **웹 애플리케이션** 레벨에서 공격을 차단
    - Nginx나 Apache 같은 Web Server들이 Packet Filtering F/W나 IPS와 같은 역할을 해줄 수 있음
    - SSL은 기본적으로 Web Server에 설치되나, 실제로 인프라 구축 시에는 IPS나 L4 Load Balancer에 설치되기도 함

## References

- [L1, L2, L3, L4, L5, L6, L7 스위치란](https://siahn95.tistory.com/161)
- [IPS(침입 방지 시스템)](https://www.fortinet.com/kr/resources/cyberglossary/what-is-an-ips)
- [Inline 구조와 Out of path 구조]()
- [네트워크 구성 - Inline 구성](https://laoching.tistory.com/entry/Inline-%EA%B5%AC%EC%A1%B0%EC%99%80-Out-of-path-%EA%B5%AC%EC%A1%B0)(https://run-it.tistory.com/40)
- [네트워크의 3가지 구조 - Inline, Out of Path, Proxy](https://techblog-history-younghunjo1.tistory.com/566)
- [L4 스위치의 A to Z (Load Balancing, Traffic Flow, SSL Offload)](https://kangmanjoo.tistory.com/161)
- [AWS의 Application Load Balancer (ALB)](https://codeabc.tistory.com/52)
- [Firewall(방화벽), IDS(침입 탐지 시스템), IPS (침입 방지 시스템),DDOS(분산 서비스 거부 공격)](https://maker5587.tistory.com/8)
