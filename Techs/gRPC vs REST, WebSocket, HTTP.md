---
tags:
  - gRPC
  - REST
  - WebSocket
  - HTTP
---

## How to choose the right architecture
Actually, you should consider your specific needs of your application.
- If you need a more flexible and a widely adopted architecture -> REST may be a better choice
- If you want to have the specification below -> gRPC may be better.
	- more performance (low latency).
	- real-time streaming handling. 
	- heavy system in micro-service 
	- More function driven API design

## gRPC
- gRPC is high performance, open-source RPC framework that can be used to build efficient, reliable and secure communication between services.
- It is built on top of HTTP/2 and use protocol buffers for data serialization.

## REST
- REST stands for representational state transfer.
- REST APIs use HTTP method to interact with resources (GET, POST, PUT, DELETE)
- Client-Server is **independent**: the client doesn't need to know how the server is implemented.
### Key points
- Client and Server - they are separate, allowing independent evolution
- **Cacheable**
- **Stateless**: Each request contains all the information from server needs to process it -> the server don't need to keep the previous requests.
- **Uniform interface** - resource are identified via URL, Operation are defined by HTTP method

HTTP is normally stateless, but it can become stateful using **session, cookies, persistent connection, or Web Socket** to maintain user context across requests

## Web socket
- Web Socket **is built on top of TCP** .
- It's diff from HTTP, but it relies on HTTP for the initial connection (handshake)

### How it works
- Web Socket starts with an HTTP-based handshake. Client sends an HTTP request with `Upgrade` header (indicate want to switch to webSocket protocol)
- Server responds with 101 status code
- After handshake is completed (http is no longer used), the connection is upgraded from HTTP to WebSocket protocol

### **Difference Between REST and HTTP**

| Feature        | REST                                | HTTP                                        |
| -------------- | ----------------------------------- | ------------------------------------------- |
| **Definition** | Architectural style for APIs        | A protocol for communication                |
| **Scope**      | A design principle for web APIs     | A lower-level communication protocol        |
| **Methods**    | Uses HTTP methods (GET, POST, etc.) | Provides methods (GET, POST, PUT, DELETE)   |
| **Stateless?** | Must be stateless                   | Can be stateful (e.g., sessions)            |
| **Format**     | Typically JSON or XML responses     | Supports various formats (HTML, JSON, etc.) |

|GraphQL|REST|
|---|---|
|Chỉ là 1 ngôn ngữ truy vấn APIs|Là 1 khái niệm, 1 loại kiến trúc phần mềm định nghĩa 1 số ràng buộc, quy tắc cần tuân theo khi design web services|
|Chỉ deploy 1 endpoint duy nhất và client có thể quyết định lấy resource nào họ cần|Deploy nhiều endpoints và mỗi endpoints thông thường trả về 1 resource duy nhất|
|Sử dụng kiến trúc hướng tới phía client|Sử dụng kiến trúc hướng tới phía server|
|Ko có cơ chế caching đc tích hợp sẵn → phải dùng lib bên ngoài|có tính năng cache mặc định|
|Ko hỗ trợ API versioning|support|
|only JSON|XML, JSON, YAML, …|
|Có định nghĩa kiểu dữ liệu rõ ràng và document tạo tự động|No|

## REST Status code

- **[1xx: Informational](https://restfulapi.net/http-status-codes/#1xx)** – Communicates transfer protocol-level information.
- **[2xx: Success](https://restfulapi.net/http-status-codes/#2xx)** – Indicates that the client’s request was accepted successfully.
    - 201 created - 206 partial - 202 accepted
- **[3xx: Redirection](https://restfulapi.net/http-status-codes/#3xx)** – Indicates that the client must take some additional action in order to complete their request.
- **[4xx: Client Error](https://restfulapi.net/http-status-codes/#4xx)** – This category of error status codes points the finger at clients.
    - 400 bad req - 401 unauthorized - 404 not found
- **[5xx: Server Error](https://restfulapi.net/http-status-codes/#5xx)** – The server takes responsibility for these error status codes.
    - 500 internal server err - 502 bad gateway