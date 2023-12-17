> DNS : 도메인명 기반 서버 조회 서비스

# 1. DNS란?
Domain Named System의 준말이며, www.naver.com 과 같이 사용자가 읽을 수 있도록 서버 주소를 명시적인 이름으로 정의 해둔 것을 뜻합니다.
이를 통해 사용자들은 명시적인 이름으로 특정 서버에 접근 할 수 있으며, 내부적으로 해당하는 이름의 실제 IP를 반환하여 요청을 처리하게 됩니다.
>[!NOTE]
>DNS의 요청은 기본적으로 UDP 기반이다.
>DNS 송수신 정보 자체가 작은 사이즈이기에 유실될 확률도 적고 유실되더라도 접속을 위한 과정이기에 큰 문제가 없다.
# 2. DNS의 주요 기능
1. IP 숫자 대신 가독성이 높은 문자 기반 값을 통한 웹사이트 
2. Host(Domain) 명을 네트워크 서비스에 필요한 IP로 변환
3. 단일 서버의 부하 분산을 위해 하나의 Domain 명에 연결된 여러 IP 서버 연결 지원
4. 성능 향상을 위해 Domain 명을 기반으로 캐싱 기능 제공
5. 최상위 Domain 및 하위 Domain을 포함한 계층적 구조
# 3. DNS 계층 구조

![](../images/Pasted%20image%2020231216210018.png)
## 3-1. DNS 서버 종류
DNS는 계층적 구조를 기반으로하여 아래와 같은 서버들로 구성되어 있다.
### 3-1. ROOT
ICANN(국제 인터넷 주소 관리 기구) 에서 관리하는 최상위 루트 DNS 서버
- TLD 서버들의 IP를 저장해두고 안내하는 기능 담당
### 3-2. TLD
Top Level Domain의 약자로 도메인 등록 기관이 관리하는 서버
- Authoritative DNS Server 주소를 저장해두고 안내하는 기능 담당
### 3-3. Authritative DNS Server (SLD)
- SLD (Second Level Domain) 서버
- 실제 개인 도메인과 IP 주소의 관계가 기록 및 갱신되는 서버
- 도메인/호스팅 업체의 네임서버 혹은 개인이 구축한 DNS 서버 등에 해당
### 3-4. Recursive DNS Server
- Local DNS Server, 사용자가 가장 먼저 접근하는 서버
- 한번 조회한 데이터를 캐싱해두고, 이후 동일한 이름의 도메인 접근 시 캐싱된 IP 기반 요청 처리
	- 없을 경우 상위 서버에 요
- 이통3사(SKT, KT, LGU+) 등에서 제공하는 DNS 서버 등에 해

# Ref
- https://cano721.tistory.com/20
- https://www.akamai.com/ko/glossary/what-is-dns