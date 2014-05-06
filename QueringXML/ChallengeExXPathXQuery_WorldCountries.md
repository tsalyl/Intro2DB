## ChallengeEX XPath XQuery - _World Countries_

**Q1.** Return the names of all countries that have at least three cities with population greater than 3 million. 

```
let $catalog := doc('countries.xml'),
  $cntry := $catalog//country
    for $c in $cntry
    where count($c/city[data(population) > 3000000]) >=3
    return $c/data(@name)
```
**Q2.** Create a list of French-speaking and German-speaking countries.

```
let $catalog := doc('countries.xml'),
    $cntry := $catalog//country,
    $F := $cntry[language='French']/data(@name),
    $G := $cntry[language='German']/data(@name)
return <result>
  <French>
  {
    for $f in $F
    return <country>{$f}</country>}
  </French>
  <German>
  {
    for $g in $G
    return <country>{$g}</country>}
  </German>
  </result>
```

**Q3.** Return the names of all countries containing a city such that some other country has a city of the same name. (Hint: You might want to use the "preceding" and/or "following" navigation axes for this query, which were not covered in the video or our demo script; they match any preceding or following node, not just siblings.) 

```
let $catalog := doc('countries.xml'),
  $cntry := $catalog//country
  for $c in $cntry
    where $c/city/data(name) = $c/following::*/city/data(name) or
      $c/city/data(name) = $c/preceding::*/city/data(name)
    return $c/data(@name)  
```

**Q4.** Return the average number of languages spoken in countries where Russian is spoken. 

```
let $catalog := doc('countries.xml'),
  $cntryR := $catalog//country[language = 'Russian']
  return avg(
  for $c in $cntryR
  return count($c/language)) 
```

**Q5.** Return all country-language pairs where the language is spoken in the country and the name of the country textually contains the language name. Return each pair as a country element with language attribute.

```
let $catalog := doc('countries.xml'),
  $cntry := $catalog//country
  for $c in $cntry
  for $L in $c/language
    where contains($c/data(@name),$L)
    return
    <country language= "{data($L)}">{$c/data(@name)}</country>
```

**Q6.** Return all countries that have at least one city with population greater than 7 million. For each one, return the country name along with the cities greater than 7 million.

```
let $countries := doc('countries.xml')/countries/country
for $c in $countries
  where count($c/city[population > 7000000]) > 0
  return 
    <country name="{$c/data(@name)}">
    {
      for $city in $c/city
        where $city[population > 7000000]
        return <big>{$city/data(name)}</big>}
    </country>
```

**Q7.** Return all countries where at least one language is listed, but the total percentage for all listed languages is less than 90%. Return the country element with its name attribute and its language subelements, but no other attributes or subelements. 

```
let $catalog := doc('countries.xml'),
  $cntry := $catalog//country[language]
    for $c in $cntry
    where sum($c/language/data(@percentage))<90
    return <country name="{$c/data(@name)}">
    { for $L in $c/language
      return $L}
    </country>
```

**Q8.** Return all countries where at least one language is listed, and every listed language is spoken by less than 20% of the population. Return the country element with its name attribute and its language subelements, but no other attributes or subelements. 

```
let $catalog := doc('countries.xml'),
  $cntry := $catalog//country[language]
    for $c in $cntry
    where every $lng in $c/language satisfies $lng/data(@percentage)<20
    return <country name="{$c/data(@name)}">{$c/language}
    </country>
```

**Q9.** Find all situations where one country's most popular language is another country's least popular, and both countries list more than one language. (Hint: You may need to explicitly cast percentages as floating-point numbers with xs:float() to get the correct answer.) 

```
let $country := doc('countries.xml')/countries/country[language],
  $most_popular := 
  for $c in $country[count(language) > 1] 
    for $l in $c/language
     where 
     xs:float($l/data(@percentage)) = 
     xs:float(max($c/language/data(@percentage)))
      return $l,
  $least_popular := 
  for $c in $country[count(language) > 1] 
    for $l in $c/language
     where 
     xs:float($l/data(@percentage)) = 
     xs:float(min($c/language/data(@percentage)))
        return $l
for $lmax in $most_popular
    for $lmin in $least_popular
      where data($lmax) = data($lmin)
        return
          <LangPair language="{data($lmin)}">
            <MostPopular>{$lmax/parent::country/data(@name)}</MostPopular>
            <LeastPopular>{$lmin/parent::country/data(@name)}</LeastPopular>
          </LangPair> 
```

**Q10.** For each language spoken in one or more countries, create a "language" element with a "name" attribute and one "country" subelement for each country in which the language is spoken. The "country" subelements should have two attributes: the country "name", and "speakers" containing the number of speakers of that language (based on language percentage and the country's population). Order the result by language name, and enclose the entire list in a single "languages" element.

```
let $country := doc('countries.xml')/countries/country[language],
  $languages := $country/language,
  $language_names := distinct-values($country/data(language))
  return <languages>
  {
    for $l_name in $language_names
      order by $l_name
      return 
        <language name="{$l_name}">
        {
          for $l in $languages
            where data($l) = $l_name
            return <country name="{data($l/parent::country/@name)}" 
            speakers=
            "{xs:int
            ($l/parent::country/@population * $l/@percentage div 100) }">}
        </language>
  }
  </languages>
```


         

