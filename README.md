What do I want to find out?
- Add a column for average donation
- The most popular categories and main categories
- The most successful categories and main categories
- Percentage of currency used
- Graph that shows the average timeline of a successful project, compares it to the amount asked for
- What goal was most successful?
- What categories had the most money pledged by average of category?
- Does the goal affect the success of the campaign?
- What are the 5 most successful campaigns in terms of USD raised?
- What are the 5 most successful campaigns in terms of # of backers?
- What is the average goal for a successful campaign vs a failed campaign?
- Which countries have the most successful campaigns?


DELETE  
FROM `winged-record-348816.kickstarter_project.data`
WHERE ID IS NULL# kickstarter

## Which categories had the most pledged per campaign on Average

SELECT category, ROUND(AVG(usd_pledged),2) AS average_pledged_per_category
FROM `winged-record-348816.kickstarter_project.data`
GROUP BY category
ORDER BY average_pledged_per_category desc

## Which main_categories had the most pledged per campaign on Average

SELECT main_category, ROUND(AVG(usd_pledged),2) AS average_pledged_per_main_category
FROM `winged-record-348816.kickstarter_project.data`
GROUP BY main_category
ORDER BY average_pledged_per_main_category desc

## Groups successes and failures by category and main_category
SELECT category,state, COUNT(state) AS total
FROM `winged-record-348816.kickstarter_project.data`
WHERE state = 'successful' OR state ='failed'
GROUP BY state, category
ORDER BY category

OR

SELECT main_category,state, COUNT(state) AS total
FROM `winged-record-348816.kickstarter_project.data`
WHERE state = 'successful' OR state ='failed'
GROUP BY state, main_category
ORDER BY main_category

## Shows the Top 10 categories with the highest number of successful campaigns
SELECT category,state, COUNT(state) AS total
FROM `winged-record-348816.kickstarter_project.data`
WHERE state = 'successful' OR state ='failed'
GROUP BY state, category
ORDER BY state desc, total desc
Limit 10

## 
