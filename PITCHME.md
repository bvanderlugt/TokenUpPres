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

<div class="left">
    <p>Pros</p>
    <ul>
        <li>Secure hardware, destroys keys if tampered with.</li>
        <li>Does support key export </li>
        <li>Supports SSL encryption offload via openssl dynamic engine</li>
        <li>Supports RSA pub/priv keys</li>
    </ul>
</div>
<div class="right">
    <p>Cons</p>
    <ul>
        <li>Not easy to use</li>
          <ul>
            <li>No web interface (so no sdk integration)</li>
            <li>lots of manual command line work</li>
            <li>no automation for provisioning</li>
          </ul>
        <li>Still Expensive (~ $30/day)</li>
    </ul>
</div>

...so we used KMS instead

___

### KMS (AWS Key Management Service)

<div class="left">
    <p>Pros</p>
    <ul>
        <li>Easy to use (web api + sdk support).</li>
        <li>Cheap as chips</li>
    </ul>
</div>
<div class="right">
    <p>Cons</p>
    <ul>
        <li>Only supports symmetric keys</li>
        <li>Does not support key export</li>
    </ul>
</div>
---
