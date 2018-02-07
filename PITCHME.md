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

[Header].[Payload].[Signature]

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiYWRtaW4iOnRydWV9.TJVA95OrM7E2cBab30RMHrHDcEfxjoYZgeFONFh7HgQ
```

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

---
### CloudHSM Overview

* Create a cluster

* Create 2-24 Hardware Security Modules (HSM) inside the cluster
  * A cluster of HSM's work as a single logical unit
  * Loadbalanced automatically


+++
### CloudHSM Overview

* management layer
  * Admins and crypto users

* client
 * key generation
 * encryption and signing algorithm's

+++
### CloudHSM overview

__Pros:__
- Secure hardware, destroys keys if tampered with. @fa[bomb] @fa[key]
- __Does__ support key export @fa[check]
- Supports SSL encryption offload via openssl dynamic engine
- Supports RSA keys

+++
### CloudHSM overview

__Cons__:
- Not easy to use
  - No web interface (so no sdk integration)
  - lots of manual command line work
  - no automation for setup
- Still Expensive (~ $30/day)

+++

___...CloudHSM is cumbersome so we used KMS instead___

---
### KMS (AWS Key Management Service)

__Pros__
- Easy to use (Web API + SDK support)
- Cheap as chips
- Uses envelope encryption
- KMS automatically maps encrypted data to key
- Governance handled by IAM infrastructure

+++
### KMS (AWS Key Management Service)

__Cons:__
- __Does not__ support key export @fa[times]

---
### Envelope Encryption

> Encrypting a key with another key.

+++
### Data Encryption Keys (DEK's)

<!-- TODO make these comments  -->
* Generate DEKs locally.
* DEKs are encrypted at rest.
* Store the DEK near the data that it encrypts.
* Generate a new DEK every time you write the data. Don't need to rotate the DEKs.
* Unique DEK per user.
* Use a strong algorithm such as 256-bit Advanced Encryption Standard (AES) in Galois Counter Mode (GCM).

+++
### Key Encryption Keys (KEK's)

* Store centrally
* Set the granularity of the DEKs they encrypt based on their use case.
* Rotate keys regularly, and also after a suspected incident.

---
### TokenUp Intro

---
### Demo

---
### Example of signing using JWA (JSON Web Algorithms)

```
const jwa = require('jwa');

const header = {
  alg: 'HS256',
  typ: 'JWT
};

const payload = {
  exp: new Date(now.getTime() + 900000),
  admin: true
}

const encodedHeader = base64url.encode(JSON.stringify(header));
const encodedPayload = base64url.encode(JSON.stringify(payload));

const leader = `${encodedHeader}.${encodedPayload}`;

const algo = jwa(algorithm);
const token = algo.sign(leader, 'SuperSecret');

```

---
### Lessons learned

- JWT's are a great alternative to session tracking.
- If you need Key Management Infrastructure on AWS check out KMS first and only then use CloudHSM.

---
### End

[Auth0 blog](https://auth0.com/blog/)
[https://cloud.google.com/kms/docs/envelope-encryption](https://cloud.google.com/kms/docs/envelope-encryption)
[https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html)
[jwt.io](https://jwt.io/)
[https://aws.amazon.com/kms/](https://aws.amazon.com/kms/)
