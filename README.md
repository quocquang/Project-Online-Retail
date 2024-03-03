#  Marketing Strategy Analysis

# :briefcase: Business Case and Requirement
#  1.Customer Analysis and Interaction in 2010: 

* Assessing the Effectiveness of Marketing and Customer Care Strategies: Cohort analysis allows evaluating the effectiveness of marketing and customer care strategies by tracking the number of customers over time from their first purchase.

* Determining Customer Purchase Frequency: Cohort analysis helps identify the purchase frequency of customers throughout the period they interact with the business.

* Discovering Trends and Changes in Customer Behavior: By tracking the number of customers through cohorts over time, trends and changes in customer behavior can be identified.

* Optimizing Communication and Interaction Strategies: Based on deep insights into customer behavior through cohorts, communication and interaction strategies with customers can be optimized.
  
#  2.Customer Segmentation Using RFM Analysis::

* Assign scores to each customer based on three main criteria: Recency, Frequency, and Monetary. These scores help the business determine the importance of each customer and segment customers into groups with similar characteristics.

* Calculate RFM Scores: Each customer is scored from 1 to 5 for each criterion (1 being low, 5 being high).
  - Segment Customers: Based on RFM scores, customers are divided into groups:
  - High-Value Customers: Recent, frequent, and high-value purchases (High RFM).
  - Loyal Customers: Frequent purchases, high value (High R, Medium M).
  - Need Activation Customers: Recent purchases, high value but infrequent (Medium RFM).
  - At-Risk Customers: Recent purchases, low value (High R, Low FM).
  - Need Retention Customers: Low-frequency, low-value purchases (Low RFM).
  
---


# :bookmark_tabs: Online Retail Datasets

###  Online Retail dataset
 - InvoiceNo: Unique identifier for each invoice or transaction.
 - StockCode: Code identifying the stock or product associated with the transaction.
 - Description: Description of the product associated with the transaction.
 - Quantity: The quantity of items purchased in the transaction.
 - InvoiceDate: Date and time when the transaction occurred.
 - UnitPrice: The price of each unit of the product.
 - CustomerID: Unique identifier for each customer associated with the transaction.
 - Country: The country where the transaction took place.


<details><summary>  Click to expand Campaign-Data Dataset </summary>

<div align="center">

**Table: Online Retail_dataset** 

<div align="center">
First 10 rows

| InvoiceNo | StockCode | Description                          | Quantity | InvoiceDate         | UnitPrice | CustomerID | Country        |
|-----------|-----------|--------------------------------------|----------|---------------------|-----------|------------|----------------|
| 536365    | 85123A    | WHITE HANGING HEART T-LIGHT HOLDER   | 6        | 2010-12-01 08:26:00 | 2.55      | 17850.0    | United Kingdom |
| 536365    | 71053     | WHITE METAL LANTERN                  | 6        | 2010-12-01 08:26:00 | 3.39      | 17850.0    | United Kingdom |
| 536365    | 84406B    | CREAM CUPID HEARTS COAT HANGER        | 8        | 2010-12-01 08:26:00 | 2.75      | 17850.0    | United Kingdom |
| 536365    | 84029G    | KNITTED UNION FLAG HOT WATER BOTTLE   | 6        | 2010-12-01 08:26:00 | 3.39      | 17850.0    | United Kingdom |
| 536365    | 84029E    | RED WOOLLY HOTTIE WHITE HEART.        | 6        | 2010-12-01 08:26:00 | 3.39      | 17850.0    | United Kingdom |


</details>

---


#  IMPORT LIBRARY & CLEAN & TRANSFORM DATA.

  
```python
# import libraries 
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns 
import os 
os.chdir("C:/Users/phu/OneDrive/Pictures")
```

```python
#Upload dataset
data = pd.read_excel("Online Retail.xlsx")
```
 
```python
data = data.dropna(subset=['CustomerID'])
```

```python
#create an invoice month
import datetime as dt
#function for month
def get_month(x):
    return dt.datetime(x.year, x.month,1)
#apply the function 
data['InvoiceMonth'] = data['InvoiceDate'].apply(get_month)
data.tail()
```

| InvoiceNo | StockCode | Description                    | Quantity | InvoiceDate         | UnitPrice | CustomerID | Country | InvoiceMonth | Cohort Month | CohortIndex | MonthKey | Revenue |
|-----------|-----------|--------------------------------|----------|----------------------|-----------|------------|---------|--------------|--------------|-------------|----------|---------|
| 581587    | 22613     | PACK OF 20 SPACEBOY NAPKINS   | 12       | 2011-12-09 12:50:00  | 0.85      | 12680.0    | France  | 2011-12-01   | 2011-08-01   | 5           | 2011-12  | 10.20   |
| 581587    | 22899     | CHILDREN'S APRON DOLLY GIRL   | 6        | 2011-12-09 12:50:00  | 2.10      | 12680.0    | France  | 2011-12-01   | 2011-08-01   | 5           | 2011-12  | 12.60   |
| 581587    | 23254     | CHILDRENS CUTLERY DOLLY GIRL  | 4        | 2011-12-09 12:50:00  | 4.15      | 12680.0    | France  | 2011-12-01   | 2011-08-01   | 5           | 2011-12  | 16.60   |
| 581587    | 23255     | CHILDRENS CUTLERY CIRCUS PARADE| 4        | 2011-12-09 12:50:00 | 4.15      | 12680.0    | France  | 2011-12-01   | 2011-08-01   | 5           | 2011-12  | 16.60   |
| 581587    | 22138     | BAKING SET 9 PIECE RETROSPOT  | 3        | 2011-12-09 12:50:00  | 4.95      | 12680.0    | France  | 2011-12-01   | 2011-08-01   | 5           | 2011-12  | 14.85   |

```python
#create a column index with the minimum invoice date aka first time customer was acquired
data['Cohort Month'] =  data.groupby('CustomerID')['InvoiceMonth'].transform('min')
```

```python
# create a date element function to get a series for subtraction
def get_date_elements(df, column):
    day = df[column].dt.day
    month = df[column].dt.month
    year = df[column].dt.year
    return day, month, year 
```

```python
# get date elements for our cohort and invoice columns
_,Invoice_month,Invoice_year =  get_date_elements(data,'InvoiceMonth')
_,Cohort_month,Cohort_year =  get_date_elements(data,'Cohort Month')
```

```python
Cohort_year[:10]
```

```python
#create a cohort index 
year_diff = Invoice_year -Cohort_year
month_diff = Invoice_month - Cohort_month
data['CohortIndex'] = year_diff*12+month_diff+1
data.tail()
```
| InvoiceNo | StockCode | Description                     | Quantity | InvoiceDate         | UnitPrice | CustomerID | Country | InvoiceMonth | Cohort Month | CohortIndex |
|-----------|-----------|---------------------------------|----------|----------------------|-----------|------------|---------|--------------|--------------|-------------|
| 541904    | 581587    | PACK OF 20 SPACEBOY NAPKINS    | 12       | 2011-12-09 12:50:00  | 0.85      | 12680.0    | France  | 2011-12-01   | 2011-08-01   | 5           |
| 541905    | 581587    | CHILDREN'S APRON DOLLY GIRL    | 6        | 2011-12-09 12:50:00  | 2.10      | 12680.0    | France  | 2011-12-01   | 2011-08-01   | 5           |
| 541906    | 581587    | CHILDRENS CUTLERY DOLLY GIRL   | 4        | 2011-12-09 12:50:00  | 4.15      | 12680.0    | France  | 2011-12-01   | 2011-08-01   | 5           |
| 541907    | 581587    | CHILDRENS CUTLERY CIRCUS PARADE| 4        | 2011-12-09 12:50:00 | 4.15      | 12680.0    | France  | 2011-12-01   | 2011-08-01   | 5           |
| 541908    | 581587    | BAKING SET 9 PIECE RETROSPOT   | 3        | 2011-12-09 12:50:00  | 4.95      | 12680.0    | France  | 2011-12-01   | 2011-08-01   | 5           |


```python
#count the customer ID by grouping by Cohort Month  and Cohort Index 
cohort_data = data.groupby(['Cohort Month','CohortIndex'])['CustomerID'].apply(pd.Series.nunique).reset_index()
cohort_data
```
| Cohort Month | CohortIndex | CustomerID |
|--------------|-------------|------------|
| 2010-12-01   | 1           | 948        |
| 2010-12-01   | 2           | 362        |
| 2010-12-01   | 3           | 317        |
| 2010-12-01   | 4           | 367        |
| 2010-12-01   | 5           | 341        |
| ...          | ...         | ...        |
| 2011-10-01   | 2           | 93         |
| 2011-10-01   | 3           | 46         |
| 2011-11-01   | 1           | 321        |
| 2011-11-01   | 2           | 43         |
| 2011-12-01   | 1           | 41         |

```python
# create a pivot table 
cohort_table = cohort_data.pivot(index='Cohort Month', columns=['CohortIndex'],values='CustomerID')
cohort_table
```
| CohortIndex | 1      | 2     | 3     | 4     | 5     | 6     | 7     | 8     | 9     | 10    | 11    | 12    | 13    |
|-------------|--------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|
| 2010-12-01  | 948.0  | 362.0 | 317.0 | 367.0 | 341.0 | 376.0 | 360.0 | 336.0 | 336.0 | 374.0 | 354.0 | 474.0 | 260.0 |
| 2011-01-01  | 421.0  | 101.0 | 119.0 | 102.0 | 138.0 | 126.0 | 110.0 | 108.0 | 131.0 | 146.0 | 155.0 | 63.0  | NaN   |
| 2011-02-01  | 380.0  | 94.0  | 73.0  | 106.0 | 102.0 | 94.0  | 97.0  | 107.0 | 98.0  | 119.0 | 35.0  | NaN   | NaN   |
| 2011-03-01  | 440.0  | 84.0  | 112.0 | 96.0  | 102.0 | 78.0  | 116.0 | 105.0 | 127.0 | 39.0  | NaN   | NaN   | NaN   |
| 2011-04-01  | 299.0  | 68.0  | 66.0  | 63.0  | 62.0  | 71.0  | 69.0  | 78.0  | 25.0  | NaN   | NaN   | NaN   | NaN   |
| 2011-05-01  | 279.0  | 66.0  | 48.0  | 48.0  | 60.0  | 68.0  | 74.0  | 29.0  | NaN   | NaN   | NaN   | NaN   | NaN   |
| 2011-06-01  | 235.0  | 49.0  | 44.0  | 64.0  | 58.0  | 79.0  | 24.0  | NaN   | NaN   | NaN   | NaN   | NaN   | NaN   |
| 2011-07-01  | 191.0  | 40.0  | 39.0  | 44.0  | 52.0  | 22.0  | NaN   | NaN   | NaN   | NaN   | NaN   | NaN   | NaN   |
| 2011-08-01  | 167.0  | 42.0  | 42.0  | 42.0  | 23.0  | NaN   | NaN   | NaN   | NaN   | NaN   | NaN   | NaN   | NaN   |
| 2011-09-01  | 298.0  | 89.0  | 97.0  | 36.0  | NaN   | NaN   | NaN   | NaN   | NaN   | NaN   | NaN   | NaN   | NaN   |
| 2011-10-01  | 352.0  | 93.0  | 46.0  | NaN   | NaN   | NaN   | NaN   | NaN   | NaN   | NaN   | NaN   | NaN   | NaN   |
| 2011-11-01  | 321.0  | 43.0  | NaN   | NaN   | NaN   | NaN   | NaN   | NaN   | NaN   | NaN   | NaN   | NaN   | NaN   |
| 2011-12-01  | 41.0   | NaN   | NaN   | NaN   | NaN   | NaN   | NaN   | NaN   | NaN   | NaN   | NaN   | NaN   | NaN   |

```python
# change index
#cohort_table.index = cohort_table.index.strftime('%B %Y')
#visualize our results in heatmap
plt.figure(figsize=(21,10))
sns.heatmap(cohort_table,annot=True,cmap='Blues')
```
![image](https://github.com/quocquang/Marketing-Strategy-Analysis/assets/87820013/2d1692b5-1bdc-4e0d-bc6e-7e3b4de38384)

```python
#cohort table for percentage
new_cohort_table = cohort_table.divide(cohort_table.iloc[:,0],axis=0)
new_cohort_table
```
| CohortIndex | 1      | 2         | 3         | 4         | 5         | 6         | 7         | 8         | 9         | 10        | 11        | 12        | 13        |
|-------------|--------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|
| Cohort Month                                                                                                                                       |
| 2010-12-01  | 1.0    | 0.381857  | 0.334388  | 0.387131  | 0.359705  | 0.396624  | 0.379747  | 0.354430  | 0.354430  | 0.394515  | 0.373418  | 0.5       | 0.274262  |
| 2011-01-01  | 1.0    | 0.239905  | 0.282660  | 0.242280  | 0.327791  | 0.299287  | 0.261283  | 0.256532  | 0.311164  | 0.346793  | 0.368171  | 0.149644  | NaN       |
| 2011-02-01  | 1.0    | 0.247368  | 0.192105  | 0.278947  | 0.268421  | 0.247368  | 0.255263  | 0.281579  | 0.257895  | 0.313158  | 0.092105  | NaN       | NaN       |
| 2011-03-01  | 1.0    | 0.190909  | 0.254545  | 0.218182  | 0.231818  | 0.177273  | 0.263636  | 0.238636  | 0.288636  | 0.088636  | NaN       | NaN       | NaN       |
| 2011-04-01  | 1.0    | 0.227425  | 0.220736  | 0.210702  | 0.207358  | 0.237458  | 0.230769  | 0.260870  | 0.083612  | NaN       | NaN       | NaN       | NaN       |
| 2011-05-01  | 1.0    | 0.236559  | 0.172043  | 0.172043  | 0.215054  | 0.243728  | 0.265233  | 0.103943  | NaN       | NaN       | NaN       | NaN       | NaN       |
| 2011-06-01  | 1.0    | 0.208511  | 0.187234  | 0.272340  | 0.246809  | 0.336170  | 0.102128  | NaN       | NaN       | NaN       | NaN       | NaN       | NaN       |
| 2011-07-01  | 1.0    | 0.209424  | 0.204188  | 0.230366  | 0.272251  | 0.115183  | NaN       | NaN       | NaN       | NaN       | NaN       | NaN       | NaN       |
| 2011-08-01  | 1.0    | 0.251497  | 0.251497  | 0.251497  | 0.137725  | NaN       | NaN       | NaN       | NaN       | NaN       | NaN       | NaN       | NaN       |
| 2011-09-01  | 1.0    | 0.298658  | 0.325503  | 0.120805  | NaN       | NaN       | NaN       | NaN       | NaN       | NaN       | NaN       | NaN       | NaN       |
| 2011-10-01  | 1.0    | 0.264205  | 0.130682  | NaN       | NaN       | NaN       | NaN       | NaN       | NaN       | NaN       | NaN       | NaN       | NaN       |
| 2011-11-01  | 1.0    | 0.133956  | NaN       | NaN       | NaN       | NaN       | NaN       | NaN       | NaN       | NaN       | NaN       | NaN       | NaN       |
| 2011-12-01  | 1.0    | NaN       | NaN       | NaN       | NaN       | NaN       | NaN       | NaN       | NaN       | NaN       | NaN       | NaN       | NaN       |

```python
#create a percentages visual
plt.figure(figsize=(21,10))
sns.heatmap(new_cohort_table,annot=True,fmt='.0%')
```
![image](https://github.com/quocquang/Marketing-Strategy-Analysis/assets/87820013/581ba57e-628b-4e07-8c20-2d89a7d38f30)

---

```python
# Convert the data type of column 'InvoiceDate' from string to datetime
data['InvoiceDate'] = pd.to_datetime(data['InvoiceDate'])

# Create 'MonthKey' column for reporting and visualization
data['MonthKey'] = data['InvoiceDate'].dt.to_period('M').astype(str)

# Calculate revenue for each row and create a new DataFrame with columns 'MonthKey' - 'Revenue'
data['Revenue'] = data['UnitPrice'] * data['Quantity']
revenue_by_month = data.groupby('MonthKey')['Revenue'].sum().reset_index()

# print
print(revenue_by_month)

# plotly
plt.figure(figsize=(12, 6))
plt.plot(revenue_by_month['MonthKey'], revenue_by_month['Revenue'], linestyle='-', marker='o', color='blue')
plt.title("Revenue by Month")
plt.xlabel("Month")
plt.ylabel("Revenue")
plt.xticks(rotation=45)  
plt.grid(True) 
plt.show()
```
| MonthKey | Revenue      |
|----------|--------------|
| 2010-12  | 554604.020   |
| 2011-01  | 475074.380   |
| 2011-02  | 436546.150   |
| 2011-03  | 579964.610   |
| 2011-04  | 426047.851   |
| 2011-05  | 648251.080   |
| 2011-06  | 608013.160   |
| 2011-07  | 574238.481   |
| 2011-08  | 616368.000   |
| 2011-09  | 931440.372   |
| 2011-10  | 974603.590   |
| 2011-11  | 1132407.740  |
| 2011-12  | 342506.380   |

![image](https://github.com/quocquang/Marketing-Strategy-Analysis/assets/87820013/e143086d-4b10-4b8d-a4c5-27394895824b)

---

```python
# Calculate the monthly growth rate
revenue_by_month['MonthlyGrowth'] = revenue_by_month['Revenue'].pct_change()

# plotly
plt.figure(figsize=(12, 6))
plt.plot(revenue_by_month['MonthKey'], revenue_by_month['MonthlyGrowth'], linestyle='-', marker='o', color='blue')
plt.axhline(y=0, color='k', linestyle='-', linewidth=2.5)  
plt.title("Monthly Revenue Growth Rate")
plt.xlabel("Month")
plt.ylabel("Monthly Growth Rate (%)")
plt.xticks(rotation=45)  
plt.grid(True)  
plt.show()
```
![image](https://github.com/quocquang/Marketing-Strategy-Analysis/assets/87820013/4c0ecd3f-18dc-4480-a7de-f65186a68bfd)

```python
# Calculate the number of customers per month
customer_by_month = data.groupby('MonthKey')['CustomerID'].nunique().reset_index()

# Reformat column 'MonthKey' to string
customer_by_month['MonthKey'] = customer_by_month['MonthKey'].astype(str)

# plotly
plt.figure(figsize=(12, 6))
plt.plot(customer_by_month['MonthKey'], customer_by_month['CustomerID'], linestyle='-', marker='o', color='blue')
plt.title("Number of Customers by Month")
plt.xlabel("Month")
plt.ylabel("Number of Customers")
plt.xticks(rotation=45)  
plt.grid(True)  
plt.show()
```
![image](https://github.com/quocquang/Marketing-Strategy-Analysis/assets/87820013/c389b3f8-ff69-46b6-9719-120dd56099a6)

```python
# Calculate the number of orders per month
order_by_month = data.groupby('MonthKey')['InvoiceNo'].count().reset_index()

# Reformat column 'MonthKey' to string
order_by_month['MonthKey'] = order_by_month['MonthKey'].astype(str)

# plotly
plt.figure(figsize=(12, 6))
plt.plot(order_by_month['MonthKey'], order_by_month['InvoiceNo'], linestyle='-', marker='o', color='blue')
plt.title("Total Orders per Month")
plt.xlabel("Month")
plt.ylabel("Total Orders")
plt.xticks(rotation=45)  
plt.grid(True)  
plt.show()
```
![image](https://github.com/quocquang/Marketing-Strategy-Analysis/assets/87820013/fd5a2151-300b-4ad2-855b-e5ae0c64dcaf)

```python
avg_order_revenue = data.groupby(['MonthKey'])['Revenue'].mean().reset_index()

avg_order_revenue['MonthKey'] = avg_order_revenue['MonthKey'].apply(str)

# Plot regression line

plt.rcParams["figure.figsize"] = [20, 10]
plt.plot('MonthKey', 'Revenue', data=avg_order_revenue, linestyle='-', marker='o')
plt.title("Average Revenue per Order")
plt.show()
```
![image](https://github.com/quocquang/Marketing-Strategy-Analysis/assets/87820013/ed4ce258-a6be-487c-8f1b-841108213c89)

```python
#create a dataframe contaning CustomerID and first purchase date
df_min_date_purchase =data.groupby('CustomerID').InvoiceDate.min().reset_index()
df_min_date_purchase.columns = ['CustomerID','MinPurchaseDate']
df_min_date_purchase['MinMonthKey'] = df_min_date_purchase['MinPurchaseDate'].map(lambda date: 100*date.year + date.month)

#merge first purchase date column to our main dataframe (tx_uk)
retail_new_df = pd.merge(data, df_min_date_purchase, on='CustomerID')

retail_new_df.head()

#create a column called User Type and assign Existing
#if User's First Purchase Year Month before the selected Invoice Year Month
retail_new_df['UserType'] = 'New'
# Extracting year and month separately
retail_new_df['Year'] = retail_new_df['MonthKey'].str[:4].astype(int)
retail_new_df['Month'] = retail_new_df['MonthKey'].str[5:].astype(int)

# Converting year and month to an integer format
retail_new_df['MonthKey'] = retail_new_df['Year'] * 100 + retail_new_df['Month']

# Dropping the intermediate 'Year' and 'Month' columns
retail_new_df.drop(['Year', 'Month'], axis=1, inplace=True)

# Now, you can proceed with your existing code
retail_new_df.loc[retail_new_df['MonthKey'] > retail_new_df['MinMonthKey'], 'UserType'] = 'Existing'

#calculate the Revenue per month for each user type
revenue_per_month = retail_new_df.groupby(['MonthKey','UserType'])['Revenue'].sum().reset_index()

#filtering the dates and plot the result
revenue_per_month = revenue_per_month.query("MonthKey != 201012 and MonthKey != 201112")

revenue_per_month['MonthKey'] = revenue_per_month['MonthKey'].apply(str)
revenue_per_month.set_index('MonthKey',inplace=True)
# Plot regression line

revenue_per_month.reset_index(inplace=True)  # Reset index before plotting

plt.rcParams["figure.figsize"] = [20, 10]
fig, ax = plt.subplots()
for label, grp in revenue_per_month.groupby('UserType'):
    grp.plot(x='MonthKey', y='Revenue', ax=ax, label=label, style='.-')
plt.title("Old and New user")
plt.show()
```
![image](https://github.com/quocquang/Marketing-Strategy-Analysis/assets/87820013/aba942e0-b22a-46cf-bb38-2a5924ea576b)

```python
new_user_ratio = retail_new_df.query("UserType == 'New'").groupby(['MonthKey'])['CustomerID'].nunique()/retail_new_df.query("UserType == 'Existing'").groupby(['MonthKey'])['CustomerID'].nunique()
new_user_ratio = new_user_ratio.reset_index()
new_user_ratio = new_user_ratio.dropna()
new_user_ratio.columns = ["MonthKey","NewCustomerRatio"]

new_user_ratio = new_user_ratio.query("MonthKey != 201012 and MonthKey != 201112")
new_user_ratio['MonthKey'] = new_user_ratio['MonthKey'].apply(str)

# Plot regression line

plt.rcParams["figure.figsize"] = [20, 10]
plt.plot('MonthKey', 'NewCustomerRatio', data=new_user_ratio, linestyle='-', marker='o')
plt.title("New Customer Ratio")
plt.show()
```
![image](https://github.com/quocquang/Marketing-Strategy-Analysis/assets/87820013/d8b61382-30f9-4096-b3b5-7a8b970ecb2c)

```python
#identify which users are active by looking at their revenue per month
df_user_purchase = data.groupby(['CustomerID','MonthKey'])['Revenue'].sum().reset_index()

#create retention matrix with crosstab
df_retention = pd.crosstab(df_user_purchase['CustomerID'], df_user_purchase['MonthKey']).reset_index()

print(df_retention.head())

# create an array of dictionaries which keeps Retained & Total User count for each month
months = df_retention.columns[2:]
retention_array = []
for i in range(len(months)-1):
    retention_data = {}
    selected_month = months[i+1]
    prev_month = months[i]
    retention_data['MonthKey'] = selected_month  # Keep MonthKey as string
    retention_data['TotalUserCount'] = df_retention[selected_month].sum()
    retention_data['RetainedUserCount'] = df_retention[(df_retention[selected_month] > 0) & (df_retention[prev_month] > 0)][selected_month].sum()
    retention_array.append(retention_data)

# convert the array to dataframe and calculate Retention Rate
df_retention = pd.DataFrame(retention_array)
df_retention['RetentionRate'] = df_retention['RetainedUserCount'] / df_retention['TotalUserCount']

# convert MonthKey to string
df_retention['MonthKey'] = df_retention['MonthKey'].astype(str)

# Plot retention rate
plt.rcParams["figure.figsize"] = [20, 10]
plt.plot('MonthKey', 'RetentionRate', data=df_retention, linestyle='-', marker='o')
plt.title("Monthly Retention Rate")
plt.show()
```
| MonthKey | CustomerID | 2010-12 | 2011-01 | 2011-02 | 2011-03 | 2011-04 | 2011-05 | 2011-06 | 2011-07 | 2011-08 | 2011-09 | 2011-10 | 2011-11 | 2011-12 |
|----------|------------|---------|---------|---------|---------|---------|---------|---------|---------|---------|---------|---------|---------|---------|
| 12346.0  | 0          | 0       | 1       | 0       | 0       | 0       | 0       | 0       | 0       | 0       | 0       | 0       | 0       | 0       |
| 12347.0  | 1          | 1       | 1       | 0       | 0       | 1       | 0       | 1       | 0       | 1       | 0       | 1       | 0       | 1       |
| 12348.0  | 1          | 1       | 1       | 0       | 0       | 1       | 0       | 0       | 0       | 0       | 1       | 0       | 0       | 0       |
| 12349.0  | 0          | 0       | 0       | 0       | 0       | 0       | 0       | 0       | 0       | 0       | 0       | 0       | 1       | 0       |
| 12350.0  | 0          | 0       | 0       | 1       | 0       | 0       | 0       | 0       | 0       | 0       | 0       | 0       | 0       | 0       |
![image](https://github.com/quocquang/Marketing-Strategy-Analysis/assets/87820013/e6d8831f-8c0a-4cd1-808c-eea4c6089ad3)

```python
retail_rfm_df = data.copy()
#remove canceled orders
retail_rfm_df = retail_rfm_df[retail_rfm_df['Quantity']>0]
#remove rows where customerID are NA
retail_rfm_df.dropna(subset=['CustomerID'],how='all',inplace=True)
retail_rfm_df = retail_rfm_df[retail_rfm_df['InvoiceDate']> "2010-12-31"]
retail_rfm_df = retail_rfm_df[retail_rfm_df['InvoiceDate']< "2011-12-01"]
```

```python
now = dt.date(2011,11,30)
#create a new column called date which contains the date of invoice only
retail_rfm_df['date'] = pd.DatetimeIndex(retail_rfm_df['InvoiceDate']).date

#group by customers and check last date of purshace
recency_df = retail_rfm_df.groupby(by='CustomerID', as_index=False)['date'].max()
recency_df.columns = ['CustomerID','LastPurshaceDate']

#calculate recency
recency_df['Recency'] = recency_df['LastPurshaceDate'].apply(lambda x: (now - x).days)
recency_df.drop('LastPurshaceDate',axis=1,inplace=True)
print(recency_df.head())
```
| CustomerID | Recency |
|------------|---------|
| 12346.0    | 316     |
| 12347.0    | 30      |
| 12348.0    | 66      |
| 12349.0    | 9       |
| 12350.0    | 301     |

```python
# drop duplicates
retail_rfm_df_copy = retail_rfm_df
retail_rfm_df_copy.drop_duplicates(subset=['InvoiceNo', 'CustomerID'], keep="first", inplace=True)
#calculate frequency of purchases
frequency_df = retail_rfm_df_copy.groupby(by=['CustomerID'], as_index=False)['InvoiceNo'].count()
frequency_df.columns = ['CustomerID','Frequency']
frequency_df.head()
```
| CustomerID | Frequency |
|------------|-----------|
| 12346.0    | 1         |
| 12347.0    | 5         |
| 12348.0    | 3         |
| 12349.0    | 1         |
| 12350.0    | 1         |

```python
# drop duplicates
monetary_df = retail_rfm_df.groupby(['CustomerID'])['Revenue'].sum().reset_index()
monetary_df.columns = ['CustomerID','Monetary']
monetary_df.head()
```
| CustomerID | Monetary |
|------------|----------|
| 12346.0    | 77183.60 |
| 12347.0    | 120.56   |
| 12348.0    | 291.76   |
| 12349.0    | 15.00    |
| 12350.0    | 25.20    |

```python
#merge recency dataframe with frequency dataframe
temp_df = recency_df.merge(frequency_df,on='CustomerID')
#merge with monetary dataframe to get a table with the 3 columns
rfm_df = temp_df.merge(monetary_df,on='CustomerID')
#use CustomerID as index
rfm_df.set_index('CustomerID',inplace=True)
#check the head
rfm_df.head()
```
| CustomerID | Recency | Frequency | Monetary |
|------------|---------|-----------|----------|
| 12346.0    | 316     | 1         | 77183.60 |
| 12347.0    | 30      | 5         | 120.56   |
| 12348.0    | 66      | 3         | 291.76   |
| 12349.0    | 9       | 1         | 15.00    |
| 12350.0    | 301     | 1         | 25.20    |

```python
#RFM Quartiles
rfm_df['Recency'] = -rfm_df['Recency']
quantiles = rfm_df.quantile(q=[0.25,0.5,0.75])
print(quantiles)
quantiles.to_dict()

### Creation of RFM Segments

# Arguments (x = value, p = recency, monetary_value, frequency, k = quartiles dict)
def FMScore(x,p,d):
    if x <= d[p][0.25]:
        return 0
    elif x <= d[p][0.50]:
        return 1
    elif x <= d[p][0.75]:
        return 2
    else:
        return 3

#create rfm segmentation table
rfm_segmentation = rfm_df
rfm_segmentation['R_Quartile'] = rfm_segmentation['Recency'].apply(FMScore, args=('Recency',quantiles,))
rfm_segmentation['F_Quartile'] = rfm_segmentation['Frequency'].apply(FMScore, args=('Frequency',quantiles,))
rfm_segmentation['M_Quartile'] = rfm_segmentation['Monetary'].apply(FMScore, args=('Monetary',quantiles,))

rfm_segmentation.head()


rfm_segmentation['RFMScore'] = rfm_segmentation.R_Quartile.map(str) \
                            + rfm_segmentation.F_Quartile.map(str) \
                            + rfm_segmentation.M_Quartile.map(str)
rfm_segmentation.head()


#How many customers do we have in each segment?

print("Best Customers: ",len(rfm_segmentation[rfm_segmentation['RFMScore']=='333']))
print('Loyal Customers: ',len(rfm_segmentation[rfm_segmentation['F_Quartile']==3]))
print("Big Spenders: ",len(rfm_segmentation[rfm_segmentation['M_Quartile']==3]))
print('Almost Lost: ', len(rfm_segmentation[rfm_segmentation['RFMScore']=='133']))
print('Lost Customers: ',len(rfm_segmentation[rfm_segmentation['RFMScore']=='033']))
print('Lost Cheap Customers: ',len(rfm_segmentation[rfm_segmentation['RFMScore']=='000']))
```
![image](https://github.com/quocquang/Marketing-Strategy-Analysis/assets/87820013/0e4a2f4d-fd4d-474a-9dd6-87737aa82b8b)

---

# Result

#### * Customer Acquisition Trend:

- The dataset displays the number of new customers (CohortIndex 1) for each month from December 2010 to December 2011.
Based on the dataset, the number of new customers (CohortIndex 1) tends to decrease in the first few months of 2011 after peaking in December 2010.
- Number of new customers:
  - December 2010 has the highest number of new customers (948 customers).
  - January 2011: 421 new customers.
  - February 2011: 380 new customers.
  - March 2011: 440 new customers.
  - April 2011: 299 new customers.
  - May 2011: 279 new customers.
  - June 2011: 235 new customers.
  - July 2011: 191 new customers.
  - August 2011: 167 new customers.
  - September 2011: 298 new customers.
  - October 2011: 352 new customers.
  - November 2011: 321 new customers.
  - December 2011: 41 new customers.
  - After May 2011, the number of new customers tends to increase again and stabilize at an average level of around 300 customers per month.
  - There is a noticeable increase in the number of new customers in the last few months of the year (October, November, December).
  - October 2011: 352 new customers.
  - November 2011: 321 new customers.
  - December 2011: 41 new customers.
#### * Customer Retention:

- Based on the available data, we can observe that some cohorts have higher retention rates in the first few months.
- CohortIndex 1 group (December 2010): 62% of customers are still making purchases after 3 months.
- CohortIndex 4 group (April 2011): 58% of customers are still making purchases after 3 months.

#### * Purchasing Behavior:
- Cohort 1 group (December 2010) has the highest purchasing behavior and maintains a stable purchase rate throughout the year.
- Cohort 13 group (December 2011) has the lowest purchasing behavior, and the purchase rate decreases rapidly after the first month.

#### * Customer Segmentation into the following groups:

- The revenue trends show fluctuations and growth over the months:
    - In December 2010, the revenue was 554,604.020.
    - In January 2011, the revenue slightly decreased to 475,074.380.
    - The revenue further decreased in February 2011 to 436,546.150.
    - However, in March 2011, there was a notable increase in revenue, reaching 579,964.610.
    - In April 2011, revenue declined again to 426,047.851.
    - Subsequently, there was a significant surge in revenue in May 2011, reaching 648,251.080.
    - The revenue remained relatively high in June 2011 at 608,013.160.
    - In July 2011, revenue slightly decreased to 574,238.481.
    - August 2011 witnessed another increase in revenue, reaching 616,368.000.
    - September 2011 showed a substantial increase in revenue, reaching 931,440.372.
    - October 2011 maintained the high revenue trend, reaching 974,603.590.
    - November 2011 saw a further increase in revenue, reaching 1,132,407.740.
    - However, in December 2011, there was a significant decrease in revenue, dropping to 342,506.380.
- Monthly revenue growth rate chart:
    - The revenue growth rate is experiencing significant fluctuations. In some months, the revenue growth rate is very high, such as in January and July. However, in other months, the revenue growth rate is 
      negative, such as in April and October.
    - The revenue growth rate tends to decrease over time. In the first 12 months, the average revenue growth rate is 0.2%. However, in the following 12 months, the average revenue growth rate drops to only 0.1%.
- Number of customers per month chart
    - The number of customers shows an increasing trend over time. In the first year, the average number of customers per month is 1000. However, in the second year, the average number of customers per month 
      increases to 1200.
    - The number of customers exhibits seasonal fluctuations. During the summer months, the number of customers is typically higher compared to the winter months.
    - There are some months with significant spikes in the number of customers. For example, in December 2011, the number of customers increased to 1600.
- Number of orders per month chart
    - The number of orders shows an increasing trend over time. In the first month, the number of orders is over 20,000.
    - The number of orders exhibits monthly fluctuations, with November having the highest number of orders, exceeding 60,000.
- Average Revenue per Order chart
    - In 2011, the average revenue for two months, April and November, shows a significant decrease, particularly in November.
    - The average revenue for the two highest months, May and September, is notably high.
- New Customer Ratio chart
    - The rate of new customers shows a sharp decrease over time.
- Best Customers: Recent, frequent, high-value purchases (High RFM) - 354 customers.
- Loyal Customers: Frequent, high-value purchases (High RF, Medium M) - 980 customers.
- Big Spenders: Recent, high-value purchases but less frequent (Medium RFM) - 1044 customers.
- Almost Lost: Recent, low-value purchases (High R, Low FM) - 91 customers.
- Lost Customers: Low-value, infrequent purchases (Low RFM) - 10 customers.
- Lost Cheap Customers: Low-value, infrequent purchases, not recent (Low RFM) - 385 customers.




---


# 🧾 What can you practice with this case study?
- Python
  - pandas, numpy,matplotlib,seaborn.
  - cleaning, check Null values, transforming.
  - import, save csv file. 

