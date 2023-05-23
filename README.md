This analyst is conducted by Thao Le Chanh.
If you use Azure Data Studio, please open the .json file to see the notebook
If you use SQL Server Studio, please open the .sql file to see my code and my analyst flow.
If you use neither of them, please open the .txt file named "A_B Testing Report Code" to read my code

Data source: Kaggle (https://www.kaggle.com/datasets/faviovaz/marketing-ab-testing)

Context: 

Marketing companies want to run successful campaigns, but the market is complex and several options can work. So normally they tun A/B tests, that is a randomized experimentation process wherein two or more versions of a variable (web page, page element, banner, etc.) are shown to different segments of people at the same time to determine which version leaves the maximum impact and drive business metrics.

The companies are interested in answering two questions:

- Would the campaign be successful?
- If the campaign was successful, how much of that success could be attributed to the ads?

Data Dictionary:

- Index: Row index
- user id: User ID (unique)
- test group: If "ad" the person saw the advertisement, if "psa" they only saw the public service announcement
- converted: If a person bought the product then True, else is False
- total ads: Amount of ads seen by person
- most ads day: Day that the person saw the biggest amount of ads
- most ads hour: Hour of day that the person saw the biggest amount of ads

CONCLUSION
1\. Based on the Chi-squared Test, the campaign was successful in terms of conversion rate.  
2\. Based on the analyst, the most profitable day is in Monday and the most profitable hours are from 16h to 20h (ad test group) and 15h to 19h (psa test group)
