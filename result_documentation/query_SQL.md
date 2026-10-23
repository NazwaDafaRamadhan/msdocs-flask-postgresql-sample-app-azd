# Rekap Query SQL --- Homework DS Week 2

Dokumen ini merangkum query SQL yang digunakan atau dijelaskan dalam
laporan praktik pengelolaan data untuk Homework DS - Enrichment Session : Exploring SQL & Github.

## Daftar Isi

1.  [Database Setup](#1-database-setup)
2.  [Membuat Tabel](#2-membuat-tabel)
3.  [Memasukkan Data](#3-memasukkan-data)
4.  [Operasi CRUD](#4-operasi-crud)
5.  [Additional Queries](#5-additional-queries)
6.  [Extra Credit --- Tabel Menu](#6-extra-credit--tabel-menu)
7.  [Restoran, Rata-rata Rating, dan Daftar
    Menu](#7-restoran-rata-rata-rating-dan-daftar-menu)

# 1. Database Setup

```sql
CREATE DATABASE
IF NOT EXISTS restaurant_reviews 
```

# 2. Membuat Tabel

### Tabel restaurant

```sql
CREATE TABLE restaurant (
    id SERIAL PRIMARY KEY,
    name VARCHAR(50) NOT NULL,
    street_address VARCHAR(50) NOT NULL,
    description VARCHAR(250)
);
```

**Keterangan:**
- `id SERIAL PRIMARY KEY` menghasilkan ID otomatis yang unik untuk setiap restoran.
- `name` menyimpan nama restoran dan data tidak boleh dikosongkan.
- `street_address` menyimpan alamat restoran dan data tidak boleh dikosongkan.
- `description` menyimpan deskripsi restoran.

### Tabel review

```sql
CREATE TABLE review (
    id SERIAL PRIMARY KEY,
    restaurant INTEGER NOT NULL,
    user_name VARCHAR(30),
    rating INTEGER,
    review_text VARCHAR(500),
    review_date TIMESTAMP,
    CONSTRAINT fk_review_restaurant
        FOREIGN KEY (restaurant)
        REFERENCES restaurant(id)
        ON DELETE CASCADE
);
```

**Keterangan:**
- `id` merupakan ID unik ulasan yang dibuat secara otomatis.
- `restaurant` menyimpan ID restoran yang diulas.
- `user_name` menyimpan nama pengguna yang memberikan ulasan.
- `rating` menyimpan nilai rating.
- `review_text` menyimpan isi ulasan.
- `review_date` menyimpan tanggal dan waktu ulasan.
- *Foreign key* memastikan setiap ulasan merujuk pada restoran yang tersedia.

# 3. Memasukkan Data

### Menambahkan minimal 3 restoran

```sql
INSERT INTO restaurant (name, street_address, description)
VALUES
    ('Digital Cafe', 'Jl. Teknologi No. 10', 'Kafe dengan suasana nyaman untuk bekerja dan belajar.'),
    ('Bamboo Garden', 'Jl. Taman Bambu No. 20', 'Restoran dengan menu makanan bergaya Asia.'),
    ('Burger Shot', 'Jl. Merdeka No. 30', 'Restoran yang menyediakan berbagai jenis burger.');
```

### Memeriksa data restoran

```sql
SELECT *
FROM restaurant
ORDER BY id;
```

### Menambahkan ulasan

```sql
INSERT INTO review (restaurant, user_name, rating, review_text, review_date)
VALUES
    (1, 'Andi', 5, 'Makanan enak dan pelayanan ramah.', '2026-10-10 12:00:00'),
    (1, 'Budi', 4, 'Tempat nyaman untuk berkumpul.', '2026-10-12 13:30:00'),
    (1, 'Citra', 5, 'Kualitas makanan sangat baik.', '2026-10-15 18:15:00'),
    (1, 'Dina', 4, 'Menu cukup beragam dan harga sesuai.', '2026-10-20 19:00:00'),

    (2, 'Eko', 4, 'Suasana restoran nyaman.', '2026-10-11 11:00:00'),
    (2, 'Fani', 5, 'Rasa makanan lezat.', '2026-10-14 12:45:00'),
    (2, 'Gilang', 3, 'Pelayanan perlu ditingkatkan.', '2026-10-17 17:20:00'),
    (2, 'Hana', 5, 'Pengalaman makan yang menyenangkan.', '2026-10-21 20:00:00'),

    (3, 'Indra', 5, 'Burger terasa segar dan lezat.', '2026-10-09 13:10:00'),
    (3, 'Jihan', 4, 'Porsi cukup besar.', '2026-10-13 14:00:00'),
    (3, 'Kevin', 4, 'Harga cukup sesuai dengan kualitas.', '2026-10-18 18:30:00'),
    (3, 'Laila', 5, 'Salah satu burger favorit saya.', '2026-10-22 19:45:00');
```

### Memeriksa data ulasan

```sql
SELECT *
FROM review
ORDER BY restaurant, review_date;
```

# 4. Operasi CRUD

### Create --- menambahkan restoran baru

``` sql
INSERT INTO restaurant (name, street_address, description)
VALUES ('Forest Matter Resto', 'Jl. Jalan ke Pasar Minggu no.192, IKN', 
'Free Oksigen and delicious food for all indonesian people');
```

### Create --- menambahkan ulasan baru

``` sql
INSERT INTO review (restaurant, user_name, rating, review_text, review_date)
VALUES
    (4, 'Bowo', 2, 'Banyak yang masih harus ditingkatkan untuk memenuhi kepuasan konsumen', '2026-10-20 20:10:24'),
    (4, 'Joko', 1, 'Kinerja tidak seperti sebelumya, diperlukan banyak evaluasi', '2026-10-22 17:08:45');
```

### Read --- mengambil ulasan dari restoran tertentu

``` sql
SELECT *
FROM review
WHERE restaurant = 4;
```

### Read --- mengambil ulasan dengan rating minimal 4

``` sql
SELECT *
FROM review
WHERE rating >= 4;
```

### Read --- menampilkan restoran beserta ulasannya

``` sql
SELECT
    res.name,
    rev.user_name,
    rev.rating,
    rev.review_text
FROM restaurant AS res
JOIN review AS rev
    ON rev.restaurant = res.id;
```

### Update --- memperbarui deskripsi restoran

``` sql
UPDATE restaurant
SET description = 'Gratis Udara sejuk serta makanan enak untuk seluruh masyarakat sekitar.'
WHERE id = 4;
```

### Update --- memperbarui rating ulasan

``` sql
UPDATE review
SET rating = 2
WHERE id = 26;
```

### Delete --- menghapus ulasan tertentu

``` sql
DELETE FROM review
WHERE id = 26;
```

### Delete --- menghapus restoran beserta ulasan terkait

``` sql
DELETE FROM restaurant
WHERE id = 4;
```

> Karena foreign key menggunakan `ON DELETE CASCADE`, ulasan yang
> merujuk pada restoran tersebut ikut terhapus secara otomatis.

# 5. Additional Queries

### Restoran dengan rata-rata rating tertinggi

``` sql
SELECT
    res.name,
    AVG(rev.rating) AS avg_rating
FROM restaurant AS res
JOIN review AS rev
    ON rev.restaurant = res.id
GROUP BY res.name
ORDER BY avg_rating DESC
LIMIT 1;
```

**Konsep yang digunakan:**
- `JOIN` menghubungkan data restoran dengan ulasannya.
- `AVG()` menghitung rata-rata rating.
- `GROUP BY` mengelompokkan ulasan berdasarkan restoran.
- `ORDER BY ... DESC` mengurutkan rata-rata dari tertinggi ke terendah.
- `LIMIT 1` menampilkan satu hasil teratas.

### Jumlah ulasan untuk setiap restoran

``` sql
SELECT
    res.name,
    COUNT(rev.id) AS total_reviews
FROM restaurant AS res
JOIN review AS rev
    ON rev.restaurant = res.id
GROUP BY res.name
ORDER BY total_reviews DESC;
```

**Konsep yang digunakan:**
- `JOIN` memastikan semua restoran tetap muncul.
- `COUNT(rev.id)` menghitung ulasan yang dimiliki tiap restoran.
- `GROUP BY` mengelompokkan hasil per restoran.

### Ulasan terbaru untuk setiap restoran

``` sql
SELECT
    res.name,
    rev.user_name,
    rev.rating,
    rev.review_text,
    rev.review_date
FROM restaurant AS res
JOIN review AS rev
    ON rev.restaurant = res.id
WHERE rev.review_date = (
    SELECT MAX(rev2.review_date)
    FROM review AS rev2
    WHERE rev2.restaurant = res.id
)
ORDER BY res.id;
```

Subquery dengan `MAX()` mencari tanggal ulasan paling baru untuk setiap restoran. Jika ada beberapa ulasan dengan tanggal terbaru yang sama, query ini akan menampilkan semuanya.

# 6. Extra Credit --- Tabel Menu

### Membuat tabel menu

``` sql
CREATE TABLE menu (
    restaurant_id INTEGER,
    item_name VARCHAR(100),
    price NUMERIC,
    description VARCHAR(250),
    PRIMARY KEY (restaurant_id, item_name),
    FOREIGN KEY (restaurant_id)
        REFERENCES restaurant(id)
);
```

**Keterangan:**
- `id` merupakan ID menu yang bertambah secara otomatis.
- `restaurant_id` menghubungkan menu dengan restoran.
- `menu_name` menyimpan nama menu.
- `price` menyimpan harga menu dengan dua angka desimal.
- `PRIMARY KEY` mengatur kombinasi antara restaurant_id dan item_name sebagai composite primary key.
- `FOREIGN KEY` menghubungkan data menu dengan id restoran.


### Menambahkan menu

``` sql
INSERT INTO menu (restaurant_id, item_name, price, description)
VALUES
    (1, 'Nasi Goreng', 25000.00, 'Nasi goreng spesial dengan telur dan sayuran'),
    (1, 'Mie Goreng', 22000.00, 'Mie goreng dengan bumbu khas restoran'),
    (1, 'Es Teh', 5000.00, 'Minuman teh dingin yang menyegarkan'),

    (2, 'Ayam Bakar', 30000.00, 'Ayam bakar dengan bumbu rempah'),
    (2, 'Soto Ayam', 20000.00, 'Soto ayam hangat dengan kuah gurih'),
    (2, 'Jus Jeruk', 10000.00, 'Jus jeruk segar tanpa pemanis berlebih'),

    (3, 'Burger', 28000.00, 'Burger dengan daging sapi dan sayuran'),
    (3, 'Kentang Goreng', 15000.00, 'Kentang goreng renyah'),
    (3, 'Milkshake', 18000.00, 'Minuman susu kocok dengan rasa cokelat');
```

### Menampilkan seluruh data menu

``` sql
SELECT * FROM menu;
```

# 7. Restoran, Rata-rata Rating, dan Daftar Menu

``` sql
SELECT
    res.name,
    AVG(rev.rating) AS avg_rating,
    STRING_AGG(DISTINCT menu.item_name, ', ') AS menu_list
FROM restaurant AS res
JOIN review AS rev
    ON rev.restaurant = res.id
JOIN menu
    ON menu.restaurant_id = res.id
GROUP BY res.id, res.name
ORDER BY res.name;
```

**Konsep yang digunakan:**
- `AVG` mendapatkan nilai rata-rata dari atribut rating.
- `STRING_AGG(DISTINCT...)` menggabungkan beberapa nilai teks data menu menjadi satu string, sekaligus menghilangkan nilai duplikat.
- `JOIN` digunakan untuk menghubungkan tabel restaurant dengan tabel menu dan review.

ATAU

``` sql
SELECT
    res.name,
    (
        SELECT ROUND(AVG(rev.rating)::NUMERIC, 2)
        FROM review rev
        WHERE rev.restaurant = res.id
    ) AS rata_rata_rating,
    (
        SELECT STRING_AGG(menu.menu_name, ', ')
        FROM menu
        WHERE menu.restaurant_id = res.id
    ) AS daftar_menu
FROM restaurant res
ORDER BY res.name;
```

**Konsep yang digunakan:**
- subquery `ROUND(AVG...)` mendapatkan nilai rata-rata dan membulatkan hasil menjadi dua angka di belakang koma dari atribut rating pada tabel review.
- subquery `STRING_AGG(DISTINCT...)` menggabungkan beberapa nilai teks data menu menjadi satu string, sekaligus menghilangkan nilai duplikat dari tabel menu.
- tanpa menggunakan klausa `JOIN` untuk mendapatkan data.