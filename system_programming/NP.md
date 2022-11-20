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



