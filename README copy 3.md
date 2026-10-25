### 4.1 스트림 형식 (Stream)

```js
// 길이 접두사 방식 패킷 파싱 (Node.js)
class PacketParser {
  private buffer = Buffer.alloc(0);

  // 수신 데이터를 버퍼에 누적하고 완성된 패킷 추출
  feed(data: Buffer): Buffer[] {
    this.buffer = Buffer.concat([this.buffer, data]);
    const packets: Buffer[] = [];

    while (this.buffer.length >= 4) {
      // 앞 4바이트에서 패킷 길이 읽기
      const packetLength = this.buffer.readUInt32BE(0);

      if (this.buffer.length < 4 + packetLength) break; // 아직 완성 안 됨

      const packet = this.buffer.slice(4, 4 + packetLength);
      packets.push(packet);
      this.buffer = this.buffer.slice(4 + packetLength); // 처리한 만큼 제거
    }

    return packets;
  }

  // 메시지를 길이 접두사 포함 버퍼로 직렬화
  static serialize(message: object): Buffer {
    const body = Buffer.from(JSON.stringify(message), 'utf8');
    const header = Buffer.alloc(4);
    header.writeUInt32BE(body.length, 0); // 앞 4바이트에 길이 기록
    return Buffer.concat([header, body]);
  }
}
```

### 4.2 메시지 형식 (Message / Datagram)

데이터가 **독립적인 단위(패킷)** 로 전달됩니다. UDP가 대표적입니다.

```
[메시지 형식]

전송: [패킷A] [패킷B] [패킷C]
수신: [패킷A] [패킷B] [패킷C]  ← 각 패킷의 경계가 보존됨

→ 수신 측에서 별도 파싱 불필요, 대신 크기 제한 있음
```

UDP는 하나의 데이터그램이 그대로 수신되므로 경계 처리가 단순합니다. 단, MTU(최대 전송 단위, 보통 1500바이트)를 초과하면 분열(Fragmentation)이 발생하므로 패킷 크기를 관리해야 합니다.

---

## 5. 컴퓨터 네트워크 식별자

네트워크에서 특정 컴퓨터와 프로세스를 식별하는 요소들입니다.

### IP 주소

네트워크 상의 컴퓨터 주소입니다.

- **공인 IP(Public IP):** 인터넷에서 유일한 주소
- **사설 IP(Private IP):** 내부 네트워크에서만 사용 (`192.168.x.x`, `10.x.x.x`, `172.16~31.x.x`)

### 포트 번호 (Port)

같은 컴퓨터 안의 어떤 프로세스(서비스)에 접속할지 구분합니다. 0~65535 범위.

```
IP 주소:포트 = 특정 컴퓨터의 특정 프로세스
예: 192.168.0.10:3000 → 3000번 포트에서 실행 중인 서버
```

| 포트 범위   | 용도                                           |
| ----------- | ---------------------------------------------- |
| 0~1023      | 잘 알려진 포트 (HTTP: 80, HTTPS: 443, SSH: 22) |
| 1024~49151  | 등록된 포트 (애플리케이션이 사용)              |
| 49152~65535 | 동적/사설 포트 (임시 클라이언트 포트)          |

### DNS (Domain Name System)

도메인 이름을 IP 주소로 변환하는 시스템입니다.

```
game.example.com → DNS 조회 → 1.2.3.4
```

```js
// NestJS에서 DNS 조회
import * as dns from "dns/promises";

const addresses = await dns.lookup("game.example.com");
console.log(addresses); // { address: '1.2.3.4', family: 4 }
```

### 소켓 (Socket)

**IP 주소 + 포트 번호**의 조합으로, 네트워크 통신의 양쪽 끝점입니다.

```
클라이언트 소켓: 203.0.113.5:54321
서버 소켓:       1.2.3.4:3000

→ 이 쌍(4-tuple)이 하나의 TCP 연결을 유일하게 식별
```

---

## 6. 컴퓨터 네트워크의 품질과 특성

### 6.1 네트워크 품질을 저해하는 것들

| 요인                  | 설명                              | 게임 서버 영향              |
| --------------------- | --------------------------------- | --------------------------- |
| **패킷 유실**         | 패킷이 목적지에 도달하지 못함     | 위치 동기화 오류, 입력 누락 |
| **지터(Jitter)**      | 패킷 도착 간격이 불규칙함         | 움직임이 끊김(끊김 현상)    |
| **레이턴시(Latency)** | 패킷 전달에 걸리는 시간           | 반응 지연, 입력 지연        |
| **대역폭 부족**       | 한 번에 전송 가능한 데이터량 한계 | 패킷 밀림, 추가 지연        |

### 6.2 전송 속도와 전송 지연 시간

**대역폭(Bandwidth)** 과 **레이턴시(Latency)** 는 별개의 개념입니다.

```
[대역폭 vs 레이턴시 비유]

고속도로 폭(차선 수) = 대역폭 (많을수록 한 번에 많이 보냄)
서울→부산 거리     = 레이턴시 (아무리 넓어도 거리는 단축 안 됨)
```

- **왕복 시간(RTT, Round Trip Time):** 패킷을 보내고 응답을 받기까지의 시간
- 게임에서는 RTT가 낮을수록 반응성이 좋음 (FPS 게임: 30ms 이하 목표)

### 6.3 네트워크 품질 기준 세 가지

1. **레이턴시(Latency):** 전달 시간 — ms 단위
2. **처리량(Throughput):** 단위 시간당 전달 데이터량 — Mbps 단위
3. **패킷 유실률(Packet Loss Rate):** 전체 패킷 중 유실 비율 — % 단위

### 6.4 무선 네트워크의 품질

Wi-Fi와 모바일(LTE/5G) 네트워크는 유선보다 불안정합니다.

- **지터가 크다:** 신호 간섭, 기지국 혼잡
- **패킷 유실이 잦다:** 전파 환경 변화
- **레이턴시 변동이 크다:** 핸드오버(기지국 전환) 시 순간 지연

모바일 게임 서버 설계 시 이러한 특성을 고려해야 합니다.

```js
// NestJS: 네트워크 품질 모니터링 (RTT 측정 ping/pong)
@WebSocketGateway()
export class GameGateway {
  @SubscribeMessage('ping')
  handlePing(client: Socket, payload: { timestamp: number }) {
    // 클라이언트가 보낸 timestamp를 그대로 반환
    client.emit('pong', { timestamp: payload.timestamp });
  }
}

// 클라이언트 측에서 RTT 계산
// sendTime = Date.now();
// socket.emit('ping', { timestamp: sendTime });
// socket.on('pong', ({ timestamp }) => {
//   const rtt = Date.now() - timestamp;
//   console.log(`RTT: ${rtt}ms`);
// });
```

---

## 7. UDP 네트워킹

**UDP(User Datagram Protocol)** 는 연결 없이 데이터그램을 전송하는 프로토콜입니다.

### 특징

| 항목      | 내용                            |
| --------- | ------------------------------- |
| 연결      | 비연결형 (연결 수립 과정 없음)  |
| 순서 보장 | ❌                              |
| 도달 보장 | ❌                              |
| 속도      | 빠름 (오버헤드 낮음)            |
| 헤더 크기 | 8바이트 (TCP 20바이트보다 작음) |

### 게임에서 UDP를 쓰는 이유

- **실시간 위치 동기화:** 이전 위치 데이터는 최신 데이터로 대체되므로 유실돼도 무방
- **낮은 레이턴시:** TCP의 재전송 대기 없이 즉시 전송
- **빠른 FPS, 레이싱 게임:** 약간의 유실보다 낮은 지연이 더 중요

```
[UDP 패킷 구조]
┌──────────┬──────────┬────────────┬──────────┬──────────────┐
│ 출발지 포트 │ 목적지 포트 │ 데이터 길이 │ 체크섬   │   데이터    │
│  (2바이트)  │  (2바이트)  │  (2바이트)  │ (2바이트) │  (가변 길이) │
└──────────┴──────────┴────────────┴──────────┴──────────────┘
```

```js
// Node.js UDP 소켓 예시
const dgram = require("dgram");

// UDP 서버
const server = dgram.createSocket("udp4");

server.on("message", (msg, rinfo) => {
  console.log(`수신: ${msg} from ${rinfo.address}:${rinfo.port}`);

  // 응답 전송
  const response = Buffer.from("pong");
  server.send(response, rinfo.port, rinfo.address);
});

server.bind(9000, () => {
  console.log("UDP 서버 포트 9000에서 대기 중");
});

// UDP 클라이언트
const client = dgram.createSocket("udp4");
const message = Buffer.from("ping");
client.send(message, 9000, "localhost", (err) => {
  if (err) client.close();
});

client.on("message", (msg) => {
  console.log(`응답: ${msg}`);
  client.close();
});
```

---

## 8. TCP 네트워킹

**TCP(Transmission Control Protocol)** 는 연결 기반의 신뢰성 있는 전송 프로토콜입니다.

### 특징

| 항목      | 내용                                 |
| --------- | ------------------------------------ |
| 연결      | 연결형 (3-way handshake로 연결 수립) |
| 순서 보장 | ✅                                   |
| 도달 보장 | ✅ (재전송 메커니즘)                 |
| 속도      | 상대적으로 느림 (오버헤드 있음)      |
| 흐름 제어 | ✅ (수신 버퍼 기반)                  |
| 혼잡 제어 | ✅ (네트워크 상태 감지)              |

### TCP 3-way Handshake (연결 수립)

```
클라이언트                    서버
    │──── SYN ───────────────→│   "연결하고 싶어요"
    │←─── SYN + ACK ──────────│   "좋아요, 연결합시다"
    │──── ACK ───────────────→│   "확인했습니다"
    │                          │
    │←── (데이터 송수신) ──────│
```

### TCP 4-way Handshake (연결 종료)

```
클라이언트                    서버
    │──── FIN ───────────────→│   "연결 끊겠습니다"
    │←─── ACK ────────────────│   "알겠습니다"
    │←─── FIN ────────────────│   "저도 끊겠습니다"
    │──── ACK ───────────────→│   "확인했습니다"
```

### 게임에서 TCP를 쓰는 경우

- **로그인, 캐릭터 데이터 로드:** 절대 유실되면 안 되는 중요 데이터
- **채팅:** 순서와 도달이 보장되어야 함
- **거래, 아이템 획득:** 데이터 정합성 필수
- **MMORPG의 대부분 통신**

```js
// NestJS WebSocket (Socket.IO — TCP 기반)
import {
  WebSocketGateway, WebSocketServer, SubscribeMessage,
  OnGatewayConnection, OnGatewayDisconnect
} from '@nestjs/websockets';
import { Server, Socket } from 'socket.io';

@WebSocketGateway({ cors: true })
export class GameGateway implements OnGatewayConnection, OnGatewayDisconnect {
  @WebSocketServer()
  server: Server;

  private connectedPlayers = new Map<string, PlayerInfo>();

  handleConnection(client: Socket) {
    console.log(`플레이어 접속: ${client.id}`);
  }

  handleDisconnect(client: Socket) {
    const player = this.connectedPlayers.get(client.id);
    if (player) {
      // 같은 룸의 다른 플레이어에게 퇴장 알림
      client.to(player.roomId).emit('playerLeft', { playerId: client.id });
      this.connectedPlayers.delete(client.id);
    }
  }

  @SubscribeMessage('joinRoom')
  handleJoinRoom(client: Socket, payload: { roomId: string; playerName: string }) {
    client.join(payload.roomId);
    this.connectedPlayers.set(client.id, {
      id: client.id,
      name: payload.playerName,
      roomId: payload.roomId,
    });

    // 룸의 다른 플레이어에게 알림
    client.to(payload.roomId).emit('playerJoined', {
      playerId: client.id,
      playerName: payload.playerName,
    });
  }
}
```

---

## 9. 패킷 유실 시 UDP와 TCP에서 현상

같은 패킷 유실이 발생해도 UDP와 TCP의 반응은 완전히 다릅니다.

### UDP에서 패킷 유실

```
전송: [패킷1] [패킷2] [패킷3] [패킷4]
수신: [패킷1]         [패킷3] [패킷4]  ← 패킷2 유실, 그냥 지나침

→ 유실된 패킷 무시, 이후 패킷은 정상 처리
→ 게임에서 위치 데이터 유실 = 잠깐 튀는 현상
```

### TCP에서 패킷 유실

```
전송: [패킷1] [패킷2] [패킷3] [패킷4]
수신: [패킷1]  유실   [패킷3] [패킷4]

→ TCP는 패킷2를 재전송 요청
→ 패킷2가 도착할 때까지 패킷3, 4도 응용 계층에 전달 못 함
→ HOL(Head-of-Line) Blocking: 유실 하나가 이후 데이터 전체를 지연시킴
```

이 HOL Blocking 때문에 실시간 게임에서는 UDP가 선호됩니다. 패킷 하나의 유실이 이후 패킷 전체를 막지 않습니다.

```
[실시간 FPS 게임 패킷 유실 비교]

UDP: 프레임 t=100ms 데이터 유실 → t=110ms 데이터로 바로 업데이트
     (약간 튀지만 계속 진행)

TCP: 프레임 t=100ms 데이터 유실 → 재전송 대기 → 100~150ms 구간 모두 지연
     (끊김 현상 발생)
```

---

## 10. 주로 사용하는 메시지 형식

게임 서버에서 패킷을 직렬화하는 주요 포맷입니다.

### JSON

```json
{
  "type": "playerMove",
  "playerId": 12345,
  "position": { "x": 100.5, "y": 0.0, "z": 200.3 },
  "timestamp": 1720000000000
}
```

- **장점:** 사람이 읽기 쉬움, 디버깅 용이, 언어/플랫폼 무관
- **단점:** 크기가 크고 파싱 비용이 높음
- **적합한 용도:** REST API, 로그인/캐릭터 데이터 등 빈도가 낮은 통신

### Protocol Buffers (protobuf)

Google이 만든 이진(binary) 직렬화 포맷입니다.

```protobuf
// player.proto
message PlayerMove {
  uint32 player_id  = 1;
  float  x          = 2;
  float  y          = 3;
  float  z          = 4;
  uint64 timestamp  = 5;
}
```

- **장점:** JSON 대비 3~10배 작은 크기, 파싱 속도 빠름
- **단점:** 사람이 읽기 어려움, 스키마 관리 필요
- **적합한 용도:** 실시간 위치 동기화, 빈도 높은 게임 상태 동기화

```js
// NestJS에서 protobuf 사용 예시
import { Injectable } from '@nestjs/common';
import * as protobuf from 'protobufjs';

@Injectable()
export class ProtobufService {
  private PlayerMove: protobuf.Type;

  async onModuleInit() {
    const root = await protobuf.load('src/proto/player.proto');
    this.PlayerMove = root.lookupType('PlayerMove');
  }

  encode(data: object): Buffer {
    const message = this.PlayerMove.create(data);
    return Buffer.from(this.PlayerMove.encode(message).finish());
  }

  decode(buffer: Buffer): object {
    return this.PlayerMove.decode(buffer) as unknown as object;
  }
}
```

### MessagePack

JSON과 호환되면서 이진 포맷으로 직렬화합니다.

- JSON보다 20~30% 작은 크기
- 별도 스키마 불필요 (JSON처럼 구조 자유)
- protobuf보다는 크지만 도입 비용이 낮음

### 포맷 선택 가이드

| 상황                              | 추천 포맷       |
| --------------------------------- | --------------- |
| 실시간 위치/상태 동기화 (UDP)     | protobuf        |
| REST API, 로그인, 설정            | JSON            |
| 기존 JSON 구조 유지하며 성능 개선 | MessagePack     |
| 내부 마이크로서비스 통신          | protobuf (gRPC) |

---

## 11. 네트워크 주소 변환 (NAT)

**NAT(Network Address Translation)** 는 사설 IP를 공인 IP로 변환해 인터넷 통신을 가능하게 하는 기술입니다.

```
[NAT 동작 원리]

사설 IP (내부망)              NAT 장비(공유기)         인터넷
192.168.0.10:54321 ──────→ 공인IP:1024 ──────→ 게임서버
192.168.0.11:54322 ──────→ 공인IP:1025 ──────→ 게임서버
192.168.0.12:54323 ──────→ 공인IP:1026 ──────→ 게임서버
```

### P2P 게임에서 NAT의 문제

P2P(Player-to-Player) 직접 연결 방식의 게임에서, NAT 뒤에 있는 두 플레이어가 직접 연결하려면 **NAT 통과(NAT Traversal)** 기술이 필요합니다.

- **STUN:** 자신의 공인 IP/포트를 알아내는 방법
- **TURN:** 릴레이 서버를 통한 우회 연결 (NAT 통과 실패 시 사용)
- **ICE:** STUN/TURN을 조합한 최적 경로 탐색 (WebRTC에서 사용)

### 게임 서버 방식의 NAT 우회

중앙 게임 서버를 두면 클라이언트는 모두 서버에 아웃바운드로 연결하므로 NAT 문제가 없습니다. 대부분의 온라인 게임이 이 방식을 채택합니다.

```
[중앙 서버 방식 — NAT 문제 없음]

플레이어A (NAT 뒤) ──→ 게임 서버 (공인 IP) ←── 플레이어B (NAT 뒤)
                   ←── 서버가 중계 ──→
```

---

## 마무리

게임 서버 개발에서 네트워크 이해는 필수입니다.

**핵심 정리:**

| 개념          | 게임 서버 적용 포인트                             |
| ------------- | ------------------------------------------------- |
| OSI 계층      | 문제 발생 시 어느 계층인지 진단                   |
| TCP vs UDP    | 실시간 동기화 → UDP, 중요 데이터 → TCP            |
| 레이턴시/지터 | 게임 품질의 핵심 지표, RTT 모니터링 필수          |
| 패킷 포맷     | 실시간 통신엔 protobuf, API엔 JSON                |
| NAT           | 중앙 서버 방식으로 우회                           |
| 스트림 파싱   | TCP 사용 시 길이 접두사 방식으로 메시지 경계 처리 |

---

_참고 도서: 게임 서버 프로그래밍 교과서 (Proudmoor, 한빛미디어)_
