[README1.md](https://github.com/user-attachments/files/26564902/README1.md)
# 📦 Dokumentasi Database: Toko Elektronik

## 🗂️ 1. Membuat Database
```sql
CREATE DATABASE toko_elektronik;
USE toko_elektronik;
```

---

## 🏗️ 2. Struktur Tabel

### 📌 Tabel: `produk`
Menyimpan data produk elektronik.

```sql
CREATE TABLE produk (
    id_produk INT AUTO_INCREMENT PRIMARY KEY,
    nama_produk VARCHAR(100),
    kategori VARCHAR(50),
    harga INT,
    stok INT
);
```

### 📌 Tabel: `transaksi`
Menyimpan data transaksi pembelian.

```sql
CREATE TABLE transaksi (
    id_transaksi INT AUTO_INCREMENT PRIMARY KEY,
    nama_pembeli VARCHAR(100),
    id_produk INT,
    jumlah INT,
    total_harga INT,
    tanggal DATE,
    FOREIGN KEY (id_produk) REFERENCES produk(id_produk)
);
```

### 📌 Tabel: `log_transaksi`
Menyimpan log aktivitas transaksi.

```sql
CREATE TABLE log_transaksi (
    id_log INT AUTO_INCREMENT PRIMARY KEY,
    keterangan VARCHAR(255),
    waktu TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

---

## ⚙️ 3. Trigger

### 🔹 BEFORE INSERT
Menghitung total harga otomatis sebelum data masuk.

```sql
CREATE TRIGGER trg_before_insert_transaksi
BEFORE INSERT ON transaksi
FOR EACH ROW
SET NEW.total_harga = NEW.jumlah *
    (SELECT harga FROM produk WHERE id_produk = NEW.id_produk);
```

---
<img width="300" height="250" alt="Before_Insert" src="https://github.com/user-attachments/assets/6f9c0da5-2c2a-4b01-949d-0806a83be17a" />

### 🔹 AFTER INSERT
Mencatat log setelah transaksi ditambahkan.

```sql
CREATE TRIGGER trg_after_insert_transaksi
AFTER INSERT ON transaksi
FOR EACH ROW
INSERT INTO log_transaksi(keterangan)
VALUES (CONCAT('Transaksi ditambahkan oleh ', NEW.nama_pembeli));
```

---
<img width="300" height="250" alt="after" src="https://github.com/user-attachments/assets/3e9c05ae-01ae-4ef5-9c87-8c22b031dd51" />

### 🔹 BEFORE UPDATE
Validasi agar jumlah tidak kurang dari 1.

```sql
CREATE TRIGGER trg_before_update_transaksi
BEFORE UPDATE ON transaksi
FOR EACH ROW
SET NEW.jumlah = IF(NEW.jumlah < 1, 1, NEW.jumlah);
```

---
<img width="300" height="250" alt="bu" src="https://github.com/user-attachments/assets/77743699-79a8-4773-888b-a87e079c389f" />

### 🔹 AFTER UPDATE
Mencatat log setelah update transaksi.

```sql
CREATE TRIGGER trg_after_update_transaksi
AFTER UPDATE ON transaksi
FOR EACH ROW
INSERT INTO log_transaksi(keterangan)
VALUES (CONCAT('Transaksi diupdate oleh ', NEW.nama_pembeli));
```

---
<img width="300" height="250" alt="au" src="https://github.com/user-attachments/assets/b22f02a2-e6c2-4971-a032-8de465e89e51" />

### 🔹 BEFORE DELETE
Mencatat log sebelum data dihapus.

```sql
CREATE TRIGGER trg_before_delete_transaksi
BEFORE DELETE ON transaksi
FOR EACH ROW
INSERT INTO log_transaksi(keterangan)
VALUES (CONCAT('Akan menghapus transaksi ID ', OLD.id_transaksi));
```

---
<img width="300" height="250" alt="bd" src="https://github.com/user-attachments/assets/38e8ea6c-1852-463e-85b8-349d222ee7d8" />

### 🔹 AFTER DELETE
Mencatat log setelah data berhasil dihapus.

```sql
CREATE TRIGGER trg_after_delete_transaksi
AFTER DELETE ON transaksi
FOR EACH ROW
INSERT INTO log_transaksi(keterangan)
VALUES (CONCAT('Transaksi berhasil dihapus ID ', OLD.id_transaksi));
```

---
<img width="300" height="250" alt="del aft" src="https://github.com/user-attachments/assets/bb44c7b7-b2d5-4799-b00c-320c793c8817" />


## 📥 4. Data Dummy

### Produk
```sql
INSERT INTO produk (nama_produk, kategori, harga, stok) VALUES
('Laptop Asus', 'Laptop', 8000000, 10),
('HP Samsung', 'Smartphone', 3000000, 20),
('TV LG', 'Televisi', 5000000, 5),
('Mouse Logitech', 'Aksesoris', 150000, 50),
('Keyboard Rexus', 'Aksesoris', 250000, 30);
```

### Transaksi
```sql
INSERT INTO transaksi (nama_pembeli, id_produk, jumlah, total_harga, tanggal) VALUES
('Andi', 3, 1, 8000000, '2026-04-01'),
('Budi', 4, 2, 6000000, '2026-04-02'),
('Citra', 5, 3, 450000, '2026-04-03');
```

---

## 👁️ 5. View

### 🔸 Produk Mahal
Menampilkan produk dengan harga > 3.000.000

```sql
CREATE VIEW view_produk_mahal AS
SELECT nama_produk, kategori, harga, stok
FROM produk
WHERE harga > 3000000;
```

```sql
SELECT * FROM view_produk_mahal;
```

---
<img width="300" height="250" alt="View_Produk_Mahal" src="https://github.com/user-attachments/assets/8b6b03b0-9daa-48fa-84c6-d44e57e21ab7" />

### 🔸 Produk Murah
Menampilkan produk dengan harga ≤ 3.000.000

```sql
CREATE VIEW view_produk_murah AS
SELECT nama_produk, kategori, harga, stok
FROM produk
WHERE harga <= 3000000;
```

```sql
SELECT * FROM view_produk_murah;
```

---
<img width="300" height="250" alt="View_Produk_Murah" src="https://github.com/user-attachments/assets/5667acef-4730-4468-9043-4e6f9f182a1b" />

## ✅ Kesimpulan
- Database terdiri dari **3 tabel utama**: produk, transaksi, dan log_transaksi
- Menggunakan **6 trigger** untuk otomatisasi dan logging
- Menggunakan **2 view** untuk mempermudah analisis data produk
- Data dummy disediakan untuk testing

---

💡 Cocok digunakan sebagai project pembelajaran **MySQL (DDL, DML, Trigger, View)**.


