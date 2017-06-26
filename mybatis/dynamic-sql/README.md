## Dynamic SQL

```
ㅁ Author: suktae.choi
ㅁ Date: 2017.06.26
ㅁ References:
 - http://www.mybatis.org/mybatis-3/dynamic-sql.html
```

### Foreach
Insert multiple values at once

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

### If
Use if statement in sql

```sql
<select id="selectItems" parameterType="map" resultMap="itemResult">
  SELECT id, item_id, item_type, item_name
  FROM mr_user_supply_box A
  WHERE id = #{id}

  <if test="itemId != null">
    AND item_id = #{itemId}
  </if>
</select>
```
