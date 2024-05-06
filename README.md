# Exploring-Sales-Insights
## A MySQL Analysis of AtliQ Hardware Sales Data
### Problem Statement
AtliQ Hardware is one of the leading computer hardware producers in India and well expanded in other countries too.

However, the management noticed that they do not get enough insights to make quick and smart data-informed decisions. They want to expand their data analytics team by adding several junior data analysts. Their data analytics director wants to hire someone who is good at both tech and soft skills. Hence, he decided to conduct a SQL challenge which will help him rate both skills.

### Data Structure
Database in MySQL is populated with 6 tables. Here is a preview of all tables with their headers;

#### *dim_customer:*

| customer_code | customer | platform | channel | market | sub_zone | region |
|---------------|----------|----------|---------|--------|----------|--------|

#### *dim_product:*

| product_code | division | segment | category | product | variant |
|--------------|----------|----------|---------|--------|----------|

#### *fact_gross_price:*

| product_code | fiscal_year | gross_price |
|---------------|----------|---------------|

#### *fact_manufacturing_cost:*

| product_code | cost_year | manufacturing_cost |
|---------------|----------|---------------|

#### *fact_pre_invoice_deductions:*

| customer_code | fiscal_year | pre_invoice_discount_pct |
|---------------|----------|---------------|

#### *fact_sales_monthly:*

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

![result_1](https://github.com/jakejosh6751/Exploring-Sales-Insights/assets/148710647/fede9ae7-2a6d-4b54-9427-0311ec8b10a6)

**Insight:**
- Atliq Exclusive operates in 8 out of arguably 36 different markets (about a quarter) in the Asia – Pacific region as a diversification strategy.
      
- Operating in multiple markets spreads risk, decreasing the vulnerability of the company to market fluctuations.
      
- It also shows Atliq Exclusive’s ability to navigate different regulatory environments for business.
      
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
	concat(round(((product_count_2021 - product_count_2020) / product_count_2020) * 100, 2), "%") as percentage_change
from cte;
```

**Result:**

![result_2](https://github.com/jakejosh6751/Exploring-Sales-Insights/assets/148710647/1353a06a-9f6d-4c46-81ea-ae9c4c1398d8)


**Insight:**
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

**Insight:**
- Notebooks, Accessories, and Peripherals representing 83% of products across all segments are the strongest segments for Atliq Hardware.

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


**Insight:**
- “Notebook” ranks highest in both year 2020 and 2021 but with only 17% increase as against “Accessories” with 49% increase and more than double the difference for “Notebooks”.

- This could be an indication of emerging market trends or shifting consumer preferences.

#### 5. **What products have the highest and lowest manufacturing costs?**

```sql
with cte as (
	select
		p.product_code,
        	concat(p.product, ' - ', ' [', p.variant, ']') as product,
		m.manufacturing_cost,
		rank() over(order by m.manufacturing_cost desc) as rnk
	from dim_product p
	inner join fact_manufacturing_cost m
	on p.product_code = m.product_code
    	order by product)
select
	product_code,
	product,
	concat('$', round(manufacturing_cost, 2)) as manufacturing_cost
from cte
where rnk in (1, (select max(rnk) from cte))
order by manufacturing_cost desc;
```

**Result:**

![result_5](https://github.com/jakejosh6751/Exploring-Sales-Insights/assets/148710647/0542fad3-cc47-4526-9c13-7d693c06bc56)


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

![image](https://github.com/jakejosh6751/Exploring-Sales-Insights/assets/148710647/78c739c8-8eec-4e78-a8fe-b2df3e3e2562)

**Insight:**
- High pre-order discount percents show signs the company has customers that are highly sensitive to price adjustments.
- On the flip side, caution must be exercised to balance discounts with profitability.

#### 7. 

**Result:**

**Insight:**

#### 8. **Which quarter of 2020 got the maximum total_sold_quantity?**

```sql
select
	case
		when month(date) between 9 and 11 then 'Q1'
		when month(date) between 3 and 5 then 'Q3'
		when month(date) between 6 and 8 then 'Q4'
        else 'Q2'
	end as Quarter,
	sum(sold_quantity) as total_sold_quantity
from fact_sales_monthly
where fiscal_year = 2020
group by Quarter;
```

**Result:**

![result_8](https://github.com/jakejosh6751/Exploring-Sales-Insights/assets/148710647/fb458288-b85f-4332-bfa0-4917c7e179b5)

**Insight:**

#### 9. **Which channel helped to bring more gross sales in the fiscal year 2021 and the percentage of contribution?**

```sql
with cte_1 as (
	select
		c.channel,
		sm.fiscal_year as fiscal_year,
		gp.gross_price * sm.sold_quantity as `Gross sales Amount`
	from dim_customer c
	left join fact_sales_monthly sm
	on c.customer_code = sm.customer_code
	left join fact_gross_price gp
	on gp.product_code  = sm.product_code
	where sm.fiscal_year = 2021),
cte_2 as (
	select
		channel,
		round(sum(`Gross sales Amount`), 2) as gross_sales_amount,
		concat(round(sum(`Gross sales Amount`) / (select sum(`Gross sales Amount`) from cte_1) * 100, 2), '%') as percentage
	from cte_1
	group by channel
	order by gross_sales_amount desc
	limit 1)
select 
	channel,
	concat('$', format(gross_sales_amount, 2)) gross_sales_amount,
	percentage
from cte_2
group by channel
order by gross_sales_amount desc
limit 1;
```

**Result:**

![result_9](https://github.com/jakejosh6751/Exploring-Sales-Insights/assets/148710647/36913ab9-8bbe-4588-8c58-f0113ed0cc35)

**Insight:**
- Retailer attracts more than 2 times the gross sales from other channels (Direct and Distributor) combined.

#### 10. **What are the top 3 products in each division that have a high total_sold_quantity in the fiscal year 2021?**

```sql
with cte as (
	select
		p.division,
		p.product_code,
		p.product,
        	p.variant,
		sum(sm.sold_quantity) as total_sold_quantity,
		rank() over(partition by p.division order by sum(sm.sold_quantity) desc) as rank_order
	from dim_product p
	left join fact_sales_monthly sm
	on p.product_code = sm.product_code
    	where fiscal_year = 2021
    	group by sm.product_code)
select
	division,
	product_code,
    	concat(product, ' - ', ' [', variant, ']') as product,
	total_sold_quantity,
	rank_order
from cte
where rank_order <= 3
order by total_sold_quantity desc;
```

**Result:**

**Insight:**


