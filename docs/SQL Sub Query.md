# SQL'de Subquery ve Constraints Konuları
Merhaba! SQL'de subquery (alt sorgu) ve constraints (kısıtlamalar) konularını yeni başlayanlar için basitçe anlatmaya çalışayım.

## Subquery (Alt Sorgu)
Subquery, bir SQL sorgusu içinde başka bir SQL sorgusunun kullanılmasıdır. Temelde dıştaki ana sorgu çalışmadan önce içteki alt sorgu çalışır ve sonuç ana sorguya aktarılır.

## Temel Subquery Örnekleri

### WHERE ile kullanılan subquery:
```
sql
-- Maaşı ortalama maaştan yüksek olan çalışanları listele
SELECT ad, soyad, maas
FROM calisanlar
WHERE maas > (SELECT AVG(maas) FROM calisanlar);
```

```
sql
-- Satış yapan personellerin bilgilerini getir
SELECT ad, soyad
FROM personeller
WHERE personel_id IN (SELECT DISTINCT personel_id FROM satislar);
```

### FROM ile kullanılan subquery:
```
sql
SELECT ort.bolum_id, ort.ortalama_maas
FROM (SELECT bolum_id, AVG(maas) as ortalama_maas 
      FROM calisanlar 
      GROUP BY bolum_id) ort
WHERE ort.ortalama_maas > 5000;
SELECT ile kullanılan subquery:
```

```
sql
SELECT urun_adi, 
       (SELECT MAX(fiyat) FROM fiyat_gecmisi WHERE urun_id = u.urun_id) as max_fiyat
FROM urunler u;
```
### Constraints (Kısıtlamalar)
#### Constraints, veritabanı tablolarına uygulanan kurallardır. Veri bütünlüğünü sağlamak için kullanılırlar.



##  Temel Constraint Türleri

### PRIMARY KEY: Bir sütunu benzersiz ve boş olmayan değerlerle tanımlar**
```
sql
CREATE TABLE ogrenciler (
    ogrenci_id INT PRIMARY KEY,
    ad VARCHAR(50)
);
```


### FOREIGN KEY: İki tablo arasında ilişki kurar
```
sql
CREATE TABLE siparisler (
    siparis_id INT PRIMARY KEY,
    musteri_id INT,
    FOREIGN KEY (musteri_id) REFERENCES musteriler(musteri_id)
);
```


### NOT NULL: Sütunun boş olamayacağını belirtir
```
sql
CREATE TABLE personeller (
    personel_id INT,
    ad VARCHAR(50) NOT NULL
);
```


### UNIQUE: Sütundaki tüm değerlerin benzersiz olmasını sağlar
```
sql
CREATE TABLE kullanicilar (
    kullanici_id INT,
    email VARCHAR(100) UNIQUE
);
```

### CHECK: Sütuna girilecek değerler için koşul belirler
```
sql
CREATE TABLE urunler (
    urun_id INT,
    fiyat DECIMAL(10,2) CHECK (fiyat > 0)
);
```

### DEFAULT: Sütun için varsayılan değer atar
```
sql
CREATE TABLE kayitlar (
    kayit_id INT,
    kayit_tarihi DATE DEFAULT GETDATE()
);
```


## Constraint Örnekleri
```
sql
-- Tablo oluştururken constraint'lerle birlikte
CREATE TABLE ogrenciler (
    ogrenci_id INT PRIMARY KEY,
    tc_no VARCHAR(11) UNIQUE,
    ad VARCHAR(50) NOT NULL,
    soyad VARCHAR(50) NOT NULL,
    kayit_tarihi DATE DEFAULT GETDATE(),
    bolum_id INT FOREIGN KEY REFERENCES bolumler(bolum_id),
    not_ortalamasi DECIMAL(3,2) CHECK (not_ortalamasi BETWEEN 0 AND 4)
);
```

```
-- Var olan tabloya constraint ekleme
ALTER TABLE ogrenciler
ADD CONSTRAINT chk_not CHECK (not_ortalamasi >= 0 AND not_ortalamasi <= 4);
```

```
-- Constraint silme
ALTER TABLE ogrenciler
DROP CONSTRAINT chk_not;
```