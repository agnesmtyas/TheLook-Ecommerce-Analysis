## <p align="center"> Business Questions
Tujuan analisis ini untuk mengetahui jawaban beberapa masalah bisnis, mendapatkan jawaban dan memberikan rekomendasi untuk meningkatkan pendapatan.
---

### 1. Bagaimana pendapatan e-commerce pada setiap tahun?
```sql
SELECT
  EXTRACT(MONTH FROM oi.Created_at) AS months,
  ROUND(SUM(CASE WHEN EXTRACT(YEAR FROM oi.created_at) = 2019 THEN oi.sale_price * o.num_of_item ELSE 0 END), 2) AS Revenue_2019,
  ROUND(SUM(CASE WHEN EXTRACT(YEAR FROM oi.created_at) = 2020 THEN oi.sale_price * o.num_of_item ELSE 0 END), 2) AS Revenue_2020,
  ROUND(SUM(CASE WHEN EXTRACT(YEAR FROM oi.created_at) = 2021 THEN oi.sale_price * o.num_of_item ELSE 0 END), 2) AS Revenue_2021,
  ROUND(SUM(CASE WHEN EXTRACT(YEAR FROM oi.created_at) = 2022 THEN oi.sale_price * o.num_of_item ELSE 0 END), 2) AS Revenue_2022,
  ROUND(SUM(CASE WHEN EXTRACT(YEAR FROM oi.created_at) = 2023 THEN oi.sale_price * o.num_of_item ELSE 0 END), 2) AS Revenue_2023
FROM bigquery-public-data.thelook_ecommerce.order_items oi
INNER JOIN `bigquery-public-data.thelook_ecommerce.orders` AS o ON oi.order_id = o.order_id
WHERE oi.status = 'Complete'
  AND EXTRACT(YEAR FROM oi.Created_at) BETWEEN 2019 AND 2023
GROUP BY months
ORDER BY months
```
### Output
![image](https://github.com/agnesmtyas/TheLook-Ecommerce-Analysis/assets/161667923/4c7460e9-1505-4434-ac4b-3e415d886e5f)

### Insight
* Pendapatan pada tahun dari tahun 2019 hingga 2023 terus mengalami peningkatkan.
* Pendapatan melonjak pada bulan Juli dan Desember. Hal ini dikarenakan Juli merupakan musim panas di beberapa wilayah sehingga orang cenderung lebih banyak berbelanja. Desember merupakan akhir tahun dimana banyak orang sedang menikmati liburan Natal dan tahun baru sehingga orang cenderung berbelanja pula pada bulan ini.

### Call to action
Perusahaan perlu menyesuaikan strategi pemasaran dengan memberikan promosi dan diskon, meningkatkan persediaan barang, dan meningkatkan interaksi dengan pelanggan seperti melakukan live agar membangun koneksi yang lebih dekat dengan audiens, serta membuat konten yang bersifat menarik dan persuasif untuk dibagikan ke media sosial.

### 2. Kapan waktu pemesanan yang paling sibuk?
```sql
SELECT
  EXTRACT(HOUR FROM created_at) AS Order_Hour,
  COUNT(order_id) AS Order_Count
FROM bigquery-public-data.thelook_ecommerce.orders
GROUP BY order_hour
ORDER BY order_count DESC
```

### Output
![image](https://github.com/agnesmtyas/TheLook-Ecommerce-Analysis/assets/161667923/2f00ee1e-cac0-4807-b1b1-4e3a41459acc)

### Insight
* Waktu pemesanan rata-rata konstan, tetapi pelanggan paling banyak melakukan transaksi pada saat jam 5 pagi sebanyak 6.779 pesanan. Sementara itu, waktu paling sedikit bertransaksi adalah pukul 7 malam.

### Call to action
Perusahaan dapat mengoptimalkan strategi promosi pada jam-jam puncak, seperti memberikan diskon/promosi dan melakukan live pada jam tersebut. 

### 3. Bagaimana tren pelanggan baru yang melakukan transaksi di bulan yang sama?
```sql
SELECT
  EXTRACT(MONTH FROM u.created_at) AS Registration_Month,
  COUNT(DISTINCT u.id) AS New_Customers,
  COUNT(DISTINCT CASE WHEN EXTRACT(MONTH FROM o.created_at) = EXTRACT(MONTH FROM delivered_at) THEN user_id END) AS Returning_Customers
FROM bigquery-public-data.thelook_ecommerce.orders AS o
INNER JOIN bigquery-public-data.thelook_ecommerce.users AS u
ON o.user_id = u.id
GROUP BY Registration_Month
ORDER BY Registration_Month
```

### Output
![image](https://github.com/agnesmtyas/TheLook-Ecommerce-Analysis/assets/161667923/77a80e39-1ea9-4669-8e8a-6a87b30d186f)

### Insight
* Bulan dengan registrasi pengguna paling banyak berada pada bulan Maret, yaitu sebanyak 7.800 pengguna dan yang melakukan transaksi sebanyak 3.707 pelanggan.

###  Call to action
Perusahaan dapat terus memanfaatkan strategi promosi pada bulan Maret untuk menarik lebih banyak pelanggan baru. Namun, perusahaan perlu mengadakan promosi yang berkelanjutan pada bulan setelahnya agar dapat meningkatkan aktivitas dan transaksi pelanggan.

### 4. Bagaimana profil pelanggan pada setiap negara? 
```sql
SELECT 
  DISTINCT Country, 
  SUM(CASE WHEN u.gender = 'M' THEN 1 else null END) AS Male,
  SUM(CASE WHEN u.gender = 'F' THEN 1 else null END) as Female,
  COUNT(oi.user_id) AS Total_Pelanggan
FROM bigquery-public-data.thelook_ecommerce.users AS u
INNER JOIN bigquery-public-data.thelook_ecommerce.order_items AS oi
ON u.id = oi.user_id
WHERE oi.status = 'Complete'
GROUP BY Country
ORDER BY Total_Pelanggan DESC
```

### Output
![image](https://github.com/agnesmtyas/TheLook-Ecommerce-Analysis/assets/161667923/8be17bdf-1a3d-4df8-92a3-70f46f187cb8)

### Insight 
* Pelanggan paling banyak berasal dari negara China dengan total sebanyak 15.665 pelanggan. Sementara itu, pelanggan paling sedikit berasal dari negara Austria dengan total sebanyak 2 pelanggan.

### Call to action
* Perusahaan dapat mengembangkan strategi pemasaran yang unik dan menarik berdasarkan preferensi dan kebutuhan antara laki-laki dan perempuan.

### 5. Apa sumber traffic yang lebih banyak diakses oleh pelanggan?
```sql
SELECT
  u.traffic_source AS Traffic_Source,
  COUNT(DISTINCT oi.user_id) AS Total_Customers,
FROM bigquery-public-data.thelook_ecommerce.users AS u
INNER JOIN bigquery-public-data.thelook_ecommerce.order_items AS oi
ON u.id = oi.user_id
GROUP BY Traffic_Source
ORDER BY Total_Customers DESC
```
### Output
![image](https://github.com/agnesmtyas/TheLook-Ecommerce-Analysis/assets/161667923/fc7a5f71-5baa-4bc6-9360-d58753895fa4)

### Insight
* Pelanggan paling banyak datang ke situs web melalui mesin pencarian, seperti Google, Mozila Firefox, Bing, dll sebanyak 56.009 pelanggan. Kemudian, diikuti oleh organik, yang merupakan pencarian yang muncul secara alami di mesin pencari sebanyak 12.080.
* Hal ini dikarenakan karena mesin pencarian menjadi alat utama yang digunakan oleh banyak orang untuk mencari informasi dan produk secara online sehingga membuat situs web lebih mudah ditemukan dan diakses oleh pengguna yang ingin mencari informasi.

### Call to action
Perusahaan mengoptimalkan situs web untuk mesin pencari, meningkatkan kehadiran di Facebook, dan mulai menyusun strategi pemasaran Email secara efektif untuk memaksimalkan traffic.
