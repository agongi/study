## ResultMap

```
ㅁ Author: suktae.choi
ㅁ Date: 2017.06.22
ㅁ References:
 - http://www.mybatis.org/mybatis-3/sqlmap-xml.html#Result_Maps
```

### Flat Model
Query result binds to resultMap model

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

### Subclass Model
Query result binds to resultMap model that has nested class
```java
/**
 * @author suktae.choi
 */
public class Box {

    private Long BoxId;
    private BoxGradeType boxGrade;
    private String boxName;

    private Card card;
}

public class Card {
  private Integer cardId;
  private CardType cardType;
  private String cardName;
}
```
```sql
select box_id, box_grade, card_id, card_type, card_name from {TABLE}
```
```sql
<resultMap id="BoxResultMap" type="com.model.Box" >
	<id property="boxId" column="box_id" />
	<result property="boxGrade" column="box_grade" />
	<result property="boxName" column="box_name" />

  <association property="card" javaType="com.model.Card">
      <result property="cardId" column="card_id"/>
      <result property="cardType" column="card_type"/>
      <result property="cardName" column="card_name"/>
  </association>
</resultMap>
```
