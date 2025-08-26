# 📊 Kimia Farma Sales Analysis

Analisis penjualan Kimia Farma berdasarkan data transaksi, produk, dan kantor cabang. Proyek ini menggunakan Google BigQuery untuk pemrosesan data dan Looker Studio untuk visualisasi dashboard interaktif.

## 🧠 Tujuan Proyek

- Menghitung nett sales dan nett profit berdasarkan harga dan diskon
- Menggabungkan data transaksi dengan data produk dan cabang
- Visualisasi performa cabang & produk
- Perbandingan Pendapatan Kimia Farma dari tahun ke tahun
- Top 10 Total transaksi cabang provinsi
- Top 10 Nett sales cabang provinsi
- Top 5 Cabang Dengan Rating Tertinggi, namun Rating Transaksi Terendah
- Indonesia's Geo Map Untuk Total Profit Masing-masing Provinsi

## 📂 Tools yang Digunakan

- BigQuery (SQL)
- Looker Studio (Dashboard)
- GitHub (Documentation)

---

[Uploading Data Dictionary.md…]()

# Data Dictionary

## Tabel: kf_final_transaction

| Kolom               | Tipe Data | Deskripsi                                              |
|---------------------|-----------|--------------------------------------------------------|
| transaction_id      | STRING    | Kode ID transaksi                                      |
| product_id          | STRING    | Kode produk obat                                       |
| branch_id           | STRING    | Kode ID cabang Kimia Farma                             |
| customer_name       | STRING    | Nama customer yang melakukan transaksi                 |
| date                | DATE      | Tanggal transaksi dilakukan                            |
| price               | FLOAT     | Harga obat                                             |
| discount_percentage | FLOAT     | Persentase diskon yang diberikan pada obat (decimal)   |
| rating              | FLOAT     | Penilaian konsumen terhadap transaksi                  |

---

## Tabel: kf_product

| Kolom            | Tipe Data | Deskripsi                   |
|------------------|-----------|-----------------------------|
| product_id       | STRING    | Kode produk obat            |
| product_name     | STRING    | Nama produk obat            |
| product_category | STRING    | Kategori produk obat        |
| price            | FLOAT     | Harga obat                  |

---

## Tabel: kf_inventory

| Kolom          | Tipe Data | Deskripsi                            |
|----------------|-----------|--------------------------------------|
| inventory_ID   | STRING    | Kode inventory produk obat           |
| branch_id      | STRING    | Kode ID cabang Kimia Farma           |
| product_id     | STRING    | Kode ID produk obat                  |
| product_name   | STRING    | Nama produk obat                     |
| opname_stock   | INTEGER   | Jumlah stok produk obat              |

---

## Tabel: kf_kantor_cabang

| Kolom           | Tipe Data | Deskripsi                                     |
|-----------------|-----------|-----------------------------------------------|
| branch_id       | STRING    | Kode ID cabang Kimia Farma                    |
| branch_category | STRING    | Kategori cabang Kimia Farma                   |
| kota            | STRING    | Kota cabang Kimia Farma                       |
| branch_name     | STRING    | Nama kantor cabang Kimia Farma                |
| provinsi        | STRING    | Provinsi cabang Kimia Farma                   |
| rating          | FLOAT     | Penilaian konsumen terhadap cabang Kimia Farma|

## 🔍 Hasil Analisis

📌 Link dashboard Looker Studio:  
👉 [Lihat Dashboard](https://lookerstudio.google.com/reporting/e1ec9d52-e4a6-45df-bc5d-45e9061a66c2)

📌 [Dashboard Screenshot]
<img width="1393" height="779" alt="Kimia Farma Analysis by Natalia Medya LIS" src="https://github.com/user-attachments/assets/09ae6f4c-02d4-4ea2-876a-734dbee4c447" />



[Query Analisis Kimia Farma.sql](https://github.com/user-attachments/files/21986982/Query.Analisis.Kimia.Farma.sql)

SELECT
  t.transaction_id,
  t.date,
  t.branch_id,
  kc.branch_name,
  kc.kota,
  kc.provinsi,
  kc.rating AS rating_cabang,
  t.customer_name,
  t.product_id,
  p.product_name,
  t.price AS actual_price,
  t.discount_percentage,

  -- Persentase gross laba
  CASE
    WHEN t.price <= 50000 THEN 0.10
    WHEN t.price > 50000 AND t.price <= 100000 THEN 0.15
    WHEN t.price > 100000 AND t.price <= 300000 THEN 0.20
    WHEN t.price > 300000 AND t.price <= 500000 THEN 0.25
    ELSE 0.30
  END AS persentase_gross_laba,

  -- Nett sales
  ROUND(t.price * (1 - t.discount_percentage), 2) AS nett_sales,

  -- Nett profit
  ROUND(
    (t.price * (1 - t.discount_percentage)) *
    CASE
      WHEN t.price <= 50000 THEN 0.10
      WHEN t.price > 50000 AND t.price <= 100000 THEN 0.15
      WHEN t.price > 100000 AND t.price <= 300000 THEN 0.20
      WHEN t.price > 300000 AND t.price <= 500000 THEN 0.25
      ELSE 0.30
    END, 2
  ) AS nett_profit,

  t.rating AS rating_transaksi

FROM
  `kimia_farma.kf_final_transaction` t
JOIN
  `kimia_farma.kf_kantor_cabang` kc
  ON t.branch_id = kc.branch_id
JOIN
  `kimia_farma.kf_product` p
  ON t.product_id = p.product_id;


---

## ✍️ Author
**Natalia Medya Lifa Ir Seno**  
[LinkedIn](https://www.linkedin.com/in/nataliamlis) / nataliamedya16@gmail.com
