# Google Data Analytics Capstone Project - Cyclistic Bike-Sharing 

## Introduction
In this case study, I take on the role of a junior data analyst, using the Ask, Prepare, Process, Analyze, Share, and Act framework to analyze user behavior and identify insights that can help shape marketing strategies to increase memberships.


### Quick Links:
1. [Handling Large File Uploads.md](Handling%20Large%20File%20Uploads.md)
2. [Data Combining](https://github.com/I-IHuang/Google-Data-Analytics-Capstone-Cyclistic-Bike-Sharing/blob/8b92ca34ab2b9da7c06235d074cb982913e35c5c/02.%20Data%20Combining.md)
3. [Data Exploration](https://github.com/I-IHuang/Google-Data-Analytics-Capstone-Cyclistic-Bike-Sharing/blob/8b92ca34ab2b9da7c06235d074cb982913e35c5c/03.%20Data%20Exploration.md)
4. [Data Cleaning and Transformation](https://github.com/I-IHuang/Google-Data-Analytics-Capstone-Cyclistic-Bike-Sharing/blob/8b92ca34ab2b9da7c06235d074cb982913e35c5c/03.%20Data%20Exploration.md)
5. [Data Analysis](https://github.com/I-IHuang/Google-Data-Analytics-Capstone-Cyclistic-Bike-Sharing/blob/8b92ca34ab2b9da7c06235d074cb982913e35c5c/03.%20Data%20Exploration.md)


## Company Background
Cyclistic, a fictional bike-sharing company in Chicago, has two main customer groups: casual riders and annual members. The company’s leadership is focused on finding ways to convert more casual riders into annual subscribers to boost long-term growth and customer loyalty.

### Overall Goal:
Identify key behavioral differences between casual and annual riders to support strategic decisions that convert casual riders into annual subscribers, encouraging long-term membership and brand loyalty.

## ASK: Business Question/Stakeholder
Three questions will guide the future marketing program:
1. How do annual members and casual riders use Cyclistic bikes differently?
2. Why would casual riders buy Cyclistic annual memberships?
3. How can Cyclistic use digital media to influence casual riders to become members?

Lily Moreno, the director of marketing and your manager, has assigned you the first question.

## Prepare: Data Uploading & Combining

### Data Source
For this analysis, I use Cyclistic’s historical trip data from Sep 2023 to Aug 2024, sourced from the [divvy_tripdata dataset](https://divvy-tripdata.s3.amazonaws.com/index.html), provided by Motivate International Inc. under a [public license](https://divvybikes.com/data-license-agreement). Note that while this data is publicly available, personally identifiable information is not included to protect user privacy. Data is stored securely and anonymized to maintain privacy. I have also verified integrity of the data and checked if the data is ROCCC, ensuring its completeness and consistency for analysis.

### Data Organization
The data is structured into monthly CSV files and includes details like user type, station information, start and end station coordinates, and user type. Some records have missing station names or coordinates (17%), but these gaps do not significantly affect the overall data quality.

### Data Cleaning, Transformation, and Processing Tool:
I chose SQL in BigQuery to clean and transform the data because it efficiently handles large-scale datasets—like the 5.6 million+ rows in this project—far beyond Excel’s capacity, while enabling faster processing, automation, and reproducibility.

### Analysis and Visualization Tool:
For visualization, I selected Power BI to align with my learning goals and the upcoming transition at my current company from Tableau to Power BI. Its user-friendly interface and integration with other tools make it ideal for in-depth analysis and reporting.

### Handling Large File Uploads in BigQuery
PowerShell command: [Handling Large File Uploads.md](Handling%20Large%20File%20Uploads.md)

Due to BigQuery’s 100 MB file size limit for uploading, I split each monthly file into two separate CSVs:
- Main Trip Data (YYYYMM.csv)
- Station Info (YYYYMM_Station.csv)

I initially dropped station and coordinate data from each monthly CSV, thinking it wasn't needed. Later, I realized it could provide key insights (e.g., common start/end points), so I extracted and saved it separately. Each file keeps `ride_id` as the primary key, enabling easy joins in BigQuery.

### Data Combining:
SQL Query: [Data Combining](#Data-Combining)

All files were initially loaded as separate tables within the dataset ‘Capstone_Bike’. These tables were then consolidated into a single unified table named `complete_table`, which contains 5,698,304 rows in total.

## Process: Data Exploration & Cleaning/Transformation

### Data Exploration
- **General Overview**: The dataset comprises a total of 5,698,304 ride records and 13 columns before data cleaning.
  
![image](https://github.com/user-attachments/assets/06bd00d1-73d2-43d9-a00a-8c1f0f6d85be)

#### Schema and Data Types
- Columns such as `ride_id`, `rideable_type`, station names, and IDs are stored as `STRINGs`.
- Time-related fields (`started_at`, `ended_at`) are stored as `TIMESTAMPs`.
- Geographic coordinates (`start_lat`, `start_lng`, `end_lat`, `end_lng`) are in `FLOAT64` format.
These appropriate data types will support time and geospatial analysis without requiring transformation.

![image](https://github.com/user-attachments/assets/802800cf-01e1-4fef-9552-cc0770621f3a)

#### Key Variables
- The member_casual variable divides users into two groups:
  - **Annual members**: 3,652,758 rides
  - **Casual riders**: 2,045,546 rides
    
 ![image](https://github.com/user-attachments/assets/14573c66-395b-4eee-864f-51699cb6d85d)


#### Missing & Blank Values
- **Start and End Station Data**:
  - Around 17% (968,463 rows) of records are missing both `start_station_name` and `start_station_id`.
  - Approximately 17.66% (1,006,059 rows) of records are missing both `end_station_name` and `end_station_id`.
  - `start_lat` and `start_lng` have no missing values, ensuring that trip origins are well documented.
  - However, `end_lat` and `end_lng` are missing in 7,656 rows (~0.13%).
  **Other Fields**:
  - No null or blank values were found for `ride_id`, `rideable_type`, `started_at`, `ended_at`, or `member_casual`, indicating reliable tracking and user categorization for most trips
    
![image](https://github.com/user-attachments/assets/570dd799-96fb-4305-9235-cec2ba90cc91)
![image](https://github.com/user-attachments/assets/fca8c8bd-7c93-4ce2-a753-f871528db1e1)

#### Data Quality
- **Duplicate Ride IDs**: A total of 636 duplicate `ride_id`s were identified, which could indicate system errors or duplicated entries that should be addressed during data cleaning.
- **Inconsistent Ride ID Lengths**: The majority of `ride_id`s are 16 characters long (5,698,210), but a few (80 and 14) have 8 or 9 characters, respectively. This inconsistency suggests a possible change in the ID generation system or data integration from different sources.

 ![image](https://github.com/user-attachments/assets/ad13d256-450c-4c99-8288-d6a88d3da41f)

- **Outliers in Ride Duration**:
  - 97,476 trips are under 1 minute, which might reflect user errors, test rides, or false starts.
  - 3,613 trips exceed 24 hours, which may represent outliers, system malfunctions, or rare extended rentals that need further investigation or filtering.
   
![image](https://github.com/user-attachments/assets/80e6eb19-6192-4b3d-9ca3-e106cf943a23)

#### Ride Types Distribution
- **Electric bikes**: 2,880,560 rides
- **Classic bikes**: 2,817,662 rides 
- **Electric scooters**: only 82 rides (The first recorded use of electric scooters appears on August 31, 2024, suggesting that scooters were a newly introduced rideable type at that time.)
  
![image](https://github.com/user-attachments/assets/e54891c1-e171-48f5-8ddf-c8ccff02b3ff)

### Data Cleaning
SQL Query: [Data Cleaning](#Data-Cleaning)

- Duplicate records based on `ride_id` were removed.
- Rows with missing values in key fields were excluded.
- Rides shorter than 1 minute or longer than 24 hours were filtered out.
- Only `ride_id`s with exactly 16 characters were retained.
- Entries with `rideable_type = 'electric scooter'` were excluded due to limited data.
- A total of 1,537,877 rows were removed during cleaning.

### Data Transformation:
The following new columns were created:
- `season`
- `month_name`
- `weekday_name`
- `time_of_day`
- `hour`

## Analyzea and Share: Data Analysis
SQL Query: [Data Analysis](#Data-Analysis)

The data was first analyzed using SQL in BigQuery to calculate key metrics for an initial understanding. After that, the data was loaded into Power BI Desktop for visualization and further insights.

To adress the business question, "How do annual members and casual riders use Cyclistic bikes differently?" 
I analyzed the ride-sharing data to find the distinct usage patterns between casual riders and annual members, focusing on bike types, time-based trends, and station preferences.

### 1. What and When Are People Riding? 
-	Annual members completed over 2.68 million rides, nearly double the 1.47 million rides taken by casual riders.
-	Annual members ride year-round, even in winter, showing more consistent, daily use.
  
![image](https://github.com/user-attachments/assets/b4604895-7773-4311-bd04-5c73d944e410)

#### 1.2 Seasonal Trends
-	Casual riders are highly seasonal: they ride most in summer, followed by Autumn, but much less in winter.
-	Annual members ride year-round, even in winter, showing more consistent, daily use.

![image](https://github.com/user-attachments/assets/de181806-93f4-45d4-88b5-00e9583f3b57)

#### 1.3 Monthly Patterns
-	For casual riders, the busiest months are July and August — peak summer — aligning with recreational use and favorable weather.
-	Annual members also peak in August but ride steadily from May to November.
  
![image](https://github.com/user-attachments/assets/ba9b312b-d508-4278-9d99-e219ab149a82)

#### 1.4 Weekday Patterns
- Casual riders prefer the weekends, especially Saturdays, pointing to leisure-focused trips.
- Annual members are most active on weekdays, particularly Wednesdays, suggesting weekday commuting or regular travel routines.

![image](https://github.com/user-attachments/assets/6f298335-56f3-4025-95ac-52df3d9193fb)

####  1.5 Time of Day Preferences
- The peak hour for both casual and member riders is 5:00 PM, with casual riders having slightly fewer rides than members at that time.
- Casual riders have the longest average ride duration in the morning and evening, likely during commuting hours before and after work.
  
![image](https://github.com/user-attachments/assets/603d1e4e-4bd9-4e86-957e-dc2b1f2c511f)

#### Hourly Patterns
-	5 PM is the most popular hour for both groups.
-	Annual members show a secondary peak around 8 AM, reinforcing the commuting hypothesis.

![image](https://github.com/user-attachments/assets/21e02187-78d9-48d2-98fa-3b0216094782)

#### Key Takeaways:
- Members ride more overall and consistently year-round.
- Casual riders peak in summer and on weekends, suggesting leisure use.
- Members ride more on weekdays, especially Wednesdays.
- Peak hour is 5 PM for both groups; members also ride more in the mornings.
- 
### Part 2: How Long Do They Ride?

#### 2.1 Average Ride Duration 
-	Casual riders have an average ride duration of approximately 24 minutes.
-	Annual members average 12 minutes per ride — half the time of casual users.
This suggests different purposes: casual riders likely ride for fun or exploration, while members use the bikes mainly to commute or get somewhere quickly.
![image](https://github.com/user-attachments/assets/551b1e1f-08ba-442b-8324-a9e513a29c7a)


#### 2.2 Seasonal Durations 
- Casual riders take long rides in summer and spring. In winter, durations drop to 18 minutes, likely due to colder weather.
- Members ride for a stable 12-13 minutes, with a slight drop in winter (11 minutes).

![image](https://github.com/user-attachments/assets/449655d0-3b16-49ff-b25e-0d7736a2c77b)

#### 2.3 Monthly Durations
- Casual riders ride longest in May (27 minutes), then shorten in winter.
- Member riders have a similar preference for May, but stay consistent year-round, dropping slightly in winter.

![image](https://github.com/user-attachments/assets/2537458b-360c-4eb2-8b32-9a1d1906c440)

#### 2.4 Weekday Durations
-	Casual riders ride longest on Sundays and Saturday, indicating relaxed weekend outings.
-	Members have their longest average durations on Saturdays, but the difference is minimal across weekdays.

![image](https://github.com/user-attachments/assets/dca8f13a-9321-4fc6-9a73-c260cf83a9d3)

#### 2.5 Time of Day Durations
-	For casual riders, the afternoon is not just the busiest period but also the longest in terms of ride duration.
-	Members have slightly longer ride durations in the evening, though the variation is minimal.
![image](https://github.com/user-attachments/assets/fb0f972d-0324-4fe4-9541-8d00b7648d79)

#### 2.6 Hourly Durations
-	Casual riders ride longest around 11 AM, possibly for brunch or sightseeing.
-	Members take the longest rides at 5 PM, likely heading home from work.
![image](https://github.com/user-attachments/assets/7d2ae255-0413-4bdd-9aeb-9e35ec37a236)

#### Key Takeaways:
- Casual riders have longer rides (~24 mins) vs. members (~12 mins).
- Casual ride durations are highest in summer and spring, especially May.
- Weekends = longer casual rides; members stay consistent.
- Longest rides: Casuals: 11 AM; Members: 5 PM.

### Where Do They Ride From? — Top 5 Popular Start & End Stations
Start Stations:
- Casual Riders:
  - Streeter Dr & Grand Ave and DuSable Lake Shore Dr & Monroe St — both located near popular tourist spots like Navy Pier and Millennium Park.
   - These stations are ideal for casual riders looking to explore Chicago’s scenic landmarks.
- Annual Members:
	- Start Stations:
	 - Kingsbury St & Kinzie St, Clinton St & Washington Blvd — located near business districts and transit hubs, making them convenient for commuting and routine travel.
![image](https://github.com/user-attachments/assets/8bf0685d-f242-4568-833e-6f238f1d3a02)

-	End Stations:
- Casual Riders:
	- Similar to the start stations, these locations are near tourist areas and provide easy access to the Lakefront Trail, museums, and downtown attractions.
- Annual Members:
	- The end stations are similar to the start stations, suggesting round trips or commuting patterns with a focus on business areas and convenient transit options.
![image](https://github.com/user-attachments/assets/7ea2edaa-785e-4ee4-bf02-97acdb283aa5)

Key Takeaways: 
- Casual Riders: Prefer stations near tourist spots, such as Navy Pier and Millennium Park, indicating leisure and recreational use.
- Annual Members: Use stations near business districts and transit hubs, suggesting regular commuting and daily travel.

### ACT: Possible Marketing Strategy to Convert Casual Riders to Members:
- Targeted Promotions: Offer exclusive discounts for casual riders to sign up for annual memberships at popular tourist stations.
- Seasonal Campaigns: Run summer and winter promotions for casual riders. Introduce seasonal trials (e.g., a 3-month summer pass) with a discount for upgrading to an annual membership.
- Reward System: Implement a loyalty program with ride points for membership discounts and a referral program for rewards.

