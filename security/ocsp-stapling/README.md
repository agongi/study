## OCSP Stapling

```
ㅁ Author: suktae.choi
- https://www.maxcdn.com/one/visual-glossary/ocsp-stapling/
```

접속하려는 서버에서 전달한 Certificate 의 유효성을 확인하기 위해, root cert 로 query 하는 과정을 거쳐야한다.

아래의 방법들은 인증서의 Root Cert 유효성을 확인하기 위한 방법들이다:

### CRL (Certificate Revocation List)

- TLS handshake
- Certificate hands-over
- Look up root certificate
  - Need to verify root is valid
- Look for CRL url described in certificate
- Request to CRL url to `get all blacklist`
- Determine this one is not in the list

> Slow in retriving all blacklist per TLS handshaking

### OCSP (Online Certificate Status Protocol)

- ... same with above
- Look for OCSP url described in certificate
- Request to OCSP url to verify it is valid or not
  - Certificate serialNo is sent as params
- OSCP responses valid or not

> Slow in ask is valid or not per TLS handshaking

### OCSP Stapling

- Target webserver caches periodically the response from OCSP vendor
  - Response valid through marked timestamp
  - Response is digital-signed prevent from modifying
- TLS handshake
- Certificate hands-over with vendor's response
- Client verify the root with given vendor's one