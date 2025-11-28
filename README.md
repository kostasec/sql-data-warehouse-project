
# SQL Data Warehouse Project

A comprehensive SQL Server data warehouse implementation following the **Medallion Architecture** (Bronze → Silver → Gold) with a Star Schema design pattern.

![Data Architecture](docs/data_architecture.png)

## 📋 Table of Contents

- [Overview](#overview)
- [Architecture](#architecture)
- [Project Structure](#project-structure)
- [Data Sources](#data-sources)
- [Data Layers](#data-layers)
- [Getting Started](#getting-started)
- [Usage](#usage)
- [Data Model](#data-model)
- [Documentation](#documentation)
- [Quality Assurance](#quality-assurance)
- [Naming Conventions](#naming-conventions)

## 🎯 Overview

This project demonstrates a complete end-to-end data warehouse solution that integrates data from multiple source systems (CRM and ERP) and transforms it into a business-ready analytical layer using the Medallion Architecture pattern.

### Key Features

- **Multi-Source Integration**: Combines data from CRM and ERP systems
- **Layered Architecture**: Bronze → Silver → Gold transformation pipeline
- **Star Schema Design**: Optimized for analytics and reporting
- **Data Quality**: Built-in quality checks and validation
- **ETL Automation**: Stored procedures for automated data loading
- **Comprehensive Documentation**: Detailed data catalog and naming conventions

## 🏗️ Architecture

The project follows the **Medallion Architecture** pattern with three distinct layers:

### Bronze Layer (Raw Data)
- **Purpose**: Stores raw, unprocessed data exactly as it comes from source systems
- **Tables**: 
  - `crm_cust_info`, `crm_prd_info`, `crm_sales_details`
  - `erp_loc_a101`, `erp_cust_az12`, `erp_px_cat_g1v2`
- **Characteristics**: No transformations, preserves original data types and formats

### Silver Layer (Cleaned Data)
- **Purpose**: Cleaned, validated, and standardized data
- **Features**:
  - Data type conversions
  - Date format standardization
  - Data validation and cleansing
  - Technical metadata columns (`dwh_create_date`)
- **Tables**: Same structure as Bronze but with cleaned data

### Gold Layer (Business Layer)
- **Purpose**: Business-ready dimensional model for analytics
- **Design**: Star Schema
- **Objects**:
  - **Dimensions**: `dim_customers`, `dim_products`
  - **Facts**: `fact_sales`
- **Implementation**: SQL Views joining Silver layer tables

![Data Flow](docs/data_flow.png)

## 📁 Project Structure

```
sql-data-warehouse-project/
│
├── datasets/                      # Source data files
│   ├── source_crm/               # CRM system data
│   │   ├── cust_info.csv
│   │   ├── prd_info.csv
│   │   └── sales_details.csv
│   └── source_erp/               # ERP system data
│       ├── CUST_AZ12.csv
│       ├── LOC_A101.csv
│       └── PX_CAT_G1V2.csv
│
├── scripts/                       # SQL scripts
│   ├── init_database.sql         # Database and schema creation
│   ├── bronze/
│   │   ├── ddl_bronze.sql        # Bronze layer table definitions
│   │   └── proc_load_bronze.sql  # Bronze layer ETL procedure
│   ├── silver/
│   │   ├── ddl_silver.sql        # Silver layer table definitions
│   │   └── proc_load_silver.sql  # Silver layer ETL procedure
│   └── gold/
│       └── ddl_gold.sql          # Gold layer views (Star Schema)
│
├── tests/                         # Data quality checks
│   ├── quality_checks_silver.sql
│   └── quality_checks_gold.sql
│
└── docs/                          # Documentation
    ├── data_architecture.png
    ├── data_flow.png
    ├── data_integration.png
    ├── data_model.png
    ├── ETL.png
    ├── data_catalog.md           # Complete data dictionary
    ├── naming_conventions.md     # Naming standards
    └── data_layers.pdf
```

## 💾 Data Sources

### CRM System
- **Customer Information**: Demographics and customer master data
- **Product Information**: Product catalog and attributes
- **Sales Details**: Transactional sales data

### ERP System
- **Customer Location** (`LOC_A101`): Geographic information
- **Customer Demographics** (`CUST_AZ12`): Additional customer attributes
- **Product Categories** (`PX_CAT_G1V2`): Product classification and maintenance info

## 🔄 Data Layers

### 1. Bronze Layer (Raw Zone)
Raw data ingestion from source systems with minimal processing.

**Tables:**
- `bronze.crm_cust_info` - Customer information from CRM
- `bronze.crm_prd_info` - Product information from CRM
- `bronze.crm_sales_details` - Sales transactions from CRM
- `bronze.erp_loc_a101` - Location data from ERP
- `bronze.erp_cust_az12` - Customer demographics from ERP
- `bronze.erp_px_cat_g1v2` - Product categories from ERP

### 2. Silver Layer (Standardized Zone)
Cleaned and validated data with consistent formats.

**Transformations:**
- Date format standardization
- Data type corrections
- Data quality validations
- Addition of technical metadata

### 3. Gold Layer (Consumption Zone)
Business-ready Star Schema for analytics and reporting.

**Dimensions:**
- `dim_customers` - Customer dimension with enriched demographics
- `dim_products` - Product dimension with category information

**Facts:**
- `fact_sales` - Sales transactions fact table

![Data Model](docs/data_model.png)

## 🚀 Getting Started

### Prerequisites

- SQL Server 2016 or later
- SQL Server Management Studio (SSMS) or Azure Data Studio
- Basic understanding of SQL and data warehousing concepts

### Installation

1. **Clone the repository**
   ```bash
   git clone https://github.com/yourusername/sql-data-warehouse-project.git
   cd sql-data-warehouse-project
   ```

2. **Create the database and schemas**
   ```sql
   -- Run this script first
   -- WARNING: This will drop existing 'DataWarehouse' database if it exists
   -- Execute: scripts/init_database.sql
   ```

3. **Create Bronze layer tables**
   ```sql
   -- Execute: scripts/bronze/ddl_bronze.sql
   ```

4. **Create Silver layer tables**
   ```sql
   -- Execute: scripts/silver/ddl_silver.sql
   ```

5. **Create Gold layer views**
   ```sql
   -- Execute: scripts/gold/ddl_gold.sql
   ```

6. **Load the data**
   ```sql
   -- Load Bronze layer
   EXEC bronze.load_bronze;
   
   -- Load Silver layer
   EXEC silver.load_silver;
   ```

## 💡 Usage

### Loading Data into Bronze Layer

```sql
-- Execute the Bronze layer ETL procedure
EXEC bronze.load_bronze;
```

The procedure loads data from source CSV files into Bronze tables using `BULK INSERT`.

### Processing Data into Silver Layer

```sql
-- Execute the Silver layer ETL procedure
EXEC silver.load_silver;
```

This performs data cleansing, validation, and standardization.

### Querying Gold Layer

```sql
-- Query customer dimension
SELECT * FROM gold.dim_customers;

-- Query product dimension
SELECT * FROM gold.dim_products;

-- Query sales facts
SELECT * FROM gold.fact_sales;

-- Example: Sales by customer
SELECT 
    c.first_name,
    c.last_name,
    SUM(f.sales_amount) AS total_sales,
    SUM(f.quantity) AS total_quantity
FROM gold.fact_sales f
JOIN gold.dim_customers c ON f.customer_key = c.customer_key
GROUP BY c.first_name, c.last_name
ORDER BY total_sales DESC;
```

### Running Quality Checks

```sql
-- Silver layer quality checks
-- Execute: tests/quality_checks_silver.sql

-- Gold layer quality checks
-- Execute: tests/quality_checks_gold.sql
```

## 📊 Data Model

### Dimension Tables

#### dim_customers
| Column | Type | Description |
|--------|------|-------------|
| customer_key | INT | Surrogate key (Primary Key) |
| customer_id | INT | Natural key from source system |
| customer_number | NVARCHAR(50) | Business identifier |
| first_name | NVARCHAR(50) | Customer first name |
| last_name | NVARCHAR(50) | Customer last name |
| country | NVARCHAR(50) | Country of residence |
| marital_status | NVARCHAR(50) | Marital status |
| gender | NVARCHAR(50) | Gender |
| birthdate | DATE | Date of birth |
| create_date | DATE | Record creation date |

#### dim_products
| Column | Type | Description |
|--------|------|-------------|
| product_key | INT | Surrogate key (Primary Key) |
| product_id | INT | Natural key from source system |
| product_number | NVARCHAR(50) | Business identifier |
| product_name | NVARCHAR(50) | Product name |
| category_id | NVARCHAR(50) | Category identifier |
| category | NVARCHAR(50) | Product category |
| subcategory | NVARCHAR(50) | Product subcategory |
| maintenance | NVARCHAR(50) | Maintenance requirement flag |
| cost | INT | Product cost |
| product_line | NVARCHAR(50) | Product line |
| start_date | DATE | Product availability start date |

### Fact Tables

#### fact_sales
| Column | Type | Description |
|--------|------|-------------|
| order_number | NVARCHAR(50) | Order identifier |
| product_key | INT | Foreign key to dim_products |
| customer_key | INT | Foreign key to dim_customers |
| order_date | DATE | Order placement date |
| shipping_date | DATE | Order shipment date |
| due_date | DATE | Payment due date |
| sales_amount | INT | Total sales amount |
| quantity | INT | Quantity sold |
| price | INT | Unit price |

## 📚 Documentation

Comprehensive documentation is available in the `docs/` folder:

- **[Data Catalog](docs/data_catalog.md)**: Complete data dictionary for all Gold layer objects
- **[Naming Conventions](docs/naming_conventions.md)**: Standards for tables, columns, and procedures
- **Architecture Diagrams**: Visual representations of data flow and system design
- **ETL Documentation**: Process flows and transformation logic

## ✅ Quality Assurance

The project includes comprehensive quality checks:

### Silver Layer Checks
- Row count validation
- Duplicate detection
- Null value checks
- Data type validation

### Gold Layer Checks
- Referential integrity validation
- Business rule validation
- Dimension completeness checks
- Fact table validation

Run quality checks after each ETL execution to ensure data integrity.

## 📝 Naming Conventions

The project follows strict naming conventions for consistency:

### Table Naming
- **Bronze/Silver**: `<sourcesystem>_<entity>` (e.g., `crm_customer_info`)
- **Gold Dimensions**: `dim_<entity>` (e.g., `dim_customers`)
- **Gold Facts**: `fact_<entity>` (e.g., `fact_sales`)

### Column Naming
- **Surrogate Keys**: `<table_name>_key` (e.g., `customer_key`)
- **Technical Columns**: `dwh_<column_name>` (e.g., `dwh_create_date`)
- **General**: `snake_case` with lowercase letters

### Stored Procedures
- **ETL Procedures**: `load_<layer>` (e.g., `load_bronze`, `load_silver`)

See [naming_conventions.md](docs/naming_conventions.md) for complete details.

## 🤝 Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## 📧 Contact

For questions or feedback, please open an issue in the GitHub repository.

## 📄 License

This project is licensed under the MIT License - see the LICENSE file for details.

---

**Note**: This is an educational project designed to demonstrate data warehouse design patterns and best practices.
