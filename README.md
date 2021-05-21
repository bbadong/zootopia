# zootopia - 동물상 매칭 서비스

# 목차

  - [개요](#개요)
    - [Team 구성](#Team-구성)
    - [서비스 개요](#서비스-개요)
    - [서비스 시나리오](#서비스-시나리오)
    
  - [분석/설계](#분석.설계)
    - [스토리보드](#스토리-보드)
    - [마이크로서비스 도출](#마이크로서비스-도출)
    - [서비스 스펙](#서비스스펙)
    - [아키텍처](#Architecture)
  - [구현](#구현)
    - [DDD 의 적용](#DDD-의-적용)
    - [API Gateway 적용](#API-Gateway-적용)
  - [테스트](#테스트)
    - [SonarQube, Sonar Scanner 를 통한 소스 정적 코드 분석 적용](#SonarQube,-Sonar-Scanner-를-통한-소스-정적-코드-분석-적용)
    - [Jmeter 를 통한 성능 테스트 적용](#Jmeter-를-통한-성능-테스트-적용)
  - [운영](#운영)
    - [ZCP](#ZCP)
    - [CI 및 이미지 저장소](#CI-및-이미지-저장소)
    - [Jenkins 를 통한 배포](#Jenkins-를-통한-배포) 
    - [로그 집중화 및 Tracing 기능 적용](#로그-집중화-및-Tracing-기능-적용)
    - [Monitoring 기능 적용](#Monitoring-기능-적용)
  - [시연](#시연)


# 개요 

## Team 구성
![팀구성](https://user-images.githubusercontent.com/29944139/118989592-1d82c900-b9bd-11eb-9bc2-0b821d849581.jpg)

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

# 분석/설계

## 스토리 보드
![스토리보드](https://user-images.githubusercontent.com/29944139/118942435-ae41b080-b98d-11eb-8238-c40c53f128e8.jpg)


## 마이크로서비스 도출

* 도메인 서열 분리
  - Core Domain: AI
    - 없어서는 안될 핵심 서비스이며, 연간 Up-time SLA 수준을 99.999% 목표, 배포주기는 예약의 경우 1주일 1회 미만, 매칭업체는 1개월 1회 미만
  - Supporting Domain: SNS 
    - 경쟁력을 내기위한 서비스이며, SLA 수준은 연간 60% 이상 uptime 목표, 배포주기는 각 팀의 자율이나 표준 스프린트 주기가 1주일 이므로 1주일 1회 이상을 기준으로 함.
  - General Domain: USER, ADMIN

### 1. AI
![EventStorming_AI](https://user-images.githubusercontent.com/29944139/119068115-7cc4f580-ba1e-11eb-99ee-38b6be1eadc6.jpg)

### 2. USER
![EventStorming_USER](https://user-images.githubusercontent.com/29944139/118923621-cd811380-b976-11eb-9435-1c05a549ecf4.jpg)

### 3. SNS
![EventStorming_SNS](https://user-images.githubusercontent.com/29944139/118923629-d1ad3100-b976-11eb-84ce-a246d9cc6dfe.jpg)

### 4. ADMIN
![EventStorming_Admin](https://user-images.githubusercontent.com/29944139/119068150-8b131180-ba1e-11eb-9765-c620d8874861.jpg)

## 서비스스펙
   
### 1. ADMIN
![ServiceSpec_ADIM](https://user-images.githubusercontent.com/29944139/118925241-58fba400-b979-11eb-9519-e381f37db5ab.jpg)

### 2. USER
![ServiceSpec_USER](https://user-images.githubusercontent.com/29944139/118926317-15099e80-b97b-11eb-82bf-956b670efbc1.jpg)

### 3. SNS
![ServiceSpec_SNS](https://user-images.githubusercontent.com/29944139/118926587-834e6100-b97b-11eb-8759-30be170fe304.jpg)



## Architecture
 ![헥사고날 아키텍처](https://user-images.githubusercontent.com/69634194/119058962-54cb9700-ba0a-11eb-8170-3db51a73ad45.gif)

# 구현

- 분석/설계 단계에서 도출된 헥사고날 아키텍처에 따라, 각 마이크로 서비스들을 스프링부트와 Node로 구현 

## DDD 의 적용
- 각 서비스내에 도출된 핵심 Aggregate Root 객체를 Entity 로 선언
  - Admin, User, SNS
- JPA로 MariaDB, OpenAPI 사용을 위한 Firebase 를 사용함
  - Entity Pattern 과 Repository Pattern 을 적용하여 JPA 를 통하여 다양한 데이터소스 유형 (RDB or NoSQL) 에 대한 별도의 처리가 없도록 데이터 접근 어댑터를 자동 생성하기 위하여 Spring Data REST 의 RestRepository 를 적용

## API Gateway 적용
- 등록된 사진의 동물상 매칭 및 UI 서비스는 BFF를 통해서 접속이 되며, Microservice의 통신은 Gateway를 통해서 호출되도록 구성

## 서비스 수행
- ZCP에서 ServiceType을 LoadBalancer로 변경이 안됨 (권한 없음)
- BFF 80, Gateway는 8080 으로 port-forward 로 서비스 수행

```console
$ kubectl get service
NAME                         TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
service/admin-app-v1-dev     ClusterIP   172.20.19.195   <none>        8080/TCP       27d
service/bff-app-v1-dev       NodePort    172.20.23.93    <none>        80:30552/TCP   35d
service/gateway-app-v1-dev   ClusterIP   172.20.96.240   <none>        8080/TCP       7h46m
service/sns-app-v1-dev       ClusterIP   172.20.78.103   <none>        8080/TCP       36d
service/user-app-v1-dev      ClusterIP   172.20.153.22   <none>        8080/TCP       7h36m

$ kubectl port-forward service/bff-app-v1-dev 380:80
Forwarding from 127.0.0.1:380 -> 80
Forwarding from [::1]:380 -> 80

$ kubectl port-forward service/gateway-app-v1-dev 38080:8080
Forwarding from 127.0.0.1:38080 -> 80
Forwarding from [::1]:38080 -> 80

```

## 이벤트 발행/구독 처리를 위해 RabbitMQ 기능 적용
- 이벤트 발행 Exchanges 와 이벤트 구독 Queues
![image](https://user-images.githubusercontent.com/59593156/119067732-a7627e80-ba1d-11eb-9b88-e6df3d2ebf30.png)
![image](https://user-images.githubusercontent.com/59593156/119067740-aaf60580-ba1d-11eb-91fa-3706133c9fb7.png)

# 테스트

## SonarQube, Sonar Scanner 를 통한 소스 정적 분석 적용
- Jenkins 파일에 해당 stage 추가하여 빌드 전 자동으로 수행
![image](https://user-images.githubusercontent.com/59593156/119074258-ab949900-ba29-11eb-9741-aeb661017370.png)

## Jmeter 를 통한 성능 테스트 적용
- Jenkins 파일에 해당 stage 추가하여 배포 후 자동으로 수행
![image](https://user-images.githubusercontent.com/59593156/119074435-ff9f7d80-ba29-11eb-8aae-95dd0aebcf40.png)
![image](https://user-images.githubusercontent.com/59593156/119074475-13e37a80-ba2a-11eb-988d-749c68a2c066.png)
![그림1](https://user-images.githubusercontent.com/59593156/119076400-712cfb00-ba2d-11eb-887e-8cf9b7b095b8.png)


# 운영

- ZCP Platform를 활용하여, CI/CD, Image Repository등의 환경을 구성

## ZCP
- ![ZCP zootopia](https://user-images.githubusercontent.com/69634194/119061044-5bf4a400-ba0e-11eb-9c17-04bad286200c.gif)

## CI 및 이미지 저장소
- ![ZCP ci habor](https://user-images.githubusercontent.com/69634194/119061022-539c6900-ba0e-11eb-9692-ecef9278fd5b.gif)

## Jenkins 를 통한 배포 
- ![jenkins zootopia](https://user-images.githubusercontent.com/69634194/119060340-f3f18e00-ba0c-11eb-858e-36deae446143.gif)

## 로그 집중화 및 Tracing 기능 적용
- Application, Kubernetes 로그를 ZCP 의 Kibana 에서 확인
- Application 로그에 Spring Sleuth, Zipkin 적용
![image](https://user-images.githubusercontent.com/59593156/119063518-4a15ff80-ba14-11eb-9bc0-39172b98db7f.png)

## Monitoring 기능 적용
- Application 에 Prometheus 적용
- Prometheus Server 에 수집된 로그를 ZCP 의 Grafana 에서 확인
![image](https://user-images.githubusercontent.com/59593156/119064134-9e6daf00-ba15-11eb-9b16-6f3f7ab4ebdf.png)
![image](https://user-images.githubusercontent.com/59593156/119064176-b9402380-ba15-11eb-8b12-eb453aa63ed2.png)


# 시연

## 초기화면 및 signin/signup
![image0](https://user-images.githubusercontent.com/69634194/119091006-5798ad00-ba47-11eb-9184-7bd9ab084370.gif)

## 나의 동물상 조회 및 추천이상형
![image1](https://user-images.githubusercontent.com/69634194/119091707-6f246580-ba48-11eb-8c28-d9015097e686.gif)

## MY PAGE
![image0 (1)](https://user-images.githubusercontent.com/69634194/119091019-5e272480-ba47-11eb-9390-216bca425df6.gif)


