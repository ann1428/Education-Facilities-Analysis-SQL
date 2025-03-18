# Education-Facilities-Analysis

## Overview
In this analysis, we examine education facilities in Canada using SQL queries on open data from Statistics Canada. The dataset, made publicly available by different levels of government, provides detailed information on educational institutions. Using SQL, we extract, transform, and analyze this data to uncover meaningful insights.

The dataset consists of four tables: facilities_info, education_info, address_info, and census_info. These tables contain comprehensive details about educational facilities, the levels of education offered, geographic locations, and census-related information. SQL's powerful capabilities - such as data manipulation, aggregation, filtering, and joins—allow us to explore trends and distributions within the Canadian education landscape.

## Dataset
* Source: https://www.statcan.gc.ca/en/lode/databases/odef
* Tables:
  facilities_info: Facility details including type, name, and managing authority.

  education_info: Information on education levels offered by facilities.

  address_info: Geographic locations and postal codes.

  census_info: Census data related to educational institutions.

## SQL Queries & Insights

### Query 1: Catholic Facilities Details
SELECT fac.facility_name, fac.facility_type, ad.address, ad.city, ad.province, ad.postal_code
FROM facility_info fac
INNER JOIN address_info ad ON fac.address_id = ad.address_id
WHERE fac.facility_id IN (
    SELECT facility_id
    FROM facility_info
    WHERE facility_type = 'Catholic'
);

![image](https://github.com/user-attachments/assets/a9d18f24-6b5a-495f-92bb-a83316e85332)


Insight: 

### Query 2: Number of Facilities in Each City
SELECT ad.city, COUNT(fac.facility_id) AS facility_count
FROM address_info ad
INNER JOIN facility_info fac ON ad.address_id = fac.address_id
GROUP BY ad.city;

![image](https://github.com/user-attachments/assets/84b3d22d-6a3f-4b96-8738-ab15b1f1cd7a)


Insight:

### Query 3: Education Levels Offered by Facilities
SELECT
COUNT(CASE WHEN early_childhood_education_status = true THEN 1 END) AS count_early_childhood_education,
COUNT(CASE WHEN kindergarten_status = true THEN 1 END) AS count_kindergarten,
COUNT(CASE WHEN elementary_status = true THEN 1 END) AS count_elementary,
COUNT(CASE WHEN junior_secondary_status = true THEN 1 END) AS count_junior_secondary,
COUNT(CASE WHEN senior_secondary_status = true THEN 1 END) AS count_senior_secondary,
COUNT(CASE WHEN official_language_status = true THEN 1 END) AS count_official_language
FROM education_info;

![image](https://github.com/user-attachments/assets/1478b3f6-e061-4f54-ae53-cb9ff90eb88f)

Insight: 

### Query 4: Facilities in Each Metropolitan Area (Sorted by Count)
SELECT census_metropolitan_area_name, COUNT(census_id) AS count_census
FROM census_info
WHERE census_metropolitan_area_name is not null
GROUP BY census_metropolitan_area_name 
ORDER BY count_census DESC;

![image](https://github.com/user-attachments/assets/9b83ba55-862d-4963-95af-56b4fde11623)

Insight: 

### Query 5: Facility Details in Toronto Census Subdivision
SELECT fac.facility_id, fac.facility_name, fac.facility_type,
	   ad.address, ad.city, ad.province, ad.postal_code
FROM facility_info fac
INNER JOIN address_info ad ON fac.address_id = ad.address_id
INNER JOIN census_info cen ON ad.census_id = cen.census_id
WHERE cen.census_subdivision_name = 'Toronto';

![image](https://github.com/user-attachments/assets/a18a2e99-88f5-454f-a439-a1ee3531a311)

Insight: 

### Query 6: Facility Types with at Least Five Facilities
SELECT facility_type, COUNT(facility_id) AS facility_count
FROM facility_info
GROUP BY facility_type
HAVING COUNT(facility_id) >= 5;

![image](https://github.com/user-attachments/assets/d343e01e-6269-4485-a681-941048aebc91)

Insight: 

### Query 7: Facility Type Count Categorization (High, Medium, Low)
WITH facility_province_counts AS (
  SELECT ad.province, f.facility_type, COUNT(*) AS facility_count
  FROM facility_info f
  INNER JOIN address_info ad ON f.address_id = ad.address_id
  GROUP BY ad.province, f.facility_type
)
SELECT fpc.province, fpc.facility_type, fpc.facility_count,
       CASE
         WHEN fpc.facility_count >= 10 THEN 'High Facility Count'
         WHEN fpc.facility_count >= 5 THEN 'Medium Facility Count'
         ELSE 'Low Facility Count'
       END AS facility_category
FROM facility_province_counts fpc;

![image](https://github.com/user-attachments/assets/79c80f12-334a-4eda-83e7-26fd6ae2272a)

Insight: 

### Query 8: Facilities Offering a Combination of Education Levels
SELECT f.facility_id, f.facility_name, f.facility_type, f.authority_name, e.early_childhood_education_status, e.kindergarten_status, e.elementary_status, e.junior_secondary_status
FROM facility_info f
LEFT JOIN education_info e ON f.education_id = e.education_id
WHERE e.early_childhood_education_status = true
  AND e.kindergarten_status = true
  AND e.elementary_status = true
  AND e.junior_secondary_status = true
ORDER BY f.facility_name;

![image](https://github.com/user-attachments/assets/ed486256-3583-424c-a35c-d5382b121760)

Insight: Identifies facilities that provide a full range of education from early childhood to junior secondary, showing integrated institutions.

