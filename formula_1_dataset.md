**SQL Solutions for F1 Dataset Challenge**

***The Schema:***

<img width="800" alt="image" src="https://github.com/afnfyz/sql_projects/assets/124072294/85842c7b-0cf5-4a89-b601-022890f59b7c">


<details>
<summary><strong>Task 1: List of Drivers</strong></summary>

```sql
SELECT driverid, forename, surname
FROM drivers
ORDER BY DRIVERID;
```

</details>

<details>
<summary><strong>Task 2: Drivers and Their Fastest Lap Speed</strong></summary>

```sql
SELECT d.driverid,
forename as First_Name,
surname as Last_Name,
fastestlapspeed as Fastest_Lap_Speed 
FROM drivers as d
INNER JOIN results as r ON d.driverid = r.driverid
WHERE fastestlapspeed IS NOT NULL;
```

</details>

<details>
<summary><strong>Task 3: Drivers and Their Fastest Lap Speed Ever</strong></summary>

```sql
SELECT 
d.DRIVERID,
d.FORENAME,
d.SURNAME,
MAX(FASTESTLAPSPEED) AS Fastest_Lap_Speed
FROM drivers d
JOIN results r ON
d.driverid = r.driverid

GROUP BY
d.DRIVERID,
d.FORENAME,
d.SURNAME

HAVING 
    Fastest_Lap_Speed IS NOT NULL
```

</details>

<details>
<summary><strong>Task 4: Drivers and Their Fastest Lap Speed in Monaco in 2021</strong></summary>

```sql
SELECT d.driverid,
d.forename,
d.surname,
MAX(FASTESTLAPSPEED) AS Fastest_Lap_Speed_Monaco_2021
FROM DRIVERS d
JOIN RESULTS r ON
d.driverid = r.driverid
JOIN RACES ra ON
r.raceid = ra.raceid
WHERE ra.name LIKE '%Monaco%'
AND
r.fastestlapspeed IS NOT null
AND
ra.year = 2021
GROUP BY 
d.driverid,
d.forename,
d.surname;
```

</details>

<details>
<summary><strong>Task 5: Drivers and Their Fastest Lap Speed in Monaco in 2021, All-Time Fastest Lap Speed</strong></summary>

```sql
SELECT d.driverid,
d.forename,
d.surname,
MAX(FASTESTLAPSPEED) AS Fastest_Lap_Speed_Monaco_2021,
(SELECT MAX(fastestlapspeed) FROM RESULTS) as All_Time_Fastest_Lap_Speed
FROM DRIVERS d
JOIN RESULTS r ON
d.driverid = r.driverid
JOIN RACES ra ON
r.raceid = ra.raceid
WHERE ra.name LIKE '%Monaco%'
AND
r.fastestlapspeed IS NOT null
AND
ra.year = 2021
GROUP BY 
d.driverid,
d.forename,
d.surname;
```

</details>

<details>
<summary><strong>Task 6: Drivers, Their Fastest Lap Speed in Monaco in 2021, and Monaco Wins</strong></summary>

```sql
WITH WINS as 
    (SELECT 
        driverid,
        SUM(wins) as total_wins
        
        FROM driver_standings as ds
        
        JOIN races ON 
        ds.raceid = races.raceid
        
        WHERE races.name ILIKE '%Monaco%'
        
        GROUP BY driverid
        )
SELECT d.driverid,
d.forename,
d.surname,
w.total_wins,
MAX(FASTESTLAPSPEED) AS Fastest_Lap_Speed_Monaco_2021
FROM DRIVERS d
JOIN RESULTS r ON
d.driverid = r.driverid
JOIN RACES ra ON
r.raceid = ra.raceid
    JOIN WINS w
ON d.driverid = w.driverid
WHERE
r.fastestlapspeed IS NOT null
AND
ra.year = 2021
AND
ra.name ILIKE '%Monaco%'
GROUP BY 
d.driverid,
d.forename,
d.surname,
w.total_wins;
```

</details>

<details>
<summary><strong>Task 7: Drivers, Their Fastest Lap Speed in Monaco in 2021 and 2019</strong></summary>

```sql
WITH MONACO_2021 AS (SELECT d.driverid,
                    d.forename,
                    d.surname,
                    w.total_wins,
                    MAX(FASTESTLAPSPEED) AS Fastest_Lap_Speed_Monaco_2021
                    FROM DRIVERS d
                    JOIN RESULTS r ON
                    d.driverid = r.driverid
                    JOIN RACES ra ON
                    r.raceid = ra.raceid
                        JOIN (SELECT driverid,
                            SUM(wins) as total_wins
                            FROM driver_standings as ds
                            JOIN races
                            ON ds.raceid = races.raceid
                            WHERE races.name ILIKE '%Monaco%'
                            GROUP BY driverid) as w
                    ON d.driverid = w.driverid
                    WHERE
                    r.fastestlapspeed IS NOT null
                    AND
                    ra.year = 2021
                    AND
                    ra.name ILIKE '%Monaco%'
                    GROUP BY 
                    d.driverid,
                    d.forename,
                    d.surname,
                    w.total_wins),
                    
    MONACO_2019 AS (SELECT d.driverid,
                    d.forename,
                    d.surname,
                    w.total_wins,
                    MAX(FASTESTLAPSPEED) AS Fastest_Lap_Speed_Monaco_2019
                    FROM DRIVERS d
                    JOIN RESULTS r ON
                    d.driverid = r.driver

id
                    JOIN RACES ra ON
                    r.raceid = ra.raceid
                        JOIN (SELECT driverid,
                            SUM(wins) as total_wins
                            FROM driver_standings as ds
                            JOIN races
                            ON ds.raceid = races.raceid
                            WHERE races.name ILIKE '%Monaco%'
                            GROUP BY driverid) as w
                    ON d.driverid = w.driverid
                    WHERE
                    r.fastestlapspeed IS NOT null
                    AND
                    ra.year = 2019
                    AND
                    ra.name ILIKE '%Monaco%'
                    GROUP BY 
                    d.driverid,
                    d.forename,
                    d.surname,
                    w.total_wins)

SELECT
                    m21.driverid,
                    m21.forename,
                    m21.surname,
                    m21.Fastest_Lap_Speed_Monaco_2021,
                    m19.Fastest_Lap_Speed_Monaco_2019
FROM MONACO_2021 m21
JOIN MONACO_2019 m19
ON m21.driverid = m19.driverid;
```

</details>