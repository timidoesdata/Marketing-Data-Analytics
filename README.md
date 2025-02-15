# Marketing-Data-Analytics 
As a junior data analyst on the Bellabeat marketing team, I got into some smart device data to drive strategic marketing decisions, and provide invaluable insights to the Bellabeat executive team using the structured roadmap of the Ask, Prepare, Process, Analyze, Share, and Act phases.

## Meet Bellabeat
Bellabeat was founded in 2013 by Urška Sršen and Sando Mur, crafting cutting-edge health-centric smart products tailored for women. With a fusion of artistry and technology, their products— Leaf, Time, and Spring, sync seamlessly with the Bellabeat app, tracking activity, sleep, stress, water intake and more. The company's mission revolves around empowering women with insights into their health habits. 
**Stakeholders:**
- Urška Sršen: Cofounder and Chief Creative Officer.
- Sando Mur:  Cofounder.
- Marketing team

![image](https://github.com/user-attachments/assets/3b3a9582-0477-47e8-a26b-0a54025fb181)

## Ask Phase
**Business Task/Goal:**
- Understand how customers use Bellabeat smart devices by identifying trends such as usage pattern, user behaviour, user demographic, features they mostly engage with, when they use their devices and more. 
- Uncover insights to inform business decision such as how to better target customers, improve user experience, drive company growth and ultimately inform marketing strategy.

## Prepare Phase
The dataset I’m working with is the [FitBit Fitness Tracker Data](https://www.kaggle.com/arashnic/fitbit), which is publicly available on [Kaggle](http://kaggle.com/) courtesy of MÖBIUS. It was gathered through a survey conducted on the Amazon Mechanical Turk platform, involving 30 eligible participants who agreed to share their FitBit device data. This comprehensive data covers their physical activity intensity, step count, heart rate, and sleep habits and was collected into 18 spreadsheets. 

The datasets I used in this analysis include;
- dailyActivity_merged 
- sleepDay_merged
- dailyCalories_merged
- hourlysteps_merged

I downloaded the datasets and examined each of them; however, I chose these four above out of many because the dailyActivity_merged dataset already contained the necessary columns in other datasets except hourly activity data and sleep activity.

**Limitations:**
- The data provided lacked essential information such as age, country, gender, or city which would be beneficial for generating insights towards specific demographics, and consequently inform targeting strategy. 
- Its sample size of 30 was not feasible for extensive results. 
- Data only showed customer behaviour for a month, which may not be indicative of how people use FitBit/health tracker apps throughout the year, giving a limited overview
- A lot has changed since 2016, therefore the data may be considered outdated relative to current trends, especially post-lockdown.

## Process 
**Cleaning and manipulating data using Microsoft Excel**

**1. Removing duplicates**
- I checked for duplicates across the datasets using the ‘remove duplicates’ tool in the ‘data’ ribbon in Excel; only sleepDay_merged had (3) duplicates which I deleted. I also saved a duplicate of the datasets for reference if need be. 

 **2. dailyActivity_merged** 
- I deleted the SedentaryActiveDistance, LightActiveDistance, ModeratelyActiveDistance, LoggedActivitiesDistance and VeryActiveDistance columns as they are not relevant to the requirements 
- I updated the data format of each column, and created a new colum “Weekday” to reveal days of the week from the “ActivityDate” column using the function TEXT(B2, “dddd”) and affected for all rows. 
- I created an “AverageMinutesActive” column to determine the average time of activity during the day from the intense to light spectrum.
- I created a “DailyDistance” column using the average of the “Total Distance” and “TrackerDistance”; I know they are the same however I did this to salvage/avoid oversights. 

 **3. sleepDay_merged**
- Updated each column into the appropriate data type, and changed sleepDay column from date-time format to date-only format
- I deleted the “TotalTimeInBed” column

 **4. dailyCalories_merged**
- I set each column to the appropriate data type
- I created a new column for "Weekday" and using the formula "=TEXT(cell, "dddd")," I extractedd the days of the week from the date column

 **5. hourlySteps_merged**
- I removed duplicates, corrected data types and created a new column for "Time of day" to split the times into "morning, afternoon, evening."
- Using the VLOOKUP function, I split time ranges into different times of the day. 

## Analyze
- I imported each dataset into the “FitBit” database I created in MS SQL
- I determined the number of participants for each table using SELECT DISTINCT and found out there were 33 respondents, as opposed to 30 which was initially stated; however, there were inconsistent inputs and missed days. 
- To work with the IDs more easily, I assigned a serial number to each from 1-33.

*I visualized in Calendar View using Power BI, and the rest using Excel*

**1. First, I determined the rate of responses**
- Activity responses per day<br>
```
SELECT ActivityDate, COUNT(Id_No) AS no_of_users FROM dailyActivity
GROUP BY ActivityDate
ORDER BY ActivityDate
```
![image](https://github.com/user-attachments/assets/fec3ac85-ad5b-4516-91b0-b4e7df0c9dab)

From the visualization above, I noted that only 21 users out of 33 recorded their activity consistently for 31 days. 

- Sleep records per day
```
SELECT SleepDay, COUNT(Id_No) AS no_of_users FROM DailyActivity
GROUP BY SleepDay
ORDER BY SleepDay
```
![image](https://github.com/user-attachments/assets/903585a6-30ec-4e8e-9f19-b9de79281eda)

From the visualization, I can easily note the fluctuations in how often users recorded their sleep data. The most amount of users who recorded sleep data was 17, while the least was only 8. 

Next, I was interested in learning how often users interacted with FitBit according to days of the week.
- Usage per day of the week
```
-- Acitivty
SELECT DISTINCT Weekday, COUNT(Id) AS use_freq FROM DailyActivity
GROUP BY Weekday
ORDER BY use_freq ASC

--Sleep record
SELECT DISTINCT Weekday, COUNT(Id) AS use_freq FROM DailySleep
GROUP BY  Weekday
ORDER BY use_freq ASC
```
![image](https://github.com/user-attachments/assets/e65e461c-d998-4bd1-96c0-165d16498d5d)

From the charts above, Tuesdays received the most activity record, while Wednesdays received the most sleep records. Mondays had the least records in both areas, with Sundays closely ahead. 

**2. Next, I determined the user behavior based on their records.**
- Average steps by day of the week
```
SELECT Weekday, AVG(TotalSteps) AS average_daily_steps FROM dailyActivity
WHERE TotalSteps > 0
GROUP BY Weekday
ORDER BY average_daily_steps DESC
```
![image](https://github.com/user-attachments/assets/86f3e904-bdf4-4f78-a258-5a768780041f)

People are taking the most steps on Tuesdays and Saturdays, but walking the least on Sundays and Fridays.

- Average calories burned per days of the week
```
SELECT Weekday, ROUND(AVG(Calories),0) AS avg_calories_burned
FROM dailyCalories
WHERE Calories > 0
GROUP BY Weekday
```
![image](https://github.com/user-attachments/assets/412fe7aa-ec65-4035-8e48-649b9689bcf8)

Similar to how often users took steps per days of the week, they burned the most calories on Tuesdays and Saturdays, however they burned the least on Sundays and Thursdays.

I visualized the correlation between steps taken and calories burned using a scatter plot, since you tend to burn more calories with activity such as walking. 

![image](https://github.com/user-attachments/assets/f3a37e56-61d0-447f-8d4a-8654fb4e8a50)

Next, I determined avg. sleeping patterns by days of the week
- Sleep levels per days of the week
```
SELECT Weekday, AVG(TotalMinutesAsleep) AS average_sleep_time FROM dailySleep
WHERE TotalSleepRecords > 0 AND TotalMinutesAsleep > 0
GROUP BY Weekday
ORDER BY average_sleep_time DESC
```
![image](https://github.com/user-attachments/assets/24978ed7-4eb5-4de2-a6ff-522c5405bdfa)

Users appeared to rest the most on Sundays, which is also the day of the week when they walked the least and burned minimum calories. 

- Activity levels by time of the day
```
SELECT Time_of_day, AVG(StepTotal) AS avg_steps
FROM hourlySteps
WHERE StepTotal > 0
GROUP BY Time_of_day
ORDER BY avg_steps
```
![image](https://github.com/user-attachments/assets/9571e600-98ea-4c74-8d49-108d9a27a917)

Users were more active during in the afternoon, between the hours of 12PM and 5PM. 

3. I categorized activity levels based on [health sources guidelines](https://www.medicinenet.com/how_many_steps_a_day_is_considered_active/article.htm) which categorizes users based on their average daily steps:

- Sedentary: Less than 5,000 steps daily
- Low active: About 5,000 to 7,499 steps daily
- Somewhat active: About 7,500 to 9,999 steps daily
- Active: More than 10,000 steps daily
- Highly active: More than 12,500 steps daily

```
WITH avg_steps_per_id AS 
(SELECT Id, AVG(TotalSteps) AS avg_steps
FROM dailyActivity
WHERE TotalSteps > 0
GROUP BY Id)
SELECT Id, avg_steps,
           CASE
           WHEN avg_steps > 12500 THEN 'highly active'
           WHEN avg_steps BETWEEN 10000 AND 12499 THEN 'active'
           WHEN avg_steps BETWEEN 7500 AND 9999 THEN 'somewhat active'
           WHEN avg_steps BETWEEN 5000 AND 7499 THEN 'low active'
           WHEN avg_steps < 5000 THEN 'sedentary'
           ELSE 'basal'
END AS Activity_levels
FROM avg_steps_per_id
ORDER BY Id 
```
![image](https://github.com/user-attachments/assets/0c012cdb-1df6-46ef-ac13-1ed6420c473a)

The least percentage of users consisting of 9 respondents have a highly active lifestyle, however most users are somewhat active. 

## Act Phase 

**Summary & Recommendations**
- Users take the most steps on Tuesdays and Thursdays, and take the least steps on Sundays and Fridays
- Users are mostly active between noon and 5PM
- Most users live somewhat active, and low active lifestyles
- Users burn more calories the more they walk 

*My conclusions are limited by constraints such as a small sample population of 33 respondents and a short time frame of one month. This would have had more depth and expansiveness if data with longer time period, recency and more users was provided.*

**Based on these insights, I recommend that Bellabeat;**
- Send tailored Promotions on Tuesdays and Thursdays: Since users are most active on these days, consider offering special promotions, discounts, or challenges specifically on Tuesdays and Thursdays. This could incentivize users to engage more with the app/device during their peak activity times.
- Encourage Midday Activity: Since most users are active between noon and 5 PM, emphasize the benefits of midday workouts in your marketing messages. Highlight features like quick workout routines, lunchtime challenges, or reminders to get up and move during the workday.

- Provide personalized activity plans: Recognizing that most users have somewhat active or low active lifestyles, offer personalized activity plans tailored to their fitness levels. This could include beginner-friendly workout routines, walking challenges with gradually increasing intensity, and achievable goals to motivate users to stay active. Encourage users to track their fitness and lifestyle to categorize their present activity levels and set goals for an improved level

- Include calorie tracking incentives: Users burn more calories the more they walk, therefore emphasize the calorie-tracking feature of the app/device. Provide real-time feedback on calories burned during walks and offer incentives or rewards for reaching calorie-burning milestones. This could include virtual badges, achievements, or discounts on fitness-related products. Emphasize on the benefits of walking to burn more calories, zeroing in on weight loss, weight maintenance and general fitness.

- Social Media Integration: Utilize social media platforms to showcase user success stories, share fitness tips, and promote the app/device. Encourage users to share their achievements on social media using specific hashtags or tagging the app's official accounts. This can help attract new users and strengthen the existing user community.
- Collaborations: Collaborate with fitness influencers, wellness experts, or local gyms to expand your reach and credibility. Partnering with influencers who resonate with your target audience can help amplify your marketing efforts and attract new users who are interested in fitness and wellness.

- Others: Send users pop-ups / notifications on Sundays and Fridays, and between 12PM and 5PM to take a walk. Also send notifications during low activity hours (after 5PM) for users to track their sleeping habits before bed.

---
Let me know what you think about this analysis. <br>
Please feel free to reach out to me on [LinkedIn](https://www.linkedin.com/in/hellotimilehin/) or via [Email](hellotimilehin@gmail.com)













