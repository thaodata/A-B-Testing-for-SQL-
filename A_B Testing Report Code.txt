/*
**DATA ANALYST REPORT 1 - MARKETING A/B TESTING  
Analyser:** Le Chanh Thao  
**Programing language:** SQL  
**Source:** Kaggle
*/

/*
**INTRODUCTION**

**Data source**:

**Context:** 

<span style="color: rgb(60, 64, 67); font-family: Inter, sans-serif;">Marketing companies want to run successful campaigns, but the market is complex and several options can work. So normally they tun A/B tests, that is a randomized experimentation process wherein two or more versions of a variable (web page, page element, banner, etc.) are shown to different segments of people at the same time to determine which version leaves the maximum impact and drive business metrics.</span>

The companies are interested in answering **two questions**:

- Would the campaign be successful?
- If the campaign was successful, how much of that success could be attributed to the ads?

**Data Dictionary:**

- Index: Row index
- user id: User ID (unique)
- test group: If "ad" the person saw the advertisement, if "psa" they only saw the public service announcement
- converted: If a person bought the product then True, else is False
- total ads: Amount of ads seen by person
- most ads day: Day that the person saw the biggest amount of ads
- most ads hour: Hour of day that the person saw the biggest amount of ads
*/

/*
**CONCLUSION**
1\. Based on the Chi-squared Test, the campaign **was successful** in terms of conversion rate.  
2\. Based on the analyst, the most profitable day is in Monday and the most profitable hours are from **16h to 20h (ad test group)** and **15h to 19h (psa test group)**
*/

/*
**LOAD AND CHECK DATA**
*/

/*
**CREATE DATA TABLE AND LOAD DATA**
*/

CREATE TABLE marketing_AB (

    [index] int,

    [user_id] varchar(10) UNIQUE,

    [test_group] varchar(10),

    [converted] varchar(10),

    [total_ads] int,

    [most_ads_day] varchar(10),

    [most_ads_hour] smallint)



SELECT top 5 * from marketing_A_B

/*
Check the shape of the dataset
*/

SELECT count(*) as Row_Count 

from marketing_A_B



SELECT COUNT(*) AS Column_Count

FROM INFORMATION_SCHEMA.COLUMNS

WHERE TABLE_NAME = 'marketing_A_B';

/*
Check the data type of each column
*/

SELECT COLUMN_NAME, DATA_TYPE

FROM INFORMATION_SCHEMA.COLUMNS

WHERE TABLE_NAME = 'marketing_A_B';

/*
Check the null value in each column
*/

SELECT 

COUNT (CASE WHEN user_id IS NULL THEN 1 END) as NullCount_user_id,

COUNT (CASE WHEN test_group IS NULL THEN 1 END) as NullCount_test_group,

COUNT (CASE WHEN converted IS NULL THEN 1 END) as NullCount_converted,

COUNT (CASE WHEN total_ads IS NULL THEN 1 END) as NullCount_total_ads,

COUNT (CASE WHEN most_ads_day IS NULL THEN 1 END) as NullCount_most_ads_day,

COUNT (CASE WHEN most_ads_hour IS NULL THEN 1 END) as NullCount_most_ads_hour

FROM marketing_A_B

/*
**EXPLORATORY DATA ANALYST (EDA)**
*/

/*
 **UNIVARIATE ANALYSIS**
*/

/*
Numerical Variable: total\_ads
*/

select 

count (user_id) as count_by_user,

min(total_ads) as min_value,

max(total_ads) as max_value,

avg(total_ads) as avg_value,

stdev(total_ads) as std_value

from marketing_A_B

/*
Categorical Variable: test\_group
*/

SELECT

test_group,

count(*)

from marketing_A_B

group by test_group

/*
Categorical Variable: converted
*/

SELECT

converted,

count(*)

from marketing_A_B

group by converted

/*
Categorical Variable: most\_ads\_day
*/

SELECT

most_ads_day,

count(*) as total_id

from marketing_A_B

group by most_ads_day

order by count(*) desc

/*
Categorical Variable: most\_ads\_hour
*/

SELECT

most_ads_hour,

count(*) as total_id

from marketing_A_B

group by most_ads_hour 

order by count (*) desc

/*
**BIVARIATE ANALYSIS**
*/

/*
One numerical and one categorical value: total\_ads and test\_group
*/

SELECT

test_group,

sum(total_ads) as total_ads

from marketing_A_B

group by test_group

/*
One numerical and one categorical value: total\_ads and most\_ads\_day
*/

SELECT

most_ads_day,

sum(total_ads) as total_ads

from marketing_A_B

group by most_ads_day

order by total_ads desc

/*
One numerical and one categorical value: total\_ads and most\_ads\_hour
*/

SELECT

most_ads_hour,

sum(total_ads) as total_ads

from marketing_A_B

group by most_ads_hour

order by total_ads desc

/*
Two categorical values: test\_group and converted
*/

select 

test_group,

count(case when converted = 'TRUE' then user_id end) as converted,

count(case when converted = 'FALSE' then user_id end) as not_converted

from marketing_A_B

group by test_group

/*
**MULTIVARIBLE ANALYSIS**
*/

/*
Two categorical values and one numerical value : (test\_group and converted) and total\_ads
*/

select

test_group,

converted,

sum(total_ads) as sum_total_ads

from marketing_A_B

group by test_group, converted

order by test_group, sum(total_ads) asc

/*
Two categorical values and one numerical value : (test\_group and most\_ads\_day) and total\_ads
*/

select

test_group,

most_ads_day,

sum(total_ads) as sum_total_ads

from marketing_A_B

group by test_group, most_ads_day

order by test_group, sum(total_ads) asc

/*
Two categorical values and one numerical value : (test\_group and most\_ads\_hour) and total\_ads
*/

select

test_group,

most_ads_hour,

sum(total_ads) as sum_total_ads

from marketing_A_B

group by test_group, most_ads_hour

order by test_group, sum(total_ads) asc

/*
**DATA PROCESSING AND FEATURE ENGINEERING**
*/

/*
**MISSING VALUE**
*/

/*
Based on the above result, the dataset has no missing value
*/

/*
**OUTLIERS**
*/

/*
Instead of using box plot which will elimiate a large row in the dataset (about 8% - based on my experience), we determine the outlier by z\_score and values \> 3 z\_score and \< -3 z\_score will be replace by the value of 3z\_score and -3z\_score perspectively.
*/

/*
Z-score calculation
*/

select 

user_id,

test_group,

total_ads,

avg(total_ads) over(partition by test_group) as var_ads,

stdev(total_ads) over (partition by test_group) as stdev_ads,

(total_ads - avg(total_ads) over(partition by test_group))/stdev(total_ads) over (partition by test_group) as z_score

into zscore_1

from marketing_A_B

group by user_id, test_group, total_ads

/*
Alter my table by replace values is out of 3 z\_score and -3 z\_score by the max value in 3z\_score and min value in 3 z\_score perspectively
*/

SELECT 
user_id,
max( case when z_score > 3 then round((3*stdev_ads + var_ads),0)
          when z_score <-3 then round((-3*stdev_ads + var_ads),0) 
          else total_ads end) as  adjusted_total_ads
into adjusted_total_ads
from zscore_1
group by user_id

/*
And we have new table with adjusted\_total\_ads named adjusted\_marketing\_A\_B and it is ready for further analysis
*/

select 

a.user_id,

a.test_group,

a.converted,

b.adjusted_total_ads,

a.most_ads_day,

a.most_ads_hour

into adjusted_marketing_A_B

from marketing_A_B as a

inner join adjusted_total_ads as b on a.user_id = b.user_id

select * from adjusted_marketing_A_B

/*
**DATA ANALYST**
*/

/*
**Question 1. Would the campaign be successful?**
*/

/*
Evaluating the success of the campaign based on the total converted user with Chi-squared Test
*/

select a.test_group,

count(distinct case when b.user_id  is not null then a.user_id end) as yes,

count(distinct case when b.user_id  is null then a.user_id end) as no

from adjusted_marketing_A_B as a

left join marketing_A_B as b on a.user_id = b.user_id and b.converted = 'TRUE'

group by a.test_group

/*
To answer the question "Is the success of campaign based on cohorted user statistical significance?" based the the number of converted user, we need to use Chi-squared test for the below hypothesis with the confidence of 95%:  
H0: There is no difference between two categories   
H1: There is a difference between two categories

After I put the result on Online Chi-squared calculator, I found out the p.value \<0.05, as a result the refuse the H0, in other word, the result of 2 test group is significantly different.

In conclusion, the different result between 2 groups is **not by chance.**

Calculating the conversion rate between 2 groups, I found that:
*/

select 

test_group, 

count(distinct case when converted = 'TRUE' then user_id end) as completion,

convert(decimal(10,3), count(distinct case when converted = 'TRUE' then user_id end))/count(distinct user_id) as conversion_rate

from adjusted_marketing_A_B

group by test_group

/*
Because higher rate so the campaign **is more successful** in terms of conversion
*/

/*
**Question 2:** **If the campaign was successful, how much of that success could be attributed to the ads?**
*/

select 

test_group, 

most_ads_day,

count(distinct case when converted = 'TRUE' then user_id end) as completion,

convert(decimal(10,3), count(distinct case when converted = 'TRUE' then user_id end))/count(distinct user_id) as conversion_rate

from adjusted_marketing_A_B

group by test_group,most_ads_day

order by convert(decimal(10,3), count(distinct case when converted = 'TRUE' then user_id end))/count(distinct user_id) desc

/*
In both test group, the day with the highest conversion rate is Monday
*/

select 

test_group, 

most_ads_hour,

count(distinct case when converted = 'TRUE' then user_id end) as completion,

convert(decimal(10,3), count(distinct case when converted = 'TRUE' then user_id end))/count(distinct user_id) as conversion_rate

from adjusted_marketing_A_B

group by test_group,most_ads_hour

order by convert(decimal(10,3), count(distinct case when converted = 'TRUE' then user_id end))/count(distinct user_id) desc

/*
In the ad test group, the most profitable time is from 16h to 20h. In contrast, the most business time in psa test group is from 15h to 19h
*/

select

converted,

count(distinct a.user_id) as total_cohorted,

avg(total_ads) as avg_ads_seen,

stdev(total_ads) as stdev_ads_seen 

from

(select 

user_id,

test_group, 

converted,

total_ads from adjusted_marketing_A_B

where test_group = 'ad') as a

group by converted