# Exploring-Sales-Insights
## A MySQL Analysis of AtliQ Hardware Sales Data
### 1. Problem Statement
AtliQ Hardwares is one of the leading computer hardware producers in India and well expanded in other countries too.

However, the management noticed that they do not get enough insights to make quick and smart data-informed decisions. They want to expand their data analytics team by adding several junior data analysts. Their data analytics director wants to hire someone who is good at both tech and soft skills. Hence, he decided to conduct a SQL challenge which will help him rate both skills.

### 2. Data Structure
Database in MySQL is populated with 6 tables.

**A preview of all 6 tables with the first 3 rows;**

*dim_customer:*

| customer_code | customer | platform | channel | market | sub_zone | region |
|---------------|----------|----------|---------|--------|----------|--------|
| 70002017 | Atliq Exclusive | Brick & Mortar | Direct | India | India| APAC |
| 70002018 | Atliq e Store | E-Commerce |Direct | India | India | APAC |
| 70003181 | Atliq Exclusive | Brick & Mortar | Direct | Indonesia | ROA | APAC |

*dim_product:*

| product_code | division | segment | category | product | variant |
|---------------|----------|----------|---------|--------|----------|
| A0118150101 | P & A | Peripherals | Internal HDD | AQ Dracula HDD – 3.5 Inch SATA 6 Gb/s 5400 RPM 256 MB Cache | Standard |
| A0118150102 | P & A | Peripherals | Internal HDD | AQ Dracula HDD – 3.5 Inch SATA 6 Gb/s 5400 RPM 256 MB Cache | Plus |
| A0118150103 | P & A | Peripherals | Internal HDD | AQ Dracula HDD – 3.5 Inch SATA 6 Gb/s 5400 RPM 256 MB Cache | Premium |

*fact_gross_price:*




-	product_code
-	fiscal_year
-	gross_price
