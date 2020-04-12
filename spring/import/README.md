## Import

```
ㅁ Author: suktae.choi
ㅁ References:
- https://jaehun2841.github.io/2019/12/22/2019-12-22-spring-import/#%EB%A6%AC%ED%8C%A9%ED%86%A0%EB%A7%81-4-importselector%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EC%84%A0%ED%83%9D%EC%A0%81-configuration-%EC%82%AC%EC%9A%A9
```

## ImportSelector

@Import 에서 load 할 Class 를 동적으로 선택할 수 있다.

> method return 이 String[]. 즉 무조건 import 할 className 을 전달해야한다.

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Import(CustomImportSelector.class)
public @interface EnableCustomFunc {
  String value();
}
```

```java
public class CustomImportSelector implements ImportSelector {

  @Override
  public String[] selectImports(AnnotationMetadata importingClassMetadata) {
    Map<String, Object> attrMap = importingClassMetadata.getAnnotationAttributes(EnableCustomFunc.class.getName());
    AnnotationAttributes attributes = AnnotationAttributes.fromMap(attrMap);

    var importName = attributes.getString("value");

    return new String[] {getMappingName(importName)};
  }

	// select importing class based on params passing through field.value
  private String getMappingName(String importName) {
    if (StringUtils.isBlank(importName)) {
      throw new IllegalArgumentException();
    }

    Class<?> clz = null;
    switch(importName) {
      case "A":
        clz = A.class;
        break;
      case "B":
        clz = B.class;
        break;
    }

    return clz.getName();
  }
}
```

## ImportAware

@Import 에 전달된 params 를 사용할때 필요함.

> method schme 이 void 이므로, 결국 setter

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Import(CustomConfig.class)
public @interface EnableCustomFunc {
  String value();
}
```

```java
@Configurationf
public class CustomConfig implements ImportAware {
  private String importClassName;
  
  @Override
  public void setImportMetadata(annoatationMetadata: AnnotationMetadata) {
    Map<String, Object> attrMap = importingClassMetadata.getAnnotationAttributes(EnableCustomFunc.class.getName());
    AnnotationAttributes attributes = AnnotationAttributes.fromMap(attrMap);

    var importName = attributes.getString("value");
    this.importClassName = importName;
  }
}
```

