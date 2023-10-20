# 네트워크

## 1. 애플리케이션 계층

### 네트워크 지연, 패킷 유실

네트워크 통신을 할 때 패킷이 서로 오가는데, 라우터에 큐를 이용해서 패킷을 저장하게 된다. 이 때 라우터에서 패킷을 수용할 수 있는 범위를 벗어나게 되면 패킷 유실이 일어나는데, 네트워크에서 발생하는 대부분의 패킷 유실은 이 때 일어난다.

> 프로세스와 프로그램이란? 🤔
>
> 프로세스: 프로그램을 운영체제 위에서 실행시키면 프로세스가 된다.

### 네트워크 애플리케이션이란?

* 운영체제 위에서 실행되는 하나의 프로세스들이 다른 머신에 있는 프로세스와 메세지를 교환하는 것이다.

### 애플리케이션 모델

* 대표적인 애플리케이션 모델은 서버-클라이언트 모델이다.
* 클라이언트란 뭔가를 필요할 때만 메세지를 보내는 액션을 취한다.
* 서버는 24시간 내내 같은 장소(고정된 주소를 가진다)에서 기다린다.

### Processes Communicating

* 같은 머신에 있는 프로세스끼리 메세지를 교환하는 것에 대비해서 파이프 같은 것을 제공해 주는데, 이것을 IPC(Inter Process Comunication)라고 한다.
* 네트워크 커뮤니케이션은 서로 다른 머신에 있는 프로세스간에 메세지를 교환하는 것이다.
* 운영체제는 IPC를 위해 소켓이라고 불리는 인터페이스를 제공한다.
  * 사용자 프로그램들이 뭔가 일을 할 때 편하게 작업할 수 있도록 API를 제공해주는데 이를 시스템콜이라고 한다. 그 중 네트워크 관련된 기능도 운영체제가 제공해 주는데, 이 API가 바로 소켓이다.
* 네트워크 커뮤니케이션을 하려면 서버의 주소(해당 프로세스의 소켓 인터페이스를 나타낸다.)를 정확히 알아야 한다. 이 주소를 IP 주소라고 한다. IP 주소는 프로세스가 실행되고 있는 머신을 지칭한다.
* 하나의 머신안에 수 많은 프로세스들이 동작하고 있는데, 각 프로세스들을 구분하기 위해 포트 번호를 사용한다.
* 즉, 소켓을 정확히 지칭하기 위해 IP주소와 포트 번호를 사용한다. (좀 더 깊이 공부하면 이 뜻이 아니라는 것이 이해된다.)
* 웹 서버 프로세스의 포트 번호는 암묵적으로 80을 사용한다.

### What transport service does an app need?

* 애플리케이션은 트랜스포트 계층에서 다음과 같은 서비스를 제공받기를 원한다.
  * Data Integrity : some apps (e.g. file transfer, web transactions) require 100% reliable data transfer
  * Timing : some apps (e.g. Internet telephony, interactive games) require low delay to be "effective"
  * Throughput : some apps (e.g. multimedia) require minimum amount of throughput to be "effective", other apps("elastic apps") make use of whatever throughput they get
  * Security : encryption, data integrity...

### Internet transport protocols services

* 트랜스포트 계층에서 제공하는 서비스는 TCP와 UDP이다.

* **TCP Service**
  * Reliable transport between sending and receiving process
  * Flow control : sender won't overwhelm receiver
  * Congestion control : throttle sender when network overloaded
  * Does not provide : timing, minimum throughput guarantee, security
  * Connection-oriented : setup required between client and server processes
* UDP Service
  * Unreliable data transfer between sending and receiving process
  * Does not provice : reliability, flow control, congestion control, timing, throughput guarantee, security, orconnection setup
* TCP와 UDP 사이에서 무엇을 사용할지 판단은 신뢰성 있게 전송하는지 안하는지 차이다.



## Web And HTTP

### HTTP Overview

* HTTP는 Hypertext transfer protocol의 약자이다. 즉, 텍스트인데 링크(동영상, 이미지, ...)들이 있는 텍스트이다.
* HTTP는 request와 response로 이루어진다.
* HTTP는 트랜스포트 계층으로부터 TCP 서비스를 제공받는다.
  * Client initiates TCP connection (creates socket) to server, port 80 -> 운영체제단에서 일어난다.
  * server acepts TCP connection from client -> 운영체제단에서 일어난다.
  * HTTP messages(application-layer protocol messages exchanged between browser (HTTP client) and Web server (HTTP server))
  * TCP connection closed
* HTTP is "stateless" : server maintains no information about past client requests

### HTTP connections

* Non-persistent HTTP
  * at most one object sent over TCP connection, connection then closed
  * downloading multiple objects required multiple connections
* Persistent HTTP
  * Multiple objects can be sent over single TCP connection between client, server

> Non-persistent HTTP 와 Persistent HTTP 이미지 첨부

### Non-persistent HTTP : response time

* RTT (definition) : time for small packet to travel from client to server and back
* HTTP response time:
  * one RTT to initiate TCP connection
  * one RTT for HTTP request and first few bytes of HTTP response to return
  * file transmission time
  * Non-persistent HTTP response time = 2RTT + file transmission time
  * HTTP 메세지는 용량이 작아서 무시할 만큼 시간이 적게 걸리지만 file transmission time은 용량이 크기 때문에 file transmission time을 더해준다.

> Non-persistent HTTP : response time 이미지 첨부

### Persistent HTTP

* Non-persistent HTTP Issues :
  * requires 2 RTTs per object
  * OS overhead for each TCP connection
  * browsers often open parallel TCP connections to fetch referenced objects
* persistent HTTP :
  * server leaves connection open after sending response
  * subsequent HTTP messages between same client/server sent over open conneciton
  * Client sends requests as soon as it encounters a referenced object
  * as little as on RTT for all the referenced objects

### HTTP request message

* two types of HTTP messages : request, response

* most important line of HTTP message is first line

  ```
  // request header
  GET /index.html HTTP/1.1\r\n
  
  // response header
  HTTP/1.1 200 OK\r\n
  ```

### User-server state: cookies

* 쿠키는 stateless를 보완하기 위해 나온 trick이다.

  ![image-20231019200759753](./images/image-20231019200759753.png)

### Web cahces (proxy server)

* goal : satisfy client request without involving origin server
* User sets browser : Web accesses via cache
* browser sends all THHP requests to cache
  * object in cache : cache returns object
  * else cache requests object from origin server, then returns object to client

* 일관성 문제가 있다.

