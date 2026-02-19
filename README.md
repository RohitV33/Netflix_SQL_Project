# 🎬 Netflix Movies and TV Shows Data Analysis using SQL

<p align="center">
  <img src="https://upload.wikimedia.org/wikipedia/commons/0/08/Netflix_2015_logo.svg" width="500"/>
</p>

---

## 📌 Overview
This project presents a comprehensive analysis of Netflix’s Movies and TV Shows dataset using **SQL**.  
The objective is to extract meaningful insights and answer real-world business questions related to content distribution, ratings, genres, countries, and keyword-based categorization.

This project demonstrates strong SQL skills such as **data cleaning, aggregation, string manipulation, window functions, and analytical querying**, making it suitable for **Data Analyst roles**.

---

## 🎯 Objectives
- Analyze the distribution of content types (**Movies vs TV Shows**)
- Identify the most common ratings for movies and TV shows
- Explore content based on release years, countries, and durations
- Analyze genres and actor appearances
- Categorize content based on keywords such as *kill* and *violence*

---

## 📂 Dataset
- **Source:** Kaggle – Netflix Movies and TV Shows Dataset  
- **Format:** CSV  
- **Content:** Movies and TV shows available on Netflix  

---

## 🗂️ Schema

```sql
DROP TABLE IF EXISTS netflix;

CREATE TABLE netflix
(
    show_id      VARCHAR(5),
    type         VARCHAR(10),
    title        VARCHAR(250),
    director     VARCHAR(550),
    casts        VARCHAR(1050),
    country      VARCHAR(550),
    date_added   VARCHAR(55),
    release_year INT,
    rating       VARCHAR(15),
    duration     VARCHAR(15),
    listed_in    VARCHAR(250),
    description  VARCHAR(550)
);
```


## 🧠 Business Problems and Solutions
1. Count the Number of Movies vs TV Shows

 ```sql
SELECT 
    type,
    COUNT(*)
FROM netflix
GROUP BY 1;
```
Objective: Determine the distribution of content types on Netflix.

2. Find the Most Common Rating for Movies and TV Shows
```sql
WITH RatingCounts AS (
    SELECT 
        type,
        rating,
        COUNT(*) AS rating_count
    FROM netflix
    GROUP BY type, rating
),

RankedRatings AS (
    SELECT 
        type,
        rating,
        rating_count,
        RANK() OVER (PARTITION BY type ORDER BY rating_count DESC) AS rank
    FROM RatingCounts
)
SELECT 
    type,
    rating AS most_frequent_rating
FROM RankedRatings
WHERE rank = 1;
```
Objective: Identify the most frequently occurring rating for each content type.

3. List All Movies Released in 2020
```sql
SELECT *
FROM netflix
WHERE release_year = 2020;
```
Objective: Retrieve all movies released in the year 2020.

4. Find the Top 5 Countries with the Most Content on Netflix
 ```sql
SELECT *
FROM (
    SELECT 
        UNNEST(STRING_TO_ARRAY(country, ',')) AS country,
        COUNT(*) AS total_content
    FROM netflix
    GROUP BY 1
)
WHERE country IS NOT NULL
ORDER BY total_content DESC
LIMIT 5;
```
Objective: Identify the top 5 countries with the highest number of content items.

5. Identify the Longest Movie

```sql
SELECT *
FROM netflix
WHERE type = 'Movie'
ORDER BY SPLIT_PART(duration, ' ', 1)::INT DESC;

```
Objective: Find the movie with the longest duration.

6. Find Content Added in the Last 5 Years

```sql
SELECT *
FROM netflix
WHERE TO_DATE(date_added, 'Month DD, YYYY') 
      >= CURRENT_DATE - INTERVAL '5 years';
```
Objective: Retrieve content added to Netflix in the last five years.

7. Find All Movies and TV Shows by Director Rajiv Chilaka

```sql
SELECT *
FROM (
    SELECT 
        *,
        UNNEST(STRING_TO_ARRAY(director, ',')) AS director_name
    FROM netflix
) 
WHERE director_name = 'Rajiv Chilaka';
```
Objective: List all content directed by Rajiv Chilaka.

8. List All TV Shows with More Than 5 Seasons
```sql
SELECT *
FROM netflix
WHERE type = 'TV Show'
  AND SPLIT_PART(duration, ' ', 1)::INT > 5;
```
Objective: Identify TV shows with more than five seasons.

9. Count the Number of Content Items in Each Genre
```sql
SELECT 
    UNNEST(STRING_TO_ARRAY(listed_in, ',')) AS genre,
    COUNT(*) AS total_content
FROM netflix
GROUP BY 1;
```
Objective: Analyze content distribution across genres.

10. Average Content Release by India (Top 5 Years)
 ```sql
SELECT  
    EXTRACT(YEAR FROM TO_DATE(date_added,'Month DD, YYYY')) AS year,
    COUNT(*) AS total_release,
    ROUND(
        COUNT(*)::NUMERIC /
        (SELECT COUNT(*) FROM netflix WHERE country = 'India')::NUMERIC * 100, 2
    ) AS avg_content
FROM netflix
WHERE country = 'India'
GROUP BY year
ORDER BY avg_content DESC
LIMIT 5;
```
Objective: Identify the top 5 years with the highest average content releases from India.

11. List All Documentary Movies
  ```sql
SELECT *
FROM netflix
WHERE listed_in ILIKE '%documentaries%';
```
Objective: Retrieve all movies classified as documentaries.

12. Find Content Without a Director
```sql
SELECT *
FROM netflix
WHERE director IS NULL;
```
Objective: Identify content with missing director information.

13. Find Movies Featuring Salman Khan in the Last 10 Years
```sql
SELECT *
FROM netflix
WHERE casts ILIKE '%Salman Khan%'
  AND release_year > EXTRACT(YEAR FROM CURRENT_DATE) - 10;

```
Objective: Find movies featuring Salman Khan released in the last 10 years.

14. Top 10 Actors in Indian Movies

```sql
SELECT 
    UNNEST(STRING_TO_ARRAY(casts, ',')) AS actor,
    COUNT(*) AS total_content
FROM netflix
WHERE country ILIKE '%India%'
GROUP BY actor
ORDER BY total_content DESC
LIMIT 10;
```
Objective: Identify the top 10 actors with the most appearances in Indian movies.

15. Categorize Content Based on Keywords (Kill & Violence)
   ```sql
SELECT 
    category,
    COUNT(*) AS content_count
FROM (
    SELECT 
        CASE 
            WHEN description ILIKE '%kill%' 
              OR description ILIKE '%violence%' THEN 'Bad'
            ELSE 'Good'
        END AS category
    FROM netflix
) categorized_content
GROUP BY category;
```
Objective: Categorize content as Good or Bad based on keyword presence.

📊 Key Insights

Movies dominate Netflix’s content library

India is one of the top content-producing countries

Keyword-based analysis helps classify content nature

Advanced SQL techniques enable deeper analytical insights

🛠️ Tools & Skills Used

PostgreSQL

SQL (CTEs, Window Functions, String Functions)

Data Cleaning & Analysis

Business-Oriented Problem Solving

👨‍💻 Author

Rohit Verma

Aspiring Data Analyst | SQL | Data Analytics

📌 This project is part of my data analytics portfolio.

⭐ If you found this project helpful, feel free to star the repository!
   
