## 📚 Table of Contents

1. [Introduction](#introduction)
2. [Data Exploration](#data-exploration)
3. [Data Cleaning & Manipulation](#data-cleaning--manipulation)
4. [Exploratory Data Analysis](#exploratory-data-analysis)
## Introduction

Clinical trials serve as the backbone of medical research, providing invaluable insights into the efficacy and safety of novel treatments and interventions. 

This data set contains the clinical trial registration records from 10 large pharmaceutical companies, from which I aim to gain an understanding of the clinical trial landscape.
#### Questions

- [Which companies conducted the most clinical trials?](#which-companies-conducted-the-most-clinical-trials)
- [How many clinical trials were conducted each year?](#how-many-clinical-trials-were-conducted-each-year)
- [What is the distribution of clinical trials across different phases?](#what-is-the-distribution-of-clinical-trials-across-different-phases)
- [Which clinical trials had the highest enrolment?](#which-clinical-trials-had-the-highest-enrolment)
- [What is the average enrolment size for clinical trials conducted by each company?](#what-is-the-average-enrolment-size-for-clinical-trials-conducted-by-each-company)
- [What is the distribution of clinical trial statuses?](#what-is-the-distribution-of-clinical-trial-statuses)
- [What are the most common conditions being tested in clinical trials?](#what-are-the-most-common-conditions-being-tested-in-clinical-trials)
- [Are there any patterns or trends in the status of clinical trials over the years?](#are-there-any-patterns-or-trends-in-the-status-of-clinical-trials-over-the-years)
- [What is the distribution of phases for the top 5 conditions](#what-is-the-distribution-of-phases-for-the-top-5-conditions)
- [What is the distribution of clinical trial phases conducted by each company?](#what-is-the-distribution-of-clinical-trial-phases-conducted-by-each-company)
- [How does the status of clinical trials differ at each company?](#how-does-the-status-of-clinical-trials-differ-at-each-company)
#### Tools

SQL in Microsoft SQL Server (MSSQL)
#### Data Source

The analysis will be done on this [dataset](https://doi.org/10.7910/DVN/S8C77Q) from Harvard Dataverse under this [license.](https://creativecommons.org/publicdomain/zero/1.0/)
## Data Exploration

Understanding of the dataset's characteristics, such as it's structure, contents, and data types. 
#### Querying contents

```sql
SELECT TOP 5 *
FROM aero_data;
```

#### Querying column names and their data type
```sql
SELECT COLUMN_NAME
	,DATA_TYPE
FROM information_schema.columns
WHERE TABLE_NAME = 'aero_data';
```

| COLUMN_NAME       | DATA_TYPE |
|-------------------|-----------|
| NCT               | nvarchar  |
| Title             | nvarchar  |
| Summary           | nvarchar  |
| Start_Year        | float     |
| Start_Month       | float     |
| Phase             | nvarchar  |
| Conditions        | nvarchar  |
| Enrollment        | float     |
| Status            | nvarchar  |
| Meshes            | nvarchar  |
| Condition_Final   | nvarchar  |
#### Are there any NULL values?

```sql
SELECT COUNT(CASE 
		WHEN NCT IS NULL
			THEN 1
		END) AS nulls_in_nct
	,COUNT(CASE 
		WHEN Title IS NULL
			THEN 1
		END) AS nulls_in_title
	,COUNT(CASE 
		WHEN Summary IS NULL
			THEN 1
		END) AS nulls_in_summary
	,COUNT(CASE 
		WHEN Start_Year IS NULL
			THEN 1
		END) AS nulls_in_start_year
	,COUNT(CASE 
		WHEN Start_Month IS NULL
			THEN 1
		END) AS nulls_in_start_month
	,COUNT(CASE 
		WHEN Phase IS NULL
			THEN 1
		END) AS nulls_in_phase
	,COUNT(CASE 
		WHEN Conditions IS NULL
			THEN 1
		END) AS nulls_in_conditions
	,COUNT(CASE 
		WHEN Enrollment IS NULL
			THEN 1
		END) AS nulls_in_enrollment
	,COUNT(CASE 
		WHEN STATUS IS NULL
			THEN 1
		END) AS nulls_in_status
	,COUNT(CASE 
		WHEN Meshes IS NULL
			THEN 1
		END) AS nulls_in_meshes
	,COUNT(CASE 
		WHEN Condition_Final IS NULL
			THEN 1
		END) AS nulls_in_condition_final
FROM aero_data;
```

| nulls_in_nct | nulls_in_title | nulls_in_summary | nulls_in_start_year | nulls_in_start_month | nulls_in_phase | nulls_in_conditions | nulls_in_enrollment | nulls_in_status | nulls_in_meshes | nulls_in_condition_final |
|--------------|-----------------|-------------------|-----------------------|-------------------------|------------------|-----------------------|-------------------------|---------------------|------------------|--------------------------|
| 0            | 184             | 17                | 0                     | 0                       | 0                | 0                     | 0                       | 0                   | 0                | 0                        |

##### Any duplicate data?

```sql
SELECT NCT
	,Title
	,Summary
	,Start_Year
	,Start_Month
	,Phase
	,Conditions
	,Enrollment
	,STATUS
	,Meshes
	,Condition_Final
	,COUNT(*)
FROM aero_data
GROUP BY NCT
	,Title
	,Summary
	,Start_Year
	,Start_Month
	,Phase
	,Conditions
	,Enrollment
	,STATUS
	,Meshes
	,Condition_Final
HAVING COUNT(*) > 1;
```

- There are 1325 rows of duplicate data
#### How complete is the dataset?

Using Start_Year, I want to see how complete the dataset is, by seeing the number of clinical trials present per year

```sql
SELECT Start_Year
	,COUNT(Start_Year) AS number_of_trials
FROM #clinical_trial_cleaned
GROUP BY Start_Year
ORDER BY Start_Year;
```

| Start_Year | number_of_trials |
|------------|-------------------|
| 1931       | 1                 |
| 1984       | 1                 |
| 1985       | 1                 |
| 1988       | 3                 |
| 1989       | 3                 |
| 1990       | 3                 |
| 1991       | 2                 |
| 1992       | 9                 |
| 1993       | 12                |
| 1994       | 5                 |
| 1995       | 16                |
| 1996       | 12                |
| 1997       | 41                |
| 1998       | 53                |
| 1999       | 80                |
| 2000       | 127               |
| 2001       | 256               |
| 2002       | 447               |
| 2003       | 701               |
| 2004       | 918               |
| 2005       | 1013              |
| 2006       | 1268              |
| 2007       | 1227              |
| 2008       | 1201              |
| 2009       | 1040              |
| 2010       | 1014              |
| 2011       | 942               |
| 2012       | 886               |
| 2013       | 799               |
| 2014       | 691               |
| 2015       | 705               |
| 2016       | 556               |
| 2017       | 472               |
| 2018       | 518               |
| 2019       | 49                |
| 2020       | 2                 |
- It is clear that the dataset is incomplete for many years

#### Ensuring the data looks correct

Looking at the years, I saw one trial having a Start_Year of 1931. This prompted me to see if there were other rows with incorrect data. 

```sql
SELECT DISTINCT Phase
FROM aero_data
ORDER BY Phase

SELECT DISTINCT Status
FROM aero_data
ORDER BY Status

SELECT DISTINCT Condition_Final
FROM aero_data
```

- The only column I found odd was N/A in the Phase column, however, I discovered that these are trials without FDA-defined phases.
## Data Cleaning & Manipulation

Before analysis, I need to:
1. Split the NCT column to get the nct number and company name
2. Remove the columns; Title, Summary, Start_Month, Condition, and Meshes
3. Remove duplicate data
4. Filter the data to just include the years between 2000 and 2018

I will do all this within a temp table, to ensure the original dataset remains intact

```sql
DROP TABLE IF EXISTS #clinical_trial_cleaned
CREATE TABLE #clinical_trial_cleaned (
	nct NVARCHAR(MAX)
	,company NVARCHAR(MAX)
	,year INT
	,phase NVARCHAR(MAX)
	,enrollment BIGINT
	,status NVARCHAR(MAX)
	,condition NVARCHAR(MAX)
	);
		
INSERT INTO #clinical_trial_cleaned
SELECT DISTINCT SUBSTRING(NCT, CHARINDEX('/', NCT) + 1, CHARINDEX('.', NCT) - CHARINDEX('/', NCT) - 1) AS nct
	,SUBSTRING(NCT, 0, CHARINDEX('/', NCT, 0)) AS company
	,Start_Year AS year
	,Phase AS phase
	,Enrollment AS enrollment
	,STATUS AS STATUS
	,Condition_Final AS condition
FROM aero_data
```

## Exploratory Data Analysis

Now that the data is cleaned and well structured, it is time to question the data. 
#### Which companies conducted the most clinical trials?

```sql
SELECT company
	,COUNT(company) AS number_of_trials
FROM #clinical_trial_cleaned
GROUP BY company
ORDER BY number_of_trials DESC;
```

| Company  | Number of Trials |
|----------|-------------------|
| GSK      | 2418              |
| Novartis | 2301              |
| Pfizer   | 1915              |
| Merck    | 1728              |
| Sanofi   | 1500              |
| JNJ      | 1089              |
| Roche    | 1082              |
| Bayer    | 616               |
| Gilead   | 415               |
| AbbVie   | 413               |
#### How many clinical trials were conducted each year?

```sql
SELECT year
	,COUNT(year) AS number_of_trials
FROM #clinical_trial_cleaned
GROUP BY year
ORDER BY year;
```

| Year | Number of Trials |
|------|-------------------|
| 2000 | 113               |
| 2001 | 217               |
| 2002 | 381               |
| 2003 | 607               |
| 2004 | 799               |
| 2005 | 897               |
| 2006 | 1104              |
| 2007 | 1115              |
| 2008 | 1089              |
| 2009 | 964               |
| 2010 | 913               |
| 2011 | 872               |
| 2012 | 802               |
| 2013 | 741               |
| 2014 | 649               |
| 2015 | 669               |
| 2016 | 555               |
| 2017 | 472               |
| 2018 | 518               |
- The number of clinical trials peaked at 2007, and has declined since then
#### What is the distribution of clinical trials across different phases?

```sql
SELECT phase
	,CAST(ROUND((COUNT(phase) * 1.0 / 13477) * 100, 2) AS FLOAT) AS percentage_of_trials
FROM #clinical_trial_cleaned
GROUP BY phase
ORDER BY phase;
```

| Phase              | Percentage of Trials |
|-------------------|-----------------------|
| Early Phase 1     | 0.07                  |
| N/A               | 1.91                  |
| Phase 1           | 18.54                 |
| Phase 1/Phase 2   | 2.36                  |
| Phase 2           | 26.19                 |
| Phase 2/Phase 3   | 0.95                  |
| Phase 3           | 35.16                 |
| Phase 4           | 14.83                 |
- The majority of clinical trials are in phase 3, followed by phase 2, phase 1 and phase 4 
#### Which clinical trials had the highest enrolment?

```sql
SELECT TOP 5 *
FROM #clinical_trial_cleaned
ORDER BY enrollment DESC;
```

| NCT | Company | Year | Phase | Enrollment | Status | Condition |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| NCT00744263 | Pfizer | 2008 | Phase 4 | 84496 | Completed | Pneumonia, Pneumococcal |
| NCT00090233 | Merck | 2001 | Phase 3 | 69274 | Completed | Rotavirus Infections |
| NCT00140673 | GSK | 2003 | Phase 3 | 63227 | Completed | Rotavirus Infections |
| NCT02251704 | GSK | 2014 | N/A | 56000 | Recruiting | Malaria |
| NCT02374450 | GSK | 2015 | N/A | 52192 | Recruiting | Malaria |
|  |  |  |  |  |  |  |
- These clinical trials led to the introduction of vaccines against Pneumonia, Rotavirus, and Malaria
#### What is the average enrolment size for clinical trials conducted by each company?

```sql
SELECT company
	,AVG(enrollment) AS average_enrollment
FROM #clinical_trial_cleaned
GROUP BY company
ORDER BY average_enrollment DESC;
```

| Company  | Average Enrollment |
|----------|---------------------|
| GSK      | 566                 |
| Sanofi   | 560                 |
| Merck    | 529                 |
| Bayer    | 453                 |
| Pfizer   | 381                 |
| Novartis | 374                 |
| Roche    | 366                 |
| JNJ      | 298                 |
| AbbVie   | 260                 |
| Gilead   | 242                 |
#### What is the distribution of clinical trial statuses?

```sql
SELECT STATUS
	,COUNT(STATUS) AS number_of_trials
FROM #clinical_trial_cleaned
GROUP BY STATUS
ORDER BY number_of_trials DESC;
```

|         Status            | Number of Trials |
|---------------------------|-------------------|
| Completed                 | 10360             |
| Terminated                | 1279              |
| Recruiting               | 798               |
| Active, not recruiting    | 646               |
| Withdrawn                 | 285               |
| Not yet recruiting        | 64                |
| Enrolling by invitation   | 15                |
| Unknown status           | 15                |
| Suspended                | 15                |
- This uneven distribution may explain the peak in clinical trials in 2007, followed by a steady decline
- This shows that this dataset likely struggles with selection bias, selecting for completed clinical trials
#### What are the most common conditions being tested in clinical trials?
```sql
SELECT TOP 5 condition
	,COUNT(condition) AS number_of_trials
FROM #clinical_trial_cleaned
GROUP BY condition
ORDER BY number_of_trials DESC;
```

| Condition                                   | Number of Trials |
|---------------------------------------------|-------------------|
| Diabetes Mellitus, Type 2                   | 532               |
| Breast Neoplasms                            | 381               |
| Pulmonary Disease, Chronic Obstructive      | 339               |
| Hypertension                                | 336               |
| Arthritis, Rheumatoid                       | 333               |
- These findings correlate well with the prevalence of these conditions
#### Are there any patterns or trends in the status of clinical trials over the years?

```sql
SELECT *
FROM (
	SELECT year
		,STATUS
	FROM #clinical_trial_cleaned
	) AS src
PIVOT(COUNT(STATUS) FOR STATUS IN (
			[Active, not recruiting]
			,[Completed]
			,[Enrolling by invitation]
			,[Not yet recruiting]
			,[Recruiting]
			,[Suspended]
			,[Terminated]
			,[Unknown status]
			,[Withdrawn]
			)) AS pvt
ORDER BY year;
```

| Year | Active, not recruiting | Completed | Enrolling by invitation | Not yet recruiting | Recruiting | Suspended | Terminated | Unknown status | Withdrawn |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| 2000 | 0 | 102 | 0 | 0 | 0 | 0 | 7 | 4 | 0 |
| 2001 | 0 | 204 | 0 | 0 | 0 | 0 | 9 | 3 | 1 |
| 2002 | 1 | 350 | 0 | 0 | 1 | 0 | 28 | 1 | 0 |
| 2003 | 0 | 554 | 0 | 0 | 0 | 0 | 53 | 0 | 0 |
| 2004 | 0 | 737 | 0 | 0 | 0 | 0 | 57 | 0 | 5 |
| 2005 | 2 | 823 | 0 | 0 | 0 | 0 | 68 | 0 | 4 |
| 2006 | 4 | 953 | 0 | 0 | 3 | 1 | 134 | 0 | 9 |
| 2007 | 8 | 949 | 0 | 0 | 0 | 0 | 139 | 1 | 18 |
| 2008 | 12 | 933 | 0 | 0 | 0 | 0 | 129 | 0 | 15 |
| 2009 | 10 | 805 | 0 | 0 | 1 | 1 | 127 | 0 | 20 |
| 2010 | 9 | 763 | 0 | 0 | 2 | 0 | 114 | 1 | 24 |
| 2011 | 25 | 740 | 0 | 0 | 7 | 1 | 74 | 1 | 24 |
| 2012 | 42 | 654 | 0 | 0 | 6 | 0 | 77 | 1 | 22 |
| 2013 | 69 | 545 | 3 | 0 | 12 | 0 | 78 | 3 | 31 |
| 2014 | 92 | 429 | 1 | 0 | 25 | 0 | 73 | 0 | 29 |
| 2015 | 126 | 403 | 2 | 0 | 63 | 3 | 53 | 0 | 19 |
| 2016 | 124 | 260 | 1 | 0 | 107 | 1 | 36 | 0 | 26 |
| 2017 | 87 | 122 | 4 | 1 | 222 | 4 | 17 | 0 | 15 |
| 2018 | 35 | 34 | 4 | 63 | 349 | 4 | 6 | 0 | 23 |
- The low number of active clinical trials during 2017/18 is likely due to clinical trials still recruiting at this time. 
- The peak in completed and terminated trials matches the distribution of clinical trials per year
#### What is the distribution of phases for the top 5 conditions 
```sql
SELECT TOP 5 condition
	,COUNT(condition) AS number_of_trials
	,SUM(CASE 
			WHEN phase = 'N/A'
				THEN 1
			ELSE 0
			END) AS not_applicable
	,SUM(CASE 
			WHEN phase = 'Early Phase 1'
				THEN 1
			ELSE 0
			END) AS early_phase_1
	,SUM(CASE 
			WHEN phase = 'Phase 1'
				THEN 1
			ELSE 0
			END) AS phase_1
	,SUM(CASE 
			WHEN phase = 'Phase 1/Phase 2'
				THEN 1
			ELSE 0
			END) AS phase_1_2
	,SUM(CASE 
			WHEN phase = 'Phase 2'
				THEN 1
			ELSE 0
			END) AS phase_2
	,SUM(CASE 
			WHEN phase = 'Phase 2/Phase 3'
				THEN 1
			ELSE 0
			END) AS phase_2_3
	,SUM(CASE 
			WHEN phase = 'Phase 3'
				THEN 1
			ELSE 0
			END) AS phase_3
	,SUM(CASE 
			WHEN phase = 'Phase 4'
				THEN 1
			ELSE 0
			END) AS phase_4
FROM #clinical_trial_cleaned
GROUP BY condition
ORDER BY number_of_trials DESC;
```

| condition                                | number_of_trials | not_applicable | early_phase_1 | phase_1 | phase_1_2 | phase_2 | phase_2_3 | phase_3 | phase_4 |
|------------------------------------------|------------------|-----------------|---------------|---------|-----------|---------|-----------|---------|---------|
| Diabetes Mellitus, Type 2                | 532              | 7               | 0             | 134     | 6         | 73      | 3         | 201     | 108     |
| Breast Neoplasms                         | 381              | 0               | 0             | 53      | 16        | 163     | 3         | 119     | 27      |
| Pulmonary Disease, Chronic Obstructive   | 339              | 3               | 0             | 91      | 3         | 74      | 1         | 117     | 50      |
| Hypertension                             | 336              | 0               | 0             | 33      | 2         | 40      | 3         | 152     | 106     |
| Arthritis, Rheumatoid                    | 333              | 4               | 0             | 47      | 6         | 88      | 2         | 130     | 56      |
#### What is the distribution of clinical trial phases conducted by each company?
```sql
SELECT company
	,CONVERT(DECIMAL(5, 2), ISNULL([Early Phase 1], 0) * 100.0 / NULLIF(SUM([Early Phase 1] + [N/A] + [Phase 1] + [Phase 1/Phase 2] + [Phase 2] + [Phase 2/Phase 3] + [Phase 3] + [Phase 4]), 0)) AS percent_early_phase_1
	,CONVERT(DECIMAL(5, 2), ISNULL([N/A], 0) * 100.0 / NULLIF(SUM([Early Phase 1] + [N/A] + [Phase 1] + [Phase 1/Phase 2] + [Phase 2] + [Phase 2/Phase 3] + [Phase 3] + [Phase 4]), 0)) AS percent_n_a
	,CONVERT(DECIMAL(5, 2), ISNULL([Phase 1], 0) * 100.0 / NULLIF(SUM([Early Phase 1] + [N/A] + [Phase 1] + [Phase 1/Phase 2] + [Phase 2] + [Phase 2/Phase 3] + [Phase 3] + [Phase 4]), 0)) AS percent_phase_1
	,CONVERT(DECIMAL(5, 2), ISNULL([Phase 1/Phase 2], 0) * 100.0 / NULLIF(SUM([Early Phase 1] + [N/A] + [Phase 1] + [Phase 1/Phase 2] + [Phase 2] + [Phase 2/Phase 3] + [Phase 3] + [Phase 4]), 0)) AS percent_phase_1_2
	,CONVERT(DECIMAL(5, 2), ISNULL([Phase 2], 0) * 100.0 / NULLIF(SUM([Early Phase 1] + [N/A] + [Phase 1] + [Phase 1/Phase 2] + [Phase 2] + [Phase 2/Phase 3] + [Phase 3] + [Phase 4]), 0)) AS percent_phase_2
	,CONVERT(DECIMAL(5, 2), ISNULL([Phase 2/Phase 3], 0) * 100.0 / NULLIF(SUM([Early Phase 1] + [N/A] + [Phase 1] + [Phase 1/Phase 2] + [Phase 2] + [Phase 2/Phase 3] + [Phase 3] + [Phase 4]), 0)) AS percent_phase_2_3
	,CONVERT(DECIMAL(5, 2), ISNULL([Phase 3], 0) * 100.0 / NULLIF(SUM([Early Phase 1] + [N/A] + [Phase 1] + [Phase 1/Phase 2] + [Phase 2] + [Phase 2/Phase 3] + [Phase 3] + [Phase 4]), 0)) AS percent_phase_3
	,CONVERT(DECIMAL(5, 2), ISNULL([Phase 4], 0) * 100.0 / NULLIF(SUM([Early Phase 1] + [N/A] + [Phase 1] + [Phase 1/Phase 2] + [Phase 2] + [Phase 2/Phase 3] + [Phase 3] + [Phase 4]), 0)) AS percent_phase_4
FROM (
	SELECT company
		,[Early Phase 1]
		,[N/A]
		,[Phase 1]
		,[Phase 1/Phase 2]
		,[Phase 2]
		,[Phase 2/Phase 3]
		,[Phase 3]
		,[Phase 4]
	FROM (
		SELECT company
			,phase
		FROM #clinical_trial_cleaned
		) AS src
	PIVOT(COUNT(phase) FOR phase IN (
				[Early Phase 1]
				,[N/A]
				,[Phase 1]
				,[Phase 1/Phase 2]
				,[Phase 2]
				,[Phase 2/Phase 3]
				,[Phase 3]
				,[Phase 4]
				)) AS pvt
	) AS subquery
GROUP BY company
	,[Early Phase 1]
	,[N/A]
	,[Phase 1]
	,[Phase 1/Phase 2]
	,[Phase 2]
	,[Phase 2/Phase 3]
	,[Phase 3]
	,[Phase 4]
ORDER BY company;
```

| company | percent_early_phase_1 | percent_n_a | percent_phase_1 | percent_phase_1_2 | percent_phase_2 | percent_phase_2_3 | percent_phase_3 | percent_phase_4 |
|---------|-----------------------|------------|------------------|-------------------|------------------|---------------------|------------------|------------------|
| AbbVie  | 0.00                  | 0.00       | 19.13            | 3.15              | 31.48            | 0.97                | 41.40            | 3.87             |
| Bayer   | 0.00                  | 4.06       | 16.72            | 2.76              | 27.11            | 0.97                | 35.39            | 12.99            |
| Gilead  | 0.00                  | 0.72       | 16.87            | 4.34              | 36.63            | 1.45                | 33.25            | 6.75             |
| GSK     | 0.00                  | 2.44       | 29.69            | 0.45              | 27.30            | 0.25                | 28.16            | 11.70            |
| JNJ     | 0.83                  | 6.98       | 16.25            | 1.65              | 21.49            | 0.64                | 37.74            | 14.42            |
| Merck   | 0.00                  | 0.69       | 20.83            | 1.62              | 21.99            | 0.58                | 42.53            | 11.75            |
| Novartis| 0.00                  | 0.74       | 11.43            | 4.09              | 28.38            | 1.87                | 35.55            | 17.95            |
| Pfizer  | 0.05                  | 1.46       | 20.57            | 1.93              | 24.91            | 1.15                | 32.58            | 17.34            |
| Roche   | 0.00                  | 2.50       | 14.97            | 2.68              | 27.26            | 0.83                | 35.86            | 15.90            |
| Sanofi  | 0.00                  | 0.73       | 11.47            | 3.53              | 25.40            | 1.00                | 36.93            | 20.93            |
- The majority of companies have the most clinical trials in phase 3, matching the average distribution
#### How does the status of clinical trials differ at each company?
```sql
SELECT company
	,CONVERT(DECIMAL(5, 2), ISNULL([Active, not recruiting], 0) * 100.0 / NULLIF(SUM([Active, not recruiting] + [Completed] + [Enrolling by invitation] + [Not yet recruiting] + [Recruiting] + [Suspended] + [Terminated] + [Unknown status] + [Withdrawn]), 0)) AS percent_active_not_recruiting
	,CONVERT(DECIMAL(5, 2), ISNULL([Completed], 0) * 100.0 / NULLIF(SUM([Active, not recruiting] + [Completed] + [Enrolling by invitation] + [Not yet recruiting] + [Recruiting] + [Suspended] + [Terminated] + [Unknown status] + [Withdrawn]), 0)) AS percent_completed
	,CONVERT(DECIMAL(5, 2), ISNULL([Enrolling by invitation], 0) * 100.0 / NULLIF(SUM([Active, not recruiting] + [Completed] + [Enrolling by invitation] + [Not yet recruiting] + [Recruiting] + [Suspended] + [Terminated] + [Unknown status] + [Withdrawn]), 0)) AS percent_enrolling_by_invitation
	,CONVERT(DECIMAL(5, 2), ISNULL([Not yet recruiting], 0) * 100.0 / NULLIF(SUM([Active, not recruiting] + [Completed] + [Enrolling by invitation] + [Not yet recruiting] + [Recruiting] + [Suspended] + [Terminated] + [Unknown status] + [Withdrawn]), 0)) AS percent_not_yet_recruiting
	,CONVERT(DECIMAL(5, 2), ISNULL([Recruiting], 0) * 100.0 / NULLIF(SUM([Active, not recruiting] + [Completed] + [Enrolling by invitation] + [Not yet recruiting] + [Recruiting] + [Suspended] + [Terminated] + [Unknown status] + [Withdrawn]), 0)) AS percent_recruiting
	,CONVERT(DECIMAL(5, 2), ISNULL([Suspended], 0) * 100.0 / NULLIF(SUM([Active, not recruiting] + [Completed] + [Enrolling by invitation] + [Not yet recruiting] + [Recruiting] + [Suspended] + [Terminated] + [Unknown status] + [Withdrawn]), 0)) AS percent_suspended
	,CONVERT(DECIMAL(5, 2), ISNULL([Terminated], 0) * 100.0 / NULLIF(SUM([Active, not recruiting] + [Completed] + [Enrolling by invitation] + [Not yet recruiting] + [Recruiting] + [Suspended] + [Terminated] + [Unknown status] + [Withdrawn]), 0)) AS percent_terminated
	,CONVERT(DECIMAL(5, 2), ISNULL([Unknown status], 0) * 100.0 / NULLIF(SUM([Active, not recruiting] + [Completed] + [Enrolling by invitation] + [Not yet recruiting] + [Recruiting] + [Suspended] + [Terminated] + [Unknown status] + [Withdrawn]), 0)) AS percent_unknown_status
	,CONVERT(DECIMAL(5, 2), ISNULL([Withdrawn], 0) * 100.0 / NULLIF(SUM([Active, not recruiting] + [Completed] + [Enrolling by invitation] + [Not yet recruiting] + [Recruiting] + [Suspended] + [Terminated] + [Unknown status] + [Withdrawn]), 0)) AS percent_withdrawn
FROM (
	SELECT company
		,STATUS
	FROM #clinical_trial_cleaned
	) AS src
PIVOT(COUNT(STATUS) FOR STATUS IN (
			[Active, not recruiting]
			,[Completed]
			,[Enrolling by invitation]
			,[Not yet recruiting]
			,[Recruiting]
			,[Suspended]
			,[Terminated]
			,[Unknown status]
			,[Withdrawn]
			)) AS pvt
GROUP BY company
	,[Active, not recruiting]
	,[Completed]
	,[Enrolling by invitation]
	,[Not yet recruiting]
	,[Recruiting]
	,[Suspended]
	,[Terminated]
	,[Unknown status]
	,[Withdrawn]
ORDER BY company;
```

| company | percent_active_not_recruiting | percent_completed | percent_enrolling_by_invitation | percent_not_yet_recruiting | percent_recruiting | percent_suspended | percent_terminated | percent_unknown_status | percent_withdrawn |
|---------|-------------------------------|---------------------|----------------------------------|----------------------------|---------------------|-------------------|---------------------|-------------------------|---------------------|
| AbbVie  | 14.29                         | 59.81               | 1.21                             | 2.42                       | 16.22               | 0.24              | 4.36                | 0.00                    | 1.45                |
| Bayer   | 9.09                          | 75.97               | 0.00                             | 0.32                       | 5.84                | 0.00              | 7.14                | 0.00                    | 1.62                |
| Gilead  | 9.64                          | 69.16               | 0.96                             | 0.00                       | 7.23                | 0.00              | 11.57               | 0.24                    | 1.20                |
| GSK     | 1.74                          | 85.53               | 0.00                             | 0.41                       | 3.02                | 0.08              | 6.04                | 0.29                    | 2.89                |
| JNJ     | 6.06                          | 75.21               | 0.09                             | 0.28                       | 7.44                | 0.09              | 9.55                | 0.09                    | 1.19                |
| Merck   | 3.76                          | 76.97               | 0.00                             | 0.35                       | 3.99                | 0.17              | 12.62               | 0.00                    | 2.14                |
| Novartis| 4.74                          | 75.53               | 0.00                             | 0.78                       | 7.82                | 0.04              | 8.34                | 0.04                    | 2.69                |
| Pfizer  | 2.92                          | 74.52               | 0.05                             | 0.05                       | 4.33                | 0.16              | 15.40               | 0.00                    | 2.56                |
| Roche   | 9.61                          | 70.43               | 0.00                             | 0.37                       | 9.80                | 0.37              | 7.21                | 0.18                    | 2.03                |
| Sanofi  | 3.27                          | 80.93               | 0.27                             | 0.67                       | 4.87                | 0.00              | 9.07                | 0.20                    | 0.73                |

- AbbVie has a high percentage of clinical trials in their recruiting and active phase, this is likely due to AbbVie being a newer pharmaceutical company, being founded in 2013
- Pfizer, Merck and Gilead has a high percentage of terminated clinical trials
- GSK has a high percentage of completed clinical trials
