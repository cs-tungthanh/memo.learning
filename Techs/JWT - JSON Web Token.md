---
tags:
  - JWT
---
# Overview
[Why is JWT popular? - YouTube](https://www.youtube.com/watch?v=P2CPd9ynFLg)

- It's JSON based
- 3 parts: Header + Payload + Signature
	- Header:
	- Payload: where you store the claims
		- Claims are statements about entity: user, ...
	- Signature: 2 algorithms: symetric and asymetric
		- HMAC SHA256 use a shared key for both signing and verification

When should not use JWT?
- should not contain highly sensitive data
- aren't ideal for managing user sessions since they're stateless
	- revoking JWT access can be challenging

**JWT consist of 3 part: header.payload.signature**
	- header: type, which algo using
	- payload: user data info custom for my app
	- signature: to check is this token is modified or not, or is it issued by trusting provider -> work like a tamper seal
		- signature = **encrypt**("**base64**(header).**base64**(payload)", secretKey)

When a hacker try to change the value in payload like: role=user -> role=admin
so at verification phase, from the modified payload we cannot encrypt into the same value as before -> it means the payload has been modified by other