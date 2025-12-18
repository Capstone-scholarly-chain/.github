## [캡스톤디자인] Hyperledger 블록체인을 이용한 학생회 장부 시스템 - Scholarly Chain

**프로젝트 기간:** 2025.03.01 ~ 2025.06.03 <br>
**팀원:** 기획 및 디자인 1명, 프론트엔드 1명, 백엔드 1명, 블록체인 및 인프라 1명

### 프로젝트 주제: 
학생회비 장부를 학과 구성원들과 투명하게 공개하는 블록체인 기반 클라우드 네이티브 웹 서비스

### 프로젝트 목표: 
학생회만의 노력에 그치지 않고 학생들의 능동적인 참여를 바탕으로, 학생회 공금 횡령 문제를 예방하며 신뢰할 수 있는 학생 자치 문화를 형성하는 것

<br>

### 기술 스택

![Frame 71](https://github.com/user-attachments/assets/4638b975-33cb-4389-ab02-5b38693c21df)

| 분류 | 기술 |
| --- | --- |
| Frontend | Next.js, Vercel |
| Backend | Spring Boot, NestJS, Firebase Cloud Messaging |
| Database | Redis, MySQL, Firebase |
| Blockchain | Hyperledger Fabric |
| Infra | AWS, Amazon EKS, Amazon S3 |

<br>

#### 왜 블록체인인가?
- 블록체인은 **탈중앙화, 투명성, 불변성, 합의 메커니즘, 스마트 컨트랙트**라는 다섯 가지 핵심 특징을 가짐
- 블록체인은 암호화 해시와 분산 네트워크 구조이기에 기록 조작이 불가능한 구조
- 모든 거래와 상태 변화가 블록에 순차적으로 기록되고 네트워크 전체에서 검증되기 때문에 승인 과정까지 투명하게 기록 가능
- 특히 Hyperledger Fabric은 허가형 네트워크로 사전 승인된 참여자들만 접근할 수 있고, 다중 조직의 합의를 통해 완전한 감사 추적이 가능

<br>

### 서비스 설명

**학생 입금 내역 신청과 학생회 출금 내역 신청**<br> 
학생) 행사명/연도/학기/금액/증빙 자료으로 입금 내역 신청<br>
학생회) 행사명/연도/학기/금액/증빙 자료으로 출금 내역 신청<br>
학생회 출금 내역 신청의 경우, **학생들이 학생회의 출금 내역을 보고 능동적으로 투표에 참여하여 해당 출금 내역을 허가할 것인지 선택 가능**

![image](https://github.com/user-attachments/assets/22f4a83e-69f4-4b8c-9044-4d82c7a097fc)

<br>

### 시스템 아키텍처

AWS EKS 기반 다중 가용영역(ap-northeast-2a, ap-northeast-2b) 클러스터로 구성
- **Frontend**: Next.js + Vercel 배포
- **Backend**: Spring Boot (프론트 통신), NestJS (블록체인 통신)
- **Blockchain**: Hyperledger Fabric 네트워크 (EKS 내 Pod로 운영)
- **Storage**: S3 (증빙 이미지), Firebase (인증서/개인키)

<img width="764" height="695" alt="image" src="https://github.com/user-attachments/assets/a72d732a-3a78-4ccb-a87a-5bb8119065b8" />


### 체인코드 주요 로직

#### (1) 학생/학생회 가입 신청 & 승인
- 성공적으로 스마트계약이 발생하면 계약이 생성되었다는 이벤트 발생
- 학생회원이 이전 스마트계약 RequestId로 승인 함수 실행시키면 Status를 PENDING → APPROVED로 변경

#### (2) 입금 기입 요청/승인
- 요청한 사람이 학생 조직원인지 검사
- PENDING 상태의 이전의 정의한 장부 구조체를 이용한 스마트 계약 생성
- 승인한 유저가 학생회원인지 검사 후 PENDING → APPROVED로 상태 변경

#### (3) 출금 기입 요청/승인
- 학생회 구성원인지 판단하고 지출내역 추가 체인코드 함수 실행
- 1시간 동안 유효한 투표를 발생하고 1시간 동안 모인 학생들의 승인/거절 비율로 결정
- 주기적으로 NestJS 서버에서 이 투표가 1시간 지났는지 검사를 한 후 지났으면 개표하도록 함

<br>

### 백엔드 사용 기술

#### Spring Boot
- 프론트엔드와의 메인 통신 서버
- 회원가입 & 로그인 (Basic Auth)
- JWT를 사용하여 인가(Authorization)
- 입금 & 출금 내역 등록
- AWS S3을 이용한 이미지(지출 내역) 업로드
- FCM을 이용한 알림 전송

#### NestJS
- Fabric SDK를 활용한 블록체인과의 직접적인 통신
- 블록체인 이벤트를 구독하여 Spring Boot에 전달

#### Redis Streams
- Spring과 NestJS 서버 간 통신에 사용
- Redis Streams은 메시지가 전달 후 사라지지 않고, ACK를 통해 수신 여부 체크 가능
- 다중 Consumer 존재시, Consumer Group을 통해 각각 나눠서 처리가 가능

<br>

### 이벤트 기반 아키텍처

**문제 상황**: Spring의 서비스A가 NestJS 이벤트를 구독하고 동시에 서비스B가 NestJS로부터 오는 다른 메시지를 처리하는데, 처리 과정에서 서비스A가 서비스B를 참조하고, 서비스B도 서비스A를 참조하는 순환 참조 문제가 발생

**해결 방법**: 구독을 이벤트 리스너를 사용하여 비동기/단방향으로 처리
- 이벤트 처리 리스너는 @EventListener로 동작
- Spring ↔ Redis ↔ NestJS 간 Pub/Sub 메시지 통신

<br>

### SDK를 통한 블록체인 & 백엔드 통신

#### 블록체인과 직접적인 통신
- NestJS에서 Fabric SDK를 통해 체인코드 함수 직접 호출
- Firebase에서 승인자 정보 가져오기 → 학생회 구성원 확인 → 트랜잭션 실행

#### 블록체인 이벤트 구독
- 체인코드에서 발생하는 이벤트(예: withdraw_entry_pending)를 NestJS에서 리스너로 등록
- 이벤트 발생 시 캐시 무효화 및 Spring에 이벤트 발행

<br>

### FCM 알림 전송
- 사용 용도: 해당 서비스에 가입한 모든 학생과 학생회에게 알림 전송
- Firebase SDK를 사용하여
- 로그인 시 프론트가 발급한 fcmToken을 DB에 저장
- 사용자별로 토큰 저장/갱신
- 알림 전송 - 서버가 토큰 조회 후 FCM API로 전송
- 로그아웃 - 토큰 삭제

### S3 이미지 업로드
- 사용 용도: 입금 내역, 출금 내역을 기입할 때 실제 송금, 출금한 내역, 영수증을 캡쳐한 이미지를 업로드
- AWS SDK 사용
- 사용자가 업로드할 파일명의 URL을 생성
- 프론트가 이미지 업로드
- 업로드 성공 후, 해당 이미지의 URL을 DB에 저장

<br>

### API 시나리오

#### 회원가입 흐름
1. POST /Register 요청
2. MySQL SELECT & INSERT query
3. Query OK
4. Redis Pub (회원가입 및 조직 가입, "spring:request:register-user")
5. Redis Sub → NestJS
6. 블록체인 회원가입 등록
7. 회원가입 완료
8. Firebase에 인증서 및 개인키 저장
9. 응답 완료
10. Redis Pub (요청 완료 응답 메시지)
11. Redis Sub → Spring
12. 조직 가입에 대한 FCM 알림

#### 회원가입 후 학생회 조직원 추가
1. FCM으로부터 알림 받음
2. POST /register/approve
3. MySQL SELECT query
4. Query OK
5. Redis Pub (승인 완료, "spring:request:approve")
6. Redis Sub → NestJS
7. Firebase에서 인증서 개인키 조회
8. 성공적으로 조회 후 지갑 생성
9. 블록체인에 조직 가입 승인 체인코드(스마트 계약) 실행
10. 승인 체인코드 응답
11. Redis Sub (요청 완료 응답 메시지)
12. Redis Pub → Spring

