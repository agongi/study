## Encrypted SNI

```
@author: suktae.choi
- https://blog.mozilla.org/security/2018/10/18/encrypted-sni-comes-to-firefox-nightly/
- https://tools.ietf.org/html/draft-ietf-tls-esni-01
- https://blog.cloudflare.com/encrypted-sni/
```

### Overview

Before TLS 1.3, TLS handshake are working as below:

![Screen Shot 2019-11-03 at 20.18.53](images/Screen%20Shot%202019-11-03%20at%2020.18.53.png)

- Client Hello
  - Desired serverName (SNI) is included to derive proper server's certificate
- Server Hello
  - Server is looking for the certificate which is described in SNI field
    - ex.) www.awt.com or files.awt.com
  - Response with matched certificate

The problem is `SNI` is communicating in plain/text so packet is pointing out `I'm going to that server.`

Now Let us go How Encrypted SNI works:

### ESNI

![Screen Shot 2019-11-03 at 20.18.57](images/Screen%20Shot%202019-11-03%20at%2020.18.57.png)

- DNS lookup
  - DNS Record already has `Public Key`
  - IP is resolved and return to client with public key

> This means ESNI must only work on supported DNS server

- Client Hello
  - Desired serverName (SNI) field is encrypted as below:
    - Create temporary symmetric key
    - Encrypt SNI value using generated symmetric key
    - Encrypted SNI value sent with symmetric that is encrypted using `public key` where comes in DNS lookup

> TLS extension is replaced from `server_name` to `encrypted_server_name` to let server knows to figure out ESNI or not

- Server Hello
  - Derive symmetric key using private key
  - Decrypt `encrypted_server_name` using acquired symmetric key
  - ... follow on going

> If server might not support ESNI or illegal key sent, then connection tear-down immediately.
>
> Then SNI flow is re-negotiated `from the beginning` which cause performance effects.

### Concerns

- Both supports TLS 1.3 or above
- DNS should support public key sharing

#### Pros

- MITM is not working

#### Cons

- Something going bad, all flows are tear-down and SNI are ongoing from the beginning

