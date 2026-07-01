# 📊 Retail Sales Performance Analysis (SQL)

## 📌 Project Overview
Proyek ini mensimulasikan peran seorang Data Analyst dalam mengolah data mentah transaksi penjualan ritel menjadi informasi strategis bagi manajemen menggunakan SQL. Analisis mencakup filter performa produk, agregasi kategori, penyaringan target omset, hingga analisis perilaku belanja pelanggan *Member* vs *Standard*.

---

## 🛠️ 1. Inisialisasi Database & Struktur Tabel.
Membuat database baru bernama `latihan` dan merancang struktur tabel dengan validasi `NOT NULL` untuk memastikan integritas data (tidak ada kolom esensial yang kosong).
```SQL
create database latihan;
use latihan;
DROP TABLE data_penjualan;
CREATE TABLE data_penjualan (
    Transaction_ID VARCHAR(10) NOT NULL,
    Date DATE NOT NULL,
    Product_Name VARCHAR(100) NOT NULL,
    Category VARCHAR(100) NOT NULL,
    Price INT NOT NULL,
    Quantity INT NOT NULL,
    Customer_Type VARCHAR(100) NOT NULL
);
SELECT * FROM data_penjualan;

INSERT INTO data_penjualan (Transaction_ID, Date, Product_Name, Category, Price, Quantity, Customer_Type)
VALUES 	('TRX001', '2026-01-15', 'Laptop Asus', 'Electronics', 8000000, 2, 'Member'),
    		('TRX002', '2026-01-16', 'Smart TV', 'Electronics', 4500000, 1, 'Standard'),
        ('TRX003', '2026-01-17', 'Kemeja Flanel', 'Apparel', 250000, 3, 'Member'),
    		('TRX004', '2026-01-18', 'Rice Cooker', 'Home Appliances', 600000, 1, 'Standard'),
    		('TRX005', '2026-01-19', 'Smartphone', 'Electronics', 5000000, 4, 'Member'),
    		('TRX006', '2026-01-20', 'Blender', 'Home Appliances', 400000, 2, 'Standard'),
    		('TRX007', '2026-01-21', 'Sepatu Running', 'Apparel', 750000, 2, 'Member'),
    		('TRX008', '2026-01-22', 'Keyboard Mechanical', 'Electronics', 800000, 5, 'Standard'),
    		('TRX009', '2026-01-23', 'Jaket Bomber', 'Apparel', 400000, 1, 'Standard'),
    		('TRX010', '2026-01-24', 'Air Fryer', 'Home Appliances', 1200000, 1, 'Member');
```


🎯 2. Eksekusi Query Analisis Bisnis & Hasil Output.
Kasus 1: Filter Pendapatan Kategori 'Electronics'.
Tujuan: Mengetahui produk elektronik mana yang menyumbang pendapatan terbesar.
```SQL
SELECT
    Product_Name,
    Category,
    (Price * Quantity) AS Total_Revenue
From data_penjualan
where Category = "Electronics"
ORDER BY Total_Revenue DESC;
```
Insight: Smartphone memimpin pendapatan di kategori elektronik dengan total Rp20.000.000 dari hasil penjualan 4 unit, mengungguli Laptop Asus secara total omset meskipun harga satuan laptop jauh lebih mahal.


Kasus 2: Analisis Performa Kategori Produk (Agregasi). 
Tujuan: Meringkas total kuantitas terjual dan total omset dari setiap kelompok kategori.
```SQL
select
	  Category,
    SUM(Quantity) AS Total_Qty_Sold,
    SUM(Price * Quantity) AS Total_Revenue
from data_penjualan
group by category
order by Total_Revenue DESC;
```
Insight: Kategori Electronics merupakan penggerak utama (driver) bisnis dengan performa dominan, menjual 12 unit dan menyumbang omset tertinggi sebesar Rp44.500.000.

Kasus 3: Menyaring Kelompok Data yang Mencapai Target (Filtering Aggregation).
Tujuan: Menyaring kategori yang berhasil melewati target pendapatan di atas Rp2.000.000 menggunakan klausul HAVING.
```SQL
select
	  Category,
    SUM(Quantity) AS Total_Qty_Sold,
    SUM(Price * Quantity) AS Total_Revenue
From data_penjualan
group by category
having Total_Revenue > 2000000
order by Total_Revenue DESC;
```
Insight: Hanya kategori Electronics dan Home Appliances yang berhasil mencapai target manajemen. Kategori Apparel otomatis tereliminasi karena total pendapatannya berada di bawah standar target (hanya Rp1.400.000).

Kasus 4: Analisis Perilaku Belanja Tipe Pelanggan (Customer Segmentation).
Tujuan: Menganalisis segmen pelanggan yang memberikan kontribusi finansial terbesar serta melihat karakteristik rata-rata harga produk yang mereka minati.
```SQL
SELECT
	  Customer_Type,
    SUM(Price * Quantity) AS Total_Revenue,
    AVG(Price) AS Average_Product_Price
from data_penjualan
group by Customer_Type
order by Total_Revenue DESC;
```
Insight: Pelanggan berstatus Member menjadi tulang punggung profitabilitas toko dengan total kontribusi Rp34.250.000 (hampir 3 kali lipat dari pelanggan Standard). Nilai Average_Product_Price menunjukkan bahwa Member memiliki daya beli lebih tinggi untuk produk premium dengan rata-rata nilai belanja Rp2.940.000 per produk.


💡 3. Rekomendasi Bisnis Berbasis Data (Actionable Insights).
Retensi Pelanggan Premium (Member): Manajemen perlu menjaga loyalitas segmen Member lewat program eksklusif atau penawaran berbasis poin, karena kelompok ini terbukti menjadi penentu utama tingginya omset toko.

Strategi Cross-Selling untuk Apparel: Mengingat kategori Apparel belum mencapai target Rp2 Juta, disarankan membuat strategi bundling (misal: beli produk Elektronik, dapat diskon khusus untuk Sepatu Running/Jaket Bomber) guna menaikkan volume penjualan Apparel.
