# Exploring-Sales-Insights
## A MySQL Analysis of AtliQ Hardware Sales Data
### Problem Statement
AtliQ Hardware is one of the leading computer hardware producers in India and well expanded in other countries too.

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

#### 1. **What does the list of markets in which customer 'Atliq Exclusive' operates its business in the APAC region look like?**

```sql
   select 
       distinct market
   from dim_customer
   where customer = 'Atliq Exclusive' and region = 'APAC'
   order by market;
```
   
**Result:**

![result_1](https://github.com/jakejosh6751/Exploring-Sales-Insights/assets/148710647/419909a0-70a4-4c24-933b-2f462f9aa0db)


**Insights:**

- AtliQ Mart operates in 8 out of arguably 36 different markets (about a quarter) in the Asia – Pacific region as a diversification strategy.
      
- Operating in multiple markets spreads risk, decreasing the vulnerability of the company to market fluctuations.
      
- It also shows AtliQ Mart’s ability to navigate different regulatory environments for business.
      
- There’s competitive advantage as the company might concentrate resources in markets where it has stronger foothold.

#### 2. **What is the percentage of unique product increase in 2021 vs 2022?**

```sql
with cte as (
	select
		count(distinct case when fiscal_year = 2020 then product_code end) as product_count_2020,
		count(distinct case when fiscal_year = 2021 then product_code end) as product_count_2021
	from fact_gross_price)
select
	product_count_2020 as unique_products_2020,
        product_count_2021 as unique_products_2021,
	round(((product_count_2021 - product_count_2020) / product_count_2020) * 100, 2) as percentage_change
from cte;
```

**Result:**

![result_2](https://github.com/jakejosh6751/Exploring-Sales-Insights/assets/148710647/c04a220a-e54c-441c-8ebc-0f5aa3958dcb)

**Insights:**

- Adding 89 new products between 2 fiscal years indicates the company’s initiative to provide a broader range of choices for customers, suggesting the market is expanding or diversifying.

- This also indicates possible increased innovation within the industry, with companies launching new products or variants to capture market share.

#### 3. **How many unique products are in each segment?**

```sql
select
	segment,
	count(distinct product_code) as product_count
from dim_product
group by segment
order by product_count desc;
```

**Result:**
   
![image](https://github.com/jakejosh6751/Exploring-Sales-Insights/assets/148710647/90a4a893-2e8f-40c6-8113-2e16a6950f20)


#### 4. **Follow-up from 3: Which segment had the most increase in unique products in 2021 vs 2020?**

```sql
with cte_1 as (
	select
		d.segment,
		f.product_code,
		f.fiscal_year
	from dim_product d
	left join fact_gross_price f
	on d.product_code = f.product_code),
cte_2 as (
	select
		segment,
	        count(distinct case when fiscal_year = 2020 then product_code end) as product_count_2020,
		count(distinct case when fiscal_year = 2021 then product_code end) as product_count_2021
	from cte_1
	group by segment)
select 
	segment,
    	product_count_2020,
	product_count_2021,
	product_count_2021 - product_count_2020 as difference
from cte_2
group by segment
order by (product_count_2021 - product_count_2020) desc;
```

**Result:**

![image](https://github.com/jakejosh6751/Exploring-Sales-Insights/assets/148710647/1795ab53-b8fe-4a41-a515-f887029459a0)


**Insights:**

- “Notebook” ranks highest in both year 2020 and 2021 but with only 17% increase as against “Accessories” with 49% increase and more than double the difference for “Notebooks”.

- This could be an indication of emerging market trends or shifting consumer preferences.

#### 5. **What products have the highest and lowest manufacturing costs?**

```sql
with cte as (
	select
		p.product_code,
		p.product,
		m.manufacturing_cost,
		rank() over(order by m.manufacturing_cost desc) as rnk
	from dim_product p
	inner join fact_manufacturing_cost m
	on p.product_code = m.product_code
    	order by product)
select
		product_code,
		product,
		round(manufacturing_cost, 2) as manufacturing_cost
from cte
where rnk in (1, (select max(rnk) from cte))
order by manufacturing_cost desc;
```

**Result:**

![result_5](https://github.com/jakejosh6751/Exploring-Sales-Insights/assets/148710647/50717439-80e1-4233-8680-dfebc38f5164)

#### 6. **Who are the top 5 customers who received an average high pre_invoice_discount_pct for the fiscal_year 2021 and in the Indian market?**

```sql
select
    c.customer_code,
    c.customer,
    round(avg(p.pre_invoice_discount_pct), 4) as average_discount_percentage
from dim_customer c
left join fact_pre_invoice_deductions p
on c.customer_code = p.customer_code
where c.market = "India" and p.fiscal_year = 2021
group by c.customer_code
order by average_discount_percentage desc
limit 5;
```
**Result:**

![image](https://github.com/jakejosh6751/Exploring-Sales-Insights/assets/148710647/bcd506b5-cee1-4423-bd73-b200b51c946f)

**Insights:**












