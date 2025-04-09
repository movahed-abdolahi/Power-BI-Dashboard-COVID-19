# 🦠 COVID-19 Global Impact Analysis

## Overview

This project investigates the global impact of COVID-19 using datasets containing country-level statistics on deaths, vaccinations, and development indicators. The analysis explores how factors like GDP per capita, Human Development Index (HDI), government policy (stringency index), and health infrastructure affected COVID-19 outcomes including total deaths, death rates, and vaccination coverage.

Data cleaning, transformation, and exploratory data analysis (EDA) were performed using **SQL** and visualized using **Power BI**. The goal was to uncover critical insights into the spread, severity, and control of the pandemic across the globe.

---

## Dataset Information

**Files Used:**

- `COVID-dataset.xlsx`: Combined and cleaned dataset containing case counts, deaths, vaccinations, and country-level socioeconomic data.
- `Date.xlsx`: Custom calendar table for time-based analysis in Power BI.
- SQL Scripts:
  - `CovidAnalysis.sql` – Initial EDA and metrics
  - `OverTimeAnalysis.sql` – Trends and temporal comparisons
  - `1.sql` – Custom aggregations and transformations
- Power BI Report:
  - `Covid-Analysis.pbix`: Final dashboard with KPIs, visuals, and filters.

---

## Data Preparation

### SQL + Power BI Integration

The SQL scripts were used to extract, clean, and aggregate data which was then loaded into Power BI for visualization. Key transformations included filtering out continent-level rollups, casting fields, calculating fatality and infection rates, and joining vaccination with deaths data.

### Custom Date Table (DAX)

Used to support dynamic filtering and time intelligence in Power BI:

```DAX
Date = 
ADDCOLUMNS(
    CALENDAR(DATE(2019,1,1), DATE(2025,12,31)),
    "Year", YEAR([Date]),
    "Month", FORMAT([Date], "MMMM"),
    "MonthNumber", MONTH([Date]),
    "Quarter", "Q" & FORMAT([Date], "Q"),
    "Day", DAY([Date]),
    "Weekday", FORMAT([Date], "dddd"),
    "WeekdayNumber", WEEKDAY([Date], 2)
)
```

---

## SQL Analysis Highlights

### 1. Data Cleaning & Structure

```sql
SELECT * FROM CovidDeaths
WHERE continent IS NOT NULL;
```

Filtered out nulls and world-level aggregates for clean, usable data.

---

### 2. Death Rate Calculation

```sql
SELECT location, date, total_cases, total_deaths,
       (total_deaths / total_cases) * 100 AS death_rate_percentage
FROM CovidDeaths
WHERE continent IS NOT NULL;
```

---

### 3. Infection Rate by Population

```sql
SELECT location, date, population, total_cases,
       (total_cases / population) * 100 AS infection_rate
FROM CovidDeaths
WHERE continent IS NOT NULL;
```

---

### 4. Highest Infection Rate by Country

```sql
SELECT location, population, MAX(total_cases) AS highest_infection_count,
       MAX((total_cases / population)) * 100 AS highest_infection_rate
FROM CovidDeaths
WHERE continent IS NOT NULL
GROUP BY location, population
ORDER BY highest_infection_rate DESC;
```

---

### 5. Countries with Highest Death Count

```sql
SELECT location, MAX(CAST(total_deaths AS INT)) AS total_death_count
FROM CovidDeaths
WHERE continent IS NOT NULL
GROUP BY location
ORDER BY total_death_count DESC;
```

---

### 6. Global Summary Statistics

```sql
SELECT SUM(new_cases) AS total_cases,
       SUM(CAST(new_deaths AS INT)) AS total_deaths,
       (SUM(CAST(new_deaths AS INT)) / SUM(new_cases)) * 100 AS global_death_rate
FROM CovidDeaths
WHERE continent IS NOT NULL;
```

---

## Vaccination Data Analysis

### 7. Deaths + Vaccinations Join

```sql
SELECT *
FROM CovidDeaths dea
JOIN CovidVaccinations vac
  ON dea.location = vac.location AND dea.date = vac.date
WHERE dea.continent IS NOT NULL;
```

---

### 8. Rolling Vaccination Count

```sql
SELECT dea.continent, dea.location, dea.date, dea.population,
       vac.new_vaccinations,
       SUM(CAST(vac.new_vaccinations AS INT)) OVER (
           PARTITION BY dea.location ORDER BY dea.date
       ) AS rolling_people_vaccinated
FROM CovidDeaths dea
JOIN CovidVaccinations vac
  ON dea.location = vac.location AND dea.date = vac.date
WHERE dea.continent IS NOT NULL;
```

---

## Over Time Trends

**SQL File: `OverTimeAnalysis.sql`**

### 9. Vaccination Growth by Country

```sql
SELECT location, date, people_vaccinated, total_vaccinations
FROM CovidVaccinations
WHERE people_vaccinated IS NOT NULL
ORDER BY location, date;
```

---

### 10. Government Stringency vs Outcomes

```sql
SELECT location, date, stringency_index, total_deaths
FROM CovidDeaths
WHERE stringency_index IS NOT NULL;
```

### 11. HDI vs Total Deaths

```sql
SELECT location, hdi, MAX(total_deaths) AS total_deaths
FROM CovidDeaths
WHERE hdi IS NOT NULL
GROUP BY location, hdi
ORDER BY total_deaths DESC;
```

---

## SQL Techniques Used

- **JOINs:** To merge death and vaccination datasets
- **Window Functions:** Used `SUM() OVER()` to track vaccination progress
- **Aggregations:** `MAX`, `SUM`, `AVG` for metrics and rankings
- **Date Filtering:** `BETWEEN`, `GROUP BY`, and `ORDER BY`
- **Data Type Casting:** `CAST` to ensure numeric calculations
- **Data Cleaning:** Used `WHERE`, `IS NOT NULL` to remove unusable records

---

## Dashboard Overview (Power BI)

Key pages and features in the `.pbix` dashboard:

### 🌍 Global Overview
- Total cases, deaths, vaccinations
- Fatality and vaccination rates by continent
- Interactive map

### 📈 Reproduction Rate Trends
- R-value vs case count over time
- Filterable by year and month

### 📅 Monthly Summary Table
- Aggregated case/death/fatality rate by month and year

### 🌐 Country Deep Dives
- Country-level breakdown (e.g., Canada) showing:
  - GDP, HDI, Median Age, Hospital Beds, Poverty Rate
  - Days segmented by fatality risk (low, medium, high)

---

## Key Insights

- Higher HDI and GDP didn’t always correlate with lower death rates.
- Government stringency impacted reproduction rate and case curves.
- Rolling vaccination analysis shows when countries ramped up efforts.
- Europe and Asia led in case and death counts, but also had high vaccination rates.

---

## Conclusion

This project demonstrates how real-world data analysis can uncover meaningful insights from global crises. Using SQL for data prep and Power BI for storytelling, we built a multi-dimensional view of COVID-19’s impact, influenced by economic, healthcare, and policy factors. The combination of structured query logic and compelling visuals brings clarity to complex global events.

---

## Files

- 📊 `COVID-dataset.xlsx`
- 📅 `Date.xlsx`
- 🧮 `CovidAnalysis.sql`
- 🔁 `OverTimeAnalysis.sql`
- 🧠 `1.sql`
- 📊 `Covid-Analysis.pbix`

---

## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for more details.

---

## Author

**Movahed Abdolahi**  
🎓 Data & BI Analyst | Power BI Enthusiast | SQL Expert  
🔗 [LinkedIn Profile](https://www.linkedin.com/in/movahed-abdolahi/)
