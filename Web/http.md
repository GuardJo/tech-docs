> HTTP 프로토콜 내용 정리
# 1. HTTP란?
HyperText Transfer Protocol의 약자로 인터넷 상에서 클라이언트와 서버 사이의 데이터 송수신을 위한 프로토콜의 일종이다. [[../CS/Network/OSI_7_Layers|OSI 7 계층]] 중 Application Layer 계층의 프로토콜로써 TCP/IP 프로토콜 상위에 위치해 있다.

>[!NOTE]
>**서버/클라이언트 모델**
>
>비스에 대한 제공자와 소비자로 구분되는 네트워크 모델로써, 제공자 측은 소비자 측에서 요청하는 데이터들에 대한 송신을 담당하며, 소비자는 요청한 데이터에 대한 수신만을 담당한다.
## 1-1. HTTP 동작
HTTP 프로토콜은 기본적으로 80 port를 통해 데이터를 송수신한다.
클라이언트에서 요청을 보내면 서버는 요청에 대한 동작을 수행하여 응답한다.
### 1-1-1. HTTP 구조
![](images/Pasted%20image%2020231217133129.png)
- Method
	GET, POST, PUT, PATCH, DELETE 등 요청 방식
- Path
	요청하는 자원 위치 (URL)
- Version of the protocol
	브라우저(클라이언트)가 사용하는 프로토콜 버전

**동작 순서**
1. 요청 URL 분석 및 접속
	- 클라이언트는 요청 보낼 URL을 서버의 IP 및 해당 포트 (default : 80)로 연결을 요청
2. Request Header 전송
	- 서버에서 정상적으로 연결 요청을 수신했을 경우, cookie, content-type 등의 헤더 정보를 서버에 전달
1. Request Body 전송
	- body 요청이 필요할 경우 json, xml 등 특정 형태의 데이터 타입을 body로 전달
2. Response Header 확인
	- 서버에서 처리 후 응답 결과 내 header 에 포함된 상태 코드나 body의 content-type 등을 확인
3. Response Body 확인
	- body가 존재하는 경우 response header에 명시된 content-type 에 따라 데이터를 식별
## 1-2. HTTP 주요 특징
### 1-2-1. Connectless
HTTP의 경우 클라이언트의 요청에 따라 서버와 연결, 응답 데이터를 수신한 경우에는 해당 연결을 끊는다. 
- *즉, 요청마다 연결 및 해제 등의 작업을 통해 각각의 요청을 별개로 구분한다.*
### 1-2-2. Stateless
HTTP가 Connectless 하기에 포함되는 속성으로, 요청마다 연결을 맺고 끊기에 해당 데이터 통신에 대한 상태를 별도로 기록할 수 없다. 
- *로그인 요청에 따라 계정 정보를 받아왔더라도 다른 요청을 할 때 앞서 요청 받은 계정 정보를 알 수 가 없다.*

>[!NOTE]
>HTTP의 이러한 특징들로 인해, 상태를 저장하기 위해서 [[Session_and_Cookie|Session 과 Cookie]]를 통한 상태 관리 등의 방법이 존재하며, [[WebScoket_Protocol|WebSocket]]과 같이 Connectless 한 HTTP 환경에서 지속적인 데이터 송수신을 위한 방법 등이 생겨났다.
## 1-3. HTTP Method
HTTP에서는 Method를 통해 해당 자원에 대한 처리를 명시하여 서버가 처리할 수 있게끔 요청한다.

**GET**
- 요청 URL 에 해당하는 자원 반환 요청

**POST**
- 요청 URL로 자원의 생성을 요청

**PATCH**
- 요청 URL에 대한 자원 수정/갱신 요청

**PUT**
- 요청 URL에 대한 자원 대체 요청

**DELETE**
- 요청 URL에 대한 자원 제거 요청

**HEAD**
- 요청 URL에 대한 응답 헤더만을 요청
	- 주로 서버의 문제 여부 파악 등

**OPTION**
- 요청 URL에서 처리 가능한 method 목록 요청
## 1-4. HTTP Status Code
HTTP에서 서버측에서 요청에 대한 응답을 보낼 때 Header에 statusCode 값을 함께 보낸다.
이는 서버측에서 응답에 대한 결과 코드이며 아래의 종류들로 이루어져있다.
### 1-4-1. 정보 (1xx)
요청을 받았으며, 이후 프로세스를 진행
- HTTP 1.0에선 제공하지 않는 기능
### 1-4-2. 성공 (2xx)
요청을 성공적으로 받음
- `200` OK
	요청이 성공적으로 수행됨
- `201` Created
	요청 성공 및 요청에 따른 데이터가 생성됨
- `202` Accepted
	요청을 수신하였지만, 그에 응답을 보낼 수 없음
	- *다른 프로세스에서 점유하거나, 배치 프로세스 진행 중임을 위한 응답*
- `204` No Content
	요청에 대한 응답 데이터를 보낼게 없지만 헤더는 보냄
### 1-4-3. Redirection (3xx)
요청에 대한 추가 작업 필요
- `301` Moved Permanently
	요청한 자원에 대한 URL이 변경되었음 (**영구적**)
- `302` Found
	요청한 자원에 대한 URL이 변경되었음 (**임시**)
- `304` Not Modified
	요청한 URL 에 대한 자원이 별도로 갱신되지 않았음
	- *이는 해당 URL에 대한 응답 데이터가 동일할 것이기에 캐싱할 수 있단 뜻*
### 1-4-4. Client Error (4xx)
요청 값이 올바르지 않거나 해당 요청을 처리할 수 없음
- `400` Bad Request
	요청에 대한 문법 등이 잘못된 경우
- `401` Unauthorized
	요청을 보낸 클라이언트의 인증 정보가 없는 경우
	- *비인증 사용자* 
- `403` Forbidden
	요청을 보낸 클라이언트가 해당 자원에 대한 접근 권한이 없는 경우
	- *서버는 해당 클라이언트가 누군지 알았으나 권한은 없음*
- `404` Not Found
	요청 자원에 대해 서버가 찾지 못했을 경우
	- *요청 URL 자체가 없거나, URL은 존재하나 해당하는 자원을 찾지 못했을 경우*
- `409` Conflict
	요청 요소가 현재 서버 상태와 충돌될 때
	
### 1-4-5. Server Error (5xx)
요청은 정상적이나 서버 측에서 해당 요청 처리 과정에서 오류가 발생함
- `500` Internal Server Error
	서버 내부에 문제가 발생한 경우
- `501` Not Implemented
	서버가 해당 요청에 대한 기능을 지원하지 않음
- `502` Bad Gateway
	서버가 게이트웨이로부터 잘못된 응답을 수신
- `503` Service Unavailable
	서버가 요청을 처리할 준비가 되어 있지 않음
	- *주로 서버 과부하 상태나 잠시 중단된 상태일 경우, 해당 코드와 함께 상세 메시지를 전달해야 함*
# Ref
- https://velog.io/@dnjscksdn98/HTTP-%ED%94%84%EB%A1%9C%ED%86%A0%EC%BD%9C%EC%97%90-%EB%8C%80%ED%95%98%EC%97%AC
- https://mangkyu.tistory.com/98
- https://www.whatap.io/ko/blog/40/
- https://nsinc.tistory.com/168