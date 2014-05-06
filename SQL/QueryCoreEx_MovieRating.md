##SQL Query CoreEX - _Movie Rating_

**Q1.** Find the titles of all movies directed by Steven Spielberg

```SQL
SELECT title FROM Movie WHERE director = 'Steven Spielberg';
```

**Q2.** Find all years that have a movie that received a rating of 4 or 5, 
    and sort them in increasing order. 
    
```SQL
SELECT year FROM Movie    
    WHERE mID in(SELECT mID FROM Rating WHERE stars>=4)
    ORDER BY year;
```

**Q3.** Find the titles of all movies that have no ratings. 

```SQL
SELECT title FROM Movie WHERE mID not in (SELECT mID FROM Rating);
```

**Q4.** Some reviewers didn't provide a date with their rating. Find the 
    names of all reviewers who have ratings with a NULL value for the 
    date.
    
```SQL
SELECT name FROM Reviewer 
    WHERE rID in (SELECT rID FROM Rating WHERE ratingDate is NULL);
    ```
    
**Q5.** Write a query to return the ratings data in a more readable format: 
    reviewer name, movie title, stars, and ratingDate. Also, sort the 
    data, first by reviewer name, then by movie title, and lastly by 
    number of stars. 
    
```SQL
SELECT name, title, stars, ratingDate
    FROM Reviewer NATURAL JOIN Rating NATURAL JOIN Movie
    ORDER BY name, title, stars;
```

**Q6.** For all cases where the same reviewer rated the same movie twice 
    and gave it a higher rating the second time, return the reviewer's 
    name and the title of the movie. 
    
```SQL
SELECT r.name,m.title
    FROM Reviewer r,Movie m
    WHERE r.rID IN (SELECT R2.rID FROM Rating R1, Rating R2
        WHERE R2.rID = R1.rID AND R2.mID = R1.mID AND 
        R2.ratingDate>R1.ratingDate AND R2.stars>R1.stars) AND
          m.mID IN (SELECT R2.mID FROM Rating R1, Rating R2
        WHERE R2.rID = R1.rID AND R2.mID = R1.mID AND 
        R2.ratingDate>R1.ratingDate AND R2.stars>R1.stars);
```

**Q7.** For each movie that has at least one rating, find the highest 
    number of stars that movie received. Return the movie title and 
    number of stars. Sort by movie title.
     
```SQL
SELECT title,Rate FROM((SELECT mID, max(stars) as Rate FROM Rating 
         GROUP BY mID HAVING count(mID)>1) NATURAL JOIN Movie)
    GROUP BY mID ORDER BY title
```  

**Q8.** List movie titles and average ratings, from highest-rated to 
    lowest-rated. If two or more movies have the same average rating, 
    list them in alphabetical order. 

```SQL
SELECT title, avg(stars) as AvgRate FROM Rating NATURAL JOIN Movie
    GROUP BY mID ORDER BY AvgRate DESC, title;
```

**Q9.** Find the names of all reviewers who have contributed three or more 
    ratings. (As an extra challenge, try writing the query without 
    HAVING or without COUNT.) 
    
```SQL
SELECT name FROM Reviewer NATURAL JOIN Rating GROUP BY rID
    HAVING count(rID)>=3
```