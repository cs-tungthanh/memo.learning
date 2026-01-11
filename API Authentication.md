---
tags:
  - token
  - auth
  - session
  - sticky
---

## API Auth Mechanism
-> Who is this client/user

Login, tokens, sessions, and certificates = **Authentication**  
Permissions and access control = **Authorization**

| Mechanism                | Auth method    |                                            |
| ------------------------ | -------------- | ------------------------------------------ |
| Token-based API          | Access token   | API with bearer token                      |
| Session-based API        | Server session |                                            |
| API key-based API        | Static secret  | example like api crawl tiktok, fb data,... |
| Certificate-based (mTLS) | Certificates   | Example: MQTT Connection                   |

|Session-based|Token-based|
|---|---|
|Server stores session|Server does not|
|Lives usually in Redis|Lives on client|
|Cookie based|Header based|
|Easy logout|Hard logout|
|Not ideal for microservices|Good for APIs|

## Session-based API
1. user login
2. server create session -> store in db -> response with header `Set-Cookie: session_id=XYZ456; HttpOnly; Secure; SameSite`
3. User sends a next request, browser will automatically attach with cookie

## Sticky Session
When server saves session on memory (not a shared tmp db like cache...) -> we need a technique: **Sticky Session**
User → Server A (session stored in A)
User → Server B (session not found) ❌

### How sticky sessions work technically
1. LB injects to cookie like: `LB_SERVER=B`
2. LB hash ip to route request to the same server
3. Header-based: Sticky routing based on session cookie or custom header.