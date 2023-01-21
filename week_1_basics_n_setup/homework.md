## Week 1 Homework

IMAM MUHAJIR

In this homework we'll prepare the environment 
and practice with Docker and SQL


## Question 1. Knowing docker tags

Run the command to get information on Docker 

```docker --help```

Now run the command to get help on the "docker build" command

Which tag has the following text? - *Write the image ID to the file* 

`docker build --help` 

- `--iidfile string` ()


## Question 2. Understanding docker first run 

Run docker with the python:3.9 image in an interactive mode and the entrypoint of bash.
Now check the python modules that are installed ( use pip list). 
How many python packages/modules are installed?

`docker run -it --entrypoint=bash python:3.9` 

- 3 packages/modules

# Prepare Postgres

Run Postgres and load data as shown in the videos
We'll use the green taxi trips from January 2019:

```wget https://github.com/DataTalksClub/nyc-tlc-data/releases/download/green/green_tripdata_2019-01.csv.gz```

You will also need the dataset with zones:

```wget https://s3.amazonaws.com/nyc-tlc/misc/taxi+_zone_lookup.csv```

Download this data and put it into Postgres (with jupyter notebooks or with a pipeline)


## Question 3. Count records 

How many taxi trips were totally made on January 15?

Tip: started and finished on 2019-01-15. 

Remember that `lpep_pickup_datetime` and `lpep_dropoff_datetime` columns are in the format timestamp (date and hour+min+sec) and not in date.


```
select count(a.*)
from
    (select date(lpep_pickup_datetime) as lpep_pickup, date(lpep_dropoff_datetime) as lpep_dropoff, *
    from green_taxi_trips) as a
where lpep_pickup = '2019-01-15' and lpep_dropoff = '2019-01-15';
```

- 20530

## Question 4. Largest trip for each day

Which was the day with the largest trip distance
Use the pick up time for your calculations.

```
select lpep_pickup, max(trip_distance)
from
   (select date(lpep_pickup_datetime) as lpep_pickup, date(lpep_dropoff_datetime) as lpep_dropoff, *
    from green_taxi_trips) as a
group by 1
order by 2 desc 
limit 1

```

- 2019-01-15

## Question 5. The number of passengers

In 2019-01-01 how many trips had 2 and 3 passengers?

for 2 passengers


```
select count(*)
    from
    (select date(lpep_pickup_datetime) as lpep_pickup, date(lpep_dropoff_datetime) as lpep_dropoff, *
    from green_taxi_trips) as a
    where lpep_pickup = '2019-01-01' and (passenger_count = 2 );
```

```
select count(*)
    from
    (select date(lpep_pickup_datetime) as lpep_pickup, date(lpep_dropoff_datetime) as lpep_dropoff, *
    from green_taxi_trips) as a
    where lpep_pickup = '2019-01-01' and (passenger_count = 3 );
```

- 2: 1282 ; 3: 254


## Question 6. Largest tip

For the passengers picked up in the Astoria Zone which was the drop off zone that had the largest tip?
We want the name of the zone, not the id.

Note: it's not a typo, it's `tip` , not `trip`

```
select DOzone, max(tip_amount) from
    (select  t2."Zone" as PUzone,a.DOzone, a.tip_amount
    from (select t."Zone" as DOzone,gt."PULocationID", gt.tip_amount  from green_taxi_trips gt
            left join taxi_zone_lookup t
            on gt."DOLocationID" = t."LocationID") a
    left join taxi_zone_lookup t2
    on a."PULocationID" = t2."LocationID") m
where PUzone = 'Astoria'
group by 1
order by 2 desc
limit 1
```

- Long Island City/Queens Plaza


