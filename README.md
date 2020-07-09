# Walmart-Sales-Analysis
**Link for interactive notebook:**
[Click Here](https://nbviewer.jupyter.org/github/ashrestha11/Walmart-Sales-Analysis/blob/master/Data%20Analysis%20-%20WMT%20daily%20Sales%202011-2016.ipynb#)
### I. Introduction

The datasets for this project was obtained from Kaggle for m5-forecasting competition. There are three different dataset that constains information about Walmart's calender, products sales and its prices from year 2011 to 2016. This repository contains notebook for data wrangling, analysis and creating prediction model by using lgboost.

### II. Data Transformation/ Wranging

Since the datasets are very large and have datatypes that allocates large memory, we have to choose datatypes for the columns that assigns as small as memory to the RAM. Therefore, we have to change the data types before we start analyzing the datasets. After, I transposed the dataset columns of daily sales to rows so we are able to use those columns as an ID to merge with other dataset. I merged the dataset with the calender dataset using the column id "D" and the product's prices data set with item_id, dept_id and wm_yr_wk (week of month, year and week). Then, the datasets for individual states was saved as .pickle because it allows us to load/save large datasets faster than csv files.

the code to transdorm the dataset:
``` python 
def transform_df(): 
   calender_dtype={"event_name_1": "category", "event_name_2": "category", "event_type_1": "category", 
      "event_type_2": "category", "weekday": "category", 'wm_yr_wk': 'int16', "wday": "int16",
     "month": "int16", "year": "int16", "snap_CA": "uint8", 'snap_TX': 'uint8', 'snap_WI': 'uint8' }
     
   calender = pd.read_csv('/Users/anuragshrestha/Desktop/m5-forecasting/calendar.csv', dtype= calender_dtype)
   calender.date = pd.to_datetime(calender.date)
      
   price_dtype = {"store_id": "category", "item_id": "category", "wm_yr_wk": "int16","sell_price":"float32" }
    #prices of products 
   product_prices = pd.read_csv('/Users/anuragshrestha/Desktop/m5-forecasting/sell_prices.csv', dtype= price_dtype)  

   days = 1913
   colstype = ['id', "store_id", 'state_id', 'item_id', 'cat_id', 'dept_id']
   d_numtype = [f"d_{day}" for day in range(1,days+1)]
 
   dtype = {num: 'int8' for num in d_numtype}
   dtype.update({col: "category" for col in colstype}) 
 
   daily_temp = pd.read_csv('/Users/anuragshrestha/Desktop/m5-forecasting/sales_train_validation.csv', usecols = d_numtype + colstype, dtype = dtype)
 
   daily_sales = pd.melt(daily_temp,id_vars = ['id', 'store_id', 'item_id','state_id','cat_id', 'dept_id'], 
                   var_name = "d", value_name = 'Sales')
 
   gc.collect()
 
   daily_sales = daily_sales.merge(calender, on = 'd', copy = 'false')
   dataset = daily_sales.merge(product_prices, on = ['store_id', 'item_id', 'wm_yr_wk'], copy = False)
 
   #dataset = dataset.drop(columns = ['id']) 
   gc.collect() #free memory 

 return dataset
``` 
 
### III. Walmart Analysis

To fully understand sales of Walmart's product, I analyzed the items in the inventories in different store. Since the frequency of sales is daily, I was able to adjust the frequency to weekly and monthly to learn more about the seasonality and trend of the sales. I used random items from different department to understand the behavior of the conusmers and when the items are sold mostly. The distributions of the prices are similar in many stores and the prices of products did not change by large amount overtime. Many of the items for all the stores in different states are not sold daily, but the stores sales are still high due to the variety of unique products available in the stores. My goal was to create the data visualization as interactive as possible, so I used Plotly to create the plots.

Since github does not fully support Plotly, you can use this link to fully the data analysis:
[Click Here](https://nbviewer.jupyter.org/github/ashrestha11/Walmart-Sales-Analysis/blob/master/Data%20Analysis%20-%20WMT%20daily%20Sales%202011-2016.ipynb#)

### IV. Future Work

For future work, I aim to create fully deployable codes that can be used for new datasets and create a dashboard for the analytics. Also, I aim to create Hadoop databases to store large databases and export the datasets from it.
