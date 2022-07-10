What do I want to find out? 
- Find the main_categories that had the largest average donations           ##DONE
- What main_categories had the most money pledged by average of category?   ##DONE
- What main_categories had the largest overages from their goal?            ##DONE
- The most popular categories and main categories                           ##DONE
- The most successful categories and main categories                        ##DONE
- Percentage of countries, success by country                               ##DONE
- Graph that shows the average timeline of a successful project, compares it to the amount asked for
- What goal was most successful?                                            ##DONE
- Does the goal affect the success of the campaign?
- What are the 5 most successful campaigns in terms of USD raised?
- What are the 5 most successful campaigns in terms of # of backers?
- What is the average goal for a successful campaign vs a failed campaign?
- Which countries have the most successful campaigns?

Tableau:
- Main_Categories
- Category
- Geography
- Timeline

1. Show your thought process: This suggests, Let's check etc.. put your thoughts onto paper
2. Change percent to actual percentages (*100)


-- I used only successful or failed in my analysis to rule out external factors. There were additional campaigns that were labeled as still live and others that were suspended. Because we don't know why they were suspended, we will not count them as failed. The goal is to find the general trends behind successful campaigns and unsuccessful kickstarter campaigns.

DELETE  
FROM `winged-record-348816.kickstarter_project.data`
WHERE ID IS NULL# kickstarter

## Which main_category (s) had the most pledged per campaign on Average
SELECT main_category, ROUND(AVG(usd_pledged),2) AS average_pledged_per_category
FROM `winged-record-348816.kickstarter_project.data`
GROUP BY main_category
ORDER BY average_pledged_per_category desc

## Groups successes and failures by main_category
SELECT main_category,state, COUNT(state) AS total
FROM `winged-record-348816.kickstarter_project.data`
WHERE state = 'successful' OR state ='failed'
GROUP BY state, main_category
ORDER BY main_category

## Shows the Top 10 main_category (s) with the highest number of successful campaigns
SELECT main_category,state, COUNT(state) AS total
FROM `winged-record-348816.kickstarter_project.data`
WHERE state = 'successful' OR state ='failed'
GROUP BY state, main_category
ORDER BY state desc, total desc
Limit 10

## Shows the main_categories that had the most pledged on average
SELECT main_category, ROUND(AVG(usd_pledged),2) AS average_per_main_category
FROM `winged-record-348816.kickstarter_project.data`
WHERE state = 'successful' OR state ='failed'
GROUP BY main_category
ORDER BY average_per_main_category desc
LIMIT 10

### Shows the main_categories and their average differential between goals and pledged amounts. Games is the only main_category that has an average above 0 USD when at least one person pledged

SELECT main_category, ROUND(AVG((usd_pledged/pledged)*(pledged-goal)),2) AS average_usd_above_goal
FROM `winged-record-348816.kickstarter_project.data`
WHERE state = 'successful' OR state ='failed' AND pledged > 0
GROUP BY main_category
ORDER BY average_usd_above_goal desc
LIMIT 10

## Shows the average differential above goal on successful campaigns
SELECT main_category, ROUND(AVG((usd_pledged/pledged)*(pledged-goal)),2) AS average_usd_above_goal
FROM `winged-record-348816.kickstarter_project.data`
WHERE state = 'successful'
GROUP BY main_category
ORDER BY average_usd_above_goal desc
LIMIT 10

## Shows the Top 20 most successful main_categories that have more than 10 campaigns
SELECT main_category,COUNT(main_category) AS total, ROUND((COUNTIF(state ='successful')/COUNT(category)),2) AS main_category_success_rate
FROM`winged-record-348816.kickstarter_project.data`
WHERE state = 'successful' OR state ='failed'
GROUP BY main_category
HAVING COUNT(main_category) > 10
ORDER BY main_category_success_rate desc
LIMIT 20
 
## Shows the Top 20 least successful categories tha have more than 10 campaigns
SELECT main_category,COUNT(main_category) AS total, ROUND((COUNTIF(state ='failed')/COUNT(category)),2) AS main_category_failure_rate
FROM`winged-record-348816.kickstarter_project.data`
WHERE state = 'successful' OR state ='failed'
GROUP BY main_category
HAVING COUNT(main_category) > 10
ORDER BY main_category_failure_rate desc
LIMIT 20

#### This combines the average_usd_above_goal and main_category_success_rate
SELECT main_category, COUNTIF(state = 'successful') AS total_successful,COUNT(main_category) AS total, ROUND((COUNTIF(state ='successful')/COUNT(main_category)),2) AS main_category_success_rate, ROUND(AVG((usd_pledged/NULLIF(pledged,0))*(pledged-goal)),2) AS average_usd_above_goal
FROM`winged-record-348816.kickstarter_project.data`
WHERE state = 'successful' OR state ='failed'
GROUP BY main_category
HAVING COUNT(main_category) > 10
ORDER BY main_category_success_rate desc, average_usd_above_goal desc
LIMIT 20

#### Most successful categories by average number of backers. Also includes the average pledged by category, and total number of backers
SELECT main_category, SUM(backers) AS total_number_of_backers, AVG(backers) AS average_per_campaign, ROUND(AVG(usd_pledged),2) AS average_pledged_per_main_category
FROM `winged-record-348816.kickstarter_project.data`
GROUP BY main_category
ORDER BY average_per_campaign desc

#### Shows the TOP 10 most-backed main_categories, could be used for a pie chart
SELECT SUM(backers)
FROM `winged-record-348816.kickstarter_project.data`
-- equal to 2476267

SELECT main_category, SUM(backers) AS backers_per_main_category, ROUND(SUM(backers)/2476267,2) AS percent_of_total
FROM `winged-record-348816.kickstarter_project.data`
GROUP BY main_category
ORDER BY backers_per_main_category desc
LIMIT 10

#### Shows the percentage of successful campaigns by country
SELECT COUNT(ID)
FROM `winged-record-348816.kickstarter_project.data`
WHERE state = 'successful'
--Equal to 8,622

SELECT country, COUNT(country) AS total_successful, ROUND(COUNT(country)/8622,2) AS percent_of_total_successful_projects
FROM `winged-record-348816.kickstarter_project.data`
WHERE state = 'successful'
GROUP BY country
ORDER BY total_successful desc

#### Checking to see if there is a large difference between success rates between 

