# Data Modelling – Star Schema Implementation

## Overview

This project follows a **Star Schema data modelling approach**, which is the industry standard for enterprise Business Intelligence solutions.

Instead of using a flat transactional table, the dataset was transformed into a structured model consisting of:

- One central **Fact table**
- Multiple surrounding **Dimension tables**
- One-to-many relationships
- Single-direction filtering (Dimension → Fact)

This mirrors real-world BI architecture used in large-scale e-commerce environments.

---

## Step 1 – Staging Layer Creation

After importing the dataset into Power BI:

- The original table was renamed to: `stg_Orders_Raw`
- A duplicate was created and renamed to: `Fact_Orders`

### Purpose:
The staging table preserves the raw dataset, while `Fact_Orders` is transformed for analytics.

This approach follows enterprise best practices where raw data is never directly modified.

---

## Step 2 – Fact Table Structuring

The `Fact_Orders` table was cleaned to retain only:

### Keys
- Order_ID  
- Order_Date  
- Customer_ID  
- Product_ID  

### Transaction-Level Attributes
- Order_Status  
- Sales_Channel  
- Payment_Method  
- Marketing_Channel  

### Numeric Measures
- Quantity  
- Unit_Price  
- Discount_Percentage  
- Revenue  
- Cost_of_Goods_Sold (COGS)  
- Gross_Profit  
- Fulfilment_Cost  

All descriptive customer and product attributes were removed to avoid redundancy.

---

## Step 3 – Customer Dimension (`Dim_Customer`)

Created by referencing `stg_Orders_Raw`.

Columns retained:
- Customer_ID (Primary Key)
- Customer_Segment
- Customer_City
- Customer_State
- Customer_Country

Duplicates were removed to ensure each Customer_ID appears once.

---

## Step 4 – Product Dimension (`Dim_Product`)

Created by referencing `stg_Orders_Raw`.

Columns retained:
- Product_ID (Primary Key)
- Product_Name
- Product_Category
- Sub_Category
- Brand

Duplicates were removed to ensure each Product_ID appears once.

---

## Step 5 – Date Dimension (`Dim_Date`)

A dedicated Date table was created using DAX:

```DAX
Dim_Date = 
ADDCOLUMNS(
    CALENDAR(DATE(2021,1,1), DATE(2024,12,31)),
    "Year", YEAR([Date]),
    "Quarter", "Q" & QUARTER([Date]),
    "Month", MONTH([Date]),
    "Month Name", FORMAT([Date], "MMMM"),
    "Week Number", WEEKNUM([Date], 2),
    "Day", DAY([Date]),
    "Weekday", FORMAT([Date], "dddd")
)```

## Purpose

The Date Dimension enables:

- Year-over-Year (YoY) analysis  
- Month-to-Date (MTD) calculations  
- Quarter-to-Date (QTD) calculations  
- Time-based slicing and filtering across all dashboards  

---

## Step 6 – Relationship Configuration

The following one-to-many relationships were created:

- `Dim_Customer[Customer_ID]` → `Fact_Orders[Customer_ID]`  
- `Dim_Product[Product_ID]` → `Fact_Orders[Product_ID]`  
- `Dim_Date[Date]` → `Fact_Orders[Order_Date]`  

### Relationship Settings

- **Cardinality:** One-to-Many (1:*)  
- **Cross-filter direction:** Single (Dimension → Fact)  
- **Active relationships:** Enabled  

---

## Final Data Model Structure
Dim_Customer

Dim_Product — Fact_Orders — Dim_Date


---

## Model Characteristics

- No dimension-to-dimension joins  
- No bidirectional filtering  
- Clean star schema layout  
- Optimized for performance and scalability  

---

## Why Star Schema?

- Improves performance  
- Simplifies DAX calculations  
- Enhances scalability  
- Reduces ambiguity in filtering  
- Reflects enterprise BI architecture  
