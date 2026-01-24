---
tags:
  - SSL
  - TLS
  - HTTPS
---
# Ref

- [https://www.cloudflare.com/learning/ssl/what-is-https/](https://www.cloudflare.com/learning/ssl/what-is-https/)
- [https://www.digicert.com/what-is-an-ssl-certificate](https://www.digicert.com/what-is-an-ssl-certificate)
---
**Symmetric** **key means**:  - **faster than Asymmetric**
- Single key for both encryption and decryption
- that to decrypt information, one must have the same key that was used to encrypt the message.
	- why is it faster: it uses simpler math operations while **asymmetric** reply on complex math operations to keep it secure
	- one point is because public key is shared so it needs more complex math to make sure from public key they cannot derive to private key
**Asymmetric key:** 
- Public and private key pair.
- If data is encrypted with one of the two keys, you must use the remaining key to decrypt it.
- UseCase: for external outside services cannot get key from internal API
---
**HTTP**: protocol is used for viewing web pages on the internet.
- In standard **HTTP,** all info is sent in plaintext, which means it is vulnerable to being intercepted.
- **HTTPS** encrypts all info → making the data impossible to read.
**HTTPS** is an implementation of TLS encryption _**on top of the HTTP protocol**_, which is used by all websites as well as some other web services.
- HTTP 1.0: every request will require a new TCP connection
- HTTP 1.1: have `keep-alive` to reuse the same TCP connection - not solve HOL
- HTTP 2.0: streams compress header - eliminate HOL but it still remains on the TCP layer
	- stream feature: support **multiplexing**  allow to send many request/response at the same time on 1 TCP connection -> help reduce HOL blocking (head of line blocking)
	- -> is used to build gRPC on HTTP/2
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

**The certificate** that is used for proving that the web server is trusted → client can exchange sensitive messages to that server
## How S work in HTTPS
**SSL (Secure Socket Layer)** is a standard technology for establishing an **encrypted link** between a server and a client (browser).
More specification: SSL is a security protocol that describes how algorithms should be used.
**SSL** refers to secure Socket layer, used to encrypt connection
**TLS** refers to Transport Layer security
**CA:** Central Authentication Service is a single sign-on protocol for the web.
- Public SSL certificates are issued by CA is a task for Load Balancer

**How does it work?**
- Step 1: Establish an SSL connection by using a process called **SSL Handshake**
    - After this, 3 keys are used to set up: **public, private, and session keys.**
- Step 2: Data exchange with the session key.

More details about **SSL** **Handshake**
1. The **browser** connects (request) to a web server secured with SSL-HTTPS.
2. The **server** sends a copy of the **certificate,** including its public key.
3. **Browser** check certificate root against a list of trusted CAs → make sure that cert is unexpired, unprovoked,…) → create a session key → encrypted with server’s public key → send.
4. The **server** will decrypt it with its private key. —> get the session key → send back acknowledgment with the session key to start an encrypted session.
5. The **server and browser** now encrypt all transmitted data with the session key.

![](../assets/https-ssl.png)TCP handshake  -> TLS handshake (session key created here) -> HTTP (data encrypted using session key)
TCP handshake and TLS handshake is not on the same moment
