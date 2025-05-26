# COPY VS ADD
```
https://pyrasis.com/jHLsAlwaysUpToDateDocker/Unit07/10
https://pyrasis.com/jHLsAlwaysUpToDateDocker/Unit07/09
```

## COPY
- 로컬 파일 또는 디렉토리
```dockerfile
COPY /script/entrypoint.sh /entrypoint.sh
```

## ADD
- 로컬 파일 또는 디렉토리
- `URL` 가능
- `압축파일` 지정시 -> 압축해제 같이 수행

```dockerfile
ADD /nginx-$NGINX_VERSION.tar.gz /nginx
```