##Modified SQL Query CoreEx - _MovieRating_
**Q1.** Add the reviewer Roger Ebert to your database, with an rID of 209. 
```SQL
INSERT INTO Reviewer (rID, name) VALUES('209','Roger Ebert')
```
**Q2.** Insert 5-star ratings by James Cameron for all movies in the 
    database. Leave the review date as NULL. 
```SQL
INSERT INTO Rating SELECT '207', mID, '5', NULL FROM Movie;
```
**Q3.** For all movies that have an average rating of 4 stars or higher, 
    add 25 to the release year. (Update the existing tuples; don't 
    insert new tuples.) 
```SQL
    UPDATE Movie SET year = case
    WHEN mID in (SELECT mID FROM Rating GROUP BY mID
                 HAVING avg(stars)>=4) 
    THEN year +25 ELSE year END;
```
**Q4.** Remove all ratings where the movie's year is before 1970 or after 
    2000, and the rating is fewer than 4 stars. 
```SQL
DELETE FROM Rating WHERE stars < 4 AND
       mID in (SELECT mID FROM Movie WHERE year < 1970 OR year > 2000);
```