# Netflix Movies and TV Shows Data Analysis using SQL

![](https://github.com/Abhisheksabha/netflix_sql_project/blob/main/logo.png)

## Overview
This project involves a comprehensive analysis of Netflix's movies and TV shows data using SQL. The goal is to extract valuable insights and answer various business questions based on the dataset. 

## Objectives

- Analyze the distribution of content types (movies vs TV shows).
- Identify the most common ratings for movies and TV shows.
- List and analyze content based on release years, countries, and durations.
- Explore and categorize content based on specific criteria and keywords.

## Dataset

The data for this project is sourced from the Kaggle dataset:

- **Dataset Link:** [Movies Dataset](https://www.kaggle.com/datasets/shivamb/netflix-shows?resource=download)

## Schema

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

## Business Problems and Solutions

### 1. Count the Number of Movies vs TV Shows

**Objective:** Determine the type of content on Netflix.

```sql
SELECT type, COUNT(show_id) FROM netflix GROUP BY type;
```


### 2. Find the Most Common Rating for Movies and TV Shows

**Objective:** Identify the most frequently occurring rating for each type of content.

```sql
SELECT type, rating, total_ratings FROM (
	SELECT type, rating, COUNT(rating) AS total_ratings, 
		RANK() OVER (PARTITION BY type ORDER BY COUNT(rating) DESC) as ranking
	FROM netflix GROUP BY type, rating 
) WHERE ranking = 1 ;
```

### 3. List All Movies Released in a Specific Year (e.g., 2020)

**Objective:** Retrieve all movies released in a specific year.

```sql
SELECT * FROM netflix 
	WHERE type = 'Movie' AND release_year = 2020;
```

### 4. Find the Top 5 Countries with the Most Content on Netflix

**Objective:** Identify the top 5 countries with the highest number of content items.

```sql
SELECT UNNEST(STRING_TO_ARRAY(country, ',')), COUNT(show_id) AS total_contents FROM netflix
	GROUP BY 1 ORDER BY 2 DESC LIMIT 5;
```


### 5. Identify the Longest Movie

**Objective:** Find the movie with the longest duration.

```sql
SELECT * FROM netflix 
	WHERE type = 'Movie' AND 
		CAST(REGEXP_REPLACE(duration, '[^0-9]', '', 'g') AS INTEGER) = (SELECT MAX(CAST(REGEXP_REPLACE(duration, '[^0-9]', '', 'g') AS INTEGER)) FROM netflix)

-- OR

SELECT * FROM netflix 
	WHERE type = 'Movie' 
	AND
	SPLIT_PART(duration, ' ', 1):: numeric = (SELECT MAX(SPLIT_PART(duration, ' ',1)::numeric ) FROM netflix)
```

### 6. Find Content Added in the Last 5 Years

**Objective:** Retrieve content added to Netflix in the last 5 years.

```sql
SELECT * FROM netflix
	WHERE TO_DATE(date_added, 'month DD, YYYY') >= CURRENT_DATE - INTERVAL '5 years' ;
```

### 7. Find All Movies/TV Shows by Director 'Rajiv Chilaka'

**Objective:** Retrieve content by director.

```sql
SELECT *
FROM (
    SELECT 
        *,
        UNNEST(STRING_TO_ARRAY(director, ',')) AS director_name
    FROM netflix
) AS t
WHERE director_name = 'Rajiv Chilaka';
```

### 8. List All TV Shows with More Than 5 Seasons

**Objective:** Identify TV shows with more than 5 seasons.

```sql
SELECT * FROM netflix 
	WHERE type = 'TV Show' 
	AND 
	CAST (REGEXP_REPLACE(duration, '[^0-9]', '', 'g') AS INTEGER) > 5 

-- OR

SELECT * FROM netflix
	WHERE type = 'TV Show' AND SPLIT_PART(duration, ' ', 1):: numeric > 5
```

### 9. Count the Number of Content Items in Each Genre

**Objective:** Count the number of content items in each genre.

```sql
SELECT UNNEST(STRING_TO_ARRAY(listed_in, ',')) AS genre, COUNT(show_id) FROM netflix 
	GROUP BY genre
```

### 10.Find each year and the average numbers of content release in India on netflix. 
return top 5 year with highest avg content release

**Objective:** Calculate and rank years by the average number of content releases in India.

```sql
SELECT 
	EXTRACT(YEAR FROM TO_DATE(date_added, 'Month DD, YYYY')) AS year,
	COUNT(show_id) AS total_content,
	ROUND(
		COUNT(show_id)::numeric /(SELECT COUNT(show_id) FROM netflix WHERE country ='India'):: numeric *100 ,2) AS avg_content
FROM netflix
	WHERE country = 'India'
	GROUP BY 1
```

### 11. List All Movies that are Documentaries

**Objective:** Retrieve all movies classified as documentaries.

```sql
SELECT * FROM netflix WHERE listed_in ILIKE '%Documentaries%'
```


### 12. Find All Content Without a Director

**Objective:** List contents that does not have a director.

```sql
SELECT * FROM netflix WHERE director IS NULL
```

### 13. Find How Many Movies Actor 'Salman Khan' Appeared in the Last 10 Years

**Objective:** Count the number of movies featuring 'Salman Khan' in the last 10 years.

```sql
SELECT * FROM netflix 
	WHERE casts ILIKE '%Salman Khan%'
	AND release_year > EXTRACT(YEAR FROM CURRENT_DATE) - 10;
```

### 14. Find the Top 10 Actors Who Have Appeared in the Highest Number of Movies Produced in India

**Objective:** Identify the top 10 actors with the most appearances in Indian-produced movies.

```sql
SELECT UNNEST(STRING_TO_ARRAY(casts, ',' )) AS actors, COUNT(show_id) AS total_movies FROM netflix
	WHERE country ILIKE '%India%'
	GROUP BY 1
	ORDER BY 2 DESC 
	LIMIT 10;
```

### 15. Categorize Content Based on the Presence of 'Kill' and 'Violence' Keywords

**Objective:** Categorize content and Count the number of items in each category.

```sql
WITH content_table AS (
	SELECT *,
		CASE 
			WHEN description ILIKE '% kill%'
			OR
			description ILIKE '%violence%'
			THEN 'Bad Content'
			ELSE
			'Good Content'
		END AS Content_category
	FROM netflix
)
SELECT content_category, COUNT(show_id) AS total_content FROM content_table
	GROUP BY 1;
```


## Findings and Conclusion

- **Content Distribution:** The dataset contains a diverse range of movies and TV shows with varying ratings and genres.
- **Common Ratings:** Insights into the most common ratings provide an understanding of the content's target audience.
- **Geographical Insights:** The top countries and the average content releases by India highlight regional content distribution.
- **Content Categorization:** Categorizing content based on specific keywords helps in understanding the nature of content available on Netflix.




















