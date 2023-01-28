## ON DUPLICATE KEY UPDATE

```
ㅁ Author: suktae.choi
- https://okky.kr/article/318516
```

- Values are about to be inserted but there is duplicate P.K in table
- Insertion failed and will go to `ON DUPLICATE KEY UPDATE`
- It can fetch values mentioned in `INSERT INTO (칼럼1, 칼럼2, 칼럼3, 날짜)` using **VALUES** keyword

```sql
<insert id="set" parameterType="java.util.Map">
	INSERT INTO 테이블
	 (컬럼1, 컬럼2, 컬럼3, 날짜)
	VALUES
  	<foreach collection="list" item="item" separator=",">
  		(#{item.컬럼1값}, #{item.컬럼2값}, #{item.컬럼3}, NOW())
  	</foreach>
  ON DUPLICATE KEY UPDATE
    컬럼3 = VALUES(컬럼3),
    날짜 = NOW()
</insert>
```

This one is not working. 칼럼3 is not mentioned in INSERT INTO
```sql
INSERT INTO (칼럼1, 칼럼2)
VALUES (#{칼럼1}, #{칼럼2})
ON DUPLICATE KEY UPDATE
칼럼3 = VALUES(칼럼3)
```
