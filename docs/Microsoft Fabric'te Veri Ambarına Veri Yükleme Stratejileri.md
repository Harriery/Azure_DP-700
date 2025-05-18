🧠 1. Microsoft Fabric'te Veri Ambarına Veri Yükleme Stratejileri (Genel Mantık)
🔍 Neden veri ambarına veri yüklenir?
İşletmeler, birçok farklı kaynaktan (Excel, veritabanı, API vs.) veri toplar.

Bu veriler hamdır, dağınıktır. Raporlama ve analiz için anlamlı hale getirilmesi gerekir.

Veri ambarı (Data Warehouse), bu verileri merkezi, temiz ve analiz edilebilir şekilde sakladığımız yerdir.

🛠️ Stratejiler nelerdir?
Batch yükleme: Belirli aralıklarla toplu veri yüklemesi (örneğin: her gece saat 2’de).

Streaming yükleme: Gerçek zamanlı veri akışı (örneğin: canlı sensör verisi).

Incremental (artımlı) yükleme: Sadece yeni ya da değişen verileri yükler.

Full yükleme: Tüm veriyi sıfırdan her defasında yükler.

🧭 2. Microsoft Fabric’te Veri Hattı (Data Pipeline) Oluşturmak
🧠 Mantığı nedir?
Veri hattı (pipeline), verinin nereden alınıp, nasıl dönüştürülüp, nereye aktarılacağını tanımlar.

Bir çeşit üretim bandı gibi düşünebilirsin. Girdiyi alır, işler ve çıktıyı verir.

🔄 İşlem Akışı:
markdown
Kopyala
Düzenle
1. Veri kaynağını seç (örneğin bir Excel dosyası)
2. Veriyi oku
3. Gerekirse dönüştür (temizle, format değiştir vs.)
4. Hedefi seç (örneğin: Fabric Warehouse)
5. Yüklemeyi başlat (manuel ya da zamanlayıcıyla otomatik)
🧩 Microsoft Fabric’te nerede yapılır?
Data pipelines > New pipeline

Azure Data Factory’deki pipeline mantığına çok benzer ama Fabric içinde entegredir.

💻 3. T-SQL Kullanarak Veri Ambarına Veri Yüklemek
🧠 Mantığı nedir?
T-SQL, SQL Server’a özel bir sorgulama dilidir.

Veri ambarına doğrudan veri yazmak için kullanılır.

Kod yazarak veri ekleme, güncelleme, silme işlemleri yapılır.

💡 Ne zaman kullanılır?
Veriyi manuel yüklüyorsan

Dönüşüm yapmak için kod yazman gerekiyorsa (örneğin: “Yalnızca 2024’teki satışları ekle”)

🔄 İşlem Akışı:
sql
Kopyala
Düzenle
1. Bir Warehouse aç (Fabric içinde)
2. SQL Endpoint’e bağlan (Not: Fabric Warehouse bir SQL bağlantı noktası sunar)
3. “INSERT INTO tablom (...) VALUES (...)” gibi T-SQL komutlarıyla veri ekle
4. Gerekirse “UPDATE”, “DELETE”, “MERGE” gibi komutlarla düzenleme yap
🔧 Microsoft Fabric’te nerede yapılır?
Warehouse > Open SQL Endpoint > New SQL Query

Azure Synapse veya SQL Server’daki gibi doğrudan kodla müdahale imkânı sağlar.

🔄 4. Dataflow Gen2 ile Veri Yükleme ve Dönüştürme
🧠 Mantığı nedir?
Teknik bilgin olmadan, görsel bir arayüzle veri dönüştürmeni sağlar.

Power BI’daki "Power Query" editörünün gelişmiş hali gibidir.

Kaynaktan veriyi al, temizle, dönüştür, warehouse’a gönder.

🧰 Ne işe yarar?
Kod yazmadan “veriyi hazır hale getirme” işini yapar.

Power Query bilen biri için kolay geçiştir.

🔄 İşlem Akışı:
markdown
Kopyala
Düzenle
1. Dataflow Gen2 oluştur
2. Veri kaynağını bağla (Excel, SQL, Web API vs.)
3. Query editor’da verileri temizle (örneğin: tarihleri düzelt, boşları kaldır)
4. Hedef olarak Fabric Warehouse’u seç
5. Veri akışını çalıştır veya zamanla
📍 Microsoft Fabric’te nerede yapılır?
Workspace > Dataflows Gen2 > New Dataflow

Azure Data Factory'deki “Mapping Data Flow”'a benzese de Power Query temelli olduğu için daha kullanıcı dostudur.

📌 Özet: Konuların Birbiriyle İlişkisi
| Konu                      | Ne yapar?                                     | Kod Gerekir mi?     | Nerede yapılır?          |
| ------------------------- | --------------------------------------------- | ------------------- | ------------------------ |
| Veri yükleme stratejileri | Nasıl ve ne zaman veri yükleyeceğini belirler | Hayır               | Genel planlama           |
| Veri hattı (Pipeline)     | Kaynaktan hedefe veri taşır                   | Hayır/Kod opsiyonel | Fabric > Data pipeline   |
| T-SQL ile yükleme         | SQL diliyle doğrudan veri ekler               | Evet                | Fabric > Warehouse > SQL |
| Dataflow Gen2             | Kod yazmadan veri dönüştürür                  | Hayır               | Fabric > Dataflows Gen2  |

