---
tags:
  - HTTP
  - TCP
---

# **What is HTTP, TCP/IP?**
## **HTTP** 
- HTTP -> transfer Hypertext -> it is a request response protocol
- HTTP is located at layer 7 (**Application layer**) -> use port 80
## **TCP/IP**
- transfer protocol
- located at layer 4 — **Transport Layer**
- Make sure the data is transfer between client and server in order, non-losing data, reliable transfer (checksum, ack..)
HTTP is single communication.

## UDP/IP - connectionless
UDP trades reliability for performance. It is fast but unreliable.
UDP đóng gói lại gói tin theo cách của riêng nó, thêm vào 4 field mới là source port, destination port, package size và checksum.
- Ko ACK, nó như send mù ko biết đã đến nơi hay chưa (UDP sends data without acknowledgement, so the sender doesn’t know whether the packet is delivered or lost)
Example:
- Media streaming: Ví dụ như việc stream video, có lost vài khung hình cũng sẽ chấp nhận được, tuy nhiên đổi lại tốc độ load nhanh.
- Gaming: Tưởng tượng bạn chơi Liên Minh Huyền Thoại và cần upload liên tục những hành động như click chuột, bấm phím để di chuyển và tung chiêu, thì tần suất để gửi dữ liệu rất nhiều. Do đó UDP sẽ rất thích hợp.
- DNS Lookup...

### TCP Handshake
TCP: Handshake is combination of 3 factors: SYN, SYN-ACK, ACK
- **SYN**: Client sends `SYN(seq=x)` to Server  
    → “I want to connect. My initial sequence number is x.”
- **SYN-ACK**: Server replies with `SYN-ACK(seq=y, ack=x+1)`  
    → “I received your SYN. My number is y, and I expect x+1 from you.”
- **ACK**: Client sends `ACK(ack=y+1)`  
    → “I received your number y. Let’s start communication.”
**Why do we need handshake:**
- make sure both side are reachable
- sequence number is delivery in order, without loss (control order + detect loss + avoid duplicates)
- at this step the sequence number is initialized and use through the entire connection to track data and ack after that

Before HTTP can exchange request and response, they must establish a TCP connection first and then HTTP replies on the TCP standard in order to do its job.
Ưu điểm:
- Reliability - Tính đáng tin cậy, TCP đảm bảo rằng dữ liệu bạn gửi đi sẽ đến đích 1 cách thành công. Bên cạnh đó cũng đảm bảo thứ tự gói tin được vận chuyển.
- Cơ chế kiểm soát tắc nghẽn và kiểm soát lỗi: Kiểm soát dữ liệu trong quá trình vận chuyển, cũng như vận chuyển lại những gói tin bị lỗi.
- Có thể quản lý được connection. Bản chất TCP là 1 dạng giao thức stateful. Kết nối có thể được quản lý nhờ quá trình thành lập kết nối - three-way handshake, sau khi quá trình vận chuyển hoàn tất thì sẽ phát tín hiệu hoàn thành và đóng kết nối.
Nhược điểm:
- Tốn băng thông hơn bởi vì gói tin nặng hơn.
- Chậm hơn, đây là cái giá phải trả cho mấy ưu điểm phía trên:
    - Với TCP, đã phải mất ít nhất 2 round trip từ cái bắt tay, sau đó lại còn mất thêm 1 vài round trip nữa để gửi và nhận dữ liệu thì nó sẽ tốn thời gian hơn đúng không nào? Bên cạnh đó chúng ta phải tính thêm thời gian cho server để xử lý thông tin trước khi trả về nữa. Việc phản hồi chậm dĩ nhiên là điều không thể tránh khỏi.

| **TCP**                                                | **UDP**                                                   |
| ------------------------------------------------------ | --------------------------------------------------------- |
| Đáng tin cậy                                           | Không đáng tin cậy                                        |
| Chậm hơn do nhiều RTT hơn                              | Nhanh hơn                                                 |
| Header nặng hơn (20-60 bytes)                          | Header 8 bytes nhẹ hơn                                    |
| Không hỗ trợ Broadcast                                 | Hỗ trợ Broadcast và Multicast                             |
| Quản lý được connection dựa vào các gói SYN/ACK/PSH... | Connectionless                                            |
| Quản lý lỗi, chống tắc nghẽn                           | Không có                                                  |
| TCP là nền tảng của HTTP, HTTPs, FTP, SMTP and Telnet. | UDP là nền tảng của DNS, DHCP, TFTP, SNMP, RIP, and VoIP. |


![[../assets/HTTP.png]]

# Overview
**HTTP** is built on top of TCP.
**HTTP** uses a TCP connection to transfer request and response between client and server.
**HTTP Methods:** GET, POST, PUT, DELETE, not common (HEAD, OPTIONS, CONNECT, TRACE, PATCH)
**HTTP Status code:**
- 200: OK
- 204: No content
- 301: Moved Permanently -> URI of resource has been changed.

Browser normally keeps multiple TCP connections to the same server and send request to it in parallel.

## HTTP versions
> Currently, I have investigated 4 versions of HTTP
### HTTP/1
Every request to the same server requires a separate TCP connection.
- we can send only 1 request per TCP connection, so all requests to be processed sequentially one by one per connection.
- Limitations: For each HTTP request/response pair we has to create a new TCP connection
	-> time-consuming for TCP handshaking

### HTTP/1.1
- Introduce: keep-alive.
- Features: Pipelining and Persistent connection(keep-alive).
- Data: Human readable.
- **Limitations**: HOL blocking.
- 3 types of connection: short-lived, persistent (default), pipelining connection.
- keep-alive: connection could be reuse for more than a request.
	- Don't need to initiate TCP handshake -> reduce request latency.

### HTTP/2
- Introduce: HTTP stream, Compressed headers.
- Features: **multiplexing** request within a frame, **SERVER PUSH**
- Data: Binary structure
- It compresses headers
- **SERVER PUSH**: Allow a server populate data in a client cache
- **Limitations**: only solve problem HOC at HTTP layer, still exist **HOC** at **TCP Layer**
- Don't need to be sent or received stream in the order
	- Example: stream 1 header, stream 2 header, stream 1 data, stream 2 data

### HTTP/3
- Introduce a new protocol QUIC (using UDP connection).
- Using QUIC instead of TCP

## HTTP Flow
1. Open TCP connection.
2. Send a HTTP message.
3. Read response sent by server.
4. Close or reuse the connection for further requests.


## Pipelining (HTTP/1.1)
If pipelining is activated -> several requests can be sent without waiting for the first response to be fully received.
> The response must be received in the same order as to requests. -> It is tricky to implement (Many Proxy did not handle it properly).

Subsequent requests on the same connection must wait for the previous requests to complete 
If a request is blocked for any reason for packet loss -> all subsequence requests are also impacted.
### Note
- **HEAD, GET** can **always be pipelined**.
- **POST** should **NOT** be pipelined.
- **PUT, DELETE** can be pipelined or **NOT depending** on whether requests in the sequence depend on the effect of other.
- HTTP pipeline requires both server and client to support it.
- Using pipeline with Proxy server is not recommended because the HOL may really slow down server responses.
### Example HOL blocking
- A client send 4 pipelined GET through a single connection to a proxy, 3 of them have in cache and 1 must send to the destination server, so in this case the proxy server has to wait for the web server response and send 4 responses to the client
- If client open 4 connections to a proxy without using pipelining, the proxy can send the 3 cached responses to client in parallel before the response from server received.

- Pipelining
	- allow multiple request to be sent over a singer TCP WITHOUT waiting for the corresponding responses
	- The requests are placed in a queue and sent over to the server over a single connection
	- the server processed the requests in the order they were received and sends back the responses in the same order.

## Multiplexing (HTTP/2)
Request Multiplexing: điều luồng dữ liệu (stream) trong cùng 1 connection.
In HTTP/1.1 it can occur HOL blocking
- It divides HTTP/1.1 messages into frames which are embedded in a stream
- Header and Data frames are separated
- Binary framing
- more performant and robust
- lighter to transport, safer to decode
- great combination with Protocol buffer
- Header compression using HPACK
- Reduce overhead and improve performance
- Send multiple requests and response in parallel over a single TCP connection.
- Reduce latency and improve network utilization.
- Server push.
- One client request, multiple responses.
- reduce round trip latency.

## HOL: Head of Line Blocking
- Imagine there’s a long queue at a office, where people are served by order. The head of line person who is being served, and they can potentially block the whole line of waiting people.
- Now imagine the line of packets that has to be processed in order, and variable processing times, network latency or some packet loss, then you have HOL.

- It occurs when a line of packets is held up in a queue by a first packet.
- Example: 
	- multiple requests in HTTP pipelining 
	- slow consumption messages from a Kafka topic partition
