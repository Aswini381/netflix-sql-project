# Netflix-sql-project and TV shows data analysis using sql


![Netflix Logo](https://github.com/Aswini381/netflix-sql-project/blob/main/logo.png)

## Overview

This project involves a comprehensive analysis of Netflix's movies and TV shows data using SQL. The goal is to extract valuable insights and answer various business questions based on the dataset. The following README provides a detailed account of the project's objectives, business problems, solutions, findings, and conclusions.


## Objectives
- Analyze the distribution of content types (movies vs TV shows).
- Identify the most common ratings for movies and TV shows.
- List and analyze content based on release years, countries, and durations.
- Explore and categorize content based on specific criteria and keywords.

## Schema


```sql
drop table if exists netflix;
create table netflix(
show_id varchar(6),
type varchar(10),
title varchar(150),
director varchar(210),
cast varchar(1000),
country varchar(150),
date_added varchar(50),
release_year  int,
rating varchar(10),
duration varchar(50),
listed_in varchar(100),
description varchar(250)
);
```

## Business Problems and Solutions

1. Count the number od movies vs TV shows.

```sql
select type,
count(*) as total_content
from netflix
group by type;
```

2. Find the most common rating for movies and TV shows.

```sql
select type,
rating
from
(select type, rating, count(*) as count_rat,
rank() over(partition by type order by count(*) desc) as ranking
from netflix
group by type,rating) as t1
where ranking=1;
```

3. List all movies released in specific year (e.g., 2020).
```sql
select * from netflix 
where type='Movie'
and release_year=2020;
```

4. Find the top 5 countries with the most content on Netflix.
```sql
SELECT 
    TRIM(SUBSTRING_INDEX(SUBSTRING_INDEX(country, ',', numbers.n), ',', -1)) AS new_country,
    COUNT(*) AS total_content
FROM netflix
JOIN (
    SELECT 1 AS n UNION SELECT 2 UNION SELECT 3 UNION SELECT 4 UNION SELECT 5
) AS numbers
ON numbers.n <= 1 + LENGTH(country) - LENGTH(REPLACE(country, ',', '')) 
WHERE TRIM(SUBSTRING_INDEX(SUBSTRING_INDEX(country, ',', numbers.n), ',', -1)) IS NOT NULL
GROUP BY new_country
ORDER BY total_content DESC
limit 5;
```

5. Identity the  longest movie??
```sql
select * from netflix
where type='movie'
and duration=(select max(duration) from netflix);
```

6. Find content added in the last 5 years.
```sql
select * from netflix
where str_to_date(date_added, '%M %d,%Y')>=date_sub(curdate(),interval 5 year);
```

7. Find all the movies/TV shows bt director 'Delhiprasad Deenadayalan' 
```sql
select type from netflix where director = 'Delhiprasad Deenadayalan';
```

8. List all TV shows with more than 5 seconds 

```sql
select * from netflix
where type='TV Show' and cast(substring_index(duration, ' ', 1) as unsigned) >5;
```

9. Count the number of content items in each genre 
```sql
select trim(substring_index(substring_index(listed_in,',',numbers.n),',',-1)) as genre,
count(*) as total_content
from netflix
join
(select 1 as n union select 2 union select 3 union select 4 union select 5) as numbers
on  numbers.n<=1+length(listed_in)-length(replace(listed_in,',',''))
group by genre
order by total_content desc;
```


10. Find each year and the average numbers of content release by india on netflix, return top 5 year with highest avg content release.

```sql
select
    extract(year from str_to_date(date_added, '%M %d, %Y')) as year,
    count(*) as total_content,
    (count(*) * 100.0 / 
        (select count(*) from netflix where country like '%India%')
    ) as avg_content_per_year
from netflix
where country like '%India%'
group by year
order by avg_content_per_year desc;
```



11. List all movies that are documentaries.

```sql
select * from netflix
where listed_in like '%documentaries%';
```

12.Find all content without a director.
```sql
select * from netflix
where director is null or TRIM(director) = '';
```

13.Find how many movies actor Julie Tejwani appeared in last 10 years. 
```sql
select * from netflix
where cast like '%Julie Tejwani%'
and release_year>date_sub(curdate(),interval 10 year);
```

14. Find the top 10 actors who have appeared in thr highest number of movies produced in india. 
```sql
select trim(substring_index(substring_index(cast, ',', numbers.n), ',', -1)) as actors,
COUNT(*) AS total_content
FROM netflix
JOIN (
    SELECT 1 AS n UNION SELECT 2 UNION SELECT 3 UNION SELECT 4 UNION SELECT 5
) AS numbers
on numbers.n<=1+length(cast)-length(replace(cast,',',''))
where country like '%india'
group by actors
order by total_content desc
limit 10;
```

15. Categories the content based on the presence of the keywords 'kill' and 'violence' in the description fielf. label content containing these keywords as 'bad' and all other content as 'good'. count how many items fall into rach category.
    
```sql
with new_table as(
select *,case
when description like '%kill%' or description like '%violence%' then 'Bad_content'
else 'Good_content'
end category
from netflix)
select category,
count(*) as total_content
from new_table
group by category
order by total_content;
```
## Findings and Conclusion

- Content Distribution: The dataset contains a diverse range of movies and TV shows with varying ratings and genres.
- Common Ratings: Insights into the most common ratings provide an understanding of the content's target audience.
- Geographical Insights: The top countries and the average content releases by India highlight regional content distribution.
- Content Categorization: Categorizing content based on specific keywords helps in understanding the nature of content available on Netflix.

This analysis provides a comprehensive view of Netflix's content and can help inform content strategy and decision-making.

## Author - Aswini

I'm passinate about SQL and interesting to work real world problems.

