
**Big Data Analytics Kimia Farma x Rakamin Academy Batch February 2024**
--- 

SQL Tool: Google BigQuery - [Lihat Script SQL](https://github.com/dambarizki28/final-task-kimia-farma-bda/blob/main/query-final-task.sql.txt)

Visualization Tool: Google Looker Studio - [Lihat Dashboard](https://lookerstudio.google.com/reporting/c96de68a-a685-4da6-a80a-6ea799e4fa6e)

Dataset: [PBI Kimia Farma x Rakamin Academy](https://www.rakamin.com/virtual-internship-experience/kimiafarma-big-data-analytics-virtual-internship-program)


Program Introduction
---
Project Based Internship (PBI) adalah sebuah program magang virtual dari Rakamin Academy yang berkolaborasi dengan beberapa perusahaan yang bertujuan menggali potensi dan menambah pengalaman bagi peserta PBI. Dalam program ini, saya berperan sebagai Big Data Analytics di Kimia Farma yang menganalisis dan melaporkan data mengenai Performance Analytics Kimia Farma di Tahun 2020 - 2023.

**Challenge**
- Membuat tabel analisa
- Membuat visualisas/dashboard Performance Analytics Kimia Farma di Tahun 2020 - 2023

**Dataset**
- Final transaction
- Inventory
- Kantor Cabang
- Product



Desain Datamart
---
**Tabel Analisa**

Tabel analisa adalah tabel yang berisi data asli yang sudah menjadi satu dengan aggregat dengan beberapa kolom yaitu 'presentase_gross_laba', 'nett_profit', dan 'nett_sales'. 

**Query Tabel Analisa**


```sql
-- Membuat tabel analisa

CREATE TABLE dataset_kimia_farma.analytic_table AS
SELECT 
    ft.transaction_id,
    ft.date, 
    ft.branch_id, 
    kc.branch_name,
    kc.kota,
    kc.provinsi,
    kc.rating,
    ft.customer_name,
    p.product_id,
    p.product_name,
    ft.price,
    ft.discount_percentage,
CASE
  WHEN ft.price <= 50000 THEN 0.10
  WHEN ft.price > 50000 - 100000 THEN 0.15
  WHEN ft.price > 100000 - 300000 THEN 0.20
  WHEN ft.price > 300000 - 500000 THEN 0.25
  WHEN ft.price > 500000 THEN 0.30
ELSE 0.30
END AS presentase_gross_laba, 
(ft.price - (ft.price * ft.discount_percentage)) AS nett_sales,
(ft.price * (1 - ft.discount_percentage) * CASE
                                              WHEN ft.price <= 50000 THEN 0.10
                                              WHEN ft.price > 50000 - 100000 THEN 0.15
                                              WHEN ft.price > 100000 - 300000 THEN 0.20
                                              WHEN ft.price > 300000 - 500000 THEN 0.25
                                              WHEN ft.price > 500000 THEN 0.30
                                            ELSE 0.30
                                              END) AS nett_profit,
ft.rating AS rating_transaksi
FROM 
    dataset_kimia_farma.kf_final_transaction AS ft 
LEFT JOIN 
    dataset_kimia_farma.kf_kantor_cabang AS kc ON ft.branch_id = kc.branch_id
LEFT JOIN 
    dataset_kimia_farma.kf_product AS p ON ft.product_id = p.product_id;
```

**Hasil Tabel Analisa**



![Screenshot 2024-03-01 112045](https://github.com/dambarizki28/final-task-kimia-farma-bda/assets/161567903/65ca036d-b27a-4375-924f-57c36336cf14)

![Screenshot 2024-03-01 112937](https://github.com/dambarizki28/final-task-kimia-farma-bda/assets/161567903/86f5607e-165a-4f6e-aadf-acec54ef904b)

![Screenshot 2024-03-01 113003](https://github.com/dambarizki28/final-task-kimia-farma-bda/assets/161567903/789c516f-9c71-4c1b-b08f-74ca826d1907)

![Screenshot 2024-03-01 113037](https://github.com/dambarizki28/final-task-kimia-farma-bda/assets/161567903/f2e9c40b-f259-4083-9efd-6a7164ecb60a)

Kemudian, dalam salah satu challenge di dashboard visualization terdapat perintah untuk membuat visualisasi perbandingan pendapatan Kimia Farma dari tahun ke tahun. Maka, yang saya lakukan adalah membuat query untuk tabel 'total_revenue'. Maka syntaxnya seperti gambar di bawah ini:

```sql
CREATE TABLE dataset_kimia_farma.total_revenue_per_year AS
SELECT EXTRACT(YEAR FROM date) AS year, SUM(nett_sales) AS total_revenue
FROM dataset_kimia_farma.analytic_table
GROUP BY year
ORDER BY year;
```

Dengan syntax di atas maka akan menghasilkan tabel 'total_revenue' sebagai berikut

![Table total revenue](https://github.com/dambarizki28/final-task-kimia-farma-bda/assets/161567903/b8a93733-ea17-4662-84b0-a8c0a7432596)




Visualisasi Data
---

[Lihat visualisasi data disini](https://lookerstudio.google.com/reporting/c96de68a-a685-4da6-a80a-6ea799e4fa6e)

![Damba_RF_-_Final_Task_Kimia_Farma_Performance_Analytics-1](https://github.com/dambarizki28/final-task-kimia-farma-bda/assets/161567903/cc17accd-fa4e-4b36-a9e9-f3692d181125)


From the visualization data that has been made, there are several insights obtained:

- Kimia Farma's highest revenue is in 2022 amounting to IDR 80,578,445,844.
- West Papua Province is the province with the least number of Kimia Farma branches with 14 branches.
- The highest total net sales were achieved by West Java Province with total net sales of Rp 94,869,594,875.
- The lowest revenue by product was achieved by the category of other anagelsics and antipyretics, pyrazolones and anilides.
