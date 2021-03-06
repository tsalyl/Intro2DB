##SQL Query CoreEx - _SocialNetwork_
 
**Q1.** Find the names of all students who are friends with someone named 
    Gabriel. 
    
```SQL
    SELECT name FROM Highschooler
    WHERE ID in (SELECT ID1 FROM Friend WHERE ID2 in
    (SELECT ID FROM Highschooler WHERE name = 'Gabriel'));
```

**Q2.** For every student who likes someone 2 or more grades younger than 
    themselves, return that student's name and grade, and the name and 
    grade of the student they like. 
    
```SQL
SELECT H1.name,H1.grade,H2.name,H2.grade FROM Likes    
         INNER JOIN Highschooler H1 ON Likes.ID1 = H1.ID    
         INNER JOIN Highschooler H2 ON Likes.ID2 = H2.ID     
         WHERE H1.grade >= H2.grade + 2 ;     
 ```
 
**Q3.** For every pair of students who both like each other, return the 
    name and grade of both students. Include each pair only once, with 
    the two names in alphabetical order. 
    
```SQL
SELECT H1.name, H1.grade, H2.name, H2.grade 
    FROM (SELECT LA.ID1 AS id1, LA.ID2 AS id2 FROM Likes LA, Likes LB
                WHERE LA.ID1 = LB.ID2 AND LA.ID2 = LB.ID1) 
          INNER JOIN Highschooler H1 ON id1 = H1.ID
          INNER JOIN Highschooler H2 ON id2 = H2.ID
    WHERE H1.name< H2.name ;
```

**Q4.** Find names and grades of students who only have friends in the same 
    grade. Return the result sorted by grade, then by name within each 
    grade. 
    
```SQL
SELECT DISTINCT H1.name,H1.grade FROM Friend
    INNER JOIN Highschooler H1 ON Friend.ID1 = H1.ID
    INNER JOIN Highschooler H2 ON Friend.ID2 = H2.ID
    WHERE  H1.grade = H2.grade 
    EXCEPT
    SELECT DISTINCT H1.name,H1.grade FROM Friend
    INNER JOIN Highschooler H1 ON Friend.ID1 = H1.ID
    INNER JOIN Highschooler H2 ON Friend.ID2 = H2.ID
    WHERE  H1.grade <> H2.grade 
    ORDER BY H1.grade, H1.name;
```

**Q5.** Find the name and grade of all students who are liked by more than 
    one other student. 
    
```SQL
SELECT name,grade FROM Highschooler
    WHERE ID IN (SELECT ID2 FROM Likes GROUP BY ID2
                 HAVING count(*)>1);
```