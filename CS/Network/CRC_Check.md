> CRC 기반 오류 검증

# 1. CRC란?
CRC (Circlic Redundancy Checking)이란, 순환 중복 검사라는 뜻을 지니고 있다.
주로 하드웨어 레벨에서의 데이터 송수신간 오류 검출 등에 사용된다.

## 1-1. CRC 흐름
1. 데이터 송신 측에서는 데이터 값에 대한 CRC 계산 값을 전송 데이터 끝에 추가하여 전송한다.
2. 데이터 수신 측은 전달받은 데이터에서 CRC 값을 계산하여 전달받은 데이터와의 정합성을 검사한다.
	1. 해당 결과에 따라 validation 여부가 결정된다.

>[!TIP]
> 주로, [OSI 7계층 기준](obsidian://open?vault=markdown_docs&file=tech%2FCS%2Fnetwork%2FOSI_7_Layers) DataLink 계층에서 프레임 송수신 간 데이터 검증에 사용된다.

## 1-2 CRC 계산 방식

![](../images/Pasted%20image%2020231216185632.png)
### 송신측

1. CRC 코드 지정
2. CRC 코드의 최고 차수만큼 전달할 데이터 값 뒤에 0를 추가한다.
3. 전달할 데이터에 0이 추가된 확장 데이터를 포함하여 CRC 코드로 mod-2 연산을 수행한다.
	-  *mod-n 연산이란, 0부터 n - 1 까지의 제한된 정수 n개를 산술 연산하는 것*
> [!NOTE]
> **mod-2 연산 방식**
> 
> 0 +(-) 0 = 0
> 0 +(-) 1 = 1
> 1 +(-) 0 = 1
> 1 +(-) 1 = 1
4. 나머지가 0인 경우, 데이터를 그대로 전달
5. 나머지가 0이 아닌 경우 기존 데이터에 나머지를 붙여서 전달

### 수신측

1. 데이터와 함께 수신된 CRC 코드를 기반으로 데이터를 연산
2. 나머지가 0이면 정상 데이터
3. 나머지가 0이 아니면 비정상 데이터로 판단단

# Ref
- https://sunro1994.tistory.com/12
- https://smashingpumpkins.tistory.com/entry/%EB%8D%B0%EC%9D%B4%ED%84%B0%EB%A7%81%ED%81%AC-%EA%B3%84%EC%B8%B5%EC%9D%98-%EA%B8%B0%EC%88%A0%EB%AC%BC%EB%A6%AC%EA%B3%84%EC%B8%B5
- https://east-star.tistory.com/26