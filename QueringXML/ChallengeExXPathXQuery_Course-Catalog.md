## ChallengeEX XPath XQuery - _Course Catalog_

**Q1.** Return the title of the course with the largest enrollment. 

```
let $catalog := doc('courses.xml'),
  $courses := $catalog//Course
  for $c in $courses
    where $c/@Enrollment = max($courses/@Enrollment)
    return $c/Title
```

**Q2.** Return course numbers of courses that have the same title as some other course. (Hint: You might want to use the "preceding" and "following" navigation axes for this query, which were not covered in the video or our demo script; they match any preceding or following node, not just siblings.) 

```
let $catalog := doc('courses.xml'),
  $courses := $catalog//Course
  for $c in $courses
    where $c/data(Title) = $c/following::*/data(Title) or
      $c/data(Title) = $c/preceding::*/data(Title)
    return $c/data(@Number)
```

**Q3.** Return the number (count) of courses that have no lecturers as instructors. 

```
let $catalog := doc('courses.xml'),
  $courses := $catalog//Course
  return count(
    for $c in $courses
      where count($c/Instructors/Lecturer) = 0
      return $c)
```

**Q4.** Return titles of courses taught by the chair of a department. For this question, you may assume that all professors have distinct last names.

```
let $catalog := doc('courses.xml'),
  $dept :=$catalog//Department,
  $courses := $catalog//Course
  for $c in $courses
    where $c/Instructors/Professor/Last_Name = $dept/Chair/Professor/Last_Name
    return $c/Title
```

**Q5.** Return titles of courses taught by a professor with the last name "Ng" but not by a professor with the last name "Thrun". 

```
let $catalog := doc('courses.xml'),
  $courses := $catalog//Course
  for $c2 in (
    for $c in $courses
      where every $name in $c//Professor/Last_Name satisfies $name != 'Thrun'
      return $c)
  where $c2//Professor/Last_Name = 'Ng'
  return $c2/Title
```

**Q6.** Return course numbers of courses that have a course taught by Eric Roberts as a prerequisite. 

```
let $catalog := doc('courses.xml'),
  $dept :=$catalog//Department,
  $courses := $catalog//Course
  for $c2 in $courses
    where $c2//Prerequisites/Prereq = (
      for $c in $courses
      where $c/Instructors/Professor/Last_Name = "Roberts"
      return $c/data(@Number))
    return $c2/data(@Number)
```

**Q7.** Create a summary of CS classes: List all CS department courses in order of enrollment. For each course include only its Enrollment (as an attribute) and its Title (as a subelement). 

```
let $catalog := doc('courses.xml'),
  $courses := $catalog//Department[data(@Code) = "CS"]/Course
  return <Summary> 
  {
    for $c in $courses
      order by xs:int($c/@Enrollment)
      return <Course Enrollment = "{$c/data(@Enrollment)}">{$c/Title}</Course>}
  </Summary>
```

**Q8.** Return a "Professors" element that contains as subelements a listing of all professors in all departments, sorted by last name with each professor appearing once. The "Professor" subelements should have the same structure as in the original data. For this question, you may assume that all professors have distinct last names. Watch out -- the presence/absence of middle initials may require some special handling.

```
let $catalog := doc('courses.xml'),
  $professors := $catalog//Professor
let $distinct_prof := (
      $professors except (
        for $p in $professors
          where ($p/Last_Name = $p/following::*/Last_Name and $p/First_Name = $p/following::*/First_Name)
          return $p))
return <Professors>
  {
    for $p in $distinct_prof
    order by $p/Last_Name
    return $p}
  </Professors>
```