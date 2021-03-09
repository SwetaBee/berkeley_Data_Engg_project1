# Project 1: SF Bikeshare Project

## Part 1:
  This project is about data scientists working for this SF Bikeshare company trying to increase ridership and offer deals. First part of this project was to explore the data. Following are the few questions I tried to answer using BigQuery (GCP). 

- What's the size of this dataset? (i.e., how many trips)

 **Answer: 983648**
```  
SQL query:   SELECT count(*)
                FROM `bigquery-public-data.san_francisco.bikeshare_trips`
```                


- What is the earliest start date and time and latest end date and time for a trip?

  **Answer:2013-08-29 09:08:00   UTC2016-08-31 23:48:00 UTC**
     
     
 ```           
 *SQL query: SELECT min(start_date)
                FROM `bigquery-public-data.san_francisco.bikeshare_trips`
            SELECT max(end_date)
                FROM `bigquery-public-data.san_francisco.bikeshare_trips`  
```

- How many bikes are there?

**Answer: 700

```
SQL query: SELECT count(distinct bike_number)
                FROM `bigquery-publicdata.san_francisco.bikeshare_trips`
 ``` 

  As we dig deeper into the data, we find the most popular places(Counts the same station names) from the Bikeshare trips table. If we consider total number of bikes as (sum of docks available and bikes available) from Bikeshare_status table, 35 total bikes are available at  station id's 90, 91 and 92.Also, using timestamps from Bikeshare trips table we were able to find out that morning (6-9 am) and evening (6-8pm) are when most commuters use bikes. 


- Question 1: which trips are most popular?

** Answer:**
    
  |start_station_name	|end_station_name                |Count|
  |     ---              |         ---                       |      |
  |Harry Bridges Plaza (Ferry Building)	|Embarcadero at Sansome|9150
  |San Francisco Caltrain 2 (330 Townsend)	|Townsend at 7th	   |8508|
  |2nd at Townsend	|Harry Bridges Plaza (Ferry Building)	   |7620|
  |Harry Bridges Plaza (Ferry Building)	|2nd at Townsend	       |6888|
  |Embarcadero at Sansome	|Steuart at Market	               |6874|
  |Townsend at 7th	|San Francisco Caltrain 2 (330 Townsend)	   |6836|


  
  * SQL query:
  ```
   SELECT start_station_name, end_station_name, COUNT(*) AS Count
    FROM `bigquery-public-data.san_francisco.bikeshare_trips`
    GROUP BY start_station_name, end_station_name
    ORDER BY Count DESC
    LIMIT 10;
   ```
   
- Question 2: what are the most bikes available at station id's 91, 90, and 92?
  * Answer: Assuming that total bikes are sum of docks available and bikes available on trip, we get
  
   station_id	total_bikes	  time
        91	35	2016-08-28 12:05:47+00:00
        91	35	2016-08-28 00:37:01+00:00
        91	35	2016-08-28 06:00:48+00:00
        91	35	2016-08-29 14:56:00+00:00
        91	35	2016-08-27 04:11:49+00:00
        91	35	2016-08-25 10:35:46+00:00
        
  * SQL query:
  
  ```
   #standardSQL
    SELECT station_id, (docks_available + bikes_available) AS total_bikes, time
    FROM `bigquery-public-data.san_francisco.bikeshare_status`
    WHERE station_id IN (90, 91, 92)
    ORDER BY total_bikes desc
    LIMIT 10
   ```
   
 
 - Question 3: what time of the day has the highest count of bike rides at station 70?
  * Answer:
  
    Count |  Hour
    ---   |   ---
    20205	|7
    26843	|8
    12989	|9
  
  * SQL query:
  
```
     #standardSQL
    SELECT COUNT(*) as Count,
    EXTRACT(HOUR FROM start_date) AS hour
    FROM `bigquery-public-data.san_francisco.bikeshare_trips` AS bikeshare_trips
    WHERE start_station_id = 70 OR end_station_id = 70
    GROUP BY hour
    ORDER BY hour ASC
    LIMIT 100;

```


## Part 2 - Querying data from the BigQuery CLI 

In this part, we are doing more data exploration using Bigquery command line (from GCP Notebook Terminal). 
The command to use is as follows:

- Use BQ from the Linux command line:

  * General query structure

    ```
    bq query --use_legacy_sql=false '
        SELECT count(*)
        FROM
           `bigquery-public-data.san_francisco.bikeshare_trips`'
    ```

### Project 1(part 2)

We performed the same question in command line and obtained same results.

  
1.  What's the size of this dataset? (i.e., how many trips)

  **Answer:**

+--------+
|  f0_   |
+--------+
| 983648 |
+--------+

  *SQL query:   
  
  ```
  bq query --use_legacy_sql=false 'SELECT count(*)>                 
  FROM `bigquery-public-data.san_francisco.bikeshare_trips`'
  ```

2.  What is the earliest start time and latest end time for a trip?

  **Answer:**
  
+---------------------+   
|         f0_         |
+---------------------+
| 2016-08-31 23:48:00 |
+---------------------+ 
|         f0_         |
+---------------------+
| 2013-08-29 09:08:00 |
+---------------------+


   *SQL query:  
   
```   
bq query --use_legacy_sql=false 'SELECT min(start_date)
                 FROM `bigquery-public-data.san_francisco.bikeshare_trips`'
                 bq query --use_legacy_sql=false 'SELECT max(end_date)
                 FROM `bigquery-public-data.san_francisco.bikeshare_trips`'
  ```
   
3.   How many bikes are there?
  
  **Answer:**
+-----+
| f0_ |
+-----+
| 700 |
+-----+

 *SQL query:
   
```   
   bq query --use_legacy_sql=false 'SELECT count(distinct bike_number)
   FROM `bigquery-public-data.san_francisco.bikeshare_trips`'
```

2. More data exploratory questions in order to recommend some offers to generate more revenue.

  * How many trips are in the morning vs in the afternoon? *
  
**Answer:**    There are about 30,000 trips in the morning (5-9am) vs about 37000 trips in the evening (4-9pm). While most trips are around 8am in the morning, most trips in the evening are about at 5pm. Here , my assumption is morning is from 5-9 am and evening is from 4-9 pm.

+--------+------+  
| Count  | hour |
+--------+------+
| 129072 |   17 |
|  96317 |   18 |
|  81238 |   16 |
|  47273 |   19 |
|  25371 |   20 |
|  16571 |   21 |
+--------+------+
| Count  | hour |
+--------+------+
| 132464 |    8 |
|  96118 |    9 |
|  67531 |    7 |
|  20519 |    6 |
|   5098 |    5 |
+--------+------+

*The SQL query is:

```
    bq query --use_legacy_sql=false '#standardSQL
> SELECT COUNT(*) as Count,
> EXTRACT(HOUR FROM start_date) AS hour
> FROM `bigquery-public-data.san_francisco.bikeshare_trips` AS bikeshare_trips
> WHERE EXTRACT(HOUR FROM start_date) > 4 AND EXTRACT(HOUR FROM start_date) < 10
> GROUP BY hour
> ORDER BY Count DESC
> LIMIT 10;'
   bq query --use_legacy_sql=false '#standardSQL
> SELECT COUNT(*) as Count,
> EXTRACT(HOUR FROM end_date) AS hour
> FROM `bigquery-public-data.san_francisco.bikeshare_trips` AS bikeshare_trips
> WHERE EXTRACT(HOUR FROM end_date) > 15 AND EXTRACT(HOUR FROM end_date) < 22
> GROUP BY hour
> ORDER BY Count DESC
> LIMIT 10;'

```

### Project Questions

 Here, I have identified the main questions in order to make recommendations for the company.

- Question 1: On an average how many bikes are available at the stations ? 

- Question 2: What is the number of trips from different start_station_id ? (Any relation with answer from Question 1 ?)

- Question 3: How many of the bikers above have monthly subcription ?

- Question 4: How does bike rides vary by month ?

- Question 5: Which station id's have highest duration of bike rides and compare them with the trip counts with lower duration of bike rides.

 

### Answers

Following are the answers to the above formulated questions:

##### Question 1: On an average how many bikes are available at the stations ?

  **Answer: Assuming bikes_available_ratio = AVG(bikes_available/(docks_available + bikes_available)), Station_id 91 seems to have lower bikes available where station_id 89 and 62 have higher number of bikes available.**
  

+------------+-------------------------------------+-----------------------+
| station_id |                name                 | bikes_available_ratio |
+------------+-------------------------------------+-----------------------+
|         91 | Cyril Magnin St at Ellis St         |   0.14695737361857836 |
|         89 | S. Market st at Park Ave            |   0.30160449210702905 |
|         62 | 2nd at Folsom                       |   0.31730670454313453 |
|         45 | Commercial at Montgomery            |   0.36907135416388387 |
|         59 | Golden Gate at Polk                 |   0.36958571200012047 |
|         67 | Market at 10th                      |    0.3735728731298074 |
|         47 | Post at Kearney                     |    0.3804489008023759 |
|         75 | Mechanics Plaza (Market at Battery) |     0.388842355551464 |
|         51 | Embarcadero at Folsom               |   0.39817583213447266 |
|         41 | Clay at Battery                     |    0.4010513646334307 |
|         56 | Beale at Market                     |    0.4011616272454333 |
  


  *SQL query:
  
  ```
  #standardSQL
    SELECT bikeshare_status.station_id,bikeshare_stations.name, AVG(bikes_available/(docks_available + bikes_available))  AS   bikes_available_ratio --/(docks_available + bikes_available)
    FROM `bigquery-public-data.san_francisco.bikeshare_status` AS bikeshare_status
    LEFT OUTER JOIN `bigquery-public-data.san_francisco.bikeshare_stations` AS bikeshare_stations
    ON bikeshare_status.station_id = bikeshare_stations.station_id
    WHERE (docks_available + bikes_available) > 0
    GROUP BY bikeshare_status.station_id, bikeshare_stations.name
    ORDER BY bikes_available_ratio ASC  
    LIMIT 100
```

##### Question 2: What is the number of trips from different start_station_id ? (Any relation with answer from Question 1 ?)


**Answer: Below we see that station_id 88,91,89 and so on from the top table and the first few stations are not so busy. However, the lower table shows higher counts of start_station_id's. 
          The relation with the first table is that some station_id's like 62 are busy (maybe because of its location). Also, from Question 1. we noticed this station has high bike availability. So, if we can inform bikers about these station id's with high bike availability, it might increase sales.**
  
+------------+------------------+-----------------------------------------------+-------+
| station_id | start_station_id |                     name                      | Count |
+------------+------------------+-----------------------------------------------+-------+
|         88 |               88 | 5th S. at E. San Salvador St                  |    20 |
|         91 |               91 | Cyril Magnin St at Ellis St                   |    69 |
|         89 |               89 | S. Market st at Park Ave                      |    84 |
|         90 |               90 | 5th St at Folsom St                           |   173 |
|         21 |               21 | Franklin at Maple                             |   241 |
|         24 |               24 | Redwood City Public Library                   |   272 |
|         23 |               23 | San Mateo County Center                       |   373 |


|         56 |               56 | Beale at Market                               | 23082 |
|         62 |               62 | 2nd at Folsom                                 | 23404 |
|         39 |               39 | Powell Street BART                            | 25204 |
|         64 |               64 | 2nd at South Park                             | 26218 |
|         76 |               76 | Market at 4th                                 | 27502 |
|         67 |               67 | Market at 10th                                | 30209 |
|         65 |               65 | Townsend at 7th                               | 34894 |
|         77 |               77 | Market at Sansome                             | 35142 |
|         74 |               74 | Steuart at Market                             | 38531 |
|         55 |               55 | Temporary Transbay Terminal (Howard at Beale) | 39200 |
|         61 |               61 | 2nd at Townsend                               | 39936 |
|         60 |               60 | Embarcadero at Sansome                        | 41137 |
|         50 |               50 | Harry Bridges Plaza (Ferry Building)          | 49062 |
|         69 |               69 | San Francisco Caltrain 2 (330 Townsend)       | 56100 |
|         70 |               70 | San Francisco Caltrain (Townsend at 4th)      | 72683 |


  *SQL query:
  
  ```
    SELECT station_id, start_station_id, name, COUNT(*) AS Count, --subscriber_type,  --end_station_name
    FROM `bigquery-public-data.san_francisco.bikeshare_trips` AS bikeshare_trips
    LEFT OUTER JOIN `bigquery-public-data.san_francisco.bikeshare_stations` AS bikeshare_stations
    ON bikeshare_trips.start_station_id = bikeshare_stations.station_id
    WHERE start_station_id = station_id
    GROUP BY station_id, start_station_id, name --subscriber_type  --, end_station_name
    ORDER BY Count DESC
    LIMIT 100;
```

#### Question 3: How many of the bikers above have monthly subcription ?

 **Answer:We notice below that most bikers who go to the busy station_id's with high count are subscribers. However as you go down the list the number of subscribers decrease and we see increasing number of customers. Notice that there are abunch of bikers who are customers who start from station_id 62. My recommendation would also be to send deals to these customers and attract them as subscribers.**
  
  +------------+------------------+-----------------------------------------------+-------+-----------------+
| station_id | start_station_id |                     name                      | Count | subscriber_type |
+------------+------------------+-----------------------------------------------+-------+-----------------+
|         70 |               70 | San Francisco Caltrain (Townsend at 4th)      | 68384 | Subscriber      |
|         69 |               69 | San Francisco Caltrain 2 (330 Townsend)       | 53694 | Subscriber      |
|         55 |               55 | Temporary Transbay Terminal (Howard at Beale) | 37888 | Subscriber      |
|         50 |               50 | Harry Bridges Plaza (Ferry Building)          | 36621 | Subscriber      |
|         61 |               61 | 2nd at Townsend                               | 35500 | Subscriber      |
|         74 |               74 | Steuart at Market                             | 34062 | Subscriber      |
|         65 |               65 | Townsend at 7th                               | 32788 | Subscriber      |


|          8 |                8 | San Salvador at 1st                           |  1777 | Subscriber      |
|         58 |               58 | San Francisco City Hall                       |  1755 | Customer        |
|         37 |               37 | Cowper at University                          |  1713 | Subscriber      |
|         63 |               63 | Howard at 2nd                                 |  1708 | Customer        |
|          5 |                5 | Adobe on Almaden                              |  1667 | Subscriber      |
|         62 |               62 | 2nd at Folsom                                 |  1582 | Customer        |
|         49 |               49 | Spear at Folsom                               |  1574 | Customer        |
|         16 |               16 | SJSU - San Salvador at 9th                    |  1539 | Subscriber      |
|         14 |               14 | Arena Green / SAP Center                      |  1520 | Subscriber      |
|         35 |               35 | University and Emerson                        |  1499 | Customer        |
|         45 |               45 | Commercial at Montgomery                      |  1491 | Customer        |
|         30 |               30 | Middlefield Light Rail Station                |  1464 | Subscriber      |
|         42 |               42 | Davis at Jackson                              |  1448 | Customer        |
|         59 |               59 | Golden Gate at Polk                           |  1397 | Customer        |
|         55 |               55 | Temporary Transbay Terminal (Howard at Beale) |  1312 | Customer        |
|         82 |               82 | Broadway St at Battery St                     |  1242 | Customer        |
|          3 |                3 | San Jose Civic Center                         |  1176 | Customer        |


 *SQL query:
 
 ```
  bq query --use_legacy_sql=false 'SELECT station_id, start_station_id, name, COUNT(*) AS Count, subscriber_type,  --end_station_name
> FROM `bigquery-public-data.san_francisco.bikeshare_trips` AS bikeshare_trips
> LEFT OUTER JOIN `bigquery-public-data.san_francisco.bikeshare_stations` AS bikeshare_stations
> ON bikeshare_trips.start_station_id = bikeshare_stations.station_id
> WHERE start_station_id = station_id
> GROUP BY station_id, start_station_id, name, subscriber_type  --, end_station_name
> ORDER BY Count DESC
> LIMIT 100;'
  ```
  
##### Question 4: How does bike rides vary by month?

  **Answer: Most popular months are August, October, july while the months when we see least bikers are December, January and February. It can be because daily commuters to office via train-stations, ferry, etc. are fewer due to vacations.In order to increase sales, my recommendation would be to give some sort of discount in those months, so people bike more. For example, attract bikers to go on long duration leisure trips in those months. Although we are showing only one result we have experimented all the years individually.** 
  
  +-------+-------+
| Count | month |
+-------+-------+
| 95576 |     8 |
| 94378 |    10 |
| 91672 |     6 |
| 89539 |     7 |
| 87321 |     9 |
| 86364 |     5 |
| 84196 |     4 |
| 81777 |     3 |
| 73091 |    11 |
| 71788 |     1 |
| 69985 |     2 |
| 57961 |    12 |
+-------+-------+
  
 *SQL query:
 
 ```
bq query --use_legacy_sql=false '#standardSQL
> SELECT COUNT(trip_id) as Count,
> EXTRACT (MONTH FROM start_date) AS month,
> --EXTRACT (YEAR FROM start_date) AS year,
> FROM `bigquery-public-data.san_francisco.bikeshare_trips` AS bikeshare_trips
> --WHERE EXTRACT(YEAR FROM start_date) IN (2015) --(2013, 2014, 2015, 2016)
> GROUP BY month --, year
> ORDER BY Count DESC
> --LIMIT 10;'
```  

##### Question 5: Which station id's have highest duration of bike rides and compare them with the trip counts with lower duration of bike rides?

 **Answer: When we sort by duration, we see that the highest duration bike rides occured from the station id's 35, 36, 24 and so on, while the lowest duration bike rides occured from station id's. Another observation is when we order them by trip_count, we noticed that the highest trip_count station id's (for example 70, 69, 62 and so on) corrspond with lower duration times. This also validate that the same most popular station from Question 1 are mostly short duration ones. 
  Later in Part 3 (in Project_1.ipynb), I have shown a plot of duration vs trip_count, which shows that short duration trips have higher counts on trips and the counts decline as duration increases. Here, I will recommend that if there are any higher duration trips closer to the popular stations, then the nearby bikers can ride bikes from that station and use them for shorted commute. I have also provided a map to locate the station id's in my Project_1.ipynb.**

+------------------+------------+--------------------+
| start_station_id | trip_count | duration_sec_mean  |
+------------------+------------+--------------------+
|               35 |       2002 |  6455.075924075927 |
|               36 |       1418 | 4079.5698166431575 |
|               24 |        272 | 3846.2426470588243 |
|               38 |       1026 | 3784.5575048732953 |
|                3 |       2137 |  3726.586803930739 |
|               33 |       1514 | 3686.3626155878483 |
|               26 |        463 | 2873.7796976241902 |
|               34 |       3281 | 2711.1737275220926 |
|               91 |         69 | 2595.6956521739125 |
|               23 |        373 |  2347.705093833781 |


|               51 |      21874 |   775.966352747554 |
|               49 |      17062 |   773.159828859456 |
|               45 |      16857 |  752.0938482529529 |
|               61 |      39936 |  749.0779747596149 |
|               63 |      20746 |  698.7669912272286 |
|               69 |      56100 |  681.0883957219247 |
|               65 |      34894 |  661.3232647446572 |
|               64 |      26218 |  650.3902662293045 |
|               55 |      39200 |  637.6137499999929 |
|               62 |      23404 |  558.6935566569781 |
|               90 |        173 |  554.8959537572254 |
+------------------+------------+--------------------+

 *SQL query:
 
 ```
 SELECT start_station_id, count(*) AS trip_count, AVG(duration_sec) AS duration_sec_mean
 FROM `bigquery-public-data.san_francisco.bikeshare_trips`
 GROUP BY start_station_id
 ORDER BY duration_sec_mean DESC 
 LIMIT 1000

```

## Part 3 - Employ notebooks to synthesize query project results


 Here, I have created a Jupyter Notebook named Project_1.ipynb in the assignment branch of my repo.
I ran the queries in the Notebook using the following commands and magic commands:
```
! bq query --use_legacy_sql=FALSE '<your-query-here>'
```
```sql
%%bigquery my_panda_data_frame
```

All my recommendations are at the end of my Project_1.ipynb. Also, I have added a map of San Francisco city using GeoPandas where I have marked the station_id's that are most popular.

A summary of my __recommendations__ are:
1. Some popular stations have higher availability of bikes and many of the bikers who ride to these popular stations are customers as opposed to subscribers. I would recommend that Lyft Bay Wheels offer some sort of deals like corporate membership or a free 30 min bike ride to whoever uses stations where bike availability if high (example station_id 62).
  
2. The winter months have less bike rides. So Lyft Bay Wheels can offer special seasonal discounts in those months. 

3. The regions with long duration trips probably need more bike stations to make the durations shorter. This would increase the accessibility in those regions and drive up revenue.