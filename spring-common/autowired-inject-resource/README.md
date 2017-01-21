## @Autowired vs @Inject vs @Resource
Simple comparison among @Autowired, @Inject and @Resource.

>###### TBD

```
ㅁ Author: suktae.choi
ㅁ Date: 2016.06.20 ~
ㅁ References:
 - http://dev-eido.tistory.com/entry/Autowired-Resource-Inject%EC%9D%98-%EC%B0%A8%EC%9D%B4
```

#### 1. @Autowired
 - byType
 - Spring Spec
 - **Support** `Required` attribute (could be null)
 - Support `byName` using @Qualifier

```java
@Autowired(required = false)
@Qualifier("idGenerationServiceMovie")
IIdGenerationService idGenerationService;
```

#### 2. @Inject
 - byType
 - J2EE Spec
 - **No** `required` attribute (could not be null)
 - Support `byName` using @Name

```java
@Inject
@Named("idGenerationServiceMovie")
IIdGenerationService idGenerationService;
```

#### 3. @Resource
 - byName
 - J2EE Spec
 - Support `byType` if there is no matching byName in sequence

```java
@Resource (name = "productDao")
productDao;
```
