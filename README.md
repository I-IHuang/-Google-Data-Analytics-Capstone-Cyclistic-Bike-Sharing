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
    
![image](https://github.com/user-attachments/assets/ec630401-96f0-4b45-a8d8-39f8a4eba06c)
![image](https://github.com/user-attachments/assets/fca8c8bd-7c93-4ce2-a753-f871528db1e1)

#### Data Quality
- **Duplicate Ride IDs**: A total of 636 duplicate `ride_id`s were identified, which could indicate system errors or duplicated entries that should be addressed during data cleaning.
- **Inconsistent Ride ID Lengths**: The majority of `ride_id`s are 16 characters long (5,698,210), but a few (80 and 14) have 8 or 9 characters, respectively. This inconsistency suggests a possible change in the ID generation system or data integration from different sources.
- 
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

## Data Analysis
SQL Query: [Data Analysis](#Data-Analysis)

The data was first analyzed using SQL in BigQuery to calculate key metrics for an initial understanding. After that, the data was loaded into Power BI Desktop for visualization and further insights.

This analysis explores various patterns in ride-sharing data, comparing the behavior of casual riders versus annual members across different rideable types, times, and stations.

### Key Insights:
- Casual riders show a preference for classic bikes and electric bikes, with the highest activity in Summer and Saturday afternoons.
- Annual members also prefer classic bikes and electric bikes, with peak activity in Summer and Wednesday afternoons.
- The busiest stations reflect patterns in rider preferences, with Streeter Dr & Grand Ave being the top station for casual riders, and Kingsbury St & Kinzie St for annual members.

### Total Rides by Rideable Type and Member Type
- Casual and member riders primarily use classic bikes and electric bikes, with very few opting for electric scooters.

### Rides by Season
- Summer is the busiest season for both casual and member riders, with a significant difference in ride volume between the two groups.
- Casual riders tend to have longer ride durations in the summer, whereas member riders have shorter durations, particularly in the winter.

### Rides by Month
- The most active months for casual riders are in the mid-year, especially in July and August.
- For member riders, August sees the peak in rides.
- Casual riders have the longest ride duration in May, while member riders also have their longest rides in May.

### Rides by Weekday
- For casual riders, Saturday is the busiest day of the week, while members favor Wednesday.
- Casual riders tend to have longer ride durations on Sundays, whereas members ride the longest on Saturdays.

### Rides by Time of Day
- Both casual and member riders predominantly ride in the afternoon.
- Casual riders have longer average ride durations in the afternoon, while member riders have shorter durations in the evening.

### Rides by Hour
- The peak hour for both casual and member riders is 5:00 PM, with casual riders having slightly fewer rides than members at that time.
- Casual riders have the longest average ride duration in the morning, while member riders have the shortest duration during the peak hour.

### Top Start Stations
- Casual riders start their rides at a few popular stations in the city, with central locations being the most frequented.
- Member riders also have a few top stations they use, which differ slightly from casual riders' preferences.

### Average Ride Duration by Season and Member Type
- Casual riders tend to have the longest ride durations during the summer season, while member riders consistently have shorter ride durations, with the shortest in winter.

## Key Takeaways:
- Casual riders show a strong preference for classic bikes and electric bikes, with their highest activity in summer and on Saturdays.
- Member riders have a similar preference for bike types but tend to ride more frequently, with shorter durations, particularly in winter.
- The afternoon is the busiest time for both groups, with the peak hour being 5:00 PM.
- Popular stations and times reflect the different riding behaviors of casual and member riders, with casual riders gravitating toward central city locations and member riders using more varied routes.

