# TokenUp: JWT as a Service

### Super Chill Tokens @fa[hand-peace-o]

---
## Motivations

- Create a single sign on service that can issue JWT
- Learn about AWS key management infrastructure
- Get practical experience with security principles
---

## Agenda

- Intro to JSON Web Tokens
- Overview of CloudHSM and why we used KMS
- Cryptography things
- TokenUp architecture
- Lessons learned

---
## JWT (“jot”)

> JSON web tokens are an open industry standard (RFC 7519) method for  representing claims securely between parties. - jwt.io

+++
## JWT: Use Cases

__Authentication:__ User logs in once and gets a token for subsequent requests. Services can be stateless (no sessions). Most common use case.

+++
## JWT: Use Cases

__Information Exchange:__ Parties can use public/private key pairs to sign tokens. Provides message authenticity (confirm sender identity) and message integrity (message has not been tampered with).

---
## JWT example
Token:

base64(header).base64(payload).signature

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiYWRtaW4iOnRydWV9.TJVA95OrM7E2cBab30RMHrHDcEfxjoYZgeFONFh7HgQ
```
Note:
(~150 bytes)

+++
Header:
```
{
  "alg": "HS256",
  "typ": "JWT"
}
```
+++
Payload (claims):
```
{
  "username": "God",
  "admin": true
}
```
+++
Signature:
```
HMACSHA256(
  base64UrlEncode(header) + "." +
  base64UrlEncode(payload),
  secret
)
```

---
### AWS Key Infrastructure offerings

##### CloudHSM and Key Management Service

---
### What are Hardware Security Modules?

<img src="https://s3-us-west-2.amazonaws.com/mediazoo/fcc205990a8176bafd29d8b7a28ddb6c--archer-tv-show-archer-fx.jpg">

---
### CloudHSM Lifecycle
* Create a cluster
* Create 1-24 Hardware Security Modules (HSM) inside the cluster
  * 2+ is HA and work as a single logical unit
  * Load balanced automatically
* Install the CloudHSM client to connect to your HSM's

+++
### CloudHSM Overview

* Management layer
  * Admins and crypto users

* Client
 * Key generation
 * Encryption and signing algorithm's

+++
### CloudHSM overview

__Pros:__
- Secure hardware, destroys keys if tampered with. @fa[bomb] @fa[key]
- __Does__ support key export @fa[check]
- Supports SSL encryption offload via openssl dynamic engine
- Supports all the keys and algorithms

+++
### CloudHSM overview

__Cons__:
- Not easy to develop with
  - Only a Java lib, OpenSSL, PCKS
- Lots of manual command line work
- No automation for setup
- Need a client to create a secure connection to the HSM (need to log in)
- Still Expensive (~ $30/day)

---
### KMS (AWS Key Management Service)

__Pros__
- Easy to use (Web API + SDK support)
- Cheap as chips
- Subtlety enforces envelope encryption (4KB write max)
- KMS automatically maps encrypted data to key
- Governance handled by IAM infrastructure

+++
### KMS (AWS Key Management Service)

__Cons:__
- __Does not__ support key export (actually pro...)@fa[times]

___

___...CloudHSM is cumbersome so we used KMS for TokenUp___

---
### Envelope Encryption

> Encrypting a key with another key.

<img src="https://s3-us-west-2.amazonaws.com/mediazoo/Snap-Lock-Vault-Key-Envelopes-Red.jpg">

+++
### Data Encryption Keys (DEK's)

* Generate locally.
* Encrypted at rest.
* Store near the data.
* Generate a new DEK every time you write.
* Unique DEK per user.
* Use a strong algorithm (256-bit AES)

+++
### Key Encryption Keys (KEK's)

* Store centrally
* Adjust DEK granularity based on their use case.
* Rotate keys regularly (esp. after suspected incident) 😬

---
### TokenUp Intro

---
### Demo
---
### Lessons learned

- JWT's are a great alternative to session tracking for web apps, or use them as api keys
- If you need Key Management Infrastructure on AWS check out KMS first and only then use CloudHSM

---
### 🎉  We're done 🎉

Some hyperlinks for more details:

[Auth0 blog has good info](https://auth0.com/blog/)

[Google has great breakdown on envelope encryption](https://cloud.google.com/kms/docs/envelope-encryption)

[da spec](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html)

[jwt.io go here first](https://jwt.io/)

[AWS KMS](https://aws.amazon.com/kms/)
