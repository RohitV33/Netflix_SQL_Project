# 🎬 Netflix Movies and TV Shows Data Analysis using SQL

<p align="center">
  <img src="https://upload.wikimedia.org/wikipedia/commons/0/08/Netflix_2015_logo.svg" width="500"/>
</p>

---

## 📌 Overview
This project involves a comprehensive analysis of Netflix's movies and TV shows data using **SQL**.  
The goal is to extract valuable insights and answer various business questions based on the dataset.

The analysis focuses on content distribution, ratings, genres, countries, duration trends, and keyword-based categorization.

---

## 🎯 Objectives
- Analyze the distribution of content types (**Movies vs TV Shows**)
- Identify the most common ratings for movies and TV shows
- List and analyze content based on release years, countries, and durations
- Explore and categorize content based on specific criteria and keywords

---

## 📂 Dataset
The data for this project is sourced from **Kaggle**.

- **Dataset:** Netflix Movies and TV Shows  
- **Format:** CSV  
- **Records:** Movies and TV shows available on Netflix  

---

## 🗂️ Schema
```sql
DROP TABLE IF EXISTS netflix;

CREATE TABLE netflix 
(
    show_id VARCHAR(6),
    type VARCHAR(10),
    title VARCHAR(150),
    director VARCHAR(208),
    casts VARCHAR(1000),
    country VARCHAR(150),
    date_added VARCHAR(50),
    release_year INT,
    rating VARCHAR(10),
    duration VARCHAR(15),
    listed_in VARCHAR(100),
    description VARCHAR(250)
);
🧠 Business Problems & Solutions
1️⃣ Count the Number of Movies vs TV Shows
SELECT type, COUNT(*)
FROM netflix
GROUP BY type;
2️⃣ Find the Most Common Rating for Movies and TV Shows
WITH RatingCounts AS (
    SELECT type, rating, COUNT(*) AS rating_count
    FROM netflix
    GROUP BY type, rating
),
RankedRatings AS (
    SELECT *,
           RANK() OVER (PARTITION BY type ORDER BY rating_count DESC) AS rank
    FROM RatingCounts
)
SELECT type, rating
FROM RankedRatings
WHERE rank = 1;
3️⃣ List All Movies Released in 2020
SELECT *
FROM netflix
WHERE release_year = 2020;
4️⃣ Top 5 Countries with the Most Content
SELECT country, COUNT(*) AS total_content
FROM (
    SELECT UNNEST(STRING_TO_ARRAY(country, ',')) AS country
    FROM netflix
) t
WHERE country IS NOT NULL
GROUP BY country
ORDER BY total_content DESC
LIMIT 5;
5️⃣ Identify the Longest Movie
SELECT *
FROM netflix
WHERE type = 'Movie'
ORDER BY SPLIT_PART(duration, ' ', 1)::INT DESC;
6️⃣ Content Added in the Last 5 Years
SELECT *
FROM netflix
WHERE TO_DATE(date_added, 'Month DD, YYYY') 
      >= CURRENT_DATE - INTERVAL '5 years';
7️⃣ Content by Director Rajiv Chilaka
SELECT *
FROM (
    SELECT *, UNNEST(STRING_TO_ARRAY(director, ',')) AS director_name
    FROM netflix
) t
WHERE director_name = 'Rajiv Chilaka';
8️⃣ TV Shows with More Than 5 Seasons
SELECT *
FROM netflix
WHERE type = 'TV Show'
  AND SPLIT_PART(duration, ' ', 1)::INT > 5;
9️⃣ Count Content by Genre
SELECT UNNEST(STRING_TO_ARRAY(listed_in, ',')) AS genre,
       COUNT(*) AS total_content
FROM netflix
GROUP BY genre;
🔟 Average Content Release by India (Top 5 Years)
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
1️⃣1️⃣ Documentary Movies
SELECT *
FROM netflix
WHERE listed_in ILIKE '%documentaries%';
1️⃣2️⃣ Content Without a Director
SELECT *
FROM netflix
WHERE director IS NULL;
1️⃣3️⃣ Movies Featuring Salman Khan (Last 10 Years)
SELECT *
FROM netflix
WHERE casts ILIKE '%Salman Khan%'
  AND release_year > EXTRACT(YEAR FROM CURRENT_DATE) - 10;
1️⃣4️⃣ Top 10 Actors in Indian Movies
SELECT UNNEST(STRING_TO_ARRAY(casts, ',')) AS actor,
       COUNT(*) AS total_content
FROM netflix
WHERE country ILIKE '%India%'
GROUP BY actor
ORDER BY total_content DESC
LIMIT 10;
1️⃣5️⃣ Content Categorization (Kill & Violence)
SELECT category, COUNT(*) AS content_count
FROM (
    SELECT 
        CASE 
            WHEN description ILIKE '%kill%' 
              OR description ILIKE '%violence%' THEN 'Bad'
            ELSE 'Good'
        END AS category
    FROM netflix
) t
GROUP BY category;
📊 Findings & Conclusion
Netflix has a diverse mix of movies and TV shows

Movies dominate the platform in quantity

India is among the top content-producing countries

Keyword-based analysis helps identify content nature

Strong SQL querying enables meaningful insights

🛠️ Tools & Skills Used
PostgreSQL

SQL (CTEs, Window Functions, String Functions)

Data Cleaning & Analysis

👨‍💻 Author
Rohit Verma
Aspiring Data Analyst | SQL | Data Analytics

⭐ If you like this project, don’t forget to star the repository!
