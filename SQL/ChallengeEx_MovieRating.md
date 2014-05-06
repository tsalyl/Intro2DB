## ChallengeEX SQL Query - _MovieRating_

**Q1.** For each movie, return the title and the 'rating spread', that is, the difference between highest and lowest ratings given to that movie. Sort by rating spread from highest to lowest, then by movie title. 

```SQL
 SELECT title, sprd FROM Movie INNER JOIN
 (SELECT mID as id, max(stars)-min(stars) AS sprd FROM Rating GROUP BY mID) 
 WHERE Movie.mID = id
 ORDER BY sprd DESC, title
 ```
 **Q2.** Find the difference between the average rating of movies released before 1980 and the average rating of movies released after 1980. (Make sure to calculate the average rating for each movie, then the average of those averages for movies before 1980 and movies after. Don't just calculate the overall average rating before and after 1980.) 
 
```SQL
SELECT max(aRp)-min(aRp) FROM(
SELECT avg(aR) AS aRp FROM Movie INNER JOIN
(SELECT mID as id, avg(stars) AS aR FROM Rating GROUP BY mID)
WHERE Movie.mID = id and Movie.year < 1980
UNION
SELECT avg(aR) AS aRp FROM Movie INNER JOIN
(SELECT mID as id, avg(stars) AS aR FROM Rating GROUP BY mID)
WHERE Movie.mID = id and Movie.year >= 1980)
```
**Q3.** Some directors directed more than one movie. For all such directors, return the titles of all movies directed by them, along with the director name. Sort by director name, then movie title. (As an extra challenge, try writing the query both with and without COUNT.) 

```SQL
SELECT title, director FROM Movie
WHERE director IN (SELECT director
FROM Movie 
GROUP BY director
HAVING count(*)>1)
ORDER BY director, title
```
**Q4.** Find the movie(s) with the highest average rating. Return the movie title(s) and average rating. (Hint: This query is more difficult to write in SQLite than other systems; you might think of it as finding the highest average rating and then choosing the movie(s) with that average rating.) 

```SQL
SELECT title, aR FROM Movie INNER JOIN
(SELECT mID as id, avg(stars) AS aR FROM Rating GROUP BY mID)
WHERE Movie.mID = id AND aR=
(SELECT max(aR) FROM Movie INNER JOIN
(SELECT mID as id, avg(stars) AS aR FROM Rating GROUP BY mID)
WHERE Movie.mID = id)
```

**Q5.** Find the movie(s) with the lowest average rating. Return the movie title(s) and average rating. (Hint: This query may be more difficult to write in SQLite than other systems; you might think of it as finding the lowest average rating and then choosing the movie(s) with that average rating.) 

```SQL
SELECT title, aR FROM Movie INNER JOIN
(SELECT mID as id, avg(stars) AS aR FROM Rating GROUP BY mID)
WHERE Movie.mID = id AND aR=
(SELECT min(aR) FROM Movie INNER JOIN
(SELECT mID as id, avg(stars) AS aR FROM Rating GROUP BY mID)
WHERE Movie.mID = id)
```

**Q6.** For each director, return the director's name together with the title(s) of the movie(s) they directed that received the highest rating among all of their movies, and the value of that rating. Ignore movies whose director is NULL. 

```SQL
SELECT director, title, max(stars) FROM 
Movie INNER JOIN Rating 
WHERE Movie.mID = Rating.mID AND director is not null
GROUP BY director
```

