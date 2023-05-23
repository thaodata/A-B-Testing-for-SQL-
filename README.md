
# A-B-Testing-for-SQL-
As a data analyst intern, I will guide you how to build an A/B testing report using SQL Server (or Azure Data Studio)

The report contains 4 part:
1. Introduction and Conclusion: set a clear context and questions which you want to ask. Plus, your boss have no time to read your report so make sure to put the conclusion at the first place.
2. Load and check data: try to give basic information about for dataset (shape, type, basic statistical value, percentile, missing values...)
3. Exploration Data Analysis (EDA): make sure you understand your dataset clearly through univariable, bivariable and multivariable analyst. You should create graphs to look more clearly
4. Data Process and Feature Engineering: it contains 3 main parts:
  4.1. Replace missing values (pick one in 3 ways: average value, 0 value or specific value which you can guess based on the relationship with other attributes)
  4.2. Finding Outliers and Replacing them (by boxplot or z_score, make sure you familiar with things like percentile, iqr and z_score. After spotting out outliers, you can treat them like delete, turn them into the maxium or minimum accepted values or scaling the whole value into log)
  4.3. Encoding (optional): if you are going to work with machine learning or just want to create dummy variables (Male = 0, Female = 1 etc), do this part
5. Data Analysis
  Answer these main questions based on your statistical knowledge and business sense. In my project, I will you Chi-squared test to determine whether the difference in conversation rate in 2 group test is significant or not.
------------------------------------------------------------------------------------------
Hope you enjoy my analysis
