# Others


### 1. Spring Scheduling

* Scheduling : 일정한 시간 간격 또는 시각에 특정 로직을 수행하기 위해 사용하는 것.
  * @Scheduled(fixedRate = 5000) => 5초마다 한번 실행.
  * 정기적인 이메일 발송, 시스템 상태 체크, 데이터 캐시 업데이트

  ![image](https://github.com/user-attachments/assets/236ca92c-beec-40eb-bd00-af8820a13513)

  
* Batch : 사용자와의 상호작용 없이 여러 개의 작업을 미리 정해진 순서에 따라 중단 없이 처리하는 것.
  * 대규모 데이터 마이그레이션, 일일 또는 월간 리포트 생성, 대량의 파일 처리 작업 등.
    
  * 다음 5가지 조건을 만족해야 "Batch"라고 한다.
    * 대용량 데이터 - 대량의 데이터를 가져오고, 전달하고, 계산하는 등의 처리 가능
    * 견고성 - 잘못된 데이터를 충돌/중단 없이 처리
    * 성능 - 지정한 시간 내에 처리 완료 및 동시에 실행되는 다른 프로그램에 방해 X
    * 자동화 - 사용자의 개입 없이 실행
    * 신뢰성 - 잘못된 부분의 추적 가능 (로깅, 알림 등)

    
#### Batch vs Scheduler
<img src="https://github.com/user-attachments/assets/66f2919c-74ae-460b-ab0a-37516d1378d3" alt="image" width="400" />


### 2. Spring Reflection

* Reflection : 컴파일이 아닌, 런타임에 클래스의 정보를 검색하거나 필드/메서드/생성자 등을 동적으로 제어할 수 있는 기능.
* Spring 에서의 Reflection 종류 :
  * DI (Dependency Injection) : 클래스의 생성자/필드/메서드 등을 Reflection을 통해 분석하고, 자동으로 의존성 주입.
  * AOP ( Aspect Oriented Programming ) : 메서드 호출을 가로채서 로깅/트랜잭션 처리 등을 수행하는 기능. (역시 런타임 단계에서 수행됨)


### 3. RESTful API
* API란 : application programming interface, 두 소프트웨어 구성 요소가 서로 통신할 수 있게 하는 메커니즘.
* REST란 : Representational State Transfer ( 표현적인 상태 전송 ), 자원을 이름으로 구분하여 해당 자원의 상태를 주고받는것. 
  * HTTP URI(Uniform Resource Identifier)을 통해 자원을 명시하고,
  * HTTP Method(POST, GET, DELETE, PATCH 등)를 통해
  * 해당 자원(URI)에 대한 CRUD Operation을 적용하는것.
    * URI(Uniform Resource Identifier) vs URL(Uniform Resource Locator) :
     * https://naver.com/category : 리소스의 위치까지만 나타내는 URL
     * https://naver.com/category/12 또는 ~~category?page=12 : URL을 포함한 URI
     * 즉, 뒤의 query가 붙으면 URI가 됨.

* RESTful API란 : REST의 원리를 따르는 API
* REST의 장점 :
  * HTTP 프로토콜의 인프라를 그대로 사용하므로 REST API 사용을 위한 별도의 인프라 구축할 필요 없음.
  * HTTP 표준 프로토콜에 따르는 모든 플랫폼에서 사용 가능.
* REST의 단점 :
  * 표준 자체가 존재하지 않아 정의가 필요하다. (POST, GET Method를 이용한다고 해도 서버에서의 연산은 개발자가 정함.)
 

### 4. socket vs port
![image](https://github.com/user-attachments/assets/0e7296ea-3871-4302-9548-1d43d5c969ce)


* Port : 네트워크 프로토콜에서 통신의 엔드포인트를 구분하는 숫자 ( 식별자 )
* Socket : [Address: Port]

* 아래처럼 내 public ip와 request 의 header 로 들어가는 Remote Address 다른 이유 : 프록시 서버, 로드밸런서 주소로 설정되기 때문(?)
<img src="https://github.com/user-attachments/assets/28ac3f87-5d94-439c-ab90-2dffc98d4ad0" width=600/>


### 5. HTTP Polling vs SSE vs Web Socket
![image](https://github.com/user-attachments/assets/d14a7430-1744-4fea-a195-2f455f162db8)

#### 5-1. Short Polling
* Short Polling : client 가 server 로 지속적으로 request 를 보내고 갱신된 데이터가 있다면 그것을 응답받음.
* 요청이 많기 때문에 네트워크 대역폭과 리소스 소모 비효율적.
  
![image](https://github.com/user-attachments/assets/e9c7c3b9-9dca-43cc-9cb4-1a07e101d68d)

#### 5-2. Long Polling
* Long Polling : request를 보낸 후 서버에서 변경이 일어나면 response를 보내는 방법.
* 데이터 변경이 잦으면 short polling과 다를게 없음.
* 데이터 변경이 드문 경우 적절함.

![image](https://github.com/user-attachments/assets/94ec7558-efac-42f8-a6e1-ad9b0df74b5c)

#### 5-3. SSE (Server Sent Event)
* Server Sent Event : client가 첫 connection 요청을 보낸 후, 서버에서 데이터 갱신이 있을 때마다 response.
* Server -> Client 로의 데이터 전송만 가능.
* 주식 시세 업데이트, 소셜 미디어 피드, 실시간 스포츠 경기 점수 갱신 등 Client -> Server 방향이 필요 없는 경우 적합.
![image](https://github.com/user-attachments/assets/a18f5718-0422-4963-876e-a334e5d89128)

#### 5-4. Web Socket
* Web Socket : Http Upgrade 요청으로 ws 프로토콜 사용.
* 데이터 오버헤드가 적음 ( HTTP Header가 없기 때문 ), 실시간 응답성 뛰어남, 양방향 통신 가능.
![image](https://github.com/user-attachments/assets/123d4f20-c1e7-4bf0-9ab5-788e3e9064b8)



### 6. 계층별 Header 생성 위치
![image](https://github.com/user-attachments/assets/aa0483fe-2052-4bd0-bbf7-a8d3a119df52)

* 패킷의 헤더를 추가하는 "객체"는 네트워크 스택의 각 계층에 구현된 "소프트웨어 모듈"이다.
* 크게 NIC, Network Stack ( os level ), Application 으로 생각하면 될듯.
