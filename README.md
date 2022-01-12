# Udacity Data Engineering Nanodegree - Project 1

## Overview

The purpose of this project is to demonstrate the creation of a relational database schema and ETL pipeline which will be used to analyze user activity for a fictional music streaming app 'Sparkify'. The analytical goal is to understand what songs users are listening to. Song metadata and user activity logs in JSON format are transformed and loaded into a Postgres database with a schema design which is appropriate for the analytical goal.

## Dependencies

* Python 3
  * psycopg2
  * pandas
* PostgreSQL

## Files

The file structure is as follows:
* `data/` - a folder containing song metadata and user activity logs in JSON format.
* `create_tables.py` - creates the database and required tables according to the schema below.
* `sql_queries.py` - defines strings containing all the required SQL queries.
* `etl.py` - performs the ETL process.
* `README.md` - this readme file.

## Datasets

Song metadata files should be placed in `data/song_data/` and user activity logs should be placed in `data/log_data/`.

An example of the song dataset format is:
```json
{
    "num_songs": 1,
    "artist_id": "ARGCY1Y1187B9A4FA5",
    "artist_latitude": 36.16778,
    "artist_longitude": -86.77836,
    "artist_location": "Nashville, TN.",
    "artist_name": "Gloriana",
    "song_id": "SOQOTLQ12AB01868D0",
    "title": "Clementina Santaf\u00e8",
    "duration": 153.33832,
    "year": 0
}
```

An example of the activity log dataset format is:
```json
{
    "artist":"Joshua Radin",
    "auth":"Logged In",
    "firstName":"Aleena",
    "gender":"F","itemInSession":19,
    "lastName":"Kirby","length":161.69751,
    "level":"paid",
    "location":"Waterloo-Cedar Falls, IA",
    "method":"PUT",
    "page":"NextSong",
    "registration":1541022995796.0,
    "sessionId":237,
    "song":"We Are Okay",
    "status":200,
    "ts":1541385750796,
    "userAgent":"Mozilla\/5.0 (Macintosh; Intel Mac OS X 10.9; rv:31.0) Gecko\/20100101 Firefox\/31.0","userId":"44"
}
```

The datasets used for testing and development of this software are:
* A subset of real song metadata from the [Million Song Dataset](http://millionsongdataset.com/).
* Log files generated from the metadata using this [Event Simulator](https://github.com/Interana/eventsim).

## Database Schema

The relational database has a star schema, optimized for OLAP workloads. It consists of a single fact table **songplays** which references four dimension tables **users**, **songs**, **artists**, and **time**.

### Fact Table

1\. The **songplays** table has attributes:

|Column|Data Type|
|---:|---|
|* songplay_id|integer|
|start_time|timestamp|
|user_id|integer|
|level|varchar|
|song_id|varchar|
|artist_id|varchar|
|session_id|integer|
|location|varchar|
|user_agent|varchar|

### Dimension Tables

1\. The **users** table has attributes:

 |Column|Data Type|
 |---:|---|
 |* user_id|integer|
 |first_name|varchar|
 |last_name|varchar|
 |gender|char(1)|
 |level|varchar|

2\. The **songs** table has attributes:

 |Column|Data Type|
 |---:|---|
 |* song_id|varchar|
 |title|varchar|
 |artist_id|varchar|
 |year|int|
 |duration|numeric|

3\. The **artists** table has attributes:

 |Column|Data Type|
 |---:|---|
 |* artist_id|varchar|
 |name|varchar|
 |location|varchar|
 |latitude|numeric|
 |longitude|numeric|

4\. The **time** table has attributes:

 |Column|Data Type|
 |---:|---|
 |* start_time|timestamp|
 |hour|integer|
 |day|integer|
 |week|integer|
 |month|integer|
 |year|integer|
 |weekday|integer|
  
## ETL Pipeline

Song and artist information is loaded from the song metadata JSON files into a pandas dataframe. The data is cleaned by replacing invalid values with null values, and is then used to populate the **songs** and **artists** tables according to the schema above.

User activity events are loaded from the events log JSON files into a pandas dataframe which is filtered to consist only of song play events ('page' = 'NextSong'). This dataframe is then used to populate the **songplay**, **users**, and **time** tables according to the schema above.

## Usage

Usage steps:
1. Copy the dataset files (JSON format) into the `data/` directory.
1. Ensure that there is a PostgreSQL server instance running.
1. Modify the database connection credentials `HOST`, `USERNAME`, `PASSWORD` in `create_tables.py` as appropriate for your PostgreSQL server instance.
1. From the projects root directory, execute the following from the command line:
```
python3 create_tables.py
python3 etl.py
```

## Querying

Once the ETL pipeline has been run, you can query the PostgreSQL database to answer analytical questions.

For example, suppose you want to find out which three days of the week have the highest total play count, a sample query would be:

```sql
SELECT
    to_char(to_date(weekday||'-2017','IDDD-IYYY'), 'Day') AS day,
    count(*) AS play_count
FROM
    songplays JOIN time ON songplays.start_time = time.start_time
GROUP BY 1
ORDER BY 2 DESC
LIMIT 3
```

Result:

```sql
    day    | play_count 
-----------+------------
 Sunday    |       1370
 Tuesday   |       1364
 Thursday  |       1295
 ```
## Copyright & License

Copyright (c) 2022 Gary Jones

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

## Credits

Project template provided by [Udacity](https://www.udacity.com).
