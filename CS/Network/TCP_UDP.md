> 전송 계층에 쓰이는 TCP/UDP 프로토콜 관련 정리
# 1. TCP/UDP란
## 1-1. TCP : Transmission Control Protocol
양 종단 호스트 간 신뢰성 있는 데이터 송수신을 위한 연결 지향 프로토콜
- TCP가 제공하는 연결성 회선을 통해 데이터를 송수신
- 신뢰성 보장
### 1-1-2. TCP 헤더
TCP에서 제공하는 주요 기능들인 흐름 제어와 데이터에 대한 신뢰성 보장, 패킷 재조립 등에 필요한 정보를 TCP 패킷 전달 시 헤더 내에 주입하여 함께 전달하여 사용한다.
![[Pasted image 20231216211856.png]]
### 1-1-3. TCP 연결 구성
TCP는 데이터 송신을 위한 Connection 연결 및 데이터 전송을 `3-handshake 방식`으로 진행한다.
![[Pasted image 20231217001217.png]]
1. 송신 측에서 수신 측으로 `SYN` 패킷을 보내어 연결을 요청한다.
2. 수신 측에서 SYN 패킷을 받았으며 거기에 `ACK` 패킷을 더해 요청받을 준비가 되었다는 것을 전달한다.
3. 송신 측에서는 수신 측에서 온 `SYN + ACK` 패킷을 통해 연결 준비가 되었다는 것을 인지한 후 데이터를 보낸다.
### 1-1-4. TCP 연결 해제
TCP는 데이터 송수신 후 관련 Connection 해제 시에는 `4-handshake` 방식 으로 진행한다.
![[Pasted image 20231217001503.png]]
1. 클라이언트가 세션을 종료하겠다는 `FIN` 플래그를 보낸다.
2. 서버는 FIN 플래그를 받았으면 일단 `ACK`를 보내 응답한다.
3. 이 후 실제 서버가 통신이 끝났으면 종료 하겠다는 `FIN`을 클라이언트에 보낸다.
4. 클라이언트는 확인했다는 메시지(`ACK`)를 보낸다.
	>[!NOTE]
	>**TIME_WAIT 상태**
	>
	>Client에서 세션을 종료하더라도 일정 시간 (default : 240s) 동안은 서버에서 뒤늦게 오는 패킷들을 받기 위해 세션을 완전히 종료하지 않는데 이를 `TIME_WAIT 상태`라고 한다.
	
	
## 1-2. UDP : User Datagram Protocol
비연결성이고 신뢰성을 제공하지 않으나 일대다 통신 지원 프로토콜
- 빠른 요청 및 응답이 주요 장점
- 1 : N 에 대한 통신 기능 제공
- TCP에 비해 header도 간단하기에 헤더 처리에도 상대적으로 빠름
# Ref
- https://mindnet.tistory.com/entry/%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC-%EC%89%BD%EA%B2%8C-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0-22%ED%8E%B8-TCP-3-WayHandshake-4-WayHandshake
- https://aws-hyoh.tistory.com/entry/TCPIP-%EC%89%BD%EA%B2%8C-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0
- https://www.ibm.com/docs/ko/aix/7.1?topic=protocol-tcpip-protocols