
# Motor Vehicle Theft Analysis — SQL + Python Project

Xxploratory data analysis (EDA) project investigating **when**, **what**, and **where** vehicles are stolen, using SQL and Python visualizations. This project answers real-world analytical questions that mirror the workflow of a **Data analyst**.

## Project Objectives

### **Objective 1 — Identify *when* vehicles are stolen**

Analyzed date-related fields to understand theft patterns.

1. Number of vehicles stolen **each year**
2. Number of vehicles stolen **each month**
3. Number of vehicles stolen **each day of the week**
4. Replaced numeric weekday with full names
5. Bar chart of thefts by day of the week

### **Objective 2 — Identify *which* vehicles are likely to be stolen**

Explored vehicle-related factors (type, age, luxury, color).

1. Most/least stolen vehicle types
2. Average age of stolen vehicles by vehicle type
3. Luxury vs Standard % by vehicle type
4. Pivot table: top 10 vehicle types × top 7 colors (+ Other)
5. Heatmap visualization of vehicle type vs color

### **Objective 3 — Identify *where* vehicles are stolen**

Analyzed theft patterns across regions using population and density data.

1. Number of stolen vehicles in each region
2. Merged region theft counts with population + density
3. Comparison of vehicle types in most vs least dense regions
4. Scatter plot (Population vs Density) sized by theft count
5. Choropleth map colored by number of stolen vehicles

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

## Sample SQL Queries

### Vehicles stolen each year

```sql
SELECT YEAR(date_stolen) AS year,
       COUNT(*) AS total_stolen
FROM stolen_vehicles
GROUP BY YEAR(date_stolen)
ORDER BY year;
```

### Most stolen vehicle types

```sql
SELECT vehicle_type,
       COUNT(*) AS stolen_count
FROM stolen_vehicles
GROUP BY vehicle_type
ORDER BY stolen_count DESC;
```

---

## Visual Examples 

 plots here, e.g.:

```
![Thefts by Day of Week](visuals/theft_by_day.png)
![Heatmap](visuals/heatmap_types_colors.png)
![Region Map](visuals/region_choropleth.png)
```

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
Analysis & Report: Jeseena Parveen K
[![LinkedIn](https://img.shields.io/badge/LinkedIn-Connect-blue?style=for-the-badge&logo=linkedin)](https://www.linkedin.com/in/jeseena-parveen-k/?skipRedirect=true)

