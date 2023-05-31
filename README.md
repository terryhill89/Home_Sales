# Home Sales

## Overview:
use your knowledge of SparkSQL to determine key metrics about home sales data. Then you'll use Spark to create temporary views, partition the data, cache and uncache a temporary table, and verify that the table has been uncached.

## Instructions
**Answer the following questions using SparkSQL:**
  - What is the average price for a four-bedroom house sold for each year? Round off your answer to two decimal places.
  - What is the average price of a home for each year it was built that has three bedrooms and three bathrooms? Round off your answer to two decimal places.
  - What is the average price of a home for each year that has three bedrooms, three bathrooms, two floors, and is greater than or equal to 2,000 square feet?     Round off your answer to two decimal places.
  - What is the "view" rating for homes costing more than or equal to $350,000? Determine the run time for this query, and round off your answer to two           decimal places.
  
  ## Home Sales Questions, Answers and Code
***1. What is the average price for a four bedroom house sold in each year rounded to two decimal places?***
![Screenshot_20230531_014621](https://github.com/terryhill89/Home_Sales/assets/112741203/682688fd-7cc7-4c52-8c0c-6d52b7e71f1c)

`spark.sql("""select round(avg(price), 2) as price, bedrooms, date_built from home_sales_temp where bedrooms==4 group by 2, 3 order by date_built desc""").show()`
 
***2. What is the average price of a home for each year it was built that has three bedrooms and three bathrooms? Round off your answer to two decimal places.***

![Screenshot_20230531_015343](https://github.com/terryhill89/Home_Sales/assets/112741203/83a9feaf-ba95-4d22-99f7-205ac8a6dc67)

`spark.sql("""select round(avg(price), 2) as price, bedrooms, date_built from home_sales_temp where bedrooms==3 and bathrooms==3 group by 2, 3 order by date_built desc""").show()`

***3. What is the average price of a home for each year that has three bedrooms, three bathrooms, two floors, and is greater than or equal to 2,000 square feet? Round off your answer to two decimal places.***

![Screenshot_20230531_015857](https://github.com/terryhill89/Home_Sales/assets/112741203/ad331721-370e-47f6-b513-3d86fc958a19)

`spark.sql("""select round(avg(price), 2) as price, bedrooms, date_built from home_sales_temp where bedrooms==3 and bathrooms==3 and floors==2 and sqft_living>=2000 group by 2, 3 order by date_built desc""").show()`

***4. What is the "view" rating for homes costing more than or equal to $350,000? Determine the run time for this query, and round off your answer to two decimal places.***

![Screenshot_20230531_020206](https://github.com/terryhill89/Home_Sales/assets/112741203/e06b1f09-a6f6-4fa0-be81-d69da39652fb)

*start_time = time.time()*
`spark.sql("""select view, round(avg(price), 2) as price
  from home_sales_temp 
  group by 1
  having avg(price) >=350000
  order by view desc""").show()
  print("--- %s seconds ---" % (time.time() - start_time))`


## Cache the files
 - Cache the the temporary table home_sales.
  `spark.sql("cache table home_sales_temp")`
 -  Check if the table is cached.
  `spark.catalog.isCached('home_sales_temp')`
 - Using the cached data, run the query that filters out the view ratings with average price 
  greater than or equal to $350,000. Determine the runtime and compare it to uncached runtime.

 ![Screenshot_20230531_020853](https://github.com/terryhill89/Home_Sales/assets/112741203/881d8543-07ea-4cd1-ae45-e37de44fdf00)
 
 *start_time = time.time()*
 `spark.sql("""select view, round(avg(price), 2) as price 
  from home_sales_temp
  group by 1
  having avg(price) >=350000
  order by view desc""").show()
print("--- %s seconds ---" % (time.time() - start_time))`
 
 ## Parquet DataFrame
  - Partition by the "date_built" field on the formatted parquet home sales data 
`home_sales_data.write.partitionBy('date_built').parquet('p_home_sales_temp',mode='overwrite')`
  - Read the formatted parquet data.
`parq_homes_df = spark.read.parquet('p_home_sales_temp')`
  - Create a temporary table for the parquet data.
 `parq_homes_df.createOrReplaceTempView('parq_homes_df_temp')`
  - Run the query that filters out the view ratings with average price of greater than or eqaul to $350,000 with the parquet DataFrame. Round your average to two decimal places. Determine the runtime and compare it to the cached version. 
 
*start_time = time.time()*
`spark.sql("""select view, round(avg(price), 2) as price 
  from home_sales_temp
  group by 1
  having avg(price) >=350000
  order by view desc""").show()
print("--- %s seconds ---" % (time.time() - start_time))`

![Screenshot_20230531_021956](https://github.com/terryhill89/Home_Sales/assets/112741203/99420e9a-408f-4e6d-8578-86b353d0a187)

### Note: 
Remember to Uncache the temporary table and Check if the temporary table is no longer cached
`spark.sql("uncache table home_sales_temp")`
`spark.catalog.isCached("home_sales_temp")`




