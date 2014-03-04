##XPath XQuery CoreEx - _World Countries_
**Q1.** Return the area of Mongolia. 
```SQL:
doc("countries.xml") //country[@name="Mongolia"]/data(@area)
```

**Q2.** Return the names of all cities that have the same name as the country in which they are located. 
```SQL:
doc("countries.xml") //city[name=parent::country/@name]/name
```

**Q3.** Return the average population of Russian-speaking countries. 
```SQL:
avg(doc("countries.xml") //country[language = "Russian"]/data(@population))
```

**Q4.** Return the names of all countries where over 50% of the population speaks German. 
```SQL:
doc("countries.xml") //country[language[text()= "German" and @percentage>50]]/data(@name)
```

**Q5.** RReturn the name of the country with the highest population. 
```SQL:
let $countries := doc('countries.xml')/countries
return $countries/*[@population =  max($countries/country/data(@population))]/data(@name)
```