# Network Programming
## The Client-Server Programming Model
모든 network application은 client-server model에 기반함   
Client-server model의 핵심적인 operation은 transaction으로, 다음과 같음    
<img src = "https://github.com/eomhs/TIL/blob/main/figures/Client-Server%20transaction.PNG" width="600" height="200"/>   

## Networks
Host에게 network는 단지 I/O device임   
물리적으로 network는 계층 구조를 가짐   

가장 낮은 단계는 LAN (local area network)로 건물이나 캠퍼스 정도를 포괄함   
가장 유명한 LAN 기술은 Ethernet으로,    
Ethernet segment는 host들이 wires에 의해 hub에 연결된 것으로 구성됨
- 각 Ethernet adapter은 고유한 48-bit address (MAC address)를 가짐
- Host는 다른 host에게 bits를 보낼 수 있고 이는 frame이라 불림
- 모든 host는 frame을 볼 수 있지만 받는 host만 읽을 수 있음

Bridged Ethernet segment는 여러 Ethernet segment들이 bridge에 연결되어 만들어짐   
Bridge는 clever해서 어느 host가 어느 ports에서 접근 가능한지 배우고 선별적으로 port에서 port로 frame을 복사함   
개념적으로 단순함을 위해 LAN은 여러 host들이 단 한 개의 wire에 연결되어 있는 상태로 생각할 수도 있음   

더 높은 단계로 여러 incompatible한 LAN들은 물리적으로 router라 불리는 컴퓨터에 의해 연결될 수 있음   
WAN (wide area networks)의 예시로 router와 router가 연결된 것이 있음   
이렇게 연결된 network는 internet이라 불림  

다른(incompatible) LAN과 WAN으로 bit를 보내는게 가능한 이유는 각 host와 router에 protocol software가 동작하기 때문  
이 software가 protocol을 구현하여 다음의 일을 함
- naming scheme 제공
    - internet protocol은 host address를 위한 uniform format을 정의함
    - 각 host는 이러한 internet address를 최소한 하나 배정받아 식별됨
- delivery mechanism 제공
    - internet protocol은 standard transter unit (packet)을 정의함
    - packet은 header와 payload로 구성됨
        - header: packet size, source and destination address 등의 정보가 들어있음
        - payload: source host가 보낸 data bit가 들어있음

그림으로 보면 다음과 같음   
<img src = "https://github.com/eomhs/TIL/blob/main/figures/Transfer%20internet.PNG" width="600" height="500"/>

## The Global IP Internet
가장 유명한 internet의 예시임   
TCP/IP protocol family에 기반함   
- IP (Internet protocol)
    - 기본적인 host-to-host간 naming scheme과 unreliable delivery capability of packet을 제공함
- UDP (Unreliable Datagram Protocol)
    - IP를 이용해 process-to-process간 unreliable datagram delivery를 제공함
- TCP (Transmission Control Protocol)
    - IP를 이용해 process-to-process간 reliable byte streams를 제공함
       
Internet client와 server는 socket interface 함수와 UNIX I/O를 이용해 통신함   
**IPv4**      
현재 가장 많이 사용하는 IP version   
Addressing: dot-decimal notation으로 표현된 32-bit addresses   
ex) 147.46.174.108   
IP address는 network byte order로 저장됨(big-endian)   
**IPv6**   
가장 새로운 IP version   
Addressing: colon (:)으로 나뉘어진 8개의 hexadectet(16bit)로 표현된 128-bit addresses   
ex) 2001:067c:10ec:0254::0216   
연속된 0은 double colon (::)으로 대체함
## Internet Domain Names
Internet은 DNS (domain name system)을 통해 IP address와 domain name 간의 mapping을 유지함   
Domain name의 set은 hierarchy를 구성하고, 가장 오른쪽의 label은 top-level domain (TLD)임   
TLD에는 generic TLD (gTLD)와 country-code TLD (ccTLD)가 있음   
각 domain은 최소 두개의 지정된 name server가 있음   
이러한 name server들이 domain에 대한 original DNS data를 가지고 있음   
각 host는 항상 127.0.0.1의 address를 가지는 localhost라는 locally defined domain name을 가지고 있음   
여러 mapping 방법이 있음
- Domain name과 IP address가 1대1 대응
- 여러 domain names가 같은 IP address로 대응
- 여러 domain names가 여러 IP address로 대응
- 유효한 domain name이지만 대응되는 IP address가 없음
## Clients, Servers, and Sockets
- **Clients**
    - ex) Web browsers, ftp, telnet, ssh
    - client는 server socket address에 있는 IP address를 통해 host를 식별함
    - server socket address에 있는 port를 통해 service를 식별함
- **Servers**
    - Servers는 long-running processes임(machine을 server라고 부르기도 함)
    - 각 server는 port에 request가 오기까지 기다림
    - ex) Web server, FTP server, Telnet server, Mail server
- **Sockets**
    - Socket이란 kernel에게는 endpoint of communication
    - application에게는 network와 read/write를 할 수 있게 해주는 file descriptor
    - network communication을 위해 사용되는 basic unit
    - Sockets interface는 network를 위한 user-level interface를 제공함
## The Sockets Interface
Overview는 다음과 같음   

<img src = "https://github.com/eomhs/TIL/blob/main/figures/Sockets%20interface.PNG" width="600" height="400"/>


- getaddrinfo(node, service, hints, res)
    - addrinfo struct의 list를 리턴함
    - node는 remote node, service는 port를 의미
    - hints는 IP version이나 TCP/UDP 등을 설정하기 위해 사용
    - res는 리턴한 list의 첫번째 addrinfo struct를 가리키는 포인터
- socket(domain, type, protocol)
    - file descriptor (endpoint for communication)을 리턴함
    - domain은 protocol family (UNIX, IPv4/6, Netlink, ...)을 의미
    - type은 communication semantics를 의미 (connection-oriented, reliable streams, unreliable connection-less streams 등)
    - protocol은 보통 0이지만 domain/type이 한 개 이상의 protocol을 지원하면 0보다 클 수 있음
- bind(sockfd, addr, addrlen)
    - server socket을 위해 사용
    - open socket에 network interface/port를 할당
    - addr에 다양한 size의 sockaddr struct 형태로 정보가 제공되어 addrlen 또한 주어져야함
- listen(sockfd, backlog)
    - socket을 passive socket으로 mark함(들어오는 connection을 accept()로 받을 준비가 됨)
    - backlog는 얼마나 많은 pending connections를 socket이 지원하는지 명시함
- accept(sockfd, addr, addrlen)
    - 들어오는 connection을 기다림
    - connection client에 대한 정보가 addr로 리턴됨
- connect(sockfd, addr, addrlen)
    - addr에 의해 식별된 server로 향하는 socket과 connection을 시작함
 
Connect / Accept 는 다음과 같이 이루어짐   
<img src = "https://github.com/eomhs/TIL/blob/main/figures/Connect%20accept.PNG" width="600" height="300"/>

Listening descriptor는 한번 생성되고 server가 살아있는 동안 계속 존재함   
Connected descriptor는 server가 client의 connection request를 accept하면 생성되고 service client가 종료하면 사라짐   
이러한 차이는 concurrent server를 가능하게 함
 