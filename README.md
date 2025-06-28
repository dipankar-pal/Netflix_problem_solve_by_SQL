# Netflix Movie and Tv Show Data Analysis Using SQL

![Netflix logo](https://github.com/dipankar-pal/Netflix_problem_solve_by_SQL/blob/main/netflix_732228.png)
Overview
This project involves a comprehensive analysis of Netflix's movies and TV shows data using SQL. The goal is to extract valuable insights and answer various business questions based on the dataset. The following README provides a detailed account of the project's objectives, business problems, solutions, findings, and conclusions.

##Objectives
Analyze the distribution of content types (movies vs TV shows).
Identify the most common ratings for movies and TV shows.
List and analyze content based on release years, countries, and durations.
Explore and categorize content based on specific criteria and keywords.
Dataset
The data for this project is sourced from the Kaggle dataset:

Dataset Link: ![Movies Dataset](https://www.kaggle.com/datasets/shivamb/netflix-shows)
##Schema
--DROP TABLE IF EXISTS netflix;
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
##Business Problems and Solutions
--netflix project
select * from netflix


-- 15 Business Problems & Solutions

--1. Count the number of Movies vs TV Shows

SELECT 
   "type",
   COUNT(show_id) as total_show
 FROM 
    netflix
GROUP BY 
    type
order by 
    2 desc;
  
--2. Find the most common rating for movies and TV shows

SELECT *
    FROM(
         SELECT
              type,
              rating,
              count(rating) ,
         RANK () OVER (partition by type order by count(rating) desc ) as ranking
               from 
			       netflix
               group by 
			       1,2) AS T1
WHERE 
    ranking = 1
     
--3. List all movies released in a specific year (e.g., 2020)

select
      title,
	  release_year
      from netflix
where 
      release_year = 2021
and   
      type = 'Movie';
	  
--4. Find the top 5 countries with the most content on Netflix
 
 
 select 
      UNNEST(STRING_TO_ARRAY(COUNTRY,',')) AS new_country,
	  count(show_id) as total_show
 from 
      netflix
 group by
      1
 order by 
      2 desc
 LIMIT 
      5;
     
--5. Identify the longest movie

SELECT 
       title,
	   type,
	   duration
	   FROM netflix
WHERE
      type = 'Movie'
	      AND
	  duration = (
                  SELECT MAX(duration)
				  from netflix
	  )

--6. Find content added in the last 5 years

SELECT 
     title,
	 type,
	 director,
	 country,
	 date_added,
	 listed_in
     FROM netflix
WHERE 
    TO_DATE(netflix.date_added,'month DD,YYYY') >= CURRENT_DATE - INTERVAL '5 YEARS'

--7. Find all the movies/TV shows by director 'Rajiv Chilaka'!

SELECT 
      title,
	  type,
	  director
FROM netflix
    where 
	    director LIKE '%Rajiv Chilaka%'

--8. List all TV shows with more than 5 seasons
  SELECT 
        title,
		type,
		director,
		casts,
		country,
		release_year,
		duration
  FROM netflix
where 
    type = 'TV Show' 
           and
	SPLIT_PART(duration,' ',1):: numeric > 5

	
--9. Count the number of content items in each genre
select 
      unnest(string_to_array(listed_in,',')) as genre,
	  count(show_id) as total_content 
from
      netflix
 group by 
      genre
 order by 
      total_content desc;
--10.Find each year and the average numbers of content release in India on netflix. 
   --return top 5 year with highest avg content release!
   SELECT 
    country,
    release_year,
    COUNT(show_id) AS total_release,
    ROUND(
        COUNT(show_id)::numeric /
        (SELECT COUNT(show_id) FROM netflix WHERE country = 'India')::numeric * 100, 2
    ) AS avg_release
FROM netflix
WHERE country = 'India'
GROUP BY country, release_year
ORDER BY avg_release DESC
LIMIT 5;
   
--11. List all movies that are documentaries

  SELECT
        type,
		duration,
		listed_in,
		description 
  from 
        netflix
  WHERE
       type = 'Movie'
  and
	   listed_in like '%Documentaries%'
--12. Find all content without a director

select *
   from 
        netflix
   where 
        director is null

--13. Find how many movies actor 'Salman Khan' appeared in last 10 years!

select 
      	*
        from netflix
  where 
        casts ilike '%Salman Khan%'
  and
        release_year > EXTRACT(YEAR FROM CURRENT_DATE)-10

--14. Find the top 10 actors who have appeared in the highest number of movies produced in India.

select 
      UNNEST(STRING_TO_ARRAY(casts,',')) AS actor,
	  COUNT(*) AS total_content
from 
      netflix
WHERE 
      country like '%India%'
GROUP BY 
      1
ORDER BY
      2 DESC
LIMIT
      10;

---"15.Categorize the content based on the presence of the keywords 'kill' and 'violence' in 
---the description field. Label content containing these keywords as 'Bad' and all other 
---content as 'Good'. Count how many items fall into each category."
WITH
    new_table as (
                  SELECT
				        *,
						CASE
						WHEN
						    description ILIKE '%kill%'
							OR
							description ILIKE '%violence%'
						THEN
						    'BED SHOW'
						ELSE
						    'GOOD SHOW'
                        END
						    category
				   FROM netflix
				)
SELECT
      category,
	  count(*) as total_content
FROM
      new_table
GROUP BY
      1
ORDER BY
      2 DESC






































































































































































































































































Findings and Conclusion
Content Distribution: The dataset contains a diverse range of movies and TV shows with varying ratings and genres.
Common Ratings: Insights into the most common ratings provide an understanding of the content's target audience.
Geographical Insights: The top countries and the average content releases by India highlight regional content distribution.
Content Categorization: Categorizing content based on specific keywords helps in understanding the nature of content available on Netflix.
This analysis provides a comprehensive view of Netflix's content and can help inform content strategy and decision-making.

Author - Zero Analyst
This project is part of my portfolio, showcasing the SQL skills essential for data analyst roles. If you have any questions, feedback, or would like to collaborate, feel free to get in touch!

Stay Updated and Join the Community
For more content on SQL, data analysis, and other data-related topics, make sure to follow me on social media and join our community:

