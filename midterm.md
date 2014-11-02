
# Midterm 
## Due: October 

1)) 

SELECT county_ansi.state, county_ansi.county, county_boundaries.geometry, leso.item_name
  FROM county_ansi
  INNER JOIN county_boundaries
     ON county_ansi.fips = county_boundaries.fips
  INNER JOIN leso
     ON county_ansi.county = leso.county AND county_ansi.state = leso.state
WHERE leso.item_name LIKE "%mine resistant%"

  See the [interactive map](https://www.google.com/fusiontables/data?docid=1QKMK82Dv0EvdLRxjpbDe3lEOd9kv78KxQo4fVYiK#map:id=3) here. 

2)) 

SELECT Item_Name, state, county, Acquisition_Cost
  FROM leso
  ORDER BY Acquisition_Cost DESC
 
The most expensive single item that can be acquired through the 1033 program is the "rotary wing aircraft," with an acquisition cost of $18,000,000. They're giant helicopters? Below is a picture of the biggest one I could find. 

![rotary airplane](http://upload.wikimedia.org/wikipedia/commons/d/d6/US_Navy_100802-N-3852A-366_An_AH-1Z_Cobra_helicopter_assigned_to_Rotary_Wing_Aircraft_Test_Squadron_%28HX%29_21,_based_in_Patuxent_River,_Md.,_approaches_the_amphibious_assault_ship_USS_Wasp_%28LHD_1%29.jpg)

SELECT state, county, item_name, COUNT(quantity)
FROM leso 
WHERE item_name = "Aircraft, Rotary Wing"
GROUP BY county

Results show that Brevard County in Florida has bought 8 of these puppies. After also querying for shipping dates, I found that they bought 6 of them within a week timespan in April of 2011. Tried to find out why. [This](http://www.abovetopsecret.com/forum/thread1028828/pg1) is what someone had to say about it. 


| State | County       | Item Name            | Quantity|
| ------|:------------:| --------------------:|--------:|
| FL    | Brevard      |Aircraft, Rotary Wing |    8    |
| AZ    | Pinal        |Aircraft, Rotary Wing |    2    |
| AL    | Chilton      |Aircraft, Rotary Wing |    1    |
| MS    | Desoto       |Aircraft, Rotary Wing |    1    |
| MO    | Saint Louis  |Aircraft, Rotary Wing |    1    |
| CA    | Santa Barbara|Aircraft, Rotary Wing |    1    |
| OK    | Oklahoma     |Aircraft, Rotary Wing |    1    |


3)) 
Later I went through what really pertains to the "1005" psc_code, and saw that there is some magazine cartridges in there, for example. It may have been smarter to specify for the specific different types of rifles, shotguns, revolvers and pistols, but I felt that that, too, would limit other types of guns. I'm going to try and address this in my no. 8 question. 

SELECT state, county, SUM(quantity)
FROM leso 
WHERE psc_code = "1005"
GROUP BY county
ORDER BY SUM(quantity) DESC
LIMIT 10



| State | County      | Sum of guns acquired |
| ------|:-----------:| --------------------:|
| TX    | El Paso     |50991 |
| PA    | Blair       |21528|   
| IL    | Franklin    |12867 |   
| AZ    | Maricopa    |11830 |    
| IL    | Jefferson   |11396|    
| CA    | Los Angeles |8183|   
| OH    | Fulton      |7043|   
| OK    | Oklahoma    |4899 |   
| CA    | Orange      |4665 |   
| TX    | Montgomery  |4446|   


4)) 


SELECT county_ansi.state, county_ansi.county, SUM(leso.quantity), census_quickfacts.PST045213, SUM(leso.quantity) / census_quickfacts.PST045213 * 1000 AS Guns_per_1000_people
  FROM county_ansi
  INNER JOIN census_quickfacts
     ON county_ansi.fips = census_quickfacts.fips
  INNER JOIN leso
     ON county_ansi.county = leso.county AND county_ansi.state = leso.state
  WHERE leso.psc_code = "1005"
  GROUP BY leso.county
  ORDER BY Guns_per_1000_people DESC 
  LIMIT 10

I'm sick of manually inputting tables, so here's a screenshot. 

![image](http://i.imgur.com/7EIxHq0.png)

5)) 

I first ran a query to see what where the top 10 ship dates that shipped the most amount of supplies. I found that a day in 2013 (October 2) had a much bigger overall shipment quantity then its runner up, June 19th of the same year (51604 was the count.) Actually, in the top 10 heaviest shipment days, 2013 took 5 spots. 

SELECT SUM(quantity), ship_date
FROM leso 
GROUP by ship_date
ORDER by SUM(quantity) DESC
LIMIT 10

Then, I decided to look at 2013 more closely, and ran a query to see all of the shipment quantities across the year 2013.

SELECT SUM(quantity), ship_date
FROM leso 
WHERE ship_date LIKE "2013%"
GROUP by ship_date
ORDER by SUM(quantity) DESC

I did separate queries to find quantity amounts per month, and plotted the following graph: 

![graph 1](http://i.imgur.com/nsAbQJm.png)


But, I thought that was pretty lame. So I did another graph showing the shipment quantities per year for the past five years to see how it would compare. 2013 was high, but not as high as 2012. Pretty cool comparison to the previous 3 years...

![graph 2](http://i.imgur.com/SUuDHiQ.png)


6))


SELECT county_ansi.state, county_ansi.county, county_boundaries.geometry
 FROM county_ansi 
 INNER JOIN county_boundaries 
 ON county_ansi.fips = county_boundaries.fips 
 LEFT JOIN leso 
 ON county_ansi.county = leso.county AND county_ansi.state = leso.state
 WHERE leso.psc_code IS NULL 

Find the resulting map [here](https://www.google.com/fusiontables/data?docid=1-KYeLgx2puinDtWwivSlQhcAGM6XfGebOHw26Qxw#map:id=3). 


7))

First I ran this query to figure out which county has acquired the most from 1003 Program in terms of total acquisiton cost. 

SELECT state, county, Quantity, Acquisition_Cost, (Quantity * Acquisition_Cost) AS Total_Acquisition_Cost
FROM leso 
GROUP BY county 
ORDER BY Total_Acquisition_Cost DESC

DC takes the win with $800,865. 

SELECT state, county, Item_name, Quantity, Acquisition_Cost, (Quantity * Acquisition_Cost) AS Total_Acquisition_Cost
FROM leso 
WHERE state = "DC"
GROUP BY Item_name
ORDER BY Total_Acquisition_Cost DESC

Check out the [list](https://docs.google.com/spreadsheets/d/1iR1BWRrNnD3WcFT0Q1mq4d-4xYA5FTvLBm1INcH9Rfo/edit?usp=sharing) of items, quantities, and total acquisition cost for 1003 Program acquisitions by D.C.

8))

Guns and Race / Guns and populated areas

I was interested in seeing the acquisition of 1003 Program gun-related items by counties in the U.S. regarding stereotyping races. My initial question curiosity sprung from this: Are counties that have higher black or American American populations getting more gun-related items from the 1003 Program? This can be seen from a number of different angles. If the results showed that counties with a larger African American population also were counties that have acquired more gun-related items from the program, are law enforcement officials in counties racist, perhaps? The 1003 program claims that it gives acquisition priority to counties with increased risk of terror and drug issues. If counties receiving more gun-related items are also counties that have experienced more safety issues regarding these categories, what race predominates in these counties?

I ran the following query: 
  SELECT county_ansi.state, county_ansi.county, county_ansi.fips, SUM(leso.quantity), census_quickfacts.RHI225213 AS AA_Pop_Percent, census_quickfacts.PST045213 
  FROM county_ansi
  INNER JOIN census_quickfacts
     ON county_ansi.fips = census_quickfacts.fips
  INNER JOIN leso
     ON county_ansi.county = leso.county AND county_ansi.state = leso.state
  WHERE leso.psc_code = "1005"
  GROUP BY leso.county
 ORDER BY SUM(leso.quantity) DESC

The results were pretty fascinating (see next paragraph). The top three counties to acquire the largest quantity of gun-related items had the following African American population percentage: El Paso, Colorado with 4.00%, Blair, Pennsylvania with 1.8%, and Franklin, Illinois with 0.5%. Although this comparison with race didn’t end up seeming THAT interesting to me, I still tried to map counties to see if there is a visual correlation. 

I ran the following query to join the county boundary data for mapping: 
  SELECT county_ansi.state, county_ansi.county, county_ansi.fips, SUM(leso.quantity), census_quickfacts.RHI225213 AS AA_Pop_Percent, census_quickfacts.PST045213, county_boundaries.geometry
  FROM county_ansi
  INNER JOIN census_quickfacts
     ON county_ansi.fips = census_quickfacts.fips
  INNER JOIN leso
     ON county_ansi.county = leso.county AND county_ansi.state = leso.state
  INNER JOIN county_boundaries 
  	ON county_boundaries.fips = county_ansi.fips
  WHERE leso.psc_code = "1005"
  GROUP BY leso.county
 ORDER BY SUM(leso.quantity) DESC


The higher the population percentage of African Americans, the “more red” the outline of the county is. The higher the quantity of gun-related items acquired, the “more red” the fill of the county is. 

![image]( http://i.imgur.com/bYVFLsU.png)

But, what I ended up finding was the most interesting about this data was looking at those counties and their populations. The population of Franklin County in Illinois is 39,202. Maricopa County in Arizona succeeds Franklin County with a whopping population 4,009,412. What could possibly be going on in Franklin County, that would drive them to acquire the third largest amount of gun-related items by county in the country? After some research I came to the conclusion that it may be a drug issue. Apparently there have been several arrests in the area for meth use. To further the investigation, I think it’d be interesting to explore the crime rates in these counties. Looking at shipment dates may also be interesting in order to see if there’s any correlation between higher crime rates at specific points in time. 

So I did another map. The higher the overal population, the "darker the blue" the outline of the county is. The higher the quantity of gun-related items acquired, the "darker the blue" the fill of the county is. 

![image 2](http://i.imgur.com/Z9l4dL3.png)

