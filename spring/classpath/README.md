## Classpath

```
ㅁ Author: suktae.choi
- http://okky.kr/article/286428
```

### classpath: vs classpath\*:
- classpath:
  - load only from current project resources
- classpath*:
  - load from current and referenced \*.jar resources

### classpath: vs classpath:/
- classpath:
  - relative path
- classpath:/
  - absolute path

### classpath:\*\*/\*.properties vs classpath:\*.properties
- classpath:\*\*/\*.properties
  - read \*.properties files in all depth of directory hierarchy (recursively)
- classpath:\*.properties
  - read \*.properties files in the root of classpath only (flat)
