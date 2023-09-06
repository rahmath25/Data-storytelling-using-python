
# Introduction

![Logo](https://www.mageworx.com/blog/wp-content/uploads/2020/01/30_blog_1060x454_Tiered_Discounts_Model__How_Does_it_Work_-1.png)

This project represents one of the accomplishments during my 6-month data science bootcamp, condensed into a focused week of intensive exploration.

Eniac is an online marketplace specializing in Apple-compatible accessories. The company's high hopes lie in Data Analysis to resolve an ongoing debate : **whether or not it’s beneficial to discount products**.

+ The Marketing Team Lead believes discounts drive growth through improved customer acquisition, satisfaction, and retention.

+ The board's primary investors express concern about aggressive discounts, citing recent results: higher orders, lower revenue. They advocate quality positioning over lowest prices.




## Problem Statement

Analytical and business skills are needed to provide clarity in the following aspects:

+ How products should be classified into different categories to simplify reports and analysis.
+ What is the distribution of product prices across different categories.
+ How many products are being discounted.
+ How big are the offered discounts as a percentage of the product prices.
+ How seasonality and special dates (Christmas, Black Friday) affect sales.
+ How could data collection be improved.
## Skills Utilized

+ **Data Cleaning and Quality** - transformed a messy dataset into a clean and trustable source ready for insight-extraction.
+ **Data Visualization** - created professional-looking plots using Python to effectively convey clear messages.
+ **Data storytelling** - crafted a narrative that drives business actions



## Tools

+ **Google Colaboratory** - Environment for Notebooks
+ **Python Programming** 
+ **Python Modules** - pandas, numPy, seaborn, matplotlib
## Dataset

Dataset was provided by the Data Science Bootcamp in the form of 4 csv files.


+ **orders.csv** - Each row in the file represents an order with a unique order_id, creation timestamp, total customer payment, and order status ("shopping basket," "pending," "completed," "cancelled"). 

+ **orderlines.csv** - Each row corresponds to a product in an order with a unique id linked to the order, product identifier, quantity, SKU, unit price (in euros), and processing timestamp.

+ **products.csv** - Each entry includes a SKU (stock keeping unit), product name, description, stock availability, and numerical product type code.

+ **brands.csv** - Comprises a short 3-character code derived from the first characters of products's SKUs and a corresponding long brand name.

Here’s the detailed description of each table and its columns.
## Data Cleaning

+ Duplicate rows were checked and eliminated from each table.
+ Rows with missing values were removed from all the tables since they constituted less than 0.003% of the total rows in the DataFrame.
+ Rows with price columns containing two or more decimal places were removed from the dataset.
+ Numeric columns that were mistakenly assigned an "object" datatype have been corrected to the appropriate numeric datatype.



## Quality Assessment

### Exclude unwanted Orders
+ Only "Completed" orders are retained for further analysis, while those in the shopping basket, pending, or canceled statuses are excluded.

+ Kept only the orders that are present in both orders and orderlines Dataframe. An inner merge was performed on the two tables, utilizing the foreign key and primary key relationships, resulting in the retention of only the order_id's that exist in both tables.

### Exclude orders with unknown products
+ Unknown product orders were excluded, while those with known products were discerned by comparing SKUs (Stock Keeping Units) in the product table to those in the orderlines table. Any remaining unknown product orders were subsequently removed for further analysis.

### Explore the revenue from different tables

**products.price:** Original product price before any discounts or promotions are applied.

**orderlines.unit_price:** Actual price at which a product is sold, which may deviate from the original price due to discounts.

**orders.total_paid:** Total amount paid for the entire order, including product prices (sum of unit prices multiplied by quantities) and any additional costs like shipping or vouchers.

**orderlines.unit_price_total:** Manually calculated by multiplying **orderlines.product_quantity** with **orderlines.unit_price**, grouped by order_id, and summarized as the sum of unit_price_total for each order.


```python
orderlines["unit_price_total"] = orderlines["product_quantity"] * orderlines["unit_price"]
orderlines_qu = orderlines.groupby("id_order", as_index=False)["unit_price_total"].sum()
```
The average difference between **orders.total_paid** and **orderlines.unit_price_total** is calculated as 4.47. 

```python
diff_df["difference"] = diff_df["total_paid"] - diff_df["unit_price_total"]
diff_df.difference.mean().round(2)
```

Outliers in **orderlines.unit_price_total** and their corresponding orders are excluded based on the rule: values below 25% quartile - (1.5 x interquartile range) or above 75% quartile + (1.5 x interquartile range) are considered outliers and are removed for further analysis.
```python
# calculate the quartiles
Q1 = diff_df["difference"].quantile(0.25)
Q3 = diff_df["difference"].quantile(0.75)

# calculate the interquartile range
IQR = Q3-Q1

# filter the DataFrame to include only "non-outliers"
diff_no_outliers_df = diff_df.loc[(diff_df["difference"] >= (Q1 - 1.5*IQR)) & (diff_df["difference"] <= (Q3 + 1.5*IQR))]
```




## Creating a Product Category and Discount Column 

### Product Category Column

The project's primary goal is to develop a discount pricing strategy based on product prices. To enhance our analysis, a product categories was created as a meaningful way to organize and manipulate the data.

A new "product_categories" column was added to the product table and initialized with empty strings.

+ Identified products with specific keywords in descriptions (e.g., "keyboard") using .loc[] and .str.contains() and assigned them to the "keyboard" category.

+ Used regex to refine category assignment for products with names like "apple iphone," etc.

+ Combined categories for various products, allowing multiple categories for each.

Overall, this process helped us categorize products based on keywords and regex patterns in their descriptions and names, enabling more detailed analysis and organization of the data.

### Discount Column

**product** and **orderlines** tables are merged and discount is calculated by following formula. Discount is calculated by **(product.price - orderlines.unit_price)/(orderlines.unit_price)**
## Data Analysis, Data Visualization and Conclusion

The process of **Data Analysis**, **Visualization**, and drawing **Conclusions** has been presented through a [PowerPoint presentation](https://docs.google.com/presentation/d/1sIlop0RNP5RHv3d-1m-w_xU1t8leh_vb1-K1AHQsark/edit?usp=sharing).
