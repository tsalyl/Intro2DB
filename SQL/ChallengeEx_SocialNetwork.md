## ChallengeEX SQL Query - _SocialNetwork_

**Q1.** Find all students who do not appear in the Likes table (as a student who likes or is liked) and return their names and grades. Sort by grade, then by name within each grade. 

```SQL
SELECT name,grade 
FROM Highschooler
WHERE ID NOT IN (SELECT ID2
FROM Likes UNION SELECT ID1
FROM Likes) 
```

**Q2.** For each student A who likes a student B where the two are not friends, find if they have a friend C in common (who can introduce them!). For all such trios, return the name and grade of A, B, and C.

```SQL
select H1.name, H1.grade, H2.name, H2.grade, H3.name, H3.grade
from Highschooler H1, Highschooler H2, Highschooler H3, Friend F1, Friend F2, (
  select * from Likes
  except
  select Likes.ID1, Likes.ID2
  from Likes, Friend
  where Friend.ID1 = Likes.ID1 and Friend.ID2 = Likes.ID2
) as LikeNotFriend
where F1.ID1 = LikeNotFriend.ID1
and F2.ID1 = LikeNotFriend.ID2
and F1.ID2 = F2.ID2
and H1.ID = LikeNotFriend.ID1
and H2.ID = LikeNotFriend.ID2
and H3.ID = F2.ID2
;
```

**Q3.** Find the difference between the number of students in the school and the number of different first names. 

```SQL
SELECT count(name)-count(DISTINCT name) FROM Highschooler
```

**Q4.** What is the average number of friends per student? (Your result should be just one number.) 

```SQL
SELECT avg(C)
FROM 
(SELECT count(*) AS C
FROM Friend
GROUP BY ID1)
```

**Q5.** Find the number of students who are either friends with Cassandra or are friends of friends of Cassandra. Do not count Cassandra, even though technically she is a friend of a friend. 

```SQL
SELECT count(DISTINCT ID2)-1 FROM Friend
WHERE ID1 IN(
SELECT ID2 FROM Friend
WHERE ID1 IN (
SELECT ID as C
FROM Highschooler
WHERE name = "Cassandra") UNION
SELECT ID FROM Highschooler WHERE name = "Cassandra"
)
```

**Q6.** Find the name and grade of the student(s) with the greatest number of friends. 

```SQL
SELECT name,grade FROM Highschooler
WHERE ID IN(
SELECT id FROM
(SELECT Friend.ID1 AS id, count(*) AS C
FROM Friend
GROUP BY ID1)
WHERE C=(SELECT max(K) FROM (SELECT count(*) AS K FROM Friend GROUP BY ID1))
)
```