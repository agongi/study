## COPY VS ADD

```
@author: suktae.choi
- https://pyrasis.com/jHLsAlwaysUpToDateDocker/Unit07/10
- https://pyrasis.com/jHLsAlwaysUpToDateDocker/Unit07/09
```

### COPY
- 로컬 파일 또는 디렉토리

```dockerfile
COPY --chown=irteam:irteam ./script/entrypoint.sh ./entrypoint.sh
```

### ADD
- 로컬 파일 또는 디렉토리
- URL 가능
- tar 파일 자동으로 압축 해제 및 추출 가능

```dockerfile
ADD --chown=irteam:irteam ./script/entrypoint.sh ./entrypoint.sh
```