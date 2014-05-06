##Modified SQL Query CoreEx - _SocialNetwork_
**Q1.** It's time for the seniors to graduate. Remove all 12th graders 
    from Highschooler. 
    
```SQL
DELETE FROM Highschooler WHERE grade >= 12;
```

**Q2.** If two students A and B are friends, and A likes B but not 
    vice-versa, remove the Likes tuple.  

```SQL
    DELETE FROM Likes WHERE ID2 IN
        (SELECT ID2 FROM Friend WHERE Likes.ID1 = ID1) AND
    ID2 NOT IN SELECT L.ID1 FROM Likes L WHERE Likes.ID1 = L.ID2);
```

**Q3.** For all cases where A is friends with B, and B is friends with C, 
    add a new friendship for the pair A and C. Do not add duplicate 
    friendships, friendships that already exist, or friendships with 
    oneself.
```SQL
    INSERT INTO Friend (ID1, ID2) SELECT DISTINCT F1.ID1, F2.ID2
    FROM Friend F1 INNER JOIN Friend F2
    ON F1.ID2 = F2.ID1 AND F1.ID1<>F2.ID1 AND F1.ID2<>F2.ID2
    WHERE F1.ID1 <> F2.ID2 
    EXCEPT
    SELECT * FROM Friend;
```