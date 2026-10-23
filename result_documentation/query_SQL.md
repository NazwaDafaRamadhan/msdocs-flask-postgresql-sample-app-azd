# Dokumentasi DS - Enrichment Session: Exploring SQL & GitHub

Dokumen ini merangkum seluruh query SQL yang dikerjakan untuk mengelola data restoran, ulasan, dan menu berdasarkan tugas homework Enrichment Session: Exploring SQL & GitHub pada Bootcamp Data Science 2026 by Digital Skola.

## 1. Membuat Database `restaurant_reviews`

Database restaurant_reviews merupakan database yang akan digunakan untuk menyimpan seluruh hasil data pada praktik query SQL 

```sql
CREATE DATABASE
IF NOT EXISTS restaurant_reviews 
```

**Keterangan:**
- Query tersebut untuk membuat database dengan nama restaurant_reviews apabila database dengan nama tersebut masih belum tersedia pada system environment.

## 2. Membuat Tabel `restaurant`

Tabel `restaurant` menyimpan informasi dasar setiap restoran, yaitu nama, alamat, dan deskripsi.

```sql
CREATE TABLE restaurant (
    id SERIAL PRIMARY KEY,
    name VARCHAR(50) not null,
    street_address VARCHAR(50) not null,
    description VARCHAR(250)
);
```

**Keterangan:**
- `id SERIAL PRIMARY KEY` menghasilkan ID otomatis yang unik untuk setiap restoran.
- `name` menyimpan nama restoran dan data tidak boleh dikosongkan.
- `street_address` menyimpan alamat restoran dan data tidak boleh dikosongkan.
- `description` menyimpan deskripsi restoran.

> Jika tabel `restaurant` sudah dibuat sebelumnya, bagian ini tidak perlu dijalankan kembali.

## 2. Menambahkan Minimal 3 Data Restoran

Perintah `INSERT INTO` digunakan untuk menambahkan tiga restoran ke tabel `restaurant`.

```sql
INSERT INTO restaurant (name, street_address, description)
VALUES
    ('Digital Cafe', 'Jl. Teknologi No. 10', 'Kafe dengan suasana nyaman untuk bekerja dan belajar.'),
    ('Bamboo Garden', 'Jl. Taman Bambu No. 20', 'Restoran dengan menu makanan bergaya Asia.'),
    ('Burger Shot', 'Jl. Merdeka No. 30', 'Restoran yang menyediakan berbagai jenis burger.');
```

## 3. Memeriksa Data Restoran

Perintah `SELECT` digunakan untuk memastikan data restoran berhasil tersimpan.

```sql
SELECT *
FROM restaurant
ORDER BY id;
```

## 4. Membuat Tabel `review`

Tabel `review` menyimpan ulasan pengguna terhadap restoran.

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

> Jika tabel `review` sudah ada, jangan jalankan `CREATE TABLE` ini kembali. Pastikan nama kolom dan constraint sesuai dengan struktur tabel yang digunakan.

## 5. Menambahkan 12 Data Ulasan

Contoh berikut menambahkan empat ulasan untuk setiap restoran dengan ID 1, 2, dan 3. Pastikan ID tersebut benar-benar tersedia pada tabel `restaurant`.

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

## 6. Memeriksa Data Ulasan

Query berikut menampilkan seluruh ulasan beserta ID restoran yang terkait.

```sql
SELECT *
FROM review
ORDER BY restaurant, review_date;
```

## 7. Menemukan Restoran dengan Rata-Rata Rating Tertinggi

Query ini menghitung rata-rata rating setiap restoran, lalu menampilkan satu restoran dengan rata-rata tertinggi.

```sql
SELECT
    r.id,
    r.name,
    AVG(rv.rating) AS average_rating
FROM restaurant AS r
JOIN review AS rv
    ON r.id = rv.restaurant
GROUP BY r.id, r.name
ORDER BY average_rating DESC
LIMIT 1;
```

**Konsep yang digunakan:**
- `JOIN` menghubungkan data restoran dengan ulasannya.
- `AVG()` menghitung rata-rata rating.
- `GROUP BY` mengelompokkan ulasan berdasarkan restoran.
- `ORDER BY ... DESC` mengurutkan rata-rata dari tertinggi ke terendah.
- `LIMIT 1` menampilkan satu hasil teratas.

Jika beberapa restoran memiliki rata-rata tertinggi yang sama, query ini hanya menampilkan satu baris. Untuk menampilkan semua restoran yang seri, diperlukan pendekatan query yang berbeda.

## 8. Menghitung Jumlah Ulasan Setiap Restoran

Query ini menghitung jumlah ulasan untuk setiap restoran, termasuk restoran yang belum memiliki ulasan.

```sql
SELECT
    r.id,
    r.name,
    COUNT(rv.id) AS total_reviews
FROM restaurant AS r
LEFT JOIN review AS rv
    ON r.id = rv.restaurant
GROUP BY r.id, r.name
ORDER BY r.id;
```

**Konsep yang digunakan:**
- `LEFT JOIN` memastikan semua restoran tetap muncul.
- `COUNT(rv.id)` menghitung ulasan yang cocok. Restoran tanpa ulasan akan memiliki nilai `0`.
- `GROUP BY` mengelompokkan hasil per restoran.

## 9. Menampilkan Ulasan Terbaru Setiap Restoran

### Opsi A — Menggunakan `DISTINCT ON` (khusus PostgreSQL)

```sql
SELECT DISTINCT ON (r.id)
    r.name AS restaurant_name,
    rv.user_name,
    rv.rating,
    rv.review_text,
    rv.review_date
FROM restaurant AS r
JOIN review AS rv
    ON r.id = rv.restaurant
ORDER BY r.id, rv.review_date DESC, rv.id DESC;
```

`DISTINCT ON (r.id)` memilih satu ulasan untuk setiap restoran. Pengurutan `review_date DESC` memprioritaskan ulasan terbaru, sedangkan `rv.id DESC` menjadi pemecah seri jika tanggal ulasan sama.

### Opsi B — Menggunakan subquery

```sql
SELECT
    r.name AS restaurant_name,
    rv.user_name,
    rv.rating,
    rv.review_text,
    rv.review_date
FROM restaurant AS r
JOIN review AS rv
    ON r.id = rv.restaurant
WHERE rv.review_date = (
    SELECT MAX(rv2.review_date)
    FROM review AS rv2
    WHERE rv2.restaurant = r.id
)
ORDER BY r.id;
```

Subquery dengan `MAX()` mencari tanggal ulasan paling baru untuk setiap restoran. Jika ada beberapa ulasan dengan tanggal terbaru yang sama, query ini akan menampilkan semuanya.

## 10. Membuat Tabel `menu`

Tabel `menu` menyimpan daftar menu yang tersedia pada setiap restoran beserta harganya.

```sql
CREATE TABLE menu (
    id SERIAL PRIMARY KEY,
    restaurant_id INTEGER NOT NULL,
    menu_name VARCHAR(100) NOT NULL,
    price NUMERIC(10, 2) NOT NULL,
    CONSTRAINT fk_menu_restaurant
        FOREIGN KEY (restaurant_id)
        REFERENCES restaurant(id)
        ON DELETE CASCADE
);
```

**Keterangan:**
- `id` merupakan ID menu yang bertambah secara otomatis.
- `restaurant_id` menghubungkan menu dengan restoran.
- `menu_name` menyimpan nama menu.
- `price` menyimpan harga menu dengan dua angka desimal.
- `ON DELETE CASCADE` menghapus menu terkait ketika restoran induknya dihapus.

## 11. Menambahkan 3 Menu untuk Masing-Masing Restoran

Query berikut menambahkan sembilan data menu: tiga menu untuk setiap restoran dengan ID 1, 2, dan 3.

```sql
INSERT INTO menu (restaurant_id, menu_name, price)
VALUES
    (1, 'Nasi Goreng', 25000),
    (1, 'Mie Goreng', 22000),
    (1, 'Es Teh', 5000),

    (2, 'Ayam Bakar', 30000),
    (2, 'Soto Ayam', 20000),
    (2, 'Jus Jeruk', 10000),

    (3, 'Burger', 28000),
    (3, 'Kentang Goreng', 15000),
    (3, 'Milkshake', 18000);
```

> Harga di atas merupakan contoh data latihan, bukan harga aktual restoran.

## 12. Memeriksa Data Menu

Untuk melihat seluruh menu yang tersimpan:

```sql
SELECT *
FROM menu
ORDER BY restaurant_id, id;
```

Untuk menampilkan nama restoran, nama menu, dan harga dalam satu hasil:

```sql
SELECT
    r.name AS restaurant_name,
    m.menu_name,
    m.price
FROM restaurant AS r
JOIN menu AS m
    ON r.id = m.restaurant_id
ORDER BY r.id, m.id;
```

## Catatan Pelaksanaan

1. Jalankan perintah pembuatan tabel hanya jika tabel tersebut belum tersedia.
2. Pastikan ID restoran 1, 2, dan 3 ada sebelum menambahkan data ulasan atau menu. Nilai ID otomatis dapat berbeda jika tabel sebelumnya sudah berisi data.
3. Contoh data ulasan dan menu di dokumen ini dapat disesuaikan dengan data yang digunakan dalam latihan.
4. `SERIAL` tersedia pada PostgreSQL. Jika menggunakan DBMS lain, sintaks untuk ID otomatis dapat berbeda.
5. Urutan pelaksanaan yang disarankan adalah: membuat tabel `restaurant` → memasukkan data restoran → membuat tabel `review` → memasukkan ulasan → menjalankan query analisis → membuat tabel `menu` → memasukkan dan memeriksa menu.
