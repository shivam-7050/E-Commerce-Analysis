# Sales-Dashboard

### Dashboard Link : https://app.powerbi.com/groups/cfd3c16e-14aa-43b0-b11f-830b5a5bf39b/reports/be4ebbad-7322-471b-9deb-3ac9fc63ec1f/d7f98bcd781b8c1e3d17?experience=power-bi


## Table of Contents
1. [Project Overview](#project-overview)
2. [Features](#features)
3. [Dataset](#dataset)
4. [Dashboard Sections](#dashboard-sections)
5. [Additional Measures and Calculated Columns](#additional-measures-and-calculated-columns)
6. [How to Use](#how-to-use)
7. [Setup Instructions](#setup-instructions)
8. [Assumptions](#assumptions)
9. [Limitations](#limitations)
10. [Future Improvements](#future-improvements)




## Project Overview

The **Sales Analysis Dashboard** is a comprehensive Power BI solution designed to track and analyze key performance indicators (KPIs) related to sales, orders, customer segmentation, product performance, and store insights. This dashboard allows businesses to visualize sales trends, understand customer behavior, and make data-driven decisions to improve overall performance.



## Features

- **Sales Trend Analysis**: Analyze monthly and yearly sales performance across different regions, products, and customer segments.
- **Order Insights**: Track the growth of orders, top-selling products, and best-performing regions.
- **Return Analysis**: Understand product returns, including returned quantities, trends over time, and store-wise analysis.
- **Customer Segmentation**: Segment customers based on demographics, purchasing behavior, and retention rates.
- **Store Performance**: Compare store-level performance by sales volume, region, and store type.
- **Product Performance**: Identify high and low-performing products based on sales velocity, profitability, and customer engagement.


## Dataset

The dashboard is built using several CSV files from Maven Market, including:

- **Transactions Data (1997-1998)**: The two years of transaction data have been combined into a single query called Total Transactions, which includes product, customer, and order details for a comprehensive view.
- **Customers Data**: Contains customer demographics and other relevant details for segmentation.
- **Products Data**: Provides product information, including sales and profitability data.
- **Returns Data**: Tracks product returns, including reasons and quantities returned.
- **Stores Data**: Lists store information, including store type, location, and other key attributes.
- **Calendar Data**: Provides date information, essential for time-series analysis.
- **Regions Data**: Offers geographic details for region-wise analysis.

The datasets are connected through fields like `product_id`, `customer_id`, and `store_id`, enabling seamless analysis across different business dimensions.



## Dashboard Sections

### 1. **Sales Analysis Page**
   - Visualizes monthly and yearly sales trends.
   - Shows sales performance by region, product, and customer segment.
   - Displays profit margins, sales growth rates, and top-selling products.

### 2. **Orders Overview Page**
   - Provides total quantity sold and orders growth percentage.
   - Visualizes orders received by month and region.
   - Displays top 10 selling products and their contribution to overall sales.

### 3. **Returns Analysis Page**
   - Tracks returned quantities and most returned products.
   - Analyzes returns by country, customer segment, and store.
   - Displays monthly trends in returns.

### 4. **Customers Insight Page**
   - Segments customers based on demographics such as membership, occupation, and education.
   - Tracks customer retention rates, growth, and churn patterns.
   - Identifies high-value customers and their contribution to total sales.

### 5. **Stores Performance Page**
   - Compares store-wise sales, quantity sold, and performance growth.
   - Analyzes sales performance by store type and region.
   - Displays store-wise sales trends over time.

### 6. **Products Analysis Page**
   - Shows top and bottom products based on sales and profit growth.
   - Visualizes product-wise quantity sold, profit margin, and active customers.
   - Identifies fast-moving and slow-moving products.

---

## Additional Measures and Calculated Columns

Here are the DAX measures and calculated fields up to **Most Returned Product**, along with their uses:

---

### 1. **Final Sales**
   - **Definition**: Total revenue from sales after deducting returns.
   - **DAX Formula**:
     ```DAX
     Sales = Total_Transactions[TotalSales]-[Total_Returned_Amount]
     ```
   - **Use**: Provides the final revenue figure after accounting for product returns, giving a clear picture of actual earnings.

### 2. **Quantity Returned**
   - **Definition**: Total quantity of products returned by customers.
   - **DAX Formula**:
     ```DAX
     Total_Quantity_Returned = SUM(Returns[quantity])
     ```
   - **Use**: Tracks the volume of products returned, helping to understand the scale of returns and potential issues with product satisfaction.

### 3. **Sales Growth %**
   - **Definition**: Percentage change in sales over a period (month, quarter, year).
   - **DAX Formula**:
     ```DAX
     Sales Growth% = DIVIDE([sales_1998]-[sales_1997],[sales_1997])
     ```
   - **Use**: Measures the growth or decline in sales compared to the previous period, providing insight into sales performance trends.

### 4. **Customers Growth %**
   - **Definition**: Percentage change in the number of customers over a time period.
   - **DAX Formula**:
     ```DAX
     Growth_Customers = DIVIDE([DistinctCustomerCount_1998]-[DistinctCustomerCount_1997],[DistinctCustomerCount_1997])
     ```
   - **Use**: Shows how the customer base is expanding or contracting, which helps in understanding customer acquisition trends.

### 5. **Orders Growth %**
   - **Definition**: Percentage growth in the number of orders over time.
   - **DAX Formula**:
     ```DAX
     Growth_Orders = DIVIDE([orders_1998]-[orders_1997],[orders_1997])
     ```
   - **Use**: Monitors the increase or decrease in the number of orders, highlighting changes in order volume over time.

### 6. **Most Ordered Product**
   - **Definition**: The product with the highest quantity sold.
   - **DAX Formula**:
     ```DAX
     MostOrderedProduct = 
            SELECTCOLUMNS( TOPN(
            1,
            SUMMARIZE(
                Products,
                Products[product_name],
                "TotalQuantity", SUM(Total_Transactions[quantity])
            ),
            [TotalQuantity], DESC
        ),
        "MostOrderedProduct",Products[product_name]
     )
     ```
   - **Use**: Identifies the best-selling product, which is crucial for understanding top performers and inventory planning.

### 7. **Most Returned Product**
   - **Definition**: The product with the highest quantity returned.
   - **DAX Formula**:
     ```DAX
     MostReturnedProduct = 
            SELECTCOLUMNS( TOPN(
            1,
            SUMMARIZE(
                Products,
                Products[product_name],
                "TotalQuantity", SUM(Returns[quantity])
            ),
            [TotalQuantity], DESC
        ),
        "MostReturnedProduct",Products[product_name]
     )

     ```
   - **Use**: Highlights the product with the highest return rate, which can indicate potential quality issues or customer dissatisfaction.


### 8. **Sales Velocity**
   - **Definition**: Measures how quickly a product is selling over a defined period.
   - **DAX Formula**:
     ```DAX
     SalesVelocity = 
        CALCULATE(
            [Quantity_Sold],
            DATESINPERIOD(Total_Transactions[transaction_date], MAX(Total_Transactions[transaction_date]), -3, MONTH)
     )

     ```
   - **Use**: Helps identify which products are selling faster than others.

### 9. **Sales Velocity Rank**
   - **Definition**: Ranks products based on their sales velocity.
   - **DAX Formula**:
     ```DAX
     SalesVelocityRank = RANKX(ALL(Products), [SalesVelocity], , ASC, Dense)
     ```
   - **Use**: Allows for easy comparison of products based on speed of sales.

### 10. **High Margin Products**
   - **Definition**: Flags products with a profit margin above 30%.
   - **DAX Formula**:
     ```DAX
     IsHighMargin = 
        IF(
            [ProfitMarginRank] <= (COUNTROWS(Products) * 0.02),
            TRUE(),
            FALSE()
     )

     ```
   - **Use**: Identifies highly profitable products.

### 11. **Slow Moving Products**
   - **Definition**: Flags products with low sales velocity.
   - **DAX Formula**:
     ```DAX
     IsSlowMoving = 
        IF(
            [SalesVelocityRank] <= (COUNTROWS(Products) * 0.02),
            TRUE(),
            FALSE()
     )
     ```
   - **Use**: Identifies products that are not selling as quickly as others.


### 12. **High Margin, Slow Moving Products**
   - **Definition**: Combines products that have both high profit margins and slow sales velocity.
   - **DAX Formula**:
     ```DAX
     IsSlowMovingHighMargin = 
        IF(
            [IsSlowMoving] && [IsHighMargin],
            TRUE(),
            FALSE()
     )

     ```
   - **Use**: Focuses on products that are profitable but may need marketing or sales adjustments due to slow movement.


### 13. **New Customer**
   - **Definition**: Identifies customers who made their first purchase within the current time period.
   - **DAX Formula**:
     ```DAX
     NewCustomers = 
     CALCULATE(
        DISTINCTCOUNT(Total_Transactions[customer_id]),
        FILTER(
            Total_Transactions,
            Total_Transactions[transaction_date] = MIN(Total_Transactions[transaction_date]) &&
            CALCULATE(
                MIN(Total_Transactions[transaction_date]),
                ALLEXCEPT(Total_Transactions, Total_Transactions[customer_id])
            ) = MIN(Total_Transactions[transaction_date])
        )
     )

     ```
   - **Use**: Tracks new customer acquisition and growth.

## How to Use

- **Filters**: Use interactive filters to segment data by product category, customer segment, or region.
- **Click and Drill-down**: Click on visual elements to drill deeper into specific data points or time periods.
- **Export Data**: Export visual data and insights for further analysis.



## Setup Instructions

### Requirements
- Power BI Desktop (latest version)
- Maven Market dataset (CSV files)

### Steps
1. **Import Data**: Load the provided CSV files into Power BI.
2. **Create Relationships**: Link tables using common keys (`product_id`, `customer_id`, `store_id`).
3. **Add Custom Measures**: Use the provided DAX formulas to create the additional measures and columns.
4. **Build Visuals**: Drag and drop fields into visuals to build insights on the dashboard.
5. **Refresh Data**: Refresh the data periodically to keep the dashboard updated with the latest sales information.



## Assumptions

- The dataset is consistent across all regions and time periods.
- Product and customer information is accurate and up-to-date.
- The provided CSV files contain clean data without major anomalies.



## Limitations

- The dashboard focuses on historical sales performance without predictive analytics.
- Data updates are manual and need to be refreshed periodically.



## Future Improvements

- Integrate real-time data updates for live tracking.
- Add predictive analytics for future sales forecasting.
- Expand customer segmentation to include more behavioral attributes.

