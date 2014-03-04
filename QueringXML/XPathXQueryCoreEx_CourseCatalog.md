##XPath XQuery CoreEX - _Course Catalog_
  
**Q1.** Return all Title elements (of both departments and courses).    

```SQL:     
doc("courses.xml")//Title
```
___
**Q2.** Return last names of all department chairs. 

```SQL:  
doc("courses.xml")//Chair/Professor/Last_Name
```
___
**Q3.** Return titles of courses with enrollment greater than 500. 
```SQL:
doc("courses.xml")//Course[@Enrollment > 500]/Title
```
___
**Q4.** Return titles of departments that have some course that takes "CS106B" as a prerequisite. 

```SQL:
doc("courses.xml")//Department[Course/Prerequisites/Prereq="CS106B"]/Title
```
___
**Q5.** Return last names of all professors or lecturers who use a middle initial. Don't worry about eliminating duplicates. 

```SQL:
doc("courses.xml")//(Professor|Lecturer)[Middle_Initial]/Last_Name
```
___
**Q6.** Return the count of courses that have a cross-listed course (i.e., that have "Cross-listed" in their description). 

```SQL:
count(doc("courses.xml")//Course[contains(Description,"Cross-listed")])
```
___
**Q7.** Return the average enrollment of all courses in the CS department. 

```SQL:
doc("courses.xml")//Department[@Code="CS"]/avg(Course/@Enrollment)
```
___
**Q8.** Return last names of instructors teaching at least one course that has "system" in its description and enrollment greater than 100. 

```SQL:
doc("courses.xml")//Course[@Enrollment>100 and contains(Description,"system")]/Instructors//Last_Name
```