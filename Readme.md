
![MySQL](https://img.shields.io/badge/MySQL-005C84?logo=mysql&logoColor=white)
![Matplotlib](https://img.shields.io/badge/Matplotlib-11557C?logo=python&logoColor=white)
![Data Visualization](https://img.shields.io/badge/Data%20Visualization-grey?style=flat&logo=databricks&logoColor=white)
![Status: Completed](https://img.shields.io/badge/Status-Completed-brightgreen)


# Motor Vehicle Theft Analysis — SQL + Python Project

Exploratory data analysis (EDA) project investigating **when**, **what**, and **where** vehicles are stolen, using SQL and Python visualizations. This project answers real-world analytical questions that mirror the workflow of a **Data analyst**.

---

<details>
<summary><strong> Data Dictionary (Click to Expand)</strong></summary>

<br>

## 🔹 **stolen_vehicles Table**

| Field          | Description                            |
| -------------- | -------------------------------------- |
| `vehicle_id`   | Unique ID of a stolen vehicle          |
| `vehicle_type` | Type of vehicle                        |
| `make_id`      | Matches `make_id` in **make_details**  |
| `model_year`   | Model year of the vehicle              |
| `vehicle_desc` | Description of the vehicle             |
| `color`        | Color of the vehicle                   |
| `date_stolen`  | Date the vehicle was stolen (MM/DD/YY) |
| `location_id`  | Matches `location_id` in **locations** |

---

## 🔹 **make_details Table**

| Field       | Description                          |
| ----------- | ------------------------------------ |
| `make_id`   | Unique ID of the make                |
| `make_name` | Name of the make                     |
| `make_type` | Category of make (Standard / Luxury) |

---

## 🔹 **locations Table**

| Field         | Description                         |
| ------------- | ----------------------------------- |
| `location_id` | Unique ID of the region             |
| `region`      | Name of the region                  |
| `country`     | Country where the region is located |
| `population`  | Population of the region            |
| `density`     | Population density (people per km²) |

---

</details>


## Project Objectives

### **Objective 1 — Identify *when* vehicles are stolen**

Analyzed date-related fields to understand theft patterns.

1. Number of vehicles stolen each year

```sql
SELECT 
COUNT(vehicle_id) AS 'Number of Vehicles Stolen',
YEAR(date_stolen) AS 'Year'
FROM stolen_vehicles
GROUP BY Year
```
Query Output: 
| Number of Vehicles | Year |
|--------------------|------|
| 1668               | 2021 |
| 2885               | 2022 |

> Insights: Vehicle thefts increased sharply from 1,668 in 2021 to 2,885 in 2022, indicating a 73% rise in reported incidents, suggesting growing theft activity or improved reporting.

2. Number of vehicles stolen each month

```sql
SELECT
  COUNT(vehicle_id) AS Number_of_Vehicles_Stolen,
  MONTH(date_stolen)     AS Month,
  MONTHNAME(date_stolen) AS Month_Name
FROM stolen_vehicles
GROUP BY MONTH(date_stolen), MONTHNAME(date_stolen)
ORDER BY Number_of_Vehicles_Stolen;
```
Query Output: 
| Number of Vehicles | Month | Month Name |
|--------------------|--------|------------|
| 329                | 4      | April      |
| 644                | 12     | December   |
| 763                | 2      | February   |
| 740                | 1      | January    |
| 1053               | 3      | March      |
| 560                | 11     | November   |
| 464                | 10     | October    |

> Vehicle thefts peak in March (1,053 thefts) and dip in April (329 thefts). The first quarter shows the highest theft activity, indicating a seasonal pattern.

3. Number of vehicles stolen each day of the week

```sql
SELECT 
COUNT(vehicle_id) AS Number_of_Vehicles_Stolen,
DAYOFWEEK(date_stolen) AS Day
FROM stolen_vehicles
GROUP BY DAYOFWEEK(date_stolen), DAYNAME(date_stolen)
ORDER BY  DAYOFWEEK(date_stolen) ;
```
4. Replaced numeric weekday with full names

```sql
SELECT
    COUNT(vehicle_id) AS Number_of_Vehicles_Stolen,
    DAYOFWEEK(date_stolen) AS Day_Number,
    DAYNAME(date_stolen) AS Day_Name
FROM stolen_vehicles
GROUP BY Day_Number, Day_Name
ORDER BY Day_Number;
```

```sql
SELECT 
    COUNT(vehicle_id) AS Number_of_Vehicles_Stolen,
    DAYOFWEEK(date_stolen) AS Day_Number,
    CASE DAYOFWEEK(date_stolen)
        WHEN 1 THEN 'Sunday'
        WHEN 2 THEN 'Monday'
        WHEN 3 THEN 'Tuesday'
        WHEN 4 THEN 'Wednesday'
        WHEN 5 THEN 'Thursday'
        WHEN 6 THEN 'Friday'
        WHEN 7 THEN 'Saturday'
    END AS Day_Name
FROM stolen_vehicles
GROUP BY Day_Number, Day_Name
ORDER BY Day_Number;
```

Query Output: Number of Vehicles Stolen Each Day of the Week
| Number of Vehicles | Day Number | Day Name   |
|--------------------|------------|------------|
| 595                | 1          | Sunday     |
| 767                | 2          | Monday     |
| 711                | 3          | Tuesday    |
| 629                | 4          | Wednesday  |
| 619                | 5          | Thursday   |
| 655                | 6          | Friday     |
| 577                | 7          | Saturday   |

> Thefts are highest on Monday (767) and lowest on Saturday (577). Weekdays show more theft activity than weekends, likely linked to increased vehicle movement.

5. Bar chart of thefts by day of the week
![Thefts by Day of Week](https://github.com/Jeseenacodes/Motor_Vehicle_Thefts-SQL_Python/blob/main/Charts/my_plot.png)

---
### **Objective 2 — Identify *which* vehicles are likely to be stolen**

Explored vehicle-related factors (type, age, luxury, color).

1. Most/least stolen vehicle types
```sql
SELECT 
    COUNT(vehicle_id) AS Number_of_Vehicles_Stolen,
    vehicle_type
    FROM stolen_vehicles
GROUP BY vehicle_type
ORDER BY 1 DESC
LIMIT 1;

SELECT 
    COUNT(vehicle_id) AS Number_of_Vehicles_Stolen,
    vehicle_type
    FROM stolen_vehicles
GROUP BY vehicle_type
ORDER BY 1 
LIMIT 1;
---
WITH theft_counts AS (
    SELECT 
        vehicle_type,
        COUNT(vehicle_id) AS total_stolen
    FROM stolen_vehicles
    GROUP BY vehicle_type
),
ranked AS (
    SELECT 
        vehicle_type,
        total_stolen,
        RANK() OVER (ORDER BY total_stolen DESC) AS rank_highest,
        RANK() OVER (ORDER BY total_stolen ASC) AS rank_lowest
    FROM theft_counts
)
SELECT 
    vehicle_type,
    total_stolen,
    CASE 
        WHEN rank_highest = 1 THEN 'Most Stolen'
        WHEN rank_lowest = 1 THEN 'Least Stolen'
    END AS category
FROM ranked
WHERE rank_highest = 1 OR rank_lowest = 1
ORDER BY category DESC, total_stolen DESC;
```
Query Output
| Vehicle Type            | Total Stolen | Category       |
|-------------------------|--------------|----------------|
| Stationwagon            | 945          | Most Stolen    |
| Special Purpose Vehicle | 1            | Least Stolen   |
| Articulated Truck       | 1            | Least Stolen   |

> Stationwagons are the most frequently stolen vehicles, with 945 thefts, while Special Purpose Vehicles and Articulated Trucks are the least targeted, with only one theft each.

2. Average age of stolen vehicles by vehicle type

```sql
SELECT 
    ROUND(AVG(YEAR(date_stolen) - model_year),2) AS Avg_Car_age,
    vehicle_type
    FROM stolen_vehicles
    WHERE vehicle_type IS NOT NULL
GROUP BY vehicle_type
ORDER BY Avg_Car_age ;
```
Query Output: 
| Avg Vehicle Age (Years) | Vehicle Type              |
|--------------------------|---------------------------|
| 4.00                     | Mobile Machine            |
| 7.00                     | Tractor                   |
| 7.48                     | Moped                     |
| 9.55                     | Roadbike                  |
| 10.50                    | Cab and Chassis Only      |
| 10.60                    | All Terrain Vehicle       |
| 11.41                    | Trailer                   |
| 11.46                    | Trailer - Heavy           |
| 13.28                    | Boat Trailer              |
| 14.67                    | Light Bus                 |
| 15.00                    | Articulated Truck         |
| 16.27                    | Hatchback                 |
| 17.82                    | Utility                   |
| 19.05                    | Saloon                    |
| 19.21                    | Stationwagon              |
| 19.44                    | Light Van                 |
| 20.50                    | Trail Bike                |
| 22.00                    | Sports Car                |
| 22.57                    | Heavy Van                 |
| 22.67                    | Convertible               |
| 23.19                    | Other Truck               |
| 27.82                    | Caravan                   |
| 27.82                    | Flat Deck Truck           |
| 34.67                    | Mobile Home - Light       |
| 64.00                    | Special Purpose Vehicle   |

> Older vehicles are stolen far more often than newer ones, with Special Purpose Vehicles (64 years) and Mobile Home – Light models (34.67 years) being the most targeted. In contrast, newer vehicles like Mobile Machines (4 years) are stolen much less frequently. This pattern suggests that thieves prefer older vehicles, likely because they have weaker security features and are easier to steal.

3. Luxury vs Standard % by vehicle type

```sql
SELECT *,
CASE 
	WHEN make_type = 'Luxury' THEN 1 ELSE 0 
    END AS make_type_cat
FROM make_details
ORDER BY make_type_cat DESC;


SELECT
    s.vehicle_type,
    ROUND(100 * SUM(CASE WHEN m.make_type = 'Luxury' THEN 1 ELSE 0 END) / COUNT(*), 2) AS Luxury_Percent,
    ROUND(100 * SUM(CASE WHEN m.make_type = 'Standard' THEN 1 ELSE 0 END) / COUNT(*), 2) AS Standard_Percent
FROM stolen_vehicles AS s
JOIN make_details AS m ON s.make_id = m.make_id
GROUP BY s.vehicle_type;

-- Pivot style
SELECT
    s.vehicle_type,
    m.make_type,
    ROUND(100 * COUNT(*) / SUM(COUNT(*)) OVER (PARTITION BY s.vehicle_type), 2) AS Percent
FROM stolen_vehicles AS s
JOIN make_details AS m ON s.make_id = m.make_id
GROUP BY s.vehicle_type, m.make_type
ORDER BY s.vehicle_type, m.make_type;

```
Query Output: 
| Vehicle Type            | % Luxury | % Standard |
|-------------------------|----------|------------|
| Trailer                 | 0.00     | 100.00     |
| Boat Trailer            | 0.00     | 100.00     |
| Roadbike                | 1.35     | 98.65      |
| Moped                   | 0.00     | 100.00     |
| Trailer - Heavy         | 0.00     | 100.00     |
| Caravan                 | 0.00     | 100.00     |
| Hatchback               | 3.26     | 96.74      |
| Saloon                  | 12.93    | 87.07      |
| Stationwagon            | 3.70     | 96.30      |
| Tractor                 | 0.00     | 100.00     |
| Trail Bike              | 0.00     | 100.00     |
| Light Van               | 1.30     | 98.70      |
| All Terrain Vehicle     | 0.00     | 100.00     |
| Utility                 | 0.21     | 99.79      |
| Other Truck             | 0.00     | 100.00     |
| Sports Car              | 22.50    | 77.50      |
| Flat Deck Truck         | 0.00     | 100.00     |
| Light Bus               | 0.00     | 100.00     |
| Mobile Home - Light     | 0.00     | 100.00     |
| Convertible             | 50.00    | 50.00      |
| Heavy Van               | 14.29    | 85.71      |
| Special Purpose Vehicle | 0.00     | 100.00     |
| Articulated Truck       | 0.00     | 100.00     |
| Cab and Chassis Only    | 0.00     | 100.00     |
| Mobile Machine          | 0.00     | 100.00     |
| *Unknown*               | 9.09     | 90.91      |

> For nearly all vehicle types, standard models account for 95–100% of thefts. The only notable exception is Convertibles, which show a 50% luxury theft rate.
Overall, thieves target standard, non-premium vehicles, likely due to availability and resale parts value.

4. Pivot table: top 10 vehicle types × top 7 colors (+ Other)

```sql
WITH top_10types AS (
    SELECT vehicle_type
    FROM stolen_vehicles
    GROUP BY vehicle_type
    ORDER BY COUNT(*) DESC
    LIMIT 10
),
top_7colors AS (
    SELECT color
    FROM stolen_vehicles
    GROUP BY color
    ORDER BY COUNT(*) DESC
    LIMIT 7
)
-- Final pivot table
SELECT 
    vehicle_type,
    SUM(CASE WHEN color = 'Silver' THEN 1 ELSE 0 END) AS Silver,
    SUM(CASE WHEN color = 'White' THEN 1 ELSE 0 END) AS White,
    SUM(CASE WHEN color = 'Black' THEN 1 ELSE 0 END) AS Black,
    SUM(CASE WHEN color = 'Blue' THEN 1 ELSE 0 END) AS Blue,
    SUM(CASE WHEN color = 'Red' THEN 1 ELSE 0 END) AS Red,
    SUM(CASE WHEN color = 'Gray' THEN 1 ELSE 0 END) AS Gray,
    SUM(CASE WHEN color = 'Green' THEN 1 ELSE 0 END) AS Green,
    SUM(CASE WHEN color NOT IN (SELECT color FROM top_7colors) THEN 1 ELSE 0 END) AS Other_Colors
FROM stolen_vehicles
WHERE vehicle_type IN (SELECT vehicle_type FROM top_10types)
GROUP BY vehicle_type
ORDER BY SUM(1) DESC;
```
Query Output:

| Vehicle Type      | Silver | White | Black | Blue | Red | Gray | Green | Other |
|-------------------|--------|-------|-------|------|-----|------|--------|-------|
| Stationwagon      | 223    | 159   | 141   | 142  | 84  | 0    | 59     | 53    |
| Saloon            | 226    | 160   | 99    | 125  | 75  | 0    | 52     | 43    |
| Hatchback         | 172    | 114   | 76    | 104  | 58  | 0    | 24     | 50    |
| Trailer           | 399    | 21    | 29    | 17   | 9   | 0    | 22     | 12    |
| Utility           | 71     | 183   | 36    | 46   | 45  | 0    | 38     | 15    |
| Roadbike          | 17     | 42    | 105   | 38   | 51  | 0    | 12     | 21    |
| Moped             | 8      | 25    | 85    | 18   | 34  | 0    | 1      | 13    |
| Light Van         | 19     | 104   | 0     | 5    | 7   | 0    | 6      | 6     |
| Boat Trailer      | 67     | 5     | 3     | 0    | 0   | 0    | 0      | 1     |
| Trailer - Heavy   | 53     | 9     | 1     | 3    | 0   | 0    | 2      | 10    |

> Silver and White are the most frequently stolen colors across top vehicle types, while Green and Gray are least targeted. This reflects common household and fleet color trends. Stationwagons, Saloons, Hatchbacks, and Trailers consistently account for the highest theft counts across all colors.

5. Heatmap visualization of vehicle type vs color
![Vehicle types and colors](https://github.com/Jeseenacodes/Motor_Vehicle_Thefts-SQL_Python/blob/main/Charts/Heatmap_1.png)

---
### **Objective 3 — Identify *where* vehicles are stolen**

Analyzed theft patterns across regions using population and density data.

1. Number of stolen vehicles in each region
```sql
SELECT
	l.region,
	COUNT(s.vehicle_id) as 'Number_of_Vehicles_Stolen'
FROM stolen_vehicles AS s
LEFT JOIN locations AS l on l.location_id = s.location_id
GROUP BY l.region
ORDER BY COUNT(s.vehicle_id) DESC; 
```
Query Output:
| Region                | Number of Vehicles |
|-----------------------|--------------------|
| Auckland              | 1638               |
| Canterbury            | 660                |
| Bay of Plenty         | 446                |
| Wellington            | 420                |
| Waikato               | 371                |
| Northland             | 234                |
| Gisborne              | 176                |
| Otago                 | 139                |
| Manawatū-Whanganui    | 139                |
| Taranaki              | 112                |
| Hawke's Bay           | 100                |
| Nelson                | 92                 |
| Southland             | 26                 |

> Vehicle thefts are heavily urban-focused, with Auckland reporting the highest numbers by a wide margin. Low-population regions like Southland and Nelson show minimal theft activity, highlighting a strong link between population density and theft risk.

2. Merged region theft counts with population + density

```sql
SELECT
	l.region, l.population, l.density,
	COUNT(s.vehicle_id) as 'Number_of_Vehicles_Stolen'
FROM stolen_vehicles AS s
LEFT JOIN locations AS l on l.location_id = s.location_id
GROUP BY l.region, l.population, l.density
ORDER BY COUNT(s.vehicle_id) DESC; 
```

Query Output: 
| Region                | Population | Density (per km²) | Number of Vehicles Stolen |
|-----------------------|------------|--------------------|----------------------------|
| Auckland              | 1,695,200  | 343.09             | 1638                       |
| Canterbury            | 655,000    | 14.72              | 660                        |
| Bay of Plenty         | 347,700    | 28.80              | 446                        |
| Wellington            | 543,500    | 67.52              | 420                        |
| Waikato               | 513,800    | 21.50              | 371                        |
| Northland             | 201,500    | 16.11              | 234                        |
| Gisborne              | 52,100     | 6.21               | 176                        |
| Otago                 | 246,000    | 7.89               | 139                        |
| Manawatū-Whanganui    | 258,200    | 11.62              | 139                        |
| Taranaki              | 127,300    | 17.55              | 112                        |
| Hawke's Bay           | 182,700    | 12.92              | 100                        |
| Nelson                | 54,500     | 129.15             | 92                         |
| Southland             | 102,400    | 3.28               | 26                         |

> Regions with larger and denser populations tend to see the most vehicle thefts, led by Auckland. However, density alone isn’t enough—Nelson is dense but still reports low thefts, suggesting influences like policing or parking behavior. Rural, low-density regions show the least theft activity overall.

3. Comparison of vehicle types in most vs least dense regions
```sql
SELECT region, density, 'Highest' As cat
FROM (
    SELECT region, density
    FROM locations
    ORDER BY density DESC
    LIMIT 3
) AS top3
UNION
SELECT region, density, 'Lowest' AS cat
FROM (
    SELECT region, density
    FROM locations
    ORDER BY density ASC
    LIMIT 3
) AS bottom3;

```

4. Scatter plot (Population vs Density) sized by theft count
![Region](https://github.com/Jeseenacodes/Motor_Vehicle_Thefts-SQL_Python/blob/main/Charts/Heatmap1.png)

5. Choropleth map colored by number of stolen vehicles
![Plot](https://github.com/Jeseenacodes/Motor_Vehicle_Thefts-SQL_Python/blob/main/Charts/Vehicle%20stolen.png)

---

## 🛠️ Tools & Technologies

| Category                  | Tools                                  |
| ------------------------- | -------------------------------------- |
| **Database**              | SQL (MySQL / PostgreSQL / SQLite)      |
| **Visualization**         | Matplotlib, Seaborn, Plotly, GeoPandas |

---

## Key Insights Summary

### 🔹 **When are vehicles stolen?**

* Theft peaks in **March** and drops significantly in **April**.
* Thefts increase towards **late year (Oct–Dec)**.
* Weekday analysis reveals clear behavioral patterns (insert from your results).


### 🔹 **Which vehicles are targeted?**

* **Most stolen vehicle types:** Stationwagon, Saloon, Hatchback, Trailer
* **Least stolen:** Mobile Machine, Special Purpose Vehicle, Mobile Home Light
* **Average age insight:** Older vehicles (15–30 years) are more commonly stolen
* **Luxury vs Standard:** Most stolen vehicles are **standard**, not luxury
* **Heatmap shows:** Colors such as **White, Silver, Black** dominate theft counts

### 🔹 **Where are vehicles stolen?**

* Densely populated regions show **higher theft activity**
* Scatter plot indicates strong correlation between **density → theft**
* Choropleth map highlights hotspots visually

---

## **Recommendations**

* **Enhance surveillance infrastructure** (CCTV, ANPR systems, night lighting) in high-risk zones.
* **Deploy predictive analysis** to identify theft-prone areas based on historical patterns.
* **Increase police patrolling** and community policing initiatives to deter offenders.
* **Encourage public awareness** on vehicle safety — locks, alarms, GPS tracking.
* **Partner with insurance companies** to promote incentives for vehicle security upgrades.
* **Conduct deeper analysis** (e.g., time-of-day, location clusters, day-of-week patterns) to guide resource allocation.
* **Implement rapid-response mechanisms** for repeated hotspots to reduce repeat offenses.

---

## Why This Project Matters

This project demonstrates my ability to:

* Write **clean SQL Queries**
* Perform **data transformations**
* Build **visualizations** that explain insights, not just charts
* Combine **SQL + Python** effectively
* Create **professional reports** and GitHub-worthy documentation

---

## Credits

Dataset: Provided as part of training/education from Maven Analytics

Analysis & Report: ![Jeseena Parveen K Badge](https://img.shields.io/badge/JeseenaParveenK-Data%20Analyst-pink?style=flat) [![LinkedIn](https://img.shields.io/badge/LinkedIn-blue?logo=linkedin&logoColor=white&style=flat)](https://www.linkedin.com/in/jeseena-parveen-k/?skipRedirect=true)




