## Foreach

```
ㅁ Author: suktae.choi
ㅁ Date: 2017.06.23
ㅁ References:
 - http://noveloper.github.io/blog/spring/2015/03/29/mybatis-foreach.html
```

Insert multiple values at once using foreach

```java
public void insert(String id, List<Item> items) {
  Map<String, Object> params = new HashMap<>();
  params.put("id", id);
  params.put("items", items);

  getSqlsession().insert("ItemRepository.insertItems", params);
}
```

```sql
<insert id="insertItems">
    INSERT INTO /*!99999 ItemRepository.insertItems */
        user_item (id, item_id, item_type, item_name)
    VALUE
    <foreach collection="items" item="item" separator=",">
        (#{id}, #{item.itemId}, #{item.itemType}, #{item.itemName})
    </foreach>
</insert>
```
