## ResultMap

```
ㅁ Author: suktae.choi
ㅁ Date: 2017.06.22
ㅁ References:
 - http://www.mybatis.org/mybatis-3/sqlmap-xml.html#Result_Maps
```

Query result binds to resultMap model.

```java
/**
 * @author suktae.choi
 */
public class Box {

    private Long BoxId;
    private BoxGradeType boxGrade;
    private String name;

    private List<Card> cards;
}
```

```sql
<resultMap id="BoxResultMap" type="com.model.Box" >
	<id property="boxId" column="box_id" />
	<result property="boxGrade" column="box_grade" />
	<result property="name" column="name" />

	<collection property="cards" ofType="com.model.Card" >
		<result property="cardId" column="card_id" />
		<result property="cardCount" column="cnt" />
	</collection>
</resultMap>
```
