# Trouble Shooting

### 1. 자취방 프로젝트 - 채팅 서비스

* ws 연결 요청 헤더 중 일부 : [host:"localhost:8080", connection:"Upgrade", upgrade:"websocket", origin:"null", sec-websocket-version:"13", sec-websocket-key:"~~"]

* 헤더 중 connection: "Upgrade", upgrade: "websocket" 가 핵심.
* Upgrade는 http method가 아님, Upgrade를 사용해서 특정 프로토콜로의 전환을 요청하는것.

<img src="https://github.com/user-attachments/assets/42af4d0f-c7c5-4178-adcc-d23d13d70819" width="400">


#### 1-1. websocket 에서 jwt 전송을 위한 고민

##### 1. jwt를 uri 쿼리에 포함시키는 경우 :
   * 보안상 token 탈취 ( 네트워크 모니터링 등을 통해 )의 위험이 있음.
![image](https://github.com/user-attachments/assets/94f478b7-acac-413c-8963-1a7eb55702fc)

##### 2. 모든 ws 요청을 허용한 뒤 jwt를 message에 포함시키는 경우 :
   * 메세지를 암호화 하기 위해 wss를 사용해야 함 -> wss를 사용하면 보안상의 문제는 없음
   * wss를 사용한다고 해도 handleTextMessage 메소드에서 모든 메세지가 들어올때마다 첫번째 메세지인지 확인해야 하기 때문에 서버 자원 낭비.
   * 모든 요청을 우선 연결하고 인증되지 않으면 연결을 끊기 때문에 서버 자원 낭비.

##### 3. handshake ( http upgrade )의 헤더에 Authoration:"jwt" 으로 추가하는 방법 :
   * 보안상 handshake에 헤더를 추가할 수 없도록 막혀있음.
   


##### 4. handshake의 sec-websocket-protocol 부분에 jwt를 넣어 보내는 방법 :
   * 정상적인 방법은 아니다.
   * 헤더를 직접 추가할 수는 없지만 sub protocol 요소를 추가할 수는 잇음.
   * 아래처럼 요청을 보내면 sec-websocket-protocol:"testToken" 형태로 헤더가 추가됨.
     ![image](https://github.com/user-attachments/assets/873b47de-c2d2-4971-a62a-e124a1b5e2e0)
     <br/><br/>
     ![image](https://github.com/user-attachments/assets/578d4d05-cb1e-4eb4-82c4-80bd7a2f4b61)
    - token 인증에 실패해서 false 반환하는 경우 -> handshake 단계에서 정상적으로 세션 종료 <br/>
    - token 인증에 성공해서 true 반환하는 경우 -> 연결 되었다가 알 수 없는 이유로 세션 종료... <br/>
    <br/>
  * 세션 종료 원인을 찾기 위한 시도 :
    * EOFException 발생
    * affterConnectionClosed의 status.getReason -> null 반환
    * 아래 사진을 보면, 작성한 코드에서 문제가 발생하지는 않는것 같고
    ![image](https://github.com/user-attachments/assets/0478ddd3-d617-4254-b7b8-69b8d90d8cd9)
    * NioEndpoint.java 에서 getSocket().read(buffer) -> -1을 반환
    ![image](https://github.com/user-attachments/assets/ba20ebf1-6172-4e24-acd6-d5308cf4afae)
    * 그런데 -1을 반환하는 원인이 socket이 종료되었기 때문인데...
    * close 된 것이 close의 원인...?


    
##### 5. STOMP ( 가장 보편적인 방법 ) :




##### 6. Secondary Token 이용 ( 임시로 채택한 방법, 추후 STOMP로 로직 변환 필요 ) :
   * 짧은 유효시간으로 token을 탈취당하더라도 위험이 적음.
   * 순서 : <br/>
     [client] JWT를 사용하여 ST(secondary token) 발급 요청<br/><br/>
     [server] 서버는 JWT를 검증 후 유효시간이 10~20초 정도로 매우 짧은 ST를 발급<br/><br/>
     [client] ST를 쿼리 파라미터에 넣어서 보내 websocket upgrade 요청<br/><br/>
     [server] HandshakeInterceptor를 사용하여 ST를 추출하고 검증<br/><br/>
   
   
