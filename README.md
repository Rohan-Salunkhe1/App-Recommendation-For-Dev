# AppStore Analysis for DEVs

## Overview

This project aims to analyze AppStore data, addressing key questions about user preferences, app ratings, download trends, and more.
Our stakeholder is an aspiring app developer who needs data-driven insights to decide what type of app to build.

## 1. Ask

### Key Questions:
- What App Categories are most popular?
- What prices should be set?
- How can the user ratings be maximised?

## 2. Prepare And Process

### Data Collection and Cleaning

The AppStore dataset, obtained from Kaggle, presented a challenge due to its size. To overcome this, the dataset was divided into four manageable files. These files were then loaded into SQLite online for initial processing, and subsequently combined to create a unified dataset for analysis.

#### Data Collection:

- **Source of AppStore Data:**
  - The dataset was originally downloaded from Kaggle.
- **Data Gathering Process:**
  - The large Kaggle dataset was divided into four smaller files for ease of handling.
  - Each file was loaded into SQLite online for preliminary processing.

#### Data Cleaning:

- **Handling Missing Values:**
  - After running the below queries no null or missing values were found

- **Data Quality Checks:**
  - Thorough checks were conducted to identify and correct any anomalies or inconsistencies in the dataset.

#### Example SQL Queries for Data Cleaning:

```sql
-- Check the number of unique apps in both tables of AppleStore
SELECT COUNT(DISTINCT id) as uniqueappid 
FROM AppleStore;

SELECT COUNT(DISTINCT id) as uniqueappid 
FROM appleStore_description_combined;

-- Check for any missing values in key fields
SELECT COUNT(*) AS MissingValues
FROM AppleStore
WHERE track_name IS NULL OR user_rating IS NULL OR prime_genre IS NULL;

SELECT COUNT(*) AS MissingValues
FROM appleStore_description_combined
WHERE app_desc IS NULL;
```


## 4. Analyse

## Data Analysis

### 1. Determine whether paid apps have higher ratings than free apps

```sql
SELECT 
  CASE 
    WHEN price > 0 THEN 'Paid'
    ELSE 'Free'
  END AS App_type, 
  AVG(user_rating) AS Avg_Rating
FROM 
  AppleStore
GROUP BY 
  App_type;
```
### 2. Check if the app with more supported devices has higher ratings
```sql
SELECT 
  MIN(sup_devices_num) AS Min_num,
  MAX(sup_devices_num) AS Max_num,
  AVG(sup_devices_num) AS Avg_num
FROM 
  AppleStore;

-- Check the distribution of supported devices
SELECT 
  DISTINCT sup_devices_num
FROM 
  AppleStore;

-- Analyze the relationship between the number of supported devices and average ratings
SELECT 
  CASE 
    WHEN sup_devices_num < 37 THEN '<37 devices supported'
    WHEN sup_devices_num BETWEEN 9 AND 20 THEN '9-20 devices supported'
    WHEN sup_devices_num BETWEEN 20 AND 30 THEN '20-30 devices supported'
    ELSE '>37 devices supported'
  END AS num_devices_supported, 
  AVG(user_rating) AS Avg_ratings
FROM 
  AppleStore
GROUP BY 
  num_devices_supported 
ORDER BY 
  Avg_ratings DESC;
```

### 3. Check genres with low ratings
```sql
SELECT 
  prime_genre, 
  AVG(user_rating) AS Avg_rating
FROM 
  AppleStore
GROUP BY 
  prime_genre
ORDER BY 
  Avg_rating ASC
LIMIT 10;
```

### 4. Check the top-rated app in each genre
```sql
SELECT 
  c.track_name,
  c.user_rating,
  c.prime_genre
FROM 
  AppleStore c
WHERE 
  c.user_rating = (
    SELECT MAX(user_rating)
    FROM AppleStore
    WHERE prime_genre = c.prime_genre
  );
```

### 5. Analyze the relationship between pricing and user ratings
```sql
SELECT
  price,
  AVG(user_rating) AS average_rating,
  COUNT(*) AS number_of_apps
FROM
  AppleStore
WHERE
  price IS NOT NULL -- Exclude apps with no specified price
GROUP BY
  price
ORDER BY
  average_rating DESC, price;
```

## 5. Share

### Quick Insights

For a brief overview of the analysis findings, here are the key insights:

1. **Paid vs. Free Apps:** Paid apps tend to have higher average ratings than free apps.

2. **Device Support Impact:** Apps supporting a higher number of devices generally receive higher average ratings.

3. **Genre Analysis:**
   - Finance and Book apps have low rating.
   - This give us a good picture on what genre we can focus on while building the app.

4. **Ratings:** The new app should aim for average rating above 3.5 in order to stand out from the crowd.
5. **User demand:** Games and Enterteinment genre have a highr competition but also have a higher demand amoungs the crowd.
 

## Conclusion

In conclusion, the data analysis of our AppStore dataset has provided valuable insights that can drive informed decision-making. Key takeaways include:

1. **Pricing Impact:** Adjusting pricing strategies based on market trends can positively influence user ratings.

2. **Device Optimization:** Optimizing app compatibility for devices with higher user representation is crucial for enhancing user experience.

3. **Genre Enhancement:** Addressing user feedback in low-rated genres presents an opportunity for continuous improvement.
