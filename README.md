# Exploring-Sales-Insights
## A MySQL Analysis of AtliQ Hardware Sales Data
### Problem Statement
AtliQ Hardwares is one of the leading computer hardware producers in India and well expanded in other countries too.

However, the management noticed that they do not get enough insights to make quick and smart data-informed decisions. They want to expand their data analytics team by adding several junior data analysts. Their data analytics director wants to hire someone who is good at both tech and soft skills. Hence, he decided to conduct a SQL challenge which will help him rate both skills.

### Data Structure
Database in MySQL is populated with 6 tables.

**A preview of all 6 tables with their headers;**

- **dim_customer:**

| customer_code | customer | platform | channel | market | sub_zone | region |
|---------------|----------|----------|---------|--------|----------|--------|

- **dim_product:**

| product_code | division | segment | category | product | variant |
|--------------|----------|----------|---------|--------|----------|

- **fact_gross_price:**

| product_code | fiscal_year | gross_price |
|---------------|----------|---------------|

- **fact_manufacturing_cost:**

| product_code | cost_year | manufacturing_cost |
|---------------|----------|---------------|

- **fact_pre_invoice_deductions:**

| customer_code | fiscal_year | pre_invoice_discount_pct |
|---------------|----------|---------------|

- **fact_sales_monthly:**

| date | product_code | customer_code | sold_quantity | fiscal_year |
|---------------|----------|---------------|-----------|------------|

### Data Exploration and Insights

1. What does the list of markets in which customer 'Atliq Exclusive' operates its business in the APAC region look like?

   ```python
   print('hello world')
   ```

