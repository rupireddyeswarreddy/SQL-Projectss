--Retrive the total gross Income--
SELECT SUM(gross_income) AS total_gross_income
FROM movie_budget;


--Give all the details of the movies having movie_id 2,4,6&8.--
SELECT * FROM movies_list
where movie_id IN (2,4,6,8);

--What is the average rating of all the movies and Round the value upto Two decimal Places--
SELECT ROUND(AVG(imbd_rating),2) AS AVERAGE_IMBD
FROM movies_list;

--Which are highest and lowest rated movies ?--
SELECT * FROM movies_list
WHERE imbd_rating IN(
					(SELECT MIN(imbd_rating) FROM movies_list),
					(SELECT MAX(imbd_rating) FROM movies_list)
);

/*Retrieve information about movies, including their title, release year, genre, IMDB rating, associated awards, 
budget, and gross income, grouped by movie title and release year, ordered by release year in descending order."*/
SELECT m.title AS movie_title,
	   m.release_year,
	   m.genre,
	   AVG(imbd_rating),
	   a.award_name,
	   a.award_category,
	   a.award_year,
	   b.budget,
	   b.gross_income
FROM movies_list m
JOIN awards a ON m.movie_id = a.movie_id
JOIN movie_budget b ON m.movie_id = b.movie_id
GROUP BY m.title,m.release_year,m.genre, a.award_name,a.award_category,a.award_year,b.budget,b.gross_income
ORDER BY m.release_year DESC;

-- Retrieve the movie titles and total budget for movies that received awards in 2023
SELECT m.title, mb.budget,mb.unit
FROM movies m
JOIN movie_budget mb ON m.movie_id = mb.movie_id
WHERE m.movie_id IN (
    SELECT movie_id 
    FROM awards
    WHERE award_year = 2023
);

--Retrive the title,release year,actor name of Best Actor--
SELECT m.title, m.release_year, ma.actor_name
FROM movies_list m
JOIN movie_actor ma ON m.movie_id = ma.movie_id
JOIN awards aw ON m.movie_id = aw.movie_id
WHERE aw.award_category = 'Best Actor';

--Retrive the data of total budget and total profit using CTE--
WITH movie_budget_cte AS (
    SELECT movie_id,SUM(budget) AS total_budget
    FROM movie_budget
    GROUP BY movie_id
),
movie_profit_cte AS (
    SELECT movie_id,SUM(gross_income - budget) AS total_profit
    FROM movie_budget
    GROUP BY movie_id
)
SELECT 
    m.title,
    mb.total_budget,
    mp.total_profit
	
FROM movies_list m
JOIN movie_budget_cte mb 
	ON m.movie_id = mb.movie_id
JOIN movie_profit_cte mp 
	ON m.movie_id = mp.movie_id
WHERE 
    mp.total_profit > 0
ORDER BY 
    mp.total_profit DESC;

--Retrive the movie with Highest Profit--
SELECT
    m.title,
    (mb.gross_income - mb.budget) AS profit,mb.unit
FROM
    movie_budget mb
JOIN
    movies_list m ON mb.movie_id = m.movie_id
ORDER BY
    profit DESC
LIMIT 1;






