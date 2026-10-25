# CHAPTER 2. 네트워크

> 『면접을 위한 CS 전공지식 노트』 2장 학습 정리  
> 네트워크의 기본 개념과 Node.js, NestJS 서버 개발에서 자주 접하는 HTTP, 프록시, 클라이언트 IP, 네트워크 진단 방법을 함께 정리한다.

---

## 2.1 네트워크의 기초

### 2.1.1 처리량과 지연 시간

#### 대역폭

**대역폭(Bandwidth)**은 단위 시간 동안 전송할 수 있는 이론적인 최대 데이터 양이다.

일반적으로 다음 단위를 사용한다.

- `bps`: 초당 비트 수
- `Mbps`: 초당 메가비트 수
- `Gbps`: 초당 기가비트 수

```text
대역폭 = 도로의 최대 차선 수
```

대역폭이 크다고 해서 항상 실제 전송 속도가 최대치로 나오는 것은 아니다.

---

#### 처리량

**처리량(Throughput)**은 단위 시간 동안 실제로 전송하거나 처리한 데이터의 양이다.

```text
처리량 = 실제로 도로를 통과한 차량 수
```

처리량은 다음 요인의 영향을 받는다.

- 네트워크 대역폭
- 패킷 손실
- 네트워크 혼잡
- 서버 처리 능력
- 데이터베이스 처리 속도
- 동시 접속자 수
- 프로토콜 오버헤드

특정 네트워크 링크의 실제 처리량은 일반적으로 해당 링크의 최대 대역폭보다 작다.

---

#### Goodput

**Goodput**은 프로토콜 헤더, 재전송 데이터 등을 제외하고 애플리케이션이 실제로 전달받은 유효 데이터의 양이다.

```text
대역폭 ≥ 처리량 ≥ Goodput
```

예를 들어 100MB를 전송했더라도 그 안에 TCP/IP 헤더와 재전송된 데이터가 포함되어 있다면 실제 애플리케이션 데이터의 양은 100MB보다 작을 수 있다.

---

#### 지연 시간

**지연 시간(Latency)**은 데이터가 출발지에서 목적지까지 전달되는 데 걸리는 시간이다.

지연 시간은 문맥에 따라 다음과 같이 구분할 수 있다.

- 단방향 지연 시간: 출발지에서 목적지까지 걸리는 시간
- RTT: 요청이 목적지에 도착한 뒤 응답이 다시 돌아오기까지 걸리는 시간
- API 응답 시간: 네트워크 지연뿐 아니라 서버 처리 시간까지 포함

```text
API 응답 시간
= DNS 조회
+ TCP 연결
+ TLS 연결
+ 네트워크 왕복 시간
+ 서버 대기 시간
+ 서버 처리 시간
+ 응답 데이터 전송 시간
```

---

#### RTT

**RTT(Round Trip Time)**는 데이터가 목적지에 도착한 뒤 응답이 다시 출발지로 돌아오기까지 걸리는 왕복 시간이다.

```text
클라이언트 → 서버 → 클라이언트
```

`ping` 명령어로 확인하는 시간이 대표적인 RTT다.

단, `ping`은 ICMP를 사용하므로 실제 HTTP API 응답 시간과 같지는 않다.

---

#### 처리량과 지연 시간 비교

| 구분        | 처리량                               | 지연 시간                                |
| ----------- | ------------------------------------ | ---------------------------------------- |
| 의미        | 단위 시간당 처리한 데이터 양         | 요청이나 데이터 전달에 걸린 시간         |
| 대표 단위   | Mbps, Gbps, TPS, RPS                 | ms, s                                    |
| 개선 방법   | 서버 확장, 병렬 처리, 커넥션 풀 조정 | 캐싱, CDN, 쿼리 최적화, 가까운 리전 사용 |
| 사용자 체감 | 많은 요청을 동시에 처리              | 하나의 요청이 빠르게 응답                |

처리량이 높아도 지연 시간이 크면 사용자는 서비스가 느리다고 느낄 수 있다.

반대로 단일 요청은 빠르더라도 동시에 처리할 수 있는 요청 수가 적으면 트래픽이 증가할 때 장애가 발생할 수 있다.

---

#### 게임 서버 실무 예시

```text
처리량 문제
→ 동시에 많은 게임 요청을 처리하지 못함
→ API 서버 증설, DB 커넥션 풀 조정, 비동기 처리 검토

지연 시간 문제
→ 한 번의 랭킹 조회가 오래 걸림
→ 인덱스 추가, 쿼리 최적화, Redis 캐싱 적용
```

---

#### 면접 답변

> 처리량은 단위 시간 동안 실제로 처리할 수 있는 데이터나 요청의 양이고, 지연 시간은 하나의 요청이나 데이터가 목적지까지 전달되는 데 걸리는 시간입니다.
>
> 처리량은 서버 확장이나 병렬 처리, 커넥션 풀 조정 등으로 개선할 수 있고 지연 시간은 캐싱, CDN, 쿼리 최적화, 네트워크 거리 단축 등으로 개선할 수 있습니다.

---

### 2.1.2 네트워크 토폴로지와 병목 현상

#### 네트워크 토폴로지

**네트워크 토폴로지(Network Topology)**는 네트워크 장치와 노드가 연결된 형태를 의미한다.

| 형태   | 설명                         | 장점                      | 단점                                    |
| ------ | ---------------------------- | ------------------------- | --------------------------------------- |
| 버스형 | 하나의 공통 회선에 노드 연결 | 구성이 단순함             | 공통 회선 장애에 취약                   |
| 스타형 | 중앙 장치에 모든 노드 연결   | 관리와 장애 파악이 쉬움   | 중앙 장치가 단일 장애 지점이 될 수 있음 |
| 링형   | 노드를 원형으로 연결         | 일정한 순서로 데이터 전달 | 한 구간 장애가 전체에 영향을 줄 수 있음 |
| 메시형 | 노드들을 여러 경로로 연결    | 장애에 강함               | 비용과 구성이 복잡함                    |
| 트리형 | 계층 구조로 노드 연결        | 확장이 쉬움               | 상위 장치 장애 영향이 큼                |

현대적인 사내 네트워크에서는 스위치를 중심으로 연결하는 스타형 구조를 많이 사용한다.

다만 실제 운영 환경에서는 이중화와 여러 토폴로지를 조합한 하이브리드 구조가 일반적이다.

---

#### 병목 현상

**병목 현상(Bottleneck)**은 시스템의 특정 구간이 전체 처리 성능을 제한하는 현상이다.

```text
클라이언트
   ↓
로드밸런서
   ↓
API 서버
   ↓
Redis
   ↓
MySQL
```

위 구조에서 API 서버를 여러 대 추가하더라도 MySQL이 처리할 수 있는 쿼리 수가 부족하면 전체 처리량은 증가하지 않는다.

이 경우 병목은 API 서버가 아니라 MySQL이다.

---

#### 서버에서 자주 발생하는 병목

- 데이터베이스 Slow Query
- 잘못된 인덱스
- DB Lock 대기
- 부족한 DB 커넥션 풀
- Redis의 특정 Key 집중
- 외부 API 응답 지연
- CPU 사용률 증가
- 메모리 부족과 Swap
- Node.js Event Loop Blocking
- 네트워크 패킷 손실
- 로드밸런서 연결 제한
- 과도한 로그 출력

---

#### 병목 분석 순서

```text
1. 사용자 또는 클라이언트 구간 확인
2. DNS, TCP, TLS 연결 시간 확인
3. 로드밸런서와 리버스 프록시 확인
4. API 서버 처리 시간 확인
5. Event Loop, CPU, 메모리 확인
6. 데이터베이스 쿼리와 Lock 확인
7. Redis와 외부 API 확인
8. 네트워크 패킷 손실과 RTT 확인
```

서버가 느리다는 이유만으로 서버 인스턴스를 먼저 늘리면 실제 병목이 데이터베이스에 있을 때 효과가 없거나 오히려 DB 부하가 증가할 수 있다.

---

### 2.1.3 네트워크 분류

| 분류 | 범위                  | 대표 예시             |
| ---- | --------------------- | --------------------- |
| PAN  | 개인 주변의 짧은 거리 | 블루투스, 스마트워치  |
| LAN  | 건물, 사무실, 가정    | 사내망, 가정용 공유기 |
| MAN  | 도시 규모             | 도시 통신망           |
| WAN  | 국가 또는 대륙 규모   | 인터넷, 기업 전용망   |

#### PAN

**PAN(Personal Area Network)**은 개인 주변의 짧은 거리에서 사용하는 네트워크다.

```text
스마트폰 ↔ 블루투스 이어폰
```

#### LAN

**LAN(Local Area Network)**은 건물이나 사무실처럼 비교적 좁은 범위의 네트워크다.

```text
PC → 사내 스위치 → 사내 서버
```

#### MAN

**MAN(Metropolitan Area Network)**은 도시 규모의 네트워크다.

#### WAN

**WAN(Wide Area Network)**은 국가나 대륙처럼 넓은 지역을 연결하는 네트워크다.

인터넷은 여러 LAN과 WAN이 서로 연결된 거대한 네트워크다.

---

### 2.1.4 네트워크 성능 분석 명령어

| 명령어       | 용도                                          |
| ------------ | --------------------------------------------- |
| `ping`       | RTT와 패킷 손실 확인                          |
| `traceroute` | macOS/Linux에서 목적지까지의 홉 확인          |
| `tracert`    | Windows에서 목적지까지의 홉 확인              |
| `ss`         | Linux에서 소켓과 포트 상태 확인               |
| `netstat`    | 네트워크 연결과 라우팅 정보 확인              |
| `lsof -i`    | macOS/Linux에서 포트를 사용하는 프로세스 확인 |
| `nslookup`   | DNS 조회                                      |
| `dig`        | DNS 상세 조회                                 |
| `curl`       | HTTP 요청과 단계별 시간 확인                  |

---

#### ping

```bash
ping api.example.com
```

확인할 수 있는 내용은 다음과 같다.

- 대상 서버와 네트워크 통신 가능 여부
- RTT
- 패킷 손실률

주의할 점은 서버나 방화벽이 ICMP를 차단할 수 있다는 것이다.

`ping`이 실패한다고 해서 반드시 HTTP 서버가 중단된 것은 아니다.

---

#### traceroute와 tracert

macOS/Linux:

```bash
traceroute api.example.com
```

Windows:

```powershell
tracert api.example.com
```

목적지까지 거치는 라우터의 경로를 확인할 수 있다.

각 라우터를 **홉(Hop)**이라고 한다.

다만 라우터가 응답하지 않거나 실제 왕복 경로가 서로 다를 수 있기 때문에 출력 결과만으로 정확한 장애 지점을 단정해서는 안 된다.

---

#### 포트 상태 확인

Linux:

```bash
ss -lntp
```

macOS:

```bash
lsof -iTCP -sTCP:LISTEN -n -P
```

특정 포트 확인:

```bash
lsof -i :3000
```

---

#### DNS 조회

```bash
nslookup api.example.com
```

```bash
dig api.example.com
```

간단한 결과만 확인:

```bash
dig +short api.example.com
```

---

#### HTTP 단계별 시간 확인

```bash
curl -o /dev/null -sS \
  -w 'remote_ip: %{remote_ip}\nDNS: %{time_namelookup}s\nTCP: %{time_connect}s\nTLS: %{time_appconnect}s\nTTFB: %{time_starttransfer}s\nTotal: %{time_total}s\nHTTP: %{http_code}\n' \
  https://api.example.com
```

| 항목                 | 의미                       |
| -------------------- | -------------------------- |
| `time_namelookup`    | DNS 조회가 끝난 시점       |
| `time_connect`       | TCP 연결이 끝난 시점       |
| `time_appconnect`    | TLS 연결이 끝난 시점       |
| `time_starttransfer` | 첫 응답 바이트를 받은 시점 |
| `time_total`         | 전체 요청이 끝난 시점      |

이 값들은 대부분 요청 시작 시점부터 측정한 누적 시간이다.

따라서 순수 TLS 소요 시간을 구하려면 다음과 같이 차이를 계산해야 한다.

```text
TLS 연결 시간
= time_appconnect - time_connect
```

---

### 2.1.5 네트워크 프로토콜 표준화

서로 다른 제조사와 운영체제의 장치가 통신하려면 데이터 형식과 통신 절차가 표준화되어야 한다.

#### 주요 표준화 기구

| 기구 | 역할                             |
| ---- | -------------------------------- |
| IETF | 인터넷 프로토콜 표준화, RFC 발행 |
| IEEE | Ethernet, Wi-Fi 등의 표준화      |
| ISO  | OSI 7계층 모델 등 국제 표준화    |
| W3C  | 웹 관련 기술 표준화              |

대표적인 IEEE 표준은 다음과 같다.

- `IEEE 802.3`: Ethernet
- `IEEE 802.11`: Wi-Fi

---

#### OSI 7계층

| 계층           | 역할                     | 대표 예시         |
| -------------- | ------------------------ | ----------------- |
| 7. 응용        | 사용자 애플리케이션 통신 | HTTP, DNS, SMTP   |
| 6. 표현        | 인코딩, 암호화, 압축     | JSON, 문자 인코딩 |
| 5. 세션        | 세션 연결과 관리         | 세션 관리         |
| 4. 전송        | 종단 간 통신, 포트       | TCP, UDP          |
| 3. 네트워크    | IP 주소와 라우팅         | IP, ICMP          |
| 2. 데이터 링크 | MAC 주소와 프레임        | Ethernet, Wi-Fi   |
| 1. 물리        | 전기·광학 신호 전달      | 케이블, 전파      |

OSI 7계층은 네트워크 통신을 설명하기 위한 이론적인 참조 모델이다.

실제 인터넷 프로토콜은 일반적으로 TCP/IP 4계층 모델로 설명한다.

---

## 2.2 TCP/IP 4계층 모델

### 2.2.1 계층 구조

| TCP/IP 계층              | 역할                          | 대표 프로토콜        |
| ------------------------ | ----------------------------- | -------------------- |
| 애플리케이션 계층        | 사용자 데이터와 서비스 처리   | HTTP, DNS, SMTP, FTP |
| 전송 계층                | 종단 간 통신과 포트 구분      | TCP, UDP             |
| 인터넷 계층              | IP 주소와 라우팅              | IPv4, IPv6, ICMP     |
| 네트워크 인터페이스 계층 | 같은 네트워크에서 프레임 전달 | Ethernet, Wi-Fi, ARP |

ARP는 TCP/IP 모델 분류 방식에 따라 인터넷 계층과 링크 계층 사이의 프로토콜로 설명되기도 하지만, 실제 역할은 같은 링크 안에서 IPv4 주소를 MAC 주소로 확인하는 것이다.

---

#### OSI와 TCP/IP 비교

| OSI 7계층         | TCP/IP 4계층        |
| ----------------- | ------------------- |
| 응용, 표현, 세션  | 애플리케이션        |
| 전송              | 전송                |
| 네트워크          | 인터넷              |
| 데이터 링크, 물리 | 네트워크 인터페이스 |

---

#### 계층화의 장점

특정 계층의 구현이 변경되어도 다른 계층에 미치는 영향을 줄일 수 있다.

예를 들어 Wi-Fi를 Ethernet으로 변경해도 HTTP 애플리케이션 코드를 전부 수정할 필요는 없다.

```text
HTTP
↓
TCP
↓
IP
↓
Ethernet 또는 Wi-Fi
```

---

#### 포트

IP 주소가 통신할 장치를 구분한다면 **포트 번호**는 해당 장치에서 통신할 프로세스나 서비스를 구분한다.

```text
IP 주소: 어느 서버인가?
포트 번호: 그 서버의 어느 서비스인가?
```

대표적인 포트는 다음과 같다.

| 서비스 | 기본 포트 |
| ------ | --------: |
| HTTP   |        80 |
| HTTPS  |       443 |
| SSH    |        22 |
| MySQL  |      3306 |
| Redis  |      6379 |

서버는 반드시 기본 포트만 사용해야 하는 것은 아니다.

예를 들어 개발 서버의 HTTP API를 `3000`, `8080`, `9090` 포트에서 실행할 수도 있다.

---

#### TCP와 UDP

| 구분            | TCP                       | UDP                                |
| --------------- | ------------------------- | ---------------------------------- |
| 연결 방식       | 연결 지향                 | 비연결                             |
| 신뢰성          | 재전송, 순서 보장         | 기본적으로 보장하지 않음           |
| 흐름 제어       | 지원                      | 지원하지 않음                      |
| 혼잡 제어       | 지원                      | 지원하지 않음                      |
| 속도와 오버헤드 | 상대적으로 큰 오버헤드    | 상대적으로 작은 오버헤드           |
| 대표 사용       | HTTP/1.1, HTTP/2, DB 연결 | DNS, 실시간 미디어, QUIC 기반 통신 |

UDP가 항상 TCP보다 빠르다고 단정할 수는 없다.

UDP는 신뢰성과 순서 보장을 애플리케이션에서 직접 구현해야 할 수 있다.

QUIC은 UDP 위에서 동작하지만 신뢰성, 혼잡 제어, 스트림 관리 등을 QUIC 자체에서 제공한다.

---

#### TCP 3-Way Handshake

TCP 연결을 시작할 때 일반적으로 다음 과정이 발생한다.

```text
클라이언트 → 서버: SYN
클라이언트 ← 서버: SYN + ACK
클라이언트 → 서버: ACK
```

1. 클라이언트가 연결을 요청한다.
2. 서버가 요청을 확인하고 자신의 연결 요청을 보낸다.
3. 클라이언트가 서버의 응답을 확인한다.

TCP 연결을 맺으려면 네트워크 왕복이 필요하므로 새로운 연결을 반복해서 생성하면 지연 시간이 증가한다.

---

### 2.2.2 캡슐화와 역캡슐화

송신 측에서는 상위 계층의 데이터에 각 계층의 헤더를 추가한다.

이를 **캡슐화(Encapsulation)**라고 한다.

```text
HTTP 데이터
↓ TCP 헤더 추가
TCP 세그먼트
↓ IP 헤더 추가
IP 패킷
↓ Ethernet 헤더와 트레일러 추가
Ethernet 프레임
↓
비트로 전송
```

수신 측에서는 헤더를 차례로 제거하며 원래 데이터를 복원한다.

이를 **역캡슐화(Decapsulation)**라고 한다.

---

### PDU

**PDU(Protocol Data Unit)**는 각 계층에서 사용하는 데이터 단위다.

| 계층                     | PDU                           |
| ------------------------ | ----------------------------- |
| 애플리케이션 계층        | 메시지 또는 데이터            |
| 전송 계층                | TCP 세그먼트 / UDP 데이터그램 |
| 인터넷 계층              | 패킷                          |
| 네트워크 인터페이스 계층 | 프레임                        |
| 물리 계층 관점           | 비트                          |

TCP/IP 4계층 모델에는 별도의 물리 계층이 없지만, 실제 프레임은 최종적으로 전기·광학·무선 신호의 비트 형태로 전달된다.

---

## 2.3 네트워크 기기

### 2.3.1 네트워크 기기의 처리 범위

네트워크 기기는 주로 어떤 계층의 주소와 헤더를 기준으로 데이터를 처리하는지에 따라 구분한다.

| 장비                      | 주로 확인하는 정보     | 대표 계층       |
| ------------------------- | ---------------------- | --------------- |
| L7 로드밸런서/API Gateway | URL, Host, HTTP Header | 애플리케이션    |
| L4 로드밸런서             | IP, TCP/UDP Port       | 전송            |
| 라우터                    | IP 주소                | 인터넷·네트워크 |
| 스위치                    | MAC 주소               | 데이터 링크     |
| 허브·리피터               | 전기·광학 신호         | 물리            |

상위 계층의 장비일수록 더 많은 정보를 이용할 수 있지만 무조건 느리다고 단정할 수는 없다.

실제 처리 성능은 하드웨어 가속, 구현 방식, 설정 등에 따라 달라진다.

---

### 2.3.2 애플리케이션 계층 장비

#### L7 로드밸런서

HTTP 요청의 다음 정보를 기준으로 요청을 전달할 서버를 선택한다.

- URL Path
- Host
- Header
- Cookie
- HTTP Method

```text
/api/users → User Service
/api/orders → Order Service
```

대표적인 예시는 다음과 같다.

- Nginx
- HAProxy
- AWS ALB
- Envoy

---

#### API Gateway

API Gateway는 단순한 요청 전달 외에도 다음 기능을 수행할 수 있다.

- 인증과 인가
- Rate Limiting
- 요청 및 응답 변환
- 라우팅
- 로깅
- API Key 관리

Gateway는 특정 계층 하나로만 한정되는 장비 이름이 아니라 서로 다른 시스템이나 프로토콜을 연결하는 넓은 개념이다.

---

### 2.3.3 전송 계층 장비

#### L4 로드밸런서

L4 로드밸런서는 주로 다음 정보를 기준으로 트래픽을 분산한다.

- 출발지 IP
- 목적지 IP
- 출발지 Port
- 목적지 Port
- TCP 또는 UDP 프로토콜

HTTP URL의 세부 경로보다는 네트워크 연결 정보를 중심으로 처리한다.

대표적인 예로 AWS NLB가 있다.

---

### 2.3.4 인터넷 계층 장비

#### 라우터

라우터는 목적지 IP 주소와 라우팅 테이블을 이용해 패킷을 다음 네트워크로 전달한다.

라우터의 주요 역할은 다음과 같다.

- 서로 다른 네트워크 연결
- 목적지 IP에 따른 경로 선택
- 다음 홉 결정
- 패킷 전달

---

### 2.3.5 데이터 링크 계층 장비

#### 스위치

스위치는 MAC 주소 테이블을 이용해 프레임을 필요한 포트로 전달한다.

```text
MAC 주소 → 연결된 스위치 포트
```

스위치의 각 포트는 일반적으로 별도의 충돌 도메인이 된다.

다만 같은 VLAN에 속한 포트들은 기본적으로 하나의 브로드캐스트 도메인에 속한다.

---

#### 브리지

브리지는 두 개 이상의 LAN 세그먼트를 연결하고 MAC 주소를 기준으로 프레임을 전달한다.

현대적인 스위치는 여러 포트를 가진 고성능 브리지로 이해할 수 있다.

---

### 2.3.6 물리 계층 장비

#### 허브

허브는 들어온 신호를 프레임의 목적지 MAC 주소를 확인하지 않고 다른 포트로 반복 전송한다.

따라서 연결된 장치가 하나의 충돌 도메인을 공유한다.

허브는 프레임을 분석해서 브로드캐스트하는 것이 아니라 물리 신호를 단순 반복한다.

---

#### 리피터

리피터는 전송 과정에서 약해지거나 왜곡된 신호를 복원해 더 먼 거리까지 전달한다.

단순히 신호의 크기만 증폭하는 것보다 신호를 재생성하는 장치로 이해하는 것이 정확하다.

---

#### 라우터와 스위치 비교

| 구분      | 라우터                  | 스위치                  |
| --------- | ----------------------- | ----------------------- |
| 주요 계층 | 인터넷·네트워크 계층    | 데이터 링크 계층        |
| 기준 주소 | IP 주소                 | MAC 주소                |
| 주요 역할 | 서로 다른 네트워크 연결 | 같은 LAN 안의 장치 연결 |
| 참고 정보 | 라우팅 테이블           | MAC 주소 테이블         |

---

## 2.4 IP 주소

### 2.4.1 ARP

**ARP(Address Resolution Protocol)**는 같은 IPv4 네트워크 안에서 IP 주소에 대응하는 MAC 주소를 알아내기 위한 프로토콜이다.

---

#### 동작 과정

```text
1. 송신자가 ARP 캐시를 확인한다.
2. MAC 주소가 없으면 ARP Request를 브로드캐스트한다.
3. 해당 IP를 가진 장치가 ARP Reply로 MAC 주소를 응답한다.
4. 송신자는 IP와 MAC 주소의 매핑을 ARP 캐시에 저장한다.
5. 확인한 MAC 주소로 Ethernet Frame을 전송한다.
```

ARP Request 예시:

```text
192.168.0.10을 가진 장치는
자신의 MAC 주소를 알려주세요.
```

ARP Request는 일반적으로 브로드캐스트되고 ARP Reply는 일반적으로 요청자에게 유니캐스트로 전달된다.

---

#### 다른 네트워크로 전송할 때

목적지 IP가 같은 네트워크에 있지 않다면 송신자는 원격 서버의 MAC 주소를 ARP로 찾지 않는다.

대신 기본 게이트웨이의 MAC 주소를 ARP로 확인한다.

```text
PC
↓ 기본 게이트웨이 MAC 주소로 프레임 전송
라우터
↓ 다음 홉으로 패킷 전달
원격 서버
```

MAC 주소는 링크 구간마다 변경될 수 있지만 IP 패킷의 목적지 IP는 일반적으로 최종 목적지를 유지한다.

단, NAT가 적용되는 구간에서는 IP 주소가 변환될 수 있다.

---

#### ARP 캐시 확인

```bash
arp -a
```

Linux에서는 다음 명령어도 사용할 수 있다.

```bash
ip neigh
```

IPv6에서는 ARP 대신 ICMPv6 기반의 NDP(Neighbor Discovery Protocol)를 사용한다.

---

#### 면접 답변

> ARP는 같은 IPv4 네트워크에서 IP 주소에 대응하는 MAC 주소를 알아내는 프로토콜입니다. ARP Request를 브로드캐스트하고 해당 IP를 가진 장치가 MAC 주소를 응답합니다.
>
> 목적지가 다른 네트워크에 있다면 원격 서버의 MAC 주소가 아니라 기본 게이트웨이의 MAC 주소를 ARP로 확인합니다.

---

### 2.4.2 홉바이홉 통신

패킷은 출발지에서 최종 목적지까지 한 번에 전달되는 것이 아니라 여러 라우터를 거쳐 전달된다.

이때 거치는 하나의 라우터 구간을 **홉(Hop)**이라고 한다.

각 라우터는 라우팅 테이블을 조회하고 패킷을 전달할 다음 홉을 선택한다.

---

#### 라우팅 테이블

라우팅 테이블에는 일반적으로 다음 정보가 저장된다.

- 목적지 네트워크
- 서브넷 마스크 또는 Prefix
- 다음 홉
- Network Interface
- Metric

라우터가 인터넷의 실제 전체 경로를 미리 아는 것은 아니다.

라우팅 테이블을 이용해 현재 위치에서 적절한 다음 홉을 결정한다.

---

#### TTL과 Hop Limit

IPv4에는 **TTL(Time To Live)** 필드가 있다.

라우터를 하나 통과할 때마다 TTL이 1씩 감소한다.

TTL이 0이 되면 라우터가 패킷을 폐기한다.

이는 라우팅 오류로 패킷이 네트워크를 무한히 순환하는 것을 방지한다.

IPv6에서는 같은 목적의 필드를 **Hop Limit**이라고 한다.

---

### 2.4.3 IP 주소 체계

#### IPv4

IPv4는 32비트 주소를 사용한다.

```text
192.168.0.1
```

8비트씩 네 개의 옥텟으로 구분한다.

```text
192 . 168 . 0 . 1
 8     8    8   8  = 32비트
```

IPv4 주소 개수는 약 43억 개다.

인터넷 사용자가 증가하면서 주소 부족 문제가 발생했고 이를 완화하기 위해 사설 IP, NAT, CIDR 등이 사용되었다.

---

#### IPv6

IPv6는 128비트 주소를 사용한다.

```text
2001:0db8:0000:0000:0000:0000:0000:0001
```

연속된 0은 다음처럼 축약할 수 있다.

```text
2001:db8::1
```

IPv6에서는 IPv4의 ARP 대신 NDP를 사용하고 브로드캐스트 대신 멀티캐스트를 활용한다.

---

#### 사설 IPv4 대역

| 대역                              | CIDR             |
| --------------------------------- | ---------------- |
| `10.0.0.0` ~ `10.255.255.255`     | `10.0.0.0/8`     |
| `172.16.0.0` ~ `172.31.255.255`   | `172.16.0.0/12`  |
| `192.168.0.0` ~ `192.168.255.255` | `192.168.0.0/16` |

사설 IP는 공용 인터넷에서 직접 라우팅되지 않는다.

외부 인터넷과 통신하려면 일반적으로 NAT를 통해 공인 IP로 변환한다.

---

#### 주요 특수 주소

| 주소              | 용도                                        |
| ----------------- | ------------------------------------------- |
| `127.0.0.1`       | IPv4 Loopback                               |
| `::1`             | IPv6 Loopback                               |
| `0.0.0.0`         | 모든 IPv4 Interface 또는 지정되지 않은 주소 |
| `255.255.255.255` | IPv4 제한 브로드캐스트                      |

서버가 다음과 같이 실행되는 경우:

```text
127.0.0.1:3000
```

현재 장치에서만 접근할 수 있다.

다음과 같이 실행하면:

```text
0.0.0.0:3000
```

서버가 모든 IPv4 Network Interface에서 연결을 받을 수 있다.

다만 방화벽과 네트워크 설정에 따라 외부 접근이 차단될 수 있다.

---

#### CIDR

**CIDR(Classless Inter-Domain Routing)**는 IP 주소와 Network Prefix 길이를 함께 표현하는 방식이다.

```text
192.168.0.0/24
```

`/24`는 앞의 24비트가 네트워크 영역이라는 뜻이다.

```text
네트워크 영역: 24비트
호스트 영역: 8비트
전체 주소 수: 2^8 = 256개
```

전통적인 IPv4 서브넷에서는 네트워크 주소와 브로드캐스트 주소를 제외하고 254개를 호스트에 사용할 수 있다.

클라우드 환경에서는 플랫폼이 추가 주소를 예약할 수 있으므로 실제 사용 가능한 주소 수가 더 적을 수 있다.

---

#### TypeScript CIDR 확인

JavaScript의 비트 연산자는 부호가 있는 32비트 정수로 처리되므로 IPv4 전체 범위를 다룰 때 주의해야 한다.

다음 예시는 `BigInt`를 사용한다.

```ts
import { isIP } from "node:net";

function ipv4ToBigInt(ip: string): bigint {
  if (isIP(ip) !== 4) {
    throw new Error(`올바르지 않은 IPv4 주소: ${ip}`);
  }

  return ip
    .split(".")
    .reduce((result, octet) => (result << 8n) | BigInt(octet), 0n);
}

function isInIpv4Subnet(ip: string, cidr: string): boolean {
  const parts = cidr.split("/");

  if (parts.length !== 2) {
    throw new Error(`올바르지 않은 CIDR: ${cidr}`);
  }

  const [networkAddress, prefixText] = parts;

  if (!/^\d+$/.test(prefixText)) {
    throw new Error(`올바르지 않은 Prefix: ${prefixText}`);
  }

  const prefix = Number(prefixText);

  if (prefix < 0 || prefix > 32) {
    throw new Error(`Prefix 범위 오류: ${prefix}`);
  }

  const fullMask = 0xffff_ffffn;

  const mask = prefix === 0 ? 0n : (fullMask << BigInt(32 - prefix)) & fullMask;

  const ipValue = ipv4ToBigInt(ip);

  const networkValue = ipv4ToBigInt(networkAddress);

  return (ipValue & mask) === (networkValue & mask);
}

console.log(isInIpv4Subnet("192.168.0.15", "192.168.0.0/24")); // true

console.log(isInIpv4Subnet("192.168.1.15", "192.168.0.0/24")); // false
```

실무에서는 직접 구현하기보다 충분히 검증된 IP/CIDR 라이브러리를 사용하는 것도 고려할 수 있다.

---

### 2.4.4 IP 주소를 이용한 위치 정보

GeoIP 데이터베이스는 IP 주소 대역을 국가나 도시 같은 위치 정보와 매핑한다.

다만 IP 기반 위치는 다음 이유로 정확하지 않을 수 있다.

- VPN
- Proxy
- 이동통신사 NAT
- 회사 네트워크
- CDN
- IP 대역 재할당
- 실제 사용자 위치와 통신사 거점 차이

따라서 GeoIP는 대략적인 국가나 지역 판별에는 사용할 수 있지만 GPS 수준의 정확한 위치 정보로 사용해서는 안 된다.

---

### 리버스 프록시 환경의 실제 클라이언트 IP

다음과 같은 구조에서는 애플리케이션 서버가 직접 연결한 상대는 클라이언트가 아니라 리버스 프록시다.

```text
클라이언트
    ↓
AWS ALB 또는 Nginx
    ↓
Node.js/NestJS 서버
```

따라서 `req.socket.remoteAddress`에는 프록시의 IP가 들어올 수 있다.

프록시는 일반적으로 다음 헤더를 이용해 원래 클라이언트 IP 정보를 전달한다.

```http
X-Forwarded-For: client, proxy1, proxy2
```

하지만 외부 클라이언트도 `X-Forwarded-For` 헤더를 임의로 보낼 수 있다.

따라서 헤더의 첫 번째 값을 무조건 신뢰하면 IP 위조가 가능하다.

---

#### 잘못된 방식

```ts
const clientIp = req.headers["x-forwarded-for"]?.toString().split(",")[0];
```

프록시 신뢰 범위를 설정하지 않고 헤더만 직접 읽는 방식은 안전하지 않다.

---

#### Express 설정

```ts
import express from "express";

const app = express();

/**
 * 실제 인프라에서 사용하는
 * 프록시 주소나 서브넷에 맞춰 설정해야 한다.
 */
app.set("trust proxy", ["loopback", "10.0.0.0/8"]);

app.get("/whoami", (req, res) => {
  res.json({
    ip: req.ip,
    proxyChain: req.ips,
  });
});
```

Express는 신뢰할 수 있는 프록시를 오른쪽부터 확인하고 최초의 신뢰할 수 없는 주소를 클라이언트 IP로 판단한다.

`trust proxy`를 무조건 `true`로 설정하면 클라이언트가 직접 서버에 접근할 수 있는 환경에서 전달 헤더가 위조될 수 있다.

---

#### NestJS 설정

```ts
// main.ts
import { NestFactory } from "@nestjs/core";
import { NestExpressApplication } from "@nestjs/platform-express";
import { AppModule } from "./app.module";

async function bootstrap(): Promise<void> {
  const app = await NestFactory.create<NestExpressApplication>(AppModule);

  /**
   * 실제 ALB, Nginx, Kubernetes 네트워크
   * 구성에 맞는 주소만 신뢰해야 한다.
   */
  app.set("trust proxy", ["loopback", "10.0.0.0/8"]);

  await app.listen(3000);
}

void bootstrap();
```

```ts
import { Controller, Get, Req } from "@nestjs/common";
import { Request } from "express";

@Controller()
export class WhoAmIController {
  @Get("whoami")
  getIp(@Req() request: Request) {
    return {
      ip: request.ip,
      proxyChain: request.ips,
    };
  }
}
```

`10.0.0.0/8`은 예시일 뿐이며 실제 운영 환경의 프록시 주소와 네트워크 경로에 맞게 설정해야 한다.

---

#### Laravel

Laravel에서는 신뢰할 프록시를 올바르게 설정한 뒤 다음과 같이 클라이언트 IP를 가져온다.

```php
use Illuminate\Http\Request;

public function whoAmI(Request $request): array
{
    return [
        'ip' => $request->ip(),
        'ips' => $request->ips(),
    ];
}
```

Laravel에서도 `X-Forwarded-For`를 직접 분리하기보다 프레임워크의 Proxy 신뢰 설정을 사용하는 것이 안전하다.

---

#### 브라우저 JavaScript

브라우저 JavaScript는 자신의 공인 IP를 안정적으로 직접 확인할 수 없다.

공인 IP가 필요하다면 다음 중 하나를 사용한다.

- 자신의 서버 API
- 신뢰할 수 있는 외부 IP 확인 API

외부 서비스를 사용하면 사용자 IP가 해당 서비스에 전달되므로 개인정보와 서비스 의존성을 고려해야 한다.

---

## 2.5 HTTP

HTTP는 애플리케이션 계층에서 요청과 응답을 주고받기 위한 프로토콜이다.

HTTP 자체는 기본적으로 상태를 저장하지 않는 **Stateless Protocol**이다.

```text
클라이언트 → HTTP Request → 서버
클라이언트 ← HTTP Response ← 서버
```

상태가 필요한 서비스에서는 다음 방법을 사용한다.

- Cookie
- Session
- Access Token
- Refresh Token
- Redis Session Store

---

### HTTP 버전 비교

| 버전     | 전송 기반 | 주요 특징                           | 주요 한계                 |
| -------- | --------- | ----------------------------------- | ------------------------- |
| HTTP/1.0 | TCP       | 한 연결당 하나의 요청이 일반적      | 연결 반복 비용            |
| HTTP/1.1 | TCP       | 지속 연결, Host Header              | 애플리케이션 계층 HOL     |
| HTTP/2   | TCP       | Binary Framing, Multiplexing, HPACK | TCP 수준 HOL              |
| HTTP/3   | QUIC/UDP  | 독립 Stream, 빠른 연결 설정, QPACK  | 인프라 지원과 운영 복잡성 |

---

### 2.5.1 HTTP/1.0

HTTP/1.0에서는 기본적으로 하나의 TCP 연결에서 하나의 요청과 응답을 처리한 뒤 연결을 종료하는 방식이 일반적이었다.

```text
TCP 연결
→ HTTP 요청
→ HTTP 응답
→ TCP 종료
```

여러 파일을 요청하면 TCP 연결도 반복해서 생성해야 했다.

```text
HTML 요청 → TCP 연결
CSS 요청  → TCP 연결
JS 요청   → TCP 연결
이미지 요청 → TCP 연결
```

TCP 3-Way Handshake가 반복되므로 RTT와 연결 비용이 증가한다.

HTTP/1.0에서도 비표준적인 Keep-Alive 확장이 사용되었지만 기본 동작으로 표준화된 것은 아니었다.

---

### 2.5.2 HTTP/1.1

HTTP/1.1에서는 지속 연결이 기본으로 사용된다.

하나의 TCP 연결에서 여러 HTTP 요청과 응답을 처리할 수 있다.

```text
TCP 연결
→ HTTP 요청 1 / 응답 1
→ HTTP 요청 2 / 응답 2
→ HTTP 요청 3 / 응답 3
→ TCP 종료
```

---

#### 주요 특징

- Persistent Connection
- `Host` Header 필수
- Chunked Transfer 지원
- Cache 제어 개선
- Pipelining 지원

---

#### Keep-Alive

HTTP/1.1에서는 별도의 `Connection: keep-alive`를 작성하지 않아도 기본적으로 연결을 유지한다.

연결을 종료하려면 다음 헤더를 사용할 수 있다.

```http
Connection: close
```

---

#### HTTP Pipelining

Pipelining은 이전 요청의 응답을 기다리지 않고 여러 요청을 연속으로 보내는 방식이다.

```text
요청 A
요청 B
요청 C
↓
응답 A
응답 B
응답 C
```

응답은 요청 순서대로 전달되어야 한다.

응답 A가 늦어지면 B와 C도 기다려야 한다.

이를 HTTP/1.1의 **애플리케이션 계층 HOL Blocking**이라고 한다.

실제 브라우저에서는 구현 복잡성과 문제 때문에 Pipelining이 널리 사용되지 않았고 여러 TCP 연결을 병렬로 생성하는 방식이 많이 사용되었다.

---

### 2.5.3 HTTP/2

HTTP/2는 HTTP 의미 체계는 유지하면서 데이터 전달 방식을 Binary Frame 기반으로 변경했다.

---

#### 주요 특징

- Binary Framing
- Stream 기반 Multiplexing
- HPACK Header Compression
- 하나의 TCP 연결 활용
- Server Push 규격 제공

---

#### Stream과 Multiplexing

HTTP/2에서는 하나의 TCP 연결 안에 여러 Stream을 만들 수 있다.

```text
하나의 TCP 연결
├─ Stream 1: HTML
├─ Stream 3: CSS
├─ Stream 5: JavaScript
└─ Stream 7: Image
```

각 요청과 응답은 작은 Frame으로 나뉘며 여러 Stream의 Frame이 하나의 연결에서 교차 전송될 수 있다.

따라서 HTTP/1.1 Pipelining처럼 앞선 응답 전체가 완료될 때까지 다음 응답을 기다릴 필요가 없다.

---

#### HTTP/2와 HOL Blocking

HTTP/2는 HTTP/1.1의 애플리케이션 계층 HOL Blocking을 개선했다.

하지만 HTTP/2는 하나의 TCP 연결 위에서 동작한다.

TCP는 Byte Stream의 순서를 보장하므로 하나의 TCP Packet이 손실되면 재전송될 때까지 뒤의 데이터를 애플리케이션에 전달하지 못한다.

```text
TCP Packet 손실
→ TCP가 재전송을 기다림
→ 같은 TCP 연결의 여러 HTTP/2 Stream이 함께 대기
```

따라서 다음처럼 구분해야 한다.

```text
HTTP/1.1 애플리케이션 계층 HOL
→ HTTP/2 Multiplexing으로 개선

TCP 전송 계층 HOL
→ HTTP/2에도 남아 있음
→ HTTP/3의 QUIC이 개선
```

> HTTP/2에서 특정 Stream의 데이터가 손실되면 해당 Stream에만 영향을 준다는 설명은 정확하지 않다.
>
> HTTP/2는 TCP를 사용하므로 TCP Packet 손실이 같은 연결의 다른 Stream에도 영향을 줄 수 있다.

---

#### HPACK Header Compression

HTTP 요청에는 반복되는 Header가 많다.

```http
User-Agent
Accept
Cookie
Authorization
```

HTTP/2는 HPACK을 사용해 반복되는 Header를 압축하고 전송량을 줄인다.

---

#### Server Push

Server Push는 클라이언트가 아직 요청하지 않은 리소스를 서버가 미리 전송할 수 있는 HTTP/2 기능이다.

```text
클라이언트: HTML 요청
서버: HTML 응답 + CSS 예상 전송
```

다만 클라이언트 캐시 상태와 실제 요청을 정확히 예상하기 어렵고 잘못 Push하면 불필요한 데이터 전송이 발생할 수 있다.

따라서 규격에는 존재하지만 실무에서는 제한적으로 사용되며 클라이언트가 비활성화할 수도 있다.

---

### 2.5.4 HTTPS

HTTPS는 HTTP 통신에 TLS를 적용한 방식이다.

```text
HTTP
↓
TLS
↓
TCP
↓
IP
```

TLS는 다음 보안 속성을 제공한다.

- 기밀성: 통신 내용을 암호화한다.
- 무결성: 데이터가 변조되지 않았는지 확인한다.
- 인증: 통신 상대가 올바른 서버인지 확인한다.

---

#### SSL과 TLS

SSL은 오래된 프로토콜이며 현재는 TLS를 사용한다.

관습적으로 SSL 인증서나 SSL/TLS라는 표현을 사용하지만 실제 현대 HTTPS 통신에서는 TLS를 사용한다.

```text
SSL 2.0, SSL 3.0
→ 사용 중단

TLS 1.0, TLS 1.1
→ 오래된 버전

TLS 1.2, TLS 1.3
→ 현재 사용되는 주요 버전
```

---

#### 인증서

서버 인증서는 서버의 신원과 공개키를 연결한다.

인증서에는 일반적으로 다음 정보가 포함된다.

- 인증서 대상 도메인
- 공개키
- 발급자
- 유효 기간
- 전자서명
- 확장 정보

클라이언트는 다음 내용을 확인한다.

- 신뢰할 수 있는 CA가 발급했는가?
- 인증서의 도메인이 접속한 도메인과 일치하는가?
- 인증서의 유효 기간이 지나지 않았는가?
- 인증서 체인이 올바른가?
- 인증서 서명이 유효한가?

---

#### TLS 1.3 핸드셰이크 개요

```text
1. ClientHello
2. ServerHello
3. 서버 인증서와 서명 검증 정보
4. 키 교환 결과로 공유 비밀 생성
5. Finished 메시지 검증
6. 대칭키 기반 암호화 통신 시작
```

---

##### 1. ClientHello

클라이언트가 다음 정보를 서버에 보낸다.

- 지원하는 TLS 버전
- Cipher Suite
- Key Share
- Random 값
- SNI
- ALPN
- 기타 확장 정보

---

##### 2. ServerHello

서버는 사용할 TLS 버전과 Cipher Suite를 선택하고 자신의 Key Share를 보낸다.

클라이언트와 서버는 일반적으로 ECDHE 방식으로 같은 공유 비밀을 계산한다.

---

##### 3. 서버 인증

서버는 인증서 체인과 자신이 인증서의 개인키를 보유하고 있음을 증명하는 전자서명을 전달한다.

클라이언트는 서버 인증서를 검증한다.

---

##### 4. 대칭키 파생

클라이언트와 서버는 키 교환으로 만든 공유 비밀과 핸드셰이크 정보를 이용해 실제 통신에 사용할 대칭키를 각각 계산한다.

대칭키 자체를 네트워크로 그대로 전송하는 것이 아니다.

---

##### 5. 암호화 통신

핸드셰이크가 끝나면 실제 HTTP 데이터는 파생된 대칭키로 암호화한다.

대칭키 암호화는 대용량 데이터를 빠르게 처리하는 데 적합하다.

---

#### 기존 설명에서 주의할 점

다음 설명은 과거 TLS의 RSA Key Exchange를 단순화한 설명이다.

```text
클라이언트가 대칭키를 생성한다.
→ 서버 공개키로 암호화한다.
→ 서버에 전달한다.
```

현대적인 TLS 1.3에서는 일반적으로 ECDHE Key Share를 교환하고 양쪽이 같은 공유 비밀과 대칭키를 계산한다.

서버 인증서의 공개키는 서버 인증과 서명 검증에 사용된다.

따라서 면접에서는 TLS 1.3 기준으로 설명하고 과거 RSA Key Exchange와 구분하는 것이 정확하다.

---

#### HTTP/2와 HTTPS

HTTP/2 규격 자체가 TLS를 반드시 요구하는 것은 아니며 평문 HTTP/2인 `h2c`도 존재한다.

다만 실제 브라우저 환경에서는 일반적으로 TLS가 적용된 `h2`를 사용한다.

따라서 “HTTP/2는 반드시 HTTPS에서만 동작한다”고 단정하기보다는 다음과 같이 설명하는 것이 정확하다.

> HTTP/2 규격에는 평문 방식도 존재하지만 실제 웹 브라우저에서는 대부분 HTTPS와 TLS를 통해 사용한다.

---

#### 면접 답변

> HTTPS는 HTTP에 TLS를 적용해 기밀성, 무결성, 서버 인증을 제공하는 통신 방식입니다.
>
> TLS 1.3에서는 클라이언트와 서버가 Key Share를 교환하고 ECDHE 등을 이용해 같은 공유 비밀을 계산합니다. 서버는 인증서와 전자서명으로 자신의 신원을 증명하고, 실제 HTTP 데이터는 파생된 대칭키로 암호화합니다.
>
> 대칭키를 서버 공개키로 암호화해 보내는 설명은 과거 RSA Key Exchange를 단순화한 것으로 TLS 1.3의 일반적인 동작과는 다릅니다.

---

### 2.5.5 HTTP/3

HTTP/3는 HTTP 의미 체계를 QUIC 위에서 전달한다.

QUIC은 UDP를 기반으로 하지만 다음 기능을 자체적으로 제공한다.

- 신뢰성 있는 전송
- 패킷 손실 복구
- 혼잡 제어
- Stream Multiplexing
- TLS 1.3 통합
- 연결 이동 지원

```text
HTTP/3
↓
QUIC + TLS 1.3
↓
UDP
↓
IP
```

---

#### HTTP/3가 UDP를 사용하는 이유

TCP는 하나의 순서 있는 Byte Stream을 제공한다.

HTTP/2에서 여러 Stream을 사용하더라도 하나의 TCP Packet이 손실되면 TCP가 해당 Packet을 복구할 때까지 뒤의 데이터 전달이 지연될 수 있다.

QUIC은 Stream 단위로 신뢰성과 순서를 관리한다.

```text
Stream A에서 Packet 손실
→ Stream A는 재전송 대기
→ Stream B와 C는 계속 진행 가능
```

한 Stream의 손실이 다른 Stream의 순서 보장을 직접 막지 않는다.

다만 QUIC도 연결 전체에 혼잡 제어를 적용하므로 패킷 손실이 전체 처리량에 아무 영향도 주지 않는다는 뜻은 아니다.

---

#### 연결 설정 시간 감소

HTTP/2를 HTTPS로 사용하면 일반적으로 다음 연결 과정이 필요하다.

```text
TCP Handshake
→ TLS Handshake
→ HTTP 통신
```

QUIC은 전송 연결과 TLS 1.3을 통합해 연결 설정에 필요한 왕복 횟수를 줄일 수 있다.

재접속에서는 조건에 따라 0-RTT를 사용할 수도 있다.

다만 0-RTT 데이터는 재전송 공격 위험이 있어 멱등성이 보장되지 않는 요청에 신중하게 사용해야 한다.

---

#### Connection ID

TCP 연결은 IP 주소와 Port 변화에 민감하다.

QUIC은 Connection ID를 사용해 네트워크가 바뀌더라도 연결을 유지할 수 있다.

```text
Wi-Fi
→ 모바일 네트워크로 전환
→ Connection ID를 이용해 연결 유지 가능
```

모바일 게임이나 모바일 웹 환경에서 유용할 수 있다.

---

#### HTTP/2와 HTTP/3 비교

| 구분          | HTTP/2                                    | HTTP/3                         |
| ------------- | ----------------------------------------- | ------------------------------ |
| 전송 기반     | TCP                                       | QUIC/UDP                       |
| 멀티플렉싱    | HTTP/2 Stream                             | QUIC Stream                    |
| Header 압축   | HPACK                                     | QPACK                          |
| TLS           | 일반적으로 별도 TLS 사용                  | QUIC에 TLS 1.3 통합            |
| 패킷 손실     | 같은 TCP 연결의 Stream들이 대기할 수 있음 | 손실된 Stream 중심으로 복구    |
| 네트워크 변경 | 연결이 끊어질 수 있음                     | Connection ID로 연결 이동 지원 |

---

## 2.6 HTTP 요청 실습

### JavaScript와 Node.js Fetch

Node.js 18 이상에서는 전역 `fetch()`를 사용할 수 있다.

```js
async function getPlayer(id) {
  const response = await fetch(`https://api.example.com/players/${id}`);

  if (!response.ok) {
    throw new Error(`요청 실패: ${response.status}`);
  }

  const player = await response.json();

  console.log(response.headers.get("content-type"));

  return player;
}

getPlayer(1).then(console.log).catch(console.error);
```

---

### TypeScript Fetch와 런타임 검증

TypeScript Interface는 컴파일 후 사라지므로 API 응답이 실제로 올바른 구조인지 보장하지 않는다.

```ts
interface Player {
  id: number;
  name: string;
  score: number;
}

function isPlayer(value: unknown): value is Player {
  if (typeof value !== "object" || value === null) {
    return false;
  }

  const player = value as Record<string, unknown>;

  return (
    typeof player.id === "number" &&
    typeof player.name === "string" &&
    typeof player.score === "number"
  );
}

async function getPlayer(id: number): Promise<Player> {
  const response = await fetch(`https://api.example.com/players/${id}`);

  if (!response.ok) {
    throw new Error(`요청 실패: ${response.status}`);
  }

  const data: unknown = await response.json();

  if (!isPlayer(data)) {
    throw new Error("올바르지 않은 응답 형식");
  }

  return data;
}
```

실무에서는 Zod, Joi, class-validator 같은 검증 도구를 사용할 수도 있다.

---

### Node.js 내장 HTTPS 모듈

```js
const https = require("node:https");

https
  .get("https://api.example.com/players/1", (response) => {
    let body = "";

    response.setEncoding("utf8");

    response.on("data", (chunk) => {
      body += chunk;
    });

    response.on("end", () => {
      const statusCode = response.statusCode ?? 0;

      if (statusCode < 200 || statusCode >= 300) {
        console.error(`요청 실패: ${statusCode}`);
        return;
      }

      try {
        console.log(JSON.parse(body));
      } catch (error) {
        console.error("JSON 파싱 실패", error);
      }
    });
  })
  .on("error", (error) => {
    console.error("네트워크 오류", error);
  });
```

일반적인 API 호출에서는 `fetch()`나 Axios가 편리하지만 Node.js의 저수준 네트워크 처리 방식을 공부할 때 `http`, `https` 모듈도 알아둘 필요가 있다.

---

### NestJS HttpModule

```ts
import { Module } from "@nestjs/common";
import { HttpModule } from "@nestjs/axios";

@Module({
  imports: [
    HttpModule.register({
      timeout: 3_000,
      maxRedirects: 3,
    }),
  ],
  providers: [PlayerClient],
  exports: [PlayerClient],
})
export class PlayerModule {}
```

```ts
import { Injectable } from "@nestjs/common";
import { HttpService } from "@nestjs/axios";
import { firstValueFrom } from "rxjs";

interface Player {
  id: number;
  name: string;
  score: number;
}

@Injectable()
export class PlayerClient {
  constructor(private readonly httpService: HttpService) {}

  async getPlayer(id: number): Promise<Player> {
    const response = await firstValueFrom(
      this.httpService.get<Player>(`https://api.example.com/players/${id}`),
    );

    return response.data;
  }
}
```

`HttpService.get<Player>()`의 제네릭 타입도 컴파일 단계의 타입 정보일 뿐이다.

외부 API 응답을 신뢰할 수 없다면 런타임 검증을 추가해야 한다.

---

### PHP Guzzle

```php
use GuzzleHttp\Client;

$client = new Client([
    'timeout' => 3.0,
]);

$response = $client->get(
    'https://api.example.com/players/1'
);

$player = json_decode(
    (string) $response->getBody(),
    true,
    512,
    JSON_THROW_ON_ERROR
);

echo $response->getProtocolVersion();
```

`getProtocolVersion()`이 `2.0`을 반환하면 해당 응답이 HTTP/2로 처리된 것이다.

실제 HTTP/2 사용 가능 여부는 Guzzle이 사용하는 cURL과 서버의 지원 여부 등에 따라 달라질 수 있다.

---

## 예상 면접 질문과 답변

### 처리량과 지연 시간의 차이는 무엇인가?

> 처리량은 단위 시간 동안 실제로 처리한 데이터나 요청의 양이고, 지연 시간은 하나의 요청이나 데이터 전달에 걸리는 시간입니다. 서버 증설은 처리량 개선에 도움이 될 수 있고 캐싱, 쿼리 최적화, 가까운 리전 사용은 지연 시간 개선에 도움이 될 수 있습니다.

### TCP/IP 4계층과 OSI 7계층의 차이는 무엇인가?

> OSI 7계층은 통신 과정을 일곱 계층으로 세분화한 참조 모델이고, TCP/IP 4계층은 실제 인터넷 프로토콜 집합을 네 계층으로 설명합니다. OSI의 응용·표현·세션 계층은 TCP/IP의 애플리케이션 계층에 대응하고, 데이터 링크·물리 계층은 네트워크 인터페이스 계층에 대응합니다.

### TCP와 UDP의 차이는 무엇인가?

> TCP는 연결 지향적이며 재전송, 순서 보장, 흐름 제어, 혼잡 제어를 제공합니다. UDP는 연결 설정 없이 데이터그램을 전송하고 이러한 기능을 기본적으로 제공하지 않아 오버헤드가 작습니다. QUIC은 UDP 위에서 신뢰성과 혼잡 제어 등을 자체 구현합니다.

### 라우터와 스위치의 차이는 무엇인가?

> 라우터는 IP 주소와 라우팅 테이블을 이용해 서로 다른 네트워크 사이에서 패킷을 전달합니다. 스위치는 MAC 주소 테이블을 이용해 같은 LAN 안에서 프레임을 적절한 포트로 전달합니다.

### ARP는 어떻게 동작하는가?

> ARP는 같은 IPv4 네트워크에서 IP 주소에 대응하는 MAC 주소를 알아내는 프로토콜입니다. ARP Request를 브로드캐스트하고 해당 IP를 가진 장치가 자신의 MAC 주소를 응답합니다. 목적지가 다른 네트워크라면 원격 서버가 아니라 기본 게이트웨이의 MAC 주소를 찾습니다.

### HTTP/1.1의 HOL Blocking을 HTTP/2가 어떻게 개선했는가?

> HTTP/1.1 Pipelining에서는 응답 순서를 유지해야 하므로 앞선 응답이 느리면 뒤의 응답도 대기합니다. HTTP/2는 요청과 응답을 Stream과 Frame으로 나누고 하나의 연결에서 다중화해 애플리케이션 계층 HOL을 개선했습니다. 다만 TCP 위에서 동작하기 때문에 TCP Packet 손실로 인한 전송 계층 HOL은 남아 있습니다.

### HTTP/2에서는 패킷 손실이 해당 Stream에만 영향을 주는가?

> 아닙니다. HTTP/2는 하나의 TCP 연결을 사용하므로 TCP Packet이 손실되면 재전송이 완료될 때까지 같은 연결의 여러 Stream이 영향을 받을 수 있습니다. Stream별 독립적인 손실 복구는 HTTP/3의 QUIC에서 개선된 부분입니다.

### TLS 핸드셰이크 과정을 설명하라.

> TLS 1.3에서는 클라이언트가 ClientHello에 지원 버전, Cipher Suite, Key Share 등을 전달하고 서버가 ServerHello로 사용할 값을 선택합니다. 양측은 ECDHE 등을 이용해 같은 공유 비밀을 계산하고 서버는 인증서와 전자서명으로 자신의 신원을 증명합니다. 이후 파생된 대칭키로 실제 애플리케이션 데이터를 암호화합니다.

### 비대칭키와 대칭키를 함께 사용하는 이유는 무엇인가?

> 비대칭키 기술은 상대방 인증과 안전한 키 설정에 유용하지만 대용량 데이터 처리 비용이 큽니다. 대칭키 암호화는 빠르기 때문에 핸드셰이크 이후 실제 HTTP 데이터 암호화에 사용합니다.

### HTTP/3가 QUIC을 사용하는 이유는 무엇인가?

> HTTP/2는 여러 Stream을 사용하지만 하나의 TCP 연결을 사용하기 때문에 TCP Packet 손실이 모든 Stream에 영향을 줄 수 있습니다. QUIC은 Stream별로 신뢰성과 순서를 관리해 한 Stream의 손실이 다른 Stream의 진행을 직접 막지 않으며 TLS 1.3을 통합해 연결 설정 지연도 줄일 수 있습니다.

### 리버스 프록시 뒤에서 실제 클라이언트 IP는 어떻게 얻는가?

> 신뢰할 수 있는 프록시가 전달한 `X-Forwarded-For` 정보를 사용합니다. 하지만 헤더를 직접 읽으면 위조될 수 있으므로 Express나 NestJS의 `trust proxy`를 실제 프록시 주소와 네트워크 구조에 맞게 설정하고 `req.ip`를 사용해야 합니다.

---

## 주요 교정 사항

- 처리량과 대역폭은 동일한 개념이 아니다.
- API 응답 시간에는 네트워크뿐 아니라 서버 처리 시간도 포함된다.
- `ping`은 ICMP RTT이며 HTTP 응답 시간을 직접 의미하지 않는다.
- ARP는 원격 서버가 아니라 같은 링크의 장치나 기본 게이트웨이 MAC 주소를 찾는다.
- IPv6에서는 ARP 대신 NDP를 사용한다.
- Gateway는 무조건 애플리케이션 계층 장비를 의미하지 않는다.
- L4 로드밸런서와 L7 로드밸런서는 처리하는 정보가 다르다.
- 허브는 MAC 주소를 보고 브로드캐스트하는 것이 아니라 물리 신호를 모든 포트로 반복한다.
- HTTP/1.1은 지속 연결이 기본이다.
- HTTP/2는 애플리케이션 계층 HOL을 개선하지만 TCP 수준 HOL은 해결하지 못한다.
- HTTP/2에서 Packet 손실이 해당 Stream에만 영향을 준다는 설명은 부정확하다.
- HTTP/2 규격은 TLS를 필수로 요구하지 않지만 브라우저에서는 대부분 HTTPS로 사용한다.
- TLS 1.3에서는 대칭키를 서버 공개키로 암호화해 전송하는 방식으로 설명하면 부정확하다.
- TLS 1.3은 Key Share를 교환하고 공유 비밀로부터 대칭키를 파생한다.
- HTTP/3는 단순히 신뢰성 없는 UDP로 HTTP를 전송하는 것이 아니라 QUIC이 신뢰성, 혼잡 제어, Stream을 제공한다.
- `X-Forwarded-For`를 직접 신뢰하면 클라이언트 IP 위조가 가능하다.
- 프록시 환경에서는 신뢰할 프록시 범위를 설정하고 프레임워크가 계산한 IP를 사용해야 한다.
