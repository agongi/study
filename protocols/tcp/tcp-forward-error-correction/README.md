## TCP Forward Error Correction
It is a technique used for controlling errors in data transmission over unreliable or noisy communication channels.

> It corrects missing or broken data using **xor-based algorithm for inferencing without retransmission**. But normal **packet size is redundant** for those error-correction meta data.

```
@author: suktae.choi
- https://en.wikipedia.org/wiki/Forward_error_correction
- http://egloos.zum.com/sorachi/v/2954919
```

 - Pros

 : Fast error correction without retransmission

 - Cons

 : Extra encode/decode calculation is required

 : Redundant meta-data is appended (FEC Code)
