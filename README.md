
1. Show your thought process: This suggests, Let's check etc.. put your thoughts onto paper
2. Change percent to actual percentages (*100)

-- Nested queries
-- 


-- I only used completed Kickstarter campaigns, designated as either 'successful' or 'failed', in my analysis. This was done to rule out the effects of ongoing campaigns on the analysis. The goal behind this analysis is to find the general trends behind successful campaigns and unsuccessful kickstarter campaigns. Alternatively, Kickstarter can use this analysis to allocate money towards marketing channels that will bring in more backers and entrepreneurs in categories that have a track record of success.

-- Finds the three pieces of information first displayed on Kickstarter's homepage
SELECT COUNT(ID) AS Projects_Funded, SUM(usd_pledged) AS Towards_Creative_Work, SUM(backers) AS Pledges
FROM `winged-record-348816.kickstarter_project.data`
WHERE state = 'successful' OR state = 'failed'

-- We'll now start by querying the main_categories column to look for successful categories

-- Groups successes and failures by main_category
SELECT main_category,state, COUNT(state) AS total
FROM `winged-record-348816.kickstarter_project.data`
WHERE state = 'successful' OR state ='failed'
GROUP BY state, main_category
ORDER BY main_category

-- Finds the Top 10 main_category (s) with the highest number of successful campaigns
SELECT main_category,state, COUNT(state) AS total
FROM `winged-record-348816.kickstarter_project.data`
WHERE state = 'successful' OR state ='failed'
GROUP BY state, main_category
ORDER BY state desc, total desc
Limit 10

-- Finds the Top 10 main_categories that had the most pledged per campaign (on average)
SELECT main_category, ROUND(AVG(usd_pledged),2) AS average_per_main_category
FROM `winged-record-348816.kickstarter_project.data`
WHERE state = 'successful' OR state ='failed'
GROUP BY main_category
ORDER BY average_per_main_category desc
LIMIT 10

-- Finds the main_categories and their average differential between goals and pledged amounts.
SELECT main_category, ROUND(AVG((usd_pledged/pledged)*(pledged-goal)),2) AS average_usd_above_goal
FROM `winged-record-348816.kickstarter_project.data`
WHERE state = 'successful' OR state ='failed' AND pledged > 0
GROUP BY main_category
ORDER BY average_usd_above_goal desc
LIMIT 10
-- Games is the only main_category that has an average above 0 USD when at least one person pledged

-- Finds the average differential below/above goal on **successful campaigns**
SELECT main_category, ROUND(AVG((usd_pledged/pledged)*(pledged-goal)),2) AS average_usd_above_goal
FROM `winged-record-348816.kickstarter_project.data`
WHERE state = 'successful'
GROUP BY main_category
ORDER BY average_usd_above_goal desc
LIMIT 10

-- With each main category consisting of multiple categories, we'll quickly look into the most and least popular categories
-- Finds the Top 20 most successful categories that have more than 10 campaigns
SELECT category,COUNT(category) AS total, ROUND((COUNTIF(state ='successful')/COUNT(category)),2) AS category_success_rate
FROM`winged-record-348816.kickstarter_project.data`
WHERE state = 'successful' OR state ='failed'
GROUP BY category
HAVING COUNT(category) > 10
ORDER BY category_success_rate desc
LIMIT 20
 
-- Finds the Top 20 least successful categories tha have more than 10 campaigns
SELECT category,COUNT(category) AS total, ROUND((COUNTIF(state ='failed')/COUNT(category)),2) AS category_failure_rate
FROM`winged-record-348816.kickstarter_project.data`
WHERE state = 'successful' OR state ='failed'
GROUP BY category
HAVING COUNT(category) > 10
ORDER BY category_failure_rate desc
LIMIT 20

-- This table combines the average_usd_above_goal and main_category_success_rate columns to find additional/patterns
SELECT main_category, COUNTIF(state = 'successful') AS total_successful,COUNT(main_category) AS total, ROUND((COUNTIF(state ='successful')/COUNT(main_category))*100,2) AS main_category_success_rate, ROUND(AVG((usd_pledged/NULLIF(pledged,0))*(pledged-goal)),2) AS average_usd_above_goal
FROM`winged-record-348816.kickstarter_project.data`
WHERE state = 'successful' OR state ='failed'
GROUP BY main_category
HAVING COUNT(main_category) > 10
ORDER BY main_category_success_rate desc, average_usd_above_goal desc
LIMIT 20

-- Finds the most successful main_categories by average number of backers. Also includes the average pledged by category, and total number of backers
SELECT main_category, SUM(backers) AS total_number_of_backers, AVG(backers) AS average_per_campaign, ROUND(AVG(usd_pledged),2) AS average_pledged_per_main_category
FROM `winged-record-348816.kickstarter_project.data`
GROUP BY main_category
ORDER BY average_per_campaign desc

-- Finds the TOP 10 most-backed main_categories
SELECT SUM(backers)
FROM `winged-record-348816.kickstarter_project.data`
-- equal to 2378111

SELECT main_category, SUM(backers) AS backers_per_main_category, ROUND((SUM(backers)/2378111)*100,2) AS percent_of_total
FROM `winged-record-348816.kickstarter_project.data`
GROUP BY main_category
ORDER BY backers_per_main_category desc
LIMIT 10

-- Now we'll query into other fields such as country, 

-- Finds the success rates of Kickstarter campaigns in each country
SELECT COUNT(ID)
FROM `winged-record-348816.kickstarter_project.data`
WHERE state = 'successful'
-- Equal to 8,622

SELECT country, COUNT(country) AS total_successful, ROUND((COUNT(country)/8622)*100,2) AS percent_of_total_successful_projects, SUM(usd_pledged) AS total_usd_pledged
FROM `winged-record-348816.kickstarter_project.data`
WHERE state = 'successful'
GROUP BY country
ORDER BY total_successful desc

-- Finds the difference in success rates between goal amounts. This only includes goals above $1,000.
SELECT goal, COUNTIF(state = 'successful') AS total_successful, COUNT(goal) AS total, ROUND((COUNTIF(state = 'successful')/COUNT(state))*100,2) AS success_rate
FROM `winged-record-348816.kickstarter_project.data`
WHERE state = 'successful' OR state = 'failed'
GROUP BY goal
HAVING COUNT(goal) > 10 AND goal > 1000
ORDER BY success_rate desc

-- Finds the Top 5 Kickstarter campaigns with the most pledged (USD)
SELECT name,goal, main_category, category, usd_pledged, backers
FROM`winged-record-348816.kickstarter_project.data`
ORDER BY pledged desc
LIMIT 10
-- The number of backers was correlated with the amount pledged
-- 7 were Games, 2 were Technology, 1 was Design

Finds if there is a correlation between the total number of campaigns in a country and the overall success rate of the country.  
SELECT country, COUNTIF(state = 'successful') AS total_successful, COUNTIF(state = 'successful') + COUNTIF(state = 'failed')  AS total, ROUND(COUNTIF(state = 'successful')/(NULLIF(COUNTIF(state = 'successful') + COUNTIF(state = 'failed'),0))*100,2) AS success_rate_by_country
FROM `winged-record-348816.kickstarter_project.data`
WHERE state = 'successful' or state = 'failed'
GROUP BY country
ORDER BY percent_of_total_successful_projects desc

Finds the average length of a successful campaign
WITH date_data AS
(
SELECT*, CAST(EXTRACT(date FROM launched) AS date) AS date_started, CAST(EXTRACT(date FROM deadline) AS date) AS date_finished
FROM `winged-record-348816.kickstarter_project.data`
)
SELECT ROUND(AVG(DATE_DIFF(date_finished, date_started, DAY)),2) AS average_successful_campaign_length
FROM date_data
WHERE state = 'successful'
-- 32.2 days
-- Average failed campaign length is 35.49 days, not a significant difference

WITH date_data AS
(
SELECT*, CAST(EXTRACT(date FROM launched) AS date) AS date_started, CAST(EXTRACT(date FROM deadline) AS date) AS date_finished
FROM `winged-record-348816.kickstarter_project.data`
)
SELECT main_category, AVG(ROUND((DATE_DIFF(date_finished, date_started, DAY)),2)) AS average_campaign_length, ROUND(COUNTIF(state = 'successful')/(NULLIF(COUNTIF(state = 'successful') + COUNTIF(state = 'failed'),0))*100,2) AS percent_of_total_successful_projects
FROM date_data
WHERE state = 'successful' OR state = 'failed'
GROUP BY main_category
ORDER BY average_campaign_length desc
