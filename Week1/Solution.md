## Week 1 Homework

In this homework we'll prepare the environment 
and practice with terraform and SQL


## Question 3. Count records 

How many taxi trips were there on January 15?

select COUNT(*)
from yellow_taxi_data
where tpep_pickup_datetime::date = date '2021-01-15';

[53024]

Consider only trips that started on January 15.


## Question 4. Largest tip for each day

Find the largest tip for each day. 

SELECT MAX(tip_amount) as max_tip_per_day, tpep_pickup_datetime::date
from yellow_taxi_data 
WHERE tpep_pickup_datetime::date >= date '2021-01-01' and tpep_pickup_datetime::date <= date '2021-01-31' 
GROUP BY tpep_pickup_datetime::date

On which day it was the largest tip in January?

with tip_per_date as (
	SELECT MAX(tip_amount) as max_tip_per_day, tpep_pickup_datetime::date
	FROM yellow_taxi_data 
	WHERE tpep_pickup_datetime::date >= date '2021-01-01' AND tpep_pickup_datetime::date <= date '2021-01-31' 
	GROUP BY tpep_pickup_datetime::date
	)
SELECT MAX(max_tip_per_day) FROM tip_per_date

[Another-possible-solution]

SELECT MAX(tip_amount) as max_tip_per_day, date_trunc('day', tpep_pickup_datetime) as pickup_day
FROM yellow_taxi_data 
WHERE tpep_pickup_datetime::date >= date '2021-01-01' AND tpep_pickup_datetime::date <= date '2021-01-31' 
GROUP BY pickup_day
ORDER BY max_tip_per_day desc
LIMIT 1

[1140.44]
[20-01-2021]

Use the pick up time for your calculations.

(note: it's not a typo, it's "tip", not "trip")


## Question 5. Most popular destination

What was the most popular destination for passengers picked up 
in central park on January 14?

Use the pick up time for your calculations.

Enter the zone name (not id). If the zone name is unknown (missing), write "Unknown"

SELECT 
	COALESCE(puzones."Zone",'Unknown') as zone,
	COUNT(*)
FROM yellow_taxi_data as yt 
	INNER JOIN taxi_zone_lookup as puzones
	ON yt."PULocationID" = puzones."LocationID" 
	LEFT JOIN taxi_zone_lookup as dozones
	ON yt."DOLocationID" = dozones."LocationID"
	WHERE puzones."Zone" ilike '%Central Park%'
	and yt.tpep_pickup_datetime::date = date '2021-01-14'
GROUP BY 1
ORDER BY COUNT(*) desc
limit 1

[1087]


## Question 6. Most expensive locations

What's the pickup-dropoff pair with the largest 
average price for a ride (calculated based on `total_amount`)?

Enter two zone names separated by a slash

For example:

"Jamaica Bay / Clinton East"

If any of the zone names are unknown (missing), write "Unknown". For example, "Unknown / Clinton East". 

SELECT 
	CONCAT(COALESCE(puzones."Zone",'Unknown'),'/',COALESCE(dozones."Zone",'Unknown')) as pickup_dropoff,
	avg(yt.total_amount) as avg_price
FROM yellow_taxi_data as yt 
	LEFT JOIN taxi_zone_lookup as puzones
	ON yt."PULocationID" = puzones."LocationID" 
	LEFT JOIN taxi_zone_lookup as dozones
	ON yt."DOLocationID" = dozones."LocationID"
GROUP BY pickup_dropoff
ORDER BY avg_price desc
limit 1

[Alphabet-City/Unknown-2292.4]

## Submitting the solutions

* Form for submitting: https://forms.gle/yGQrkgRdVbiFs8Vd7
* You can submit your homework multiple times. In this case, only the last submission will be used. 

Deadline: 26 January (Wednesday), 22:00 CET


## Solution

Here is the solution to questions 3-6: [video](https://www.youtube.com/watch?v=HxHqH2ARfxM&list=PL3MmuxUbc_hJed7dXYoJw8DoCuVHhGEQb)

