# Naming Conventions

This document defines the naming standards used for schemas, tables, columns, and stored procedures in the data warehouse.

The goal is to ensure consistency, readability, maintainability, and clear lineage across the Bronze, Silver, and Gold layers.

---

## Table of Contents

1. [General Principles](#general-principles)
2. [Table Naming Conventions](#table-naming-conventions)
   - [Bronze Layer](#bronze-layer)
   - [Silver Layer](#silver-layer)
   - [Gold Layer](#gold-layer)
3. [Column Naming Conventions](#column-naming-conventions)
   - [Surrogate Keys](#surrogate-keys)
   - [Technical Columns](#technical-columns)
4. [Stored Procedure Naming](#stored-procedure-naming)
5. [Quick Reference](#quick-reference)

---

## General Principles

All database objects should follow these naming standards:

- Use `snake_case`.
- Use lowercase letters.
- Separate words with underscores (`_`).
- Use English for all object names.
- Use clear and descriptive names.
- Avoid SQL reserved words.
- Keep naming consistent across each data layer.

Examples:

customer_id  
product_name  
sales_amount  
dwh_load_date

---

## Table Naming Conventions

### Bronze Layer

The Bronze layer stores raw data coming directly from source systems.

#### Pattern

`<sourcesystem>_<entity>`

#### Components

- `<sourcesystem>`: Name of the source system.
  - Examples: `crm`, `erp`
- `<entity>`: Exact table name from the source system.

#### Rule

Table names must:

- start with the source system name
- preserve the original source table name
- avoid unnecessary renaming

#### Example

`crm_customer_info`

Meaning:

- `crm` → source system
- `customer_info` → original source table

---

### Silver Layer

The Silver layer contains cleaned, standardized, and validated data.

#### Pattern

`<sourcesystem>_<entity>`

#### Components

- `<sourcesystem>`: Name of the source system.
- `<entity>`: Original table name from the source system.

#### Rule

Silver tables keep the same source-oriented naming convention as Bronze.

The table name remains stable even though the data may be:

- cleaned
- validated
- deduplicated
- standardized
- transformed

#### Example

`crm_customer_info`

Typical flow:

Bronze  
`crm_customer_info`

↓

Silver  
`crm_customer_info`

---

### Gold Layer

The Gold layer contains business-ready data designed for analytics and reporting.

#### Pattern

`<category>_<entity>`

#### Components

- `<category>`: Describes the role of the table.
- `<entity>`: Business-oriented name of the entity or process.

Examples of entities:

- `customers`
- `products`
- `sales`
- `sales_monthly`

#### Examples

- `dim_customers`
- `dim_products`
- `fact_sales`
- `report_sales_monthly`

---

### Gold Category Prefixes

| Prefix | Meaning | Example |
|---|---|---|
| `dim_` | Dimension table | `dim_customers` |
| `fact_` | Fact table | `fact_sales` |
| `report_` | Reporting / aggregated table | `report_sales_monthly` |

---

## Column Naming Conventions

### Surrogate Keys

Surrogate primary keys in dimension tables must use the suffix `_key`.

#### Pattern

`<entity>_key`

#### Components

- `<entity>`: Name of the business entity.
- `_key`: Indicates that the column is a surrogate key.

#### Example

`customer_key`

Used in:

`dim_customers`

Example structure:

- `customer_key`
- `customer_id`
- `first_name`
- `last_name`
- `country`

Where:

- `customer_key` → Data warehouse surrogate key
- `customer_id` → Source/business identifier

---

### Technical Columns

Technical metadata columns must use the prefix `dwh_`.

#### Pattern

`dwh_<column_name>`

#### Components

- `dwh_`: Reserved prefix for data warehouse metadata.
- `<column_name>`: Descriptive name of the technical column.

#### Example

`dwh_load_date`

Meaning:

`dwh_load_date` → Date when the record was loaded into the warehouse.

Example:

- `customer_id`
- `first_name`
- `country`
- `dwh_load_date`

Technical columns should represent system or pipeline metadata rather than business attributes.

---

## Stored Procedure Naming

Stored procedures used to load warehouse layers must follow the pattern:

`load_<layer>`

#### Components

- `load_`: Indicates that the procedure performs a loading process.
- `<layer>`: Name of the target warehouse layer.

Possible layer values:

- `bronze`
- `silver`
- `gold`

#### Examples

- `load_bronze`
- `load_silver`
- `load_gold`

Meaning:

- `load_bronze` → Loads data into the Bronze layer.
- `load_silver` → Loads or transforms data into the Silver layer.
- `load_gold` → Loads business-ready data into the Gold layer.

---

## Quick Reference

| Object | Layer | Pattern | Example |
|---|---|---|---|
| Source Table | Bronze | `<sourcesystem>_<entity>` | `crm_customer_info` |
| Cleaned Source Table | Silver | `<sourcesystem>_<entity>` | `crm_customer_info` |
| Dimension Table | Gold | `dim_<entity>` | `dim_customers` |
| Fact Table | Gold | `fact_<entity>` | `fact_sales` |
| Report Table | Gold | `report_<entity>` | `report_sales_monthly` |
| Surrogate Key | Gold | `<entity>_key` | `customer_key` |
| Technical Column | Any applicable layer | `dwh_<column_name>` | `dwh_load_date` |
| Load Procedure | Bronze / Silver / Gold | `load_<layer>` | `load_silver` |

---

## Naming Flow Summary

SOURCE  
`customer_info`

↓

BRONZE  
`crm_customer_info`

↓

SILVER  
`crm_customer_info`

↓

GOLD  
`dim_customers`  
`fact_sales`  
`report_sales_monthly`

---

## Summary

- Bronze and Silver use source-oriented naming.
- Gold uses business-oriented naming.
- Surrogate keys use the `_key` suffix.
- Technical metadata columns use the `dwh_` prefix.
- Stored procedures follow the `load_<layer>` pattern.
