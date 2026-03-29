---
tags:
  - SSL
  - TLS
  - HTTPS
  - TCP
---
# Ref

- [https://www.cloudflare.com/learning/ssl/what-is-https/](https://www.cloudflare.com/learning/ssl/what-is-https/)
- [https://www.digicert.com/what-is-an-ssl-certificate](https://www.digicert.com/what-is-an-ssl-certificate)
---
**Symmetric** **key means**:  - **faster than Asymmetric**
- Single key for both encryption and decryption
-  why is it faster: it uses simpler math operations while **asymmetric** reply on complex math operations to keep it secure
- Compare with asymmetric: because **public key is shared** so it needs more complex math to make sure from public key they cannot derive to private key
**Asymmetric key:** 
- Public and private key pair.
- If data is encrypted with one of the two keys, you must use the remaining key to decrypt it.
- UseCase: for external outside services cannot get key from internal API
---
**HTTP**: protocol is used for viewing web pages on the internet.
- In standard **HTTP,** all info is sent in plaintext, which means it is vulnerable to being intercepted.
- **HTTPS** encrypts all info → making the data impossible to read.
**HTTP** is built on top of TCP.
**HTTP** uses a TCP connection to transfer request and response between client and server.
**HTTP Methods:** GET, POST, PUT, DELETE, not common (HEAD, OPTIONS, CONNECT, TRACE, PATCH)
**HTTP Status code:**
- 200: OK
- 204: No content
- 301: Moved Permanently -> URI of resource has been changed.
Browser normally keeps multiple TCP connections to the same server and send request to it in parallel.

**HTTPS** is an implementation of TLS encryption _**on top of the HTTP protocol**_, which is used by all websites as well as some other web services.
- HTTP 1.0: every request will require a new TCP connection
- HTTP 1.1: 
	- **pipelining**: requests in queue
	- **keep-alive** to reuse the same TCP connection
	- data in human readable
- HTTP 2.0: 
	- feature: **stream**,  **binary transmission**
	- streams compress header to optimize data sending
	- stream feature: support **multiplexing**  allow to send many request/response at the same time on 1 TCP connection -> help reduce HOL blocking (head of line blocking)
	- -> is used to build gRPC on HTTP/2
	- eliminate HOL but it still remains on the TCP layer
-  HTTP/3
	- Introduce a new protocol QUIC (using UDP connection).
	- Using QUIC instead of TCP


**HOC issue**: imagine like there are one large road, but we must process one by one in order -> it will stuck when a request are delayed affect to the queue
## What is multiplexing in HTTP/2
- it means sending many thing at the same time over 1 TCP connection
- it is like highway
	- without multiplexing: 1 car on 1 line - car go one by one
	- with multiplexing: many line - cars go at the same time
### Without multiplexing (HTTP/1.1)
Imagine this situation:
1. Client sends **Request A**
2. Server responds **Response A**
3. Only after that → Request B
4. Then Response B
Even if Request B is small, it must **wait**.
This problem is called:  👉 **Head-of-Line Blocking**  - Requests block each other in HTTP/1.1.

**Pipelining — requests in a queue:** With pipelining, requests go out one after another, but the server _must_ **respond in the same order the requests came in**. So if request 1 is slow — a big database query — requests 2 and 3 are stuck waiting behind it. Even if they're ready. That's head-of-line blocking.
**Multiplexing — requests in parallel lanes:** With multiplexing in HTTP/2, each request gets its own stream with a stream ID. They all travel over the same TCP connection but completely independently. Response 2 can come back before response 1 if it's ready first. No waiting in line.

**The certificate** that is used for proving that the web server is trusted → client can exchange sensitive messages to that server
## How S work in HTTPS
**SSL (Secure Socket Layer)** is a standard technology for establishing an **encrypted link** between a server and a client (browser).
More specification: SSL is a security protocol that describes how algorithms should be used.
**SSL** refers to secure Socket layer, used to encrypt connection
**TLS** refers to Transport Layer security

**CAS: Central Authentication Service** is a single sign-on protocol for the web.
- Public SSL certificates are issued by CA is a task for Load Balancer

**CA: Certificate authority**
your browser and OS installed with a list of trusted CA. It's used to check whether this cert is signed by someone in my trusted list

**How does it work?**
- Step 1: Establish an SSL connection by using a process called **SSL Handshake**
    - After this, 3 keys are used to set up: **public, private, and session keys.**
- Step 2: Data exchange with the session key.

## TCP Handshake
- **SYN**: Client sends `SYN(seq=x)` to Server → I want to connect. My initial sequence number is x.
- **SYN-ACK**: Server replies with `SYN-ACK(seq=y, ack=x+1)` → I received your SYN. My number is y, and I expect x+1 from you.
- **ACK**: Client sends `ACK(ack=y+1)` → I received your number y. **Let’s start communication.**

**Why do we need handshake:**
- make sure both side are reachable
- sequence number is delivery in order, without loss (control order + detect loss + avoid duplicates)
- at this step the sequence number is initialized and use through the entire connection to track data and ack after that

## SSL Handshake
1. The **browser** connects (request) to a web server secured with SSL-HTTPS.
2. The **server** sends a copy of the **certificate,** including its public key.
3. **Browser** check certificate root against a list of trusted CAs → make sure that cert is unexpired, unprovoked,…) → create a session key → encrypted with server’s public key → send.
4. The **server** will decrypt it with its private key. —> get the session key → send back acknowledgment with the session key to start an encrypted session.
5. The **server and browser** now encrypt all transmitted data with the session key.

![](../assets/https-ssl.png)**TCP handshake  -> TLS handshake** (session key created here) -> HTTP (data encrypted using session key)
TCP handshake and TLS handshake is not on the same moment


```
Layer 7 — Application
  REST:  JSON.stringify() → UTF-8 bytes
  gRPC:  protobuf.serialize() → compact binary
                    │
                    └── gRPC bytes already smaller here

Layer 6 — Presentation
  REST:  TLS encrypt → optionally gzip
  gRPC:  TLS encrypt → HPACK compress headers
                    │
                    └── HPACK saves repeated header bytes

Layer 4 — Transport
  REST:  HTTP/1.1 → one request per TCP stream
  gRPC:  HTTP/2   → multiple streams per TCP connection
                    │
                    └── biggest performance difference HERE

Layer 3 — Network
  REST:  IP packets
  gRPC:  IP packets        ← same, no difference

Layer 1 — Physical
  REST:  binary signals
  gRPC:  binary signals    ← same, no difference
```

### TCP Handshake
Before HTTP can exchange request and response, they must establish a TCP connection first and then HTTP replies on the TCP standard in order to do its job.
Ưu điểm:
- Reliability - Tính đáng tin cậy, TCP đảm bảo rằng dữ liệu bạn gửi đi sẽ đến đích 1 cách thành công. Bên cạnh đó cũng đảm bảo thứ tự gói tin được vận chuyển.
- Cơ chế kiểm soát tắc nghẽn và kiểm soát lỗi: Kiểm soát dữ liệu trong quá trình vận chuyển, cũng như vận chuyển lại những gói tin bị lỗi.
- Có thể quản lý được connection. Bản chất TCP là 1 dạng giao thức stateful. Kết nối có thể được quản lý nhờ quá trình thành lập kết nối - three-way handshake, sau khi quá trình vận chuyển hoàn tất thì sẽ phát tín hiệu hoàn thành và đóng kết nối.
Nhược điểm:
- Tốn băng thông hơn bởi vì gói tin nặng hơn.
- Chậm hơn, đây là cái giá phải trả cho mấy ưu điểm phía trên:
    - Với TCP, đã phải mất ít nhất 2 round trip từ cái bắt tay, sau đó lại còn mất thêm 1 vài round trip nữa để gửi và nhận dữ liệu thì nó sẽ tốn thời gian hơn đúng không nào? Bên cạnh đó chúng ta phải tính thêm thời gian cho server để xử lý thông tin trước khi trả về nữa. Việc phản hồi chậm dĩ nhiên là điều không thể tránh khỏi.

![[../assets/HTTP.png]]