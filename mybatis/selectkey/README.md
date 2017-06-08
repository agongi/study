## Selectkey

```
ㅁ Author: suktae.choi
ㅁ Date: 2017.06.08
ㅁ References:
 - http://www.mybatis.org/mybatis-3/sqlmap-xml.html#insert_update_and_delete
```

You can modify row in DB and get the affected value right before/after query execution as following:

```sql
<update id="updateCoin" parameterType="map">
	UPDATE /*!99999 repository.updateCoin */
		user_balance
	SET
		coin = (coin + #{coinChange}),
		mod_ymdt = NOW()
	WHERE
		user_key = #{userKey}

	<selectKey keyProperty="coinTotal" resultType="Integer" order="AFTER">
		SELECT coin
		FROM user_balance
		WHERE user_key = #{userKey}
	</selectKey>
</update>
```

```java
public Integer updateCoin(String userKey, Integer coinChange) {
    Map<String, Object> map = new HashMap<>();
    map.put("userKey", userKey);
    map.put("coinChange", coinChange);
    map.put("coinTotal", null);
    // update coin
    if (getSqlSession().update("repository.updateCoin", map) > 0) {
      // get updated coin in one way
      return (Integer) params.get("coinTotal");  
    }

    return null;
}
```
