# TokenUp: JWT as a Service

### Super Chill Tokens @fa[hand-peace-o]

---

## Agenda

- Intro to JSON Web Tokens
- Security cryptography refresh
- Overview of cloudHSM and why we used KMS.
- TokenUp architecture
- Lessons learned

---

## Motivations

- Create a single sign on service that can issue JWT tokens.
- Learn about AWS key management services (CloudHSM and KMS).
- Get practical experience with security principles.

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
+++
Token:

[Header].[Payload].[Signature]

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiYWRtaW4iOnRydWV9.TJVA95OrM7E2cBab30RMHrHDcEfxjoYZgeFONFh7HgQ
```

---

### Security Principles
- Encryption
  - Implementations (ssl)
  - RSA public/private keys

- Message authenticity and verification
  - hash functions vs. checksums, MAC/HMAC, keys
---

### cloudHSM overview

Cloud-based hardware security module (HSM)

__Pros:__
- Secure hardware, destroys keys if tampered with. @fa[bomb] @fa[key]
- __Does__ support key export @fa[check]
- Supports SSL encryption offload via openssl dynamic engine
- Supports RSA pub/priv keys

+++
### cloudHSM overview

__Cons__:
- Not easy to use
  - No web interface (so no sdk integration)
  - lots of manual command line work
  - no automation for setup
- Still Expensive (~ $30/day)

___...so we used KMS instead___

+++
### KMS (AWS Key Management Service)

__Pros__
- Easy to use (web api + sdk support)
- Cheap as chips
- Uses envelope encryption

+++
### KMS (AWS Key Management Service)

__Cons:__
- Only supports symmetric keys
- __Does not__ support key export @fa[times]


---
### TokenUp architecture

- Boxy data flow thing there

---
### Demo time

<!-- TODO
- Show create tokens
- Show using admin route
- Show the token in jwt.io -->

<!-- TODO fill me in checkout code rending: https://gitpitch.com/gitpitch/templates/aqua#/3 -->

---
### Example of signing

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

- Envelope encryption and master key management are good ideas.
- If you need Key Management Infrastructure on AWS check out KMS first and only then use CloudHSM.

---
### End
