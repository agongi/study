## @Pageable

```
ㅁ Author: suktae.choi
ㅁ Date: 2017.08.01
```

- Request - http://localhost/users?page=2&size=40

- Configuration

```xml
<mvc:argument-resolvers>
    <!-- @Pageable -->
    <bean class="org.springframework.data.web.PageableHandlerMethodArgumentResolver"/>
</mvc:argument-resolvers>
```

- Controller

```java
@RequestMapping(value = "/users", method = RequestMethod.GET)
public ModelAndView getUsers(@PageableDefault(page = 0, size = 20) Pageable pageable) {
  // ...
}
```

- DAO

```java
public List<Info> select(SignUpType signUpType, Pageable pageable) {
  if (pageable == null) {
    pageable = PageRequest.of(0, 10);
  }

  Map<String, Object> params = new HashMap<>();
  params.put("signUpType", signUpType);
  params.put("pageable", pageable);

  return getSelectSession().selectList(getNameSpace() + "select", params);
}
```

- SQL

```sql
<select id="select" parameterType="map" resultMap="Info">
  SELECT
    *
  FROM
    user
  WHERE 1 = 1
  <if test="signUpType != null">
    AND sign_up_type = #{signUpType}
  </if>
  ORDER BY reg_ymdt DESC
  LIMIT #{pageable.page}, #{pageable.size}
</select>
```
