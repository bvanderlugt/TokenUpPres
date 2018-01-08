# TokenUp: JWT as a Service

### Super Chill Tokens

---

## Agenda

- Intro to JSON Web Tokens

- Security cryptography refresh

- Overview of cloudHSM and why we used KMS.

- TokenUp architecture

- Lessons learned

---

## Motivation

- Impinj ramp up

- Practical security experience

- Flexing infrastructure muscle

<!-- ---?code=src/go/server.go&lang=golang&title=Golang File -->

---

## JWT (“jot”)

> JSON web tokens are an open industry standard (RFC 7519) method for  representing claims securely between parties. - jwt.io

#### Two use cases:

1. Authentication: User logs in once and gets a token for subsequent requests. Services can be stateless (no sessions). Most common use case.

2. Information Exchange: Parties can use public/private key pairs to sign tokens. Provides message authenticity (confirm sender identity) and message integrity (message has not been tampered with).

---

## JWT example

Header:
```
{
  "alg": "HS256",
  "typ": "JWT"
}
```

Payload (claims):
```
{
  "username": "God",
  "admin": true
}
```

Signature:
```
HMACSHA256(
  base64UrlEncode(header) + "." + base64UrlEncode(payload), secret)
```

---

### Security Overview

<!-- TODO -->
- Encryption
  - Implementations (ssl)
  - RSA public/private keys

- Message authenticity and verification
  - hash functions vs. checksums, MAC/HMAC, keys

---

### cloudHSM overview

- Cloud-based hardware security module (HSM)


#### Pros:
- Secure hardware, destroys keys if tampered with. :bomb: :key:
- __Does__ support key export :white_check_mark:
- Supports SSL encryption offload via openssl dynamic engine
- Supports RSA pub/priv keys

#### Cons:
- Not easy to use
  - No web interface (so no sdk integration)
  - lots of manual command line work
  - no automation for setup
- Still Expensive (~ $30/day)

...so we used KMS instead

#### Pros:
- Easy to use (web api + sdk support)
- Cheap as chips

#### Cons:
- Only supports symmetric keys :sweat:
- __Does not__ support key export :x:

---
