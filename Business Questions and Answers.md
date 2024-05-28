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

### 6. Siapa saja pelanggan yang melakukan pembelian paling besar?

```sql
SELECT
  DISTINCT u.id AS user_id,
  u.first_name AS First_Name,
  u.last_name AS Last_Name,
  u.country AS Country,
  ROUND(SUM(o.num_of_item * oi.sale_price),1) AS Total_Purchase
FROM bigquery-public-data.thelook_ecommerce.users as u
INNER JOIN bigquery-public-data.thelook_ecommerce.orders as o
ON u.id = o.user_id
INNER JOIN bigquery-public-data.thelook_ecommerce.order_items as oi
ON  o.order_id = oi.order_id
WHERE o.status = 'Complete'
GROUP BY  user_id, First_Name, Last_Name, Country
ORDER BY Total_Purchase DESC
LIMIT 10
```

### Output
![image](https://github.com/agnesmtyas/TheLook-Ecommerce-Analysis/assets/161667923/f76fe742-a112-4b4f-9e42-b635b13c6e72)

### Insight
* Pelanggan yang melakukan pembelian paling besar adalah Andrew Wilson yang berada di negara Poland dengan total pembayaran sebesar 4.702,3
* Andrew Wilson memiliki total pembayaran paling besar karena melakukan pembelian produk dalam jumlah besar atau membeli barang dengan harga yang tinggi.

### Call to action
Untuk mempertahankan loyalitas pelanggan, perusahaan perlu memberikan layanan pelanggan layanan yang efisien, mempertahankan produk yang berkualitas, serta menawarkan program loyalitas. Selain itu, diskon dengan nominal yang lebih tinggi bisa dipertimbangkan untuk diberikan kepada pelanggan yang mencapai minimal pembayaran tertentu.

### 7. Negara apa yang memberikan pendapatan paling besar?

```sql
SELECT
    DISTINCT u.country as Country,
    ROUND(SUM(oi.sale_price * o.num_of_item),1) AS Revenue
FROM bigquery-public-data.thelook_ecommerce.users as u
INNER JOIN bigquery-public-data.thelook_ecommerce.order_items as oi
ON u.id = oi.user_id
INNER JOIN bigquery-public-data.thelook_ecommerce.orders as o
ON oi.order_id = o.order_id
GROUP BY Country
ORDER BY Revenue DESC
LIMIT 10
```

### Output
![image](https://github.com/agnesmtyas/TheLook-Ecommerce-Analysis/assets/161667923/7d527a8c-1259-4837-9a81-a67291fbed5b)

### Insight
* China memberikan pendapatan untuk e-commerce paling besar dengan total pendapatan sebesar 6.936.191,9, diikuti oleh United States dan Brasil.
* Hal ini dikarenakan China memiliki populasi yang sangat besar, pertumbuhan pasar dan ekonomi yang pesat, dan infrastruktur teknologi yang canggih sehingga banyak warga China yang melakukan transaksi melalui e-commerce.

### Call to action
Perusahaan dapat meningkatkan pendapatan e-commerce dengan melakukan ekspansi global dan melakukan penguatan kemitraan sembari memantau perubahan pasar dan kebutuhan konsumen supaya negara-negara yang menghasilkan pendapatan besar dapat meningkatkan transaksinya terhadap e-commerce.

### 8. Apa kategori produk yang menghasilkan pendapatan paling besar?

```sql
SELECT
  category AS Product_Category,
  SUM(num_of_item) AS Quantity,
  ROUND(SUM(sale_price * num_of_item),1) AS Revenue
FROM bigquery-public-data.thelook_ecommerce.order_items AS oi
INNER JOIN bigquery-public-data.thelook_ecommerce.orders AS o
ON oi.order_id = o.order_id
INNER JOIN bigquery-public-data.thelook_ecommerce.products AS p
ON p.id = oi.product_id
WHERE oi.status = 'Complete'
GROUP BY Category
ORDER BY Revenue DESC
LIMIT 5
```

### Output
![image](https://github.com/agnesmtyas/TheLook-Ecommerce-Analysis/assets/161667923/fa7e0fba-877f-4fef-adab-cd8da9fa5e97)

### Insight
* Kategori yang memberikan pendapatan paling besar adalah Outwear & Coats, diikuti oleh Jeans, Sweaters, Suit & Sport Coats, dan Swim.
* Kategori Outwear  & Coats kemungkinan besar terkait dengan musim tertentu, seperti musim dingin atau hujan sehingga konsumen membutuhkan penggunaan mantel atau pakaian luar.

### Call to action
Untuk mempertahankan keuntungan ini, perusahaan dapat meningkatkan stok dan strategi promosi atau diskon ketika musim hujan atau musim dingin akan/sedang berlangsung.

### 9. Bagaimana kategori umur pelanggan pada kategori produk yang paling laris?

```sql
SELECT
  CASE
    WHEN u.age < 12 THEN 'Children'
    WHEN u.age BETWEEN 12 AND 20 THEN 'Youth'
    WHEN u.age BETWEEN 21 AND 30 THEN 'Young Adults'
    WHEN u.age BETWEEN 31 AND 65 THEN 'Adults'
    WHEN u.age > 65 THEN 'Older Adults'
    END AS age_group,
  COUNT(DISTINCT u.id) AS Total_Pelanggan
FROM bigquery-public-data.thelook_ecommerce.users AS u
INNER JOIN bigquery-public-data.thelook_ecommerce.order_items AS oi
ON u.id = oi.user_id
INNER JOIN bigquery-public-data.thelook_ecommerce.inventory_items AS ii
ON ii.product_id = oi.product_id
WHERE ii.product_category = 'Outerwear & Coats'
GROUP BY age_group
ORDER BY Total_Pelanggan DESC
```

### Output
![image](https://github.com/agnesmtyas/TheLook-Ecommerce-Analysis/assets/161667923/810bb65a-0c66-4ad0-a50e-3304b414c31d)

### Insight
* Pada kategori produk yang paling laris, yaitu Outerwear & Coats, pelanggan terbanyak berada pada kelompok umur Adults sebanyak 5.120 pelanggan, diikuti kelompok umur Young Adults sebanyak 1.433 pelanggan.
* Pakaian outerwear & coats menjadi populer di kalangan umur yang lebih dewasa, hal ini dikarekanakan kebutuhan yang lebih besar untuk pakaian outer atau fashion. Kalangan Young Adults lebih peka terhadap tren mode dan gaya, mereka cenderung membeli produk outerwear & coats untuk menjaga penampilan mereka di musim dingin.

### Call to action
Perusahaan dapat mengarahkan strategi pemasaran dan promosi untuk lebih tepat sasaran, seperti di kelompok umur adults dan young adults dan memenuhi kebutuhan pelanggan yang dominan.

### 10.	Brand apa yang paling sering dikembalikan oleh pelanggan?

```sql
SELECT 
  DISTINCT p.brand AS Brand,
  COUNT(o.num_of_item) AS Total_Returned
FROM bigquery-public-data.thelook_ecommerce.products as p
INNER JOIN bigquery-public-data.thelook_ecommerce.order_items as oi
ON p.id = oi.product_id
INNER JOIN bigquery-public-data.thelook_ecommerce.orders as o
ON oi.order_id = o.order_id
WHERE o.status = 'Returned'
GROUP BY Brand
ORDER BY Total_Returned DESC
LIMIT 5
```

### Output 
![image](https://github.com/agnesmtyas/TheLook-Ecommerce-Analysis/assets/161667923/a5c7fadf-2fe8-41a7-9567-4ce6cc297aca)

### Insight
* Brand yang sering dikembalikan oleh pelanggan adalah brand clothing, seperti Allegra K sebanyak 615 unit, diikuti oleh Calvin Klein sebanyak 324 unit.
* Hal ini dikarenakan pakaian yang tiba tidak sesuai dengan ukuran yang diinginkan oleh pelanggan maupun kualitas produk yang tidak sesuai dengan ekspektasi pelanggan.

### Call to action
Perusahaan dapat meninjau kembali kualitas produk yang dihasilkan serta meningkatkan detail deskripsi produk agar barang lebih sesuai dan mengurangi tingkat pengembalian produk.
