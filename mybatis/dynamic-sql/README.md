## Dynamic SQL

```
@author: suktae.choi
- http://www.mybatis.org/mybatis-3/dynamic-sql.html
```

### \<foreach\>
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
    INSERT INTO
        user_item (id, item_id, item_type, item_name)
    VALUE
    <foreach collection="items" item="item" open="(" separator="," close=")" index="index" >
        #{id}, #{item.itemId}, #{item.itemType}, #{item.itemName}
    </foreach>
</insert>
```

```java
public void insert(List<Item> items) {
  getSqlsession().insert("ItemRepository.insertItems", items);
}
```

```sql
<insert id="insertItems">
    INSERT INTO
        user_item (item_id, item_type, item_name)
    VALUE
    <foreach collection="list" item="item" open="(" separator="," close=")" index="index" >
        #{item.itemId}, #{item.itemType}, #{item.itemName}
    </foreach>
</insert>
```

> List type will be automatically assigned to map named **list**, Array is as **array**

### \<if test\>
Use if statement in sql

```sql
<select id="selectItems" parameterType="map" resultMap="itemResult">
  SELECT id, item_id, item_type, item_name
  FROM user_item
  WHERE id = #{id}

  <if test="itemId != null">
    AND item_id = #{itemId}
  </if>
</select>
```

### \<sql\>
Refer to table columns

```sql
<sql id="itemSqlColumns">
    id,
    item_id,
    item_type,
    item_name
</sql>

<select id="selectItem" resultMap="itemResult" parameterType="map">
    SELECT <include refid="itemSqlColumns"/>
    FROM  user_item
    WHERE id = #{id}
</select>
```
