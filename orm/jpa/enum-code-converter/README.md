## EnumCodeConverter

```
ㅁ Author: suktae.choi
ㅁ Date: 2018.01.09
```

### Code
Generic EnumCode Converter

```java
public class EnumCodeConverter<T extends Enum<T> & CodeEnum> implements AttributeConverter<T, String> {
    private Class<T> type;
    private Map<String, T> codeMap = new HashMap<>();

    public EnumCodeConverter() {
        type = (Class<T>) ((ParameterizedType) getClass().getGenericSuperclass()).getActualTypeArguments()[0];

        for (T e : type.getEnumConstants()) {
            codeMap.put(e.getCode(), e);
        }    
    }

    @Override
    public String convertToDatabaseColumn(T attribute) {
        return attribute.getCode();
    }

    @Override
    public T convertToEntityAttribute(String dbData) {
        return codeMap.get(dbData);
    }
}
```

How to use

```java
public class Person {
  // ...

  @Converter(autoApply = true)
  public static class
}
```
