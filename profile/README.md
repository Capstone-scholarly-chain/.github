## [캡스톤디자인] Hyperledger 블록체인을 이용한 학생회 장부 시스템 - Schoraly chain

**프로젝트 기간:** 2025.03.01 ~ 2025.06.03 <br>
 **팀원:** 기획 및 디자인 1명, 프론트엔드 1명, 백엔드 1명, 블록체인 및 인프라 1명
### 프로젝트 주제: 
학생회비 장부를 학과 구성원들과 투명하게 공개하는 블록체인 기반 클라우드 네이티브 웹 서비스
### 프로젝트 목표: 
학생회만의 노력에 그치지 않고 학생들의 능동적인 참여를 바탕으로, 학생회 공금 횡령 문제를 예방하며 신뢰할 수 있는 학생 자치 문화를 형성하는 것
<br><br><br>
### 기술 스택
<div>
<!--   <img src="https://cdn.jsdelivr.net/gh/devicons/devicon@latest/icons/nextjs/nextjs-original.svg" width="80" height="80"/>
  <img src="https://cdn.jsdelivr.net/gh/devicons/devicon@latest/icons/react/react-original.svg" width="80" height="80"/>       
  <img src="https://cdn.jsdelivr.net/gh/devicons/devicon@latest/icons/vercel/vercel-original-wordmark.svg" width="80" height="80"/> <br>
  <img src="https://cdn.jsdelivr.net/gh/devicons/devicon@latest/icons/spring/spring-original.svg" width="80" height="80"/>   
  <img src="https://cdn.jsdelivr.net/gh/devicons/devicon@latest/icons/nestjs/nestjs-original.svg" width="80" height="80"/> <br>
  <img src="https://cdn.jsdelivr.net/gh/devicons/devicon@latest/icons/mysql/mysql-original.svg" width="80" height="80"/>        
  <img src="https://cdn.jsdelivr.net/gh/devicons/devicon@latest/icons/redis/redis-original.svg" width="80" height="80"/> <br>      
  <img src="https://cdn.jsdelivr.net/gh/devicons/devicon@latest/icons/amazonwebservices/amazonwebservices-original-wordmark.svg" width="80" height="80"/>     
</div> -->
  
![Frame 71](https://github.com/user-attachments/assets/4638b975-33cb-4389-ab02-5b38693c21df)

<br> 

#### 왜 블록체인인가?
- 블록체인은 **탈중앙화, 투명성, 불변성, 합의 메커니즘, 스마트 컨트랙트**라는 다섯 가지 핵심 특징을 가짐
- 블록체인은 암호화 해시와 분산 네트워크 구조이기에 기록 조작이 불가능한 구조
- 모든 거래와 상태 변화가 블록에 순차적으로 기록되고 네트워크 전체에서 검증되기 때문에 승인 과정까지 투명하게 기록 가능
- 특히 Hyperledger Fabric은 허가형 네트워크로 사전 승인된 참여자들만 접근할 수 있고, 다중 조직의 합의를 통해 완전한 감사 추적이 가능
<br><br>
### 서비스 설명
**학생 입금 내역 신청과 학생회 출금 내역 신청**<br> 
학생) 행사명/연도/학기/금액/증빙 자료으로 입금 내역 신청<br>
학생회) 행사명/연도/학기/금액/증빙 자료으로 출금 내역 신청<br>
학생회 출금 내역 신청의 경우, **학생들이 학생회의 출금 내역을 보고 능동적으로 투표에 참여하여 해당 출금 내역을 허가할 것인지 선택 가능**

![image](https://github.com/user-attachments/assets/22f4a83e-69f4-4b8c-9044-4d82c7a097fc)
<br><br><br>




