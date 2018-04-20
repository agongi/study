## @Pageable

```
ㅁ Author: suktae.choi
ㅁ Date: 2017.08.01
```

**Request**
- http://localhost/users?page=2&size=40

Configuration
```xml
<mvc:argument-resolvers>
    <!-- @Pageable -->
    <bean class="org.springframework.data.web.PageableHandlerMethodArgumentResolver"/>
</mvc:argument-resolvers>
```

**PageableRequest**
```java
@RequestMapping(value = "/users", method = RequestMethod.GET)
public ModelAndView getUsers(@PageableDefault(page = 0, size = 20) Pageable pageable) {
  // invokes service
}

public List<User> getUsers(Pageable pageable) {
  if (pageable == null) {
    pageable = PageRequest.of(0, 10);
  }

  // ... invokes sql
}
```
```sql
<select id="selectUsers" parameterType="map" resultMap="User">
  SELECT *
  FROM user
  LIMIT #{pageable.page}, #{pageable.size}
</select>
```

**PageableResponse**
```java
public interface UserService <T extends AbstractUser> {
  Page<T> getUsers();
}

public class UserServiceImpl implements UserService <BraveUser> {
  @Override
  public Page<BraveUser> getUsers() {
    return new PageImpl<>(
      Stream.<BraveUser>of(new BraveUser(), new BraveUser()).collect(Collectors.toList()),
      PageRequest.of(0, 20),
      100
    );
  }
}
```
