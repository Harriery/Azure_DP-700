# 🔁 Medallion Mimarisi ve Süreç Akışı (Genel Bakış)
Microsoft Fabric + Medallion mimarisi ile çalışırken:

## Bronze Katman (Ham veri)
Kaynaktan alınan ham veriler burada tutulur. Minimum işlem yapılır. Delta formatta tutulur.
➤ Kullanım amacı: Veriyi değiştirmeden arşivlemek, izlenebilirlik sağlamak.

## Silver Katman (Temizlenmiş veri)
Bronze verilerden gelen veriler temizlenir, dönüştürülür (örneğin tarih formatı düzeltilir).
➤ Kullanım amacı: Analiz edilebilir duruma getirmek.

## Gold Katman (Raporlama katmanı)
Silver'dan gelen veriyle ölçüm tabloları (fact) ve boyut tabloları (dimension) hazırlanır.
➤ Kullanım amacı: Power BI veya başka BI araçlarında doğrudan kullanılabilir hale getirmek.


### Bu ayrım sayesinde:

Veri kalitesi kontrol edilir

Her adımda log tutulabilir

Yeniden işleme kolaylaşır



## 💾 Delta Lake ve Delta Tablolar

**Delta Lake**, Apache Spark tabanlı bir "veri gölü" (data lake) formatıdır. Avantajları:

ACID işlemleri destekler (güvenli veri işleme)

Versiyonlama sağlar (time travel)

Efficient upsert/merge işlemleri

Schema enforcement & evolution destekler

**Delta Table** Ne Zaman Kullanılır?

Veri güncellenebilir, değişebilir durumdaysa (Silver ve Gold katmanlar)

Veri analizi ve raporlama yapılacaksa

Incremental işlem yapılacaksa (örn. sadece yeni gelen veriyi ekleme


🛠️ Tablo Oluşturma Süreci ve DataFrame Kullanımı

💡 DataFrame Nedir?

PySpark veya Fabric notebook'larında veri üzerinde işlem yapmak için kullanılan ana yapıdır.

SQL gibi düşünebilirsin ama Python üzerinden veri ile çalışmanı sağlar.

## ⚙️ Delta Tablo Oluşturma Aşamaları:

### DataFrame oluştur:
```
from pyspark.sql.functions import *
df = spark.read.format("csv").option("header", True).load("/path/to/file.csv")
```

### Temizlik, dönüşüm işlemleri:
```
df_clean = df.withColumn("date", to_date("date", "yyyy-MM-dd"))
```

### Delta tablo olarak kaydet:
```
df_clean.write.format("delta").mode("overwrite").saveAsTable("bronze.orders")
```
**Not:** Delta tabloyu doğrudan CREATE TABLE SQL komutuyla da oluşturabilirsin ama genelde ilk DataFrame ile başlamak daha pratiktir.

# 🧩 Temel Kavramlar ve Ne Zaman Kullanılır?
Kavram	Ne işe yarar?	Ne zaman kullanılır?
#### Delta tablo	Veri üzerinde versiyonlama, güncelleme, silme, sorgulama sağlar.	Tüm katmanlarda veri tutmak için.
#### DataFrame	Bellekte geçici veri yapısıdır.	Veriyi işlerken, dönüştürürken veya tabloya yazmadan önce.
#### Dimension tablo	Referans verisi: müşteri, ürün, tarih gibi.	Gold katmanda.
#### Fact tablo	Ölçüm verileri: satışlar, miktarlar vb.	Gold katmanda.
#### Schema / Sütun tipi belirleme	Veriyi kontrol altında tutmak için.	Veri yazarken, okurken.
#### CREATE TABLE / MERGE INTO	Delta tablo oluşturur ya da günceller.	Başlangıçta veya veri güncellemede.

# 🔧 Adım Adım Süreç ve Kod Açıklamaları
## 🥉 1. Bronze Katman – Ham Veriyi Delta Formatında Kaydet
📌 Amaç:
Ham veriyi bozmadan saklamak. Gelecekte sorun çıkarsa geri dönebilmek.

**Format:** CSV, JSON, Parquet

### İşlemler:

**Kaynaktan veri okuma**

**Temel schema belirleme**

**Ham veri saklama (hiçbir temizlik yok)**


### 📍 Adımlar:
```
python

bronze_df = spark.read.csv('/path/to/raw.csv', header=True, inferSchema=True)
```
#### ✅ DataFrame oluşturduk. Çünkü gelen veriyi işlememiz gerekiyor.

```
python
bronze_df.write.format("delta").mode("overwrite").save("/lakehouse/bronze/raw_table")
```
#### ✅ Delta tabloya yazıyoruz. Bu bir fiziksel tablodur ama henüz metastore’da tanımlı değil.

```
sql
-- Spark SQL ile tabloyu görünür yapmak
CREATE TABLE IF NOT EXISTS bronze_raw
USING DELTA
LOCATION '/lakehouse/bronze/raw_table';
```
✅ CREATE TABLE komutu ile artık Fabric veya Not Defteri içinden bu tabloyu sorgulayabiliriz.


## 🥈 2. Silver Katman – Temizleme, Dönüştürme
📌 Amaç:
Eksik veriyi sil, gereksiz kolonları at tarihleri düzenle, bozuk kayıtları çıkar veri türlerini dönüştür, tipleri düzelt.Null değerleri temizle Artık analiz yapılabilir bir veri elde et.


```
python
silver_df = bronze_df.dropna() \
    .withColumn("tarih", to_date(col("tarih"), "yyyy-MM-dd"))
```
#### ✅ dropna() ile boş kayıtlar gitti.

#### ✅ withColumn ile tarih formatı düzeldi. Şimdi veri “temiz”.

```
python
silver_df.write.format("delta").mode("overwrite").save("/lakehouse/silver/clean_table")
```
#### ✅ Temizlenmiş veriyi yeni bir Delta tabloya kaydettik.

```
sql
CREATE TABLE IF NOT EXISTS silver_clean
USING DELTA
LOCATION '/lakehouse/silver/clean_table';
```


## 🥇 3. Gold Katman – Dimension & Fact Tablolar
📌 Amaç:
Power BI raporları için uygun modeller oluşturmak.

### İşlemler:

**Join işlemleri (fact + dimension)**

**Hesaplamalar (örn. total sales)**

**Business logiğe uygun modelleme**

### 🎯 Dimension Tablo Örneği:
```
python
dim_customer_df = silver_df.select("customer_id", "customer_name").dropDuplicates()
```

#### ✅ select ve dropDuplicates ile boyut tablosunu oluşturduk.

```
python
dim_customer_df.write.format("delta").mode("overwrite").save("/lakehouse/gold/dim_customer")
```
```
sql
CREATE TABLE IF NOT EXISTS dim_customer
USING DELTA
LOCATION '/lakehouse/gold/dim_customer';
```

### Fact ve Dimension Tablolar

#### Dimension

Sabit bilgiler (müşteri, ürün)

#### Fact

Olay/veri (sipariş, satış)

### Ne Zaman Kullanılır?

Gold katmanda veri modelleme yaparken

Power BI gibi araçlara bağlanmadan önce



### 📊 Fact Tablo Örneği:

```
python
fact_sales_df = silver_df.groupBy("customer_id").agg(sum("sales").alias("total_sales"))
```
#### ✅ Analize uygun ölçüm (fact) verisi: toplam satışlar.

```
python
fact_sales_df.write.format("delta").mode("overwrite").save("/lakehouse/gold/fact_sales")
```

```
sql
CREATE TABLE IF NOT EXISTS fact_sales
USING DELTA
LOCATION '/lakehouse/gold/fact_sales';
```

### 🔁 Delta Güncelleme (Opsiyonel)
Eğer tabloya her seferinde veri eklemek yerine “güncelleme” yapmak istiyorsan:

```
python
from delta.tables import DeltaTable

deltaTable = DeltaTable.forPath(spark, "/lakehouse/silver/clean_table")

deltaTable.alias("target").merge(
    source=silver_df.alias("source"),
    condition="target.id = source.id"
).whenMatchedUpdateAll().whenNotMatchedInsertAll().execute()
```

#### ✅ Bu kodla mevcut tabloya yeni veriler eklenir veya var olanlar güncellenir.


## 🧠 Zihinsel Yol Haritası: “Ne zaman ne yapmalıyım?”
## Süreç	Ne yapılmalı?	Hangi araç/kod?

**Veri geldiğinde	DataFrame ile oku	spark.read.**()

**Ham saklama	Delta tabloya yaz	.write.format("delta")...**

**Görünür hale getirme	SQL ile tabloyu CREATE ET	CREATE TABLE ... LOCATION ...**

**Veri temizleme	DataFrame işlemleri (dropna, withColumn)**	
**df.dropna().withColumn(...)**

**Temiz veri saklama	Yeni Delta tabloya yaz	.write.format("delta")...**

**Rapor için hazırlık	Dimension & Fact tablolar oluştur	select, groupBy, agg**

**Güncelleme gerekiyorsa	MERGE INTO işlemi	DeltaTable.merge(...)**

## 🧩 Ekstra Bilgiler
### DataFrame:
 Kod içinde veriyi işlemenin temel yapısıdır. Tablolara veri yazmadan önce her zaman bir DataFrame üzerinde çalışırız.

### Delta tablo:
 Kalıcı, versiyonlu ve sorgulanabilir veri yapısıdır. Özellikle “veri ambarı” kurarken bu format tercih edilir.

### SQL ile CREATE TABLE:
 Tabloyu metastore’a kaydederek görsel araçlardan ulaşılabilir hale getiririz (örn. Power BI).

### DeltaTable.merge: 
Güncelleme (upsert) yapar. Her veri yeniden yazılmak zorunda kalmaz.