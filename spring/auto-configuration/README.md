# AutoConfiguration
```
https://www.baeldung.com/spring-boot-custom-auto-configuration
https://velog.io/@on5949/Spring-Boot-AutoConfiguration%EC%9D%80-%EC%96%B4%EB%96%BB%EA%B2%8C-%EB%8F%99%EC%9E%91%ED%95%A0%EA%B9%8C
```

@SpringBootApplication > @EnableAutoConfiguration 으로 사용 선언이 되어 있습니다.

- AutoConfigurationImportSelector
  - ImportCandidates.load 를 통해 대상을 읽고

```java
protected List<String> getCandidateConfigurations(AnnotationMetadata metadata, AnnotationAttributes attributes) {
    List<String> configurations = ImportCandidates.load(AutoConfiguration.class, getBeanClassLoader())
        .getCandidates();
    
    // ...
```
- ImportCandidates
  - `META-INF/spring/%s.imports` 에 선언된 내용을 load 합니다

```java
private static final String LOCATION = "META-INF/spring/%s.imports";

public static ImportCandidates load(Class<?> annotation, ClassLoader classLoader) {
    Assert.notNull(annotation, "'annotation' must not be null");
    ClassLoader classLoaderToUse = decideClassloader(classLoader);
    String location = String.format(LOCATION, annotation.getName());
    // ... 
}
```

- `/META-INF/spring/org.springframework.boot.autoconfigure.AutoConfiguration.imports`

```
org.springframework.boot.actuate.autoconfigure.amqp.RabbitHealthContributorAutoConfiguration
org.springframework.boot.actuate.autoconfigure.audit.AuditAutoConfiguration
org.springframework.boot.actuate.autoconfigure.audit.AuditEventsEndpointAutoConfiguration
org.springframework.boot.actuate.autoconfigure.availability.AvailabilityHealthContributorAutoConfiguration
org.springframework.boot.actuate.autoconfigure.availability.AvailabilityProbesAutoConfiguration
org.springframework.boot.actuate.autoconfigure.beans.BeansEndpointAutoConfiguration
org.springframework.boot.actuate.autoconfigure.cache.CachesEndpointAutoConfiguration
```

- 이후 해당 클래스를 처리하며 @Conditional 조건에 맞으면 작업을 수행합니다 