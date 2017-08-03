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
public ModelAndView getUsers(@PageableDefault(size = 20) Pageable pageable) {
  // ...
}
```

- DAO

```java
public List<BattleInfo> selectBattleInfosByFinishYn(SignUpType signUpType, Pageable pageable) {
  Map<String, Object> params = new HashMap<>();
  params.put("signUpType", signUpType);
  params.put("pageable", pageable);

  return getSelectSession().selectList(getNameSpace() + "selectBattleInfos", params);
}
```

- SQL

```sql
<select id="selectUsers" parameterType="map" resultMap="UserResult">
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
