# zootopia - 동물상 매칭 서비스

# 목차
- 개요

# 개요 
## Team 구성
## 서비스 개요
- 새로운 만남을 하고 싶은 외로운 싱글을 위한 만남추천 App으로 AI기반 본인의 동물상과 잘 맞는 이상형을 추천하는 Fun 미팅앱 입니다.
## 서비스 시나리오
1. 내 사진을 업로드 하여 나의 동물상을 조회한다.
2. 나의 동물상에 어울리는 동물상을 가진 이상형 목록을 추천 받는다.
3. 이상형 회원에게 좋아요를 표시 할 수 있다.
4. 이상형 회원에게 메세지를 보낼 수 있다.
5. 이상형 회원에게 당근을 보낼 수 있다.
6. 회원은 본인이 받은 메세지, 당근을 거부 할 수 있다.
7. 좋아요, 메세지, 당근을 보내거나 거부 하면 보내고 받은 동물상의 정보에 대하여 통계를 집계한다.
## 스토리 보드
![스토리보드](https://user-images.githubusercontent.com/29944139/118942435-ae41b080-b98d-11eb-8238-c40c53f128e8.jpg)

# 분석/설계

- Arch.
 - 로깅/모니터링/MQ 화면 캡처 
 - 마이크설계, 헥사고날 정리 

## 마이크로서비스 도출
- AI&ADMIN
![EventStorming_AI_Admin](https://user-images.githubusercontent.com/29944139/118923611-c6f29c00-b976-11eb-9ef1-167277af612d.jpg)
- USER
![EventStorming_USER](https://user-images.githubusercontent.com/29944139/118923621-cd811380-b976-11eb-9435-1c05a549ecf4.jpg)
- SNS
![EventStorming_SNS](https://user-images.githubusercontent.com/29944139/118923629-d1ad3100-b976-11eb-84ce-a246d9cc6dfe.jpg)
## 서비스스펙
- ADMIN
![ServiceSpec_ADIM](https://user-images.githubusercontent.com/29944139/118925241-58fba400-b979-11eb-9519-e381f37db5ab.jpg)
- USER
![ServiceSpec_USER](https://user-images.githubusercontent.com/29944139/118926317-15099e80-b97b-11eb-82bf-956b670efbc1.jpg)
- SNS
![ServiceSpec_SNS](https://user-images.githubusercontent.com/29944139/118926587-834e6100-b97b-11eb-8759-30be170fe304.jpg)




# 구현 

