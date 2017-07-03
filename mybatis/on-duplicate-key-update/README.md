## ON DUPLICATE KEY UPDATE

```
ㅁ Author: suktae.choi
ㅁ Date: 2017.07.04
ㅁ References:
 - https://okky.kr/article/318516
```

You can modify row in DB and get the affected **ONLY ONE value** right before/after query execution as following:

```sql
<insert id="set" parameterType="java.util.Map">
	INSERT INTO 테이블
	 (컬럼1, 컬럼2, 컬럼3, 날짜)
	VALUES
  	<foreach collection="list" item="item" separator=",">
  		(#{item.컬럼1값}, #{item.컬럼2값}, #{item.컬럼3}, NOW())
  	</foreach>
  ON DUPLICATE KEY UPDATE
    컬럼3 = 컬럼3 + VALUES(컬럼3),
    날짜 = NOW()
</insert>
```
