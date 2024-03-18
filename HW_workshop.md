###1.
```CREATE MATERIALIZED VIEW latest_dropoff_time AS
    WITH t AS (
        SELECT MAX(tpep_dropoff_datetime) AS latest_dropoff_time
        FROM trip_data
    )
    SELECT taxi_zone.Zone as taxi_zone, latest_dropoff_time
    FROM t,
            trip_data
    JOIN taxi_zone
        ON trip_data.DOLocationID = taxi_zone.location_id
    WHERE trip_data.tpep_dropoff_datetime = t.latest_dropoff_time;
    
select * from latest_dropoff_time;
```

###2.
```
with dane as(select avg(tpep_dropoff_datetime-tpep_pickup_datetime) as avgtrip_time,pulocationid|| ' '||dolocationid from trip_data group by 2) select * from dane order by 1 desc limit 1;
with dane as (select pulocationid||' '||dolocationid as cos from trip_data) select count(*) from dane where cos='262 223'; 
```

###3.
```
psql -U root -d dev -h localhost -p 4566  -c 'select max(tpep_pickup_datetime) from trip_data limit 1' 
with dane as (
        select t1.zone||' - '||t2.zone as trip
        from trip_data t
        join taxi_zone t1 on t1.location_id = t.pulocationid
        join taxi_zone t2 on t2.location_id = t.pulocationid
        where tpep_pickup_datetime >= '2022-01-02 17:53:33' and tpep_pickup_datetime <= '2022-01-03 10:53:33'
)select  trip,count(trip) from dane group by trip order by 2 desc;

-- 2022-01-03 10:53:33
-- 2022-01-02 10:53:33
```
