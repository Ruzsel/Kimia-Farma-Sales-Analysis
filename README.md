# Kimia-Farma-Sales-Analysis
### "The project I created while participating in the 'Project-Based Virtual Intern : Big Data Analytics Kimia Farma x Rakamin Academy' program by Rakamin Academy."
## Table of Contents
- [Project Overview](#project-overview)
- [Data Sources and Tools](#data-sources)
- [Data Analysis](#data-analysis)
- [Interactive Dashboard](#interactive-dashboard)
## Project Overview
Creating a Data Mart, which involved designing both base and aggregate tables. Developing visualizations for the Data Mart using Microsoft Power BI. In the process, I utilized PostgreSQL as the primary database management system
![Kimia Farma PBI-1](https://github.com/Ruzsel/Kimia-Farma-Sales-Analysis/assets/150054552/21076d98-d322-467f-8887-dbae5c00e212)

### Data Sources
Data_Intern.xlsx : The raw sales data of various Kimia Farma pharmaceutical brands provided by Rakamin Academy
### Tools
- Excel : Dataset
- VS Code Studio : Extracting each Sheet from Data_Intern.xlsx
- PostgreSQL : Creating Data Mart, designing both base and aggregate tables
- Microsoft Power BI : Data Visualizations
### Data Analysis
Extract each sheet in the xlsx file
```python
import pandas as pd

excel_file = pd.ExcelFile('Data_Intern.xlsx')
for sheet_name in excel_file.sheet_names:
    df = pd.read_excel(excel_file, sheet_name)
    df.to_csv(f'{sheet_name}.csv', index=False)
```
Creating base table `barang_base`
```sql
CREATE TABLE barang_base(
  id_barang VARCHAR(15) PRIMARY KEY UNIQUE,
  nama_barang VARCHAR(50),
  kode_lini SMALLINT,
  lini VARCHAR(30),
  kemasan VARCHAR(20)
  );
INSERT INTO barang_base(id_barang, nama_barang, kode_lini, lini, kemasan)
SELECT
  kode_barang,
  nama_barang,
  kode_lini,
  lini,
  kemasan
FROM
  barang;
```
| Column       | Data Type   | Description                      | Transformation |
|--------------|-------------|----------------------------------|----------------|
| id_barang    | VARCHAR(15) | Unique identifier of the product | Primary Key, Unique |
| nama_barang  | VARCHAR(50) | Name of the product              | -                |
| kode_lini    | SMALLINT    | Code for the product line        | -                |
| lini         | VARCHAR(30) | Name of the product line         | -                |
| kemasan      | VARCHAR(20) | Type of packaging                | -                |

Creating base table `pelanggan_base`
```sql
CREATE TABLE pelanggan_base(
  id_customer VARCHAR(15) PRIMARY KEY UNIQUE,
  nama VARCHAR(40),
  id_cabang VARCHAR(15),
  area_cabang VARCHAR(30),
  id_group VARCHAR(10),
  jenis_group VARCHAR(20)
);
INSERT INTO pelanggan_base(id_customer, nama, id_cabang, area_cabang, id_group, jenis_group)
SELECT
  id_customer,
  nama,
  id_cabang_sales,
  cabang_sales,
  id_group,
  "group"
FROM
  pelanggan
;
```
| Column         | Data Type   | Description                           | Transformation     |
|----------------|-------------|---------------------------------------|--------------------|
| id_customer    | VARCHAR(15) | Unique identifier of the customer     | Primary Key, Unique |
| nama           | VARCHAR(40) | Name of the pharmacy or clinic        | -                  |
| id_cabang      | VARCHAR(15) | Identifier of the branch pharmacy/clinic | -                  |
| area_cabang    | VARCHAR(30) | City of the pharmacy/clinic           | -                  |
| id_group       | VARCHAR(10) | Z31=Clinic Z32=Pharmacy               | -                  |
| jenis_group    | VARCHAR(20) | Description of whether the branch is a clinic or pharmacy | -                  |

Creating Aggregate table `penjualan_agg`
```sql
CREATE TABLE penjualan_agg(
  id_invoice VARCHAR(15) PRIMARY KEY UNIQUE,
  id_cabang VARCHAR(15),
  id_customer VARCHAR(20),
  tanggal DATE,
  jumlah_barang SMALLINT,
  harga NUMERIC
);
INSERT INTO penjualan_agg(id_invoice, id_cabang, id_customer, tanggal, jumlah_barang, harga)
SELECT
  id_invoice,
  id_cabang,
  id_customer,
  tanggal,
  jumlah_barang,
  harga
FROM
  pelanggan
;
```
```sql
ALTER TABLE
  penjualan_agg
ADD CONSTRAINT
  fk_penjualan_barang
FOREIGN KEY
  (id_barang)
REFERENCES
  barang_base(id_barang)
;
ALTER TABLE
  penjualan_agg
ADD CONSTRAINT
  fk_penjualan_pelanggan
FOREIGN KEY
  (id_customer)
REFERENCES
  pelanggan_base(id_customer)
;
```
```sql
ALTER TABLE
  penjualan_agg
ADD COLUMN
  revenue NUMERIC;
UPDATE
  penjualan_agg
SET
  revenue = jumlah_barang * harga
;
```
```sql
ALTER TABLE penjulana_agg
ADD COLUMN hari VARCHAR(10),
ADD COLUMN minggu INTEGER;
UPDATE penjualan_agg
SET
  hari = TO_CHAR(tanggal::date, 'Day'),
  minggu = EXTRACT(WEEK FROM tanggal)
;
```

| Column         | Data Type   | Description                                   | Transformation            |
|----------------|-------------|-----------------------------------------------|---------------------------|
| id_invoice     | VARCHAR(15) | Unique identifier of the purchase invoice    | Primary Key, Unique      |
| id_barang      | VARCHAR(15) | Unique identifier of the item                | Foreign key to barang_base.id_barang |
| id_customer    | VARCHAR(20) | Unique identifier of the customer             | Foreign key to pelanggan_base.id_customer |
| tanggal        | DATE        | Date of the invoice                          | -                         |
| jumlah_barang  | SMALLINT    | Quantity of products sold                     | -                         |
| harga          | NUMERIC     | Price of each product                         | -                         |
| revenue        | NUMERIC     | Gross revenue from product sales              | Column 'jumlah_barang' * 'harga' |
| hari           | VARCHAR(10) | Day name when the sale occurred              | EXTRACT day from 'tanggal' |
| minggu         | INTEGER     | Week number of the sale occurrence            | EXTRACT week from 'tanggal' as integer |

## Interactive Dashboard
https://lnkd.in/gTtQV3tS
## LinkedIn

Click here>>> <a href="https://www.linkedin.com/in/fairuzmujahidannabil" target="_blank"><img src="https://upload.wikimedia.org/wikipedia/commons/c/ca/LinkedIn_logo_initials.png" alt="LinkedIn" width="30"></a> Fairuz Mujahid Annabil
