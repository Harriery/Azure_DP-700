# Microsoft Fabric Veri Güvenliği Laboratuvarı
Bu laboratuvar, Microsoft Fabric'te veri güvenliği mekanizmalarını uygulamayı ve test etmeyi öğretir. Aşağıda öğrendiklerinizin detaylı bir özeti ve adım adım uygulama kılavuzu bulunmaktadır.

# 📌 Öğrenilen Kavramlar ve Amaçları
## 1. Dinamik Veri Maskeleme (Dynamic Data Masking)
Ne?: Hassas verilerin yetkisiz kullanıcılar tarafından görüntülenmesini kısmen veya tamamen engeller.
### Neden?: 
GDPR, HIPAA gibi veri gizliliği düzenlemelerine uyum sağlamak için.
### Ne Zaman?: 
Müşteri e-postaları, telefon numaraları gibi PII (Kişisel Tanımlanabilir Bilgiler) içeren sütunlarda.
### Nasıl?: 
MASKED WITH ifadesiyle sütun tanımlanır.

## 2. Satır Düzeyinde Güvenlik (Row-Level Security - RLS)
### Ne?:
 Kullanıcıların tablodaki yalnızca belirli satırları görmesini sağlar.
### Neden?:
 Departmanların yalnızca kendi verilerini görmesi gerektiğinde.
### Ne Zaman?:
 Çok kiracılı sistemlerde veya bölümlere göre veri erişimi kısıtlaması gerektiğinde.
### Nasıl?:
 Güvenlik politikaları (SECURITY POLICY) ve predicate fonksiyonlarıyla.

## 3. Sütun Düzeyinde Güvenlik (Column-Level Security)
### Ne?: 
Belirli sütunlara erişimi kısıtlar.
### Neden?: 
Kredi kartı numarası gibi kritik verilerin yetkisiz erişime karşı korunması.
### Ne Zaman?:
 Hassas sütunların yalnızca yetkili personel tarafından görülmesi gerektiğinde.
### Nasıl?:
 GRANT/DENY SELECT ON [tablo](sütun) komutlarıyla.

## 4. SQL Ayrıntılı İzinler (GRANT/DENY)
### Ne?: 
Nesne düzeyinde izin yönetimi.
### Neden?:
 Kullanıcıların belirli tablo veya prosedürleri kullanabilmesi için.
### Ne Zaman?:
 Rol bazlı erişim kontrolü (RBAC) uygulamak istediğinizde.
### Nasıl?: 
GRANT EXECUTE, DENY SELECT gibi T-SQL komutlarıyla.

# 🛠️ Adım Adım Laboratuvar Kılavuzu
## 1. Ortam Hazırlığı
sql
### 1. Çalışma Alanı Oluşturma (Fabric Portalı)
-- - https://app.fabric.microsoft.com adresine gidin
-- - Sol menüden "Çalışma Alanları" > "Yeni Çalışma Alanı"
-- - Kapasite olarak "Fabric (Trial)" seçin

### 2. Test Kullanıcıları Ekleme (Workspace Admin tarafından)
-- - Çalışma alanı ayarları > "Manage access"
-- - "Viewer" rolüne testuser@firma.com ekleyin
## 2. Veri Ambarı ve Tablo Oluşturma
sql
### 1. Warehouse oluşturma
-- - Sol menüden "Warehouse" seçeneği ile yeni ambar oluşturun

### 2. Customers tablosu ile DDM uygulama
```
CREATE TABLE dbo.Customers
(   
    CustomerID INT NOT NULL,   
    FirstName varchar(50) MASKED WITH (FUNCTION = 'partial(1,"XXXXXXX",0)') NULL,     
    LastName varchar(50) NOT NULL,     
    Phone varchar(20) MASKED WITH (FUNCTION = 'default()') NULL,     
    Email varchar(50) MASKED WITH (FUNCTION = 'email()') NULL   
);
   
INSERT dbo.Customers VALUES
(1,'Catherine','Abel','555-555-5555','catherine@firma.com'),
(2,'Kim','Abercrombie','444-444-4444','kim@firma.com');
```

#### Admin olarak verileri görüntüleme (maskeleme yok)
```
SELECT * FROM dbo.Customers;
```
### 3. Dinamik Maskeleme Testi
sql
#### 1. Test kullanıcısı olarak bağlanın (Switch User veya yeni oturum)
#### 2. Maskelenmiş veriyi görüntüleme
```
SELECT * FROM dbo.Customers;
-- Çıktı: FirstName: CXXXXXXX, Phone: xxx-xxx-5555, Email: cXXX@XXX.com
```
#### 3. Admin olarak UNMASK izni verme
```
GRANT UNMASK ON dbo.Customers TO [testuser@firma.com];
```

#### 4. Test kullanıcısı tekrar sorguladığında artık maskesiz veri görecek
### 4. Satır Düzeyinde Güvenlik (RLS)
sql
#### 1. Sales tablosu oluşturma
```
CREATE TABLE dbo.Sales  
(  
    OrderID INT,  
    SalesRep VARCHAR(60),  
    Product VARCHAR(10),  
    Quantity INT  
);
    
INSERT dbo.Sales VALUES
(1, 'user1@firma.com', 'Valve', 5),   
(2, 'user2@firma.com', 'Wheel', 2);
```

#### 2. RLS fonksiyonu ve politikası oluşturma
```
CREATE SCHEMA rls;
GO

CREATE FUNCTION rls.fn_securitypredicate(@SalesRep AS VARCHAR(60))
    RETURNS TABLE  
WITH SCHEMABINDING  
AS  
    RETURN SELECT 1 WHERE @SalesRep = USER_NAME();
GO   

CREATE SECURITY POLICY SalesFilter  
ADD FILTER PREDICATE rls.fn_securitypredicate(SalesRep) ON dbo.Sales  
WITH (STATE = ON);
GO
```
#### 3. Test kullanıcısı (user1@firma.com) olarak bağlanıp test etme
```
SELECT * FROM dbo.Sales; -- Yalnızca kendi satırlarını görecek
```
### 5. Sütun Düzeyinde Güvenlik
sql

#### 1. Orders tablosu oluşturma
```
CREATE TABLE dbo.Orders
(   
    OrderID INT,   
    CustomerID INT,  
    CreditCard VARCHAR(20)      
);   
INSERT dbo.Orders VALUES
(1234, 5678, '1111-2222-3333-4444');
```

#### 2. CreditCard sütununa erişimi engelleme
```
DENY SELECT ON dbo.Orders (CreditCard) TO [testuser@firma.com];
```
#### 3. Test kullanıcısı olarak:
```
SELECT * FROM dbo.Orders; -- Hata verecek
SELECT OrderID, CustomerID FROM dbo.Orders; -- Çalışacak
```
### 6. SQL Ayrıntılı İzinler
sql
#### 1. Prosedür ve tablo oluşturma
```
CREATE PROCEDURE dbo.sp_GetParts
AS
SELECT PartID, PartName FROM dbo.Parts;
GO

CREATE TABLE dbo.Parts
(
    PartID INT,
    PartName VARCHAR(25)
);
INSERT dbo.Parts VALUES (1, 'Wheel');
```

#### 2. İzinleri yapılandırma
```
GRANT EXECUTE ON dbo.sp_GetParts TO [testuser@firma.com];
DENY SELECT ON dbo.Parts TO [testuser@firma.com];
```

#### 3. Test kullanıcısı:
```
EXEC dbo.sp_GetParts; -- Çalışacak
SELECT * FROM dbo.Parts; -- Hata verecek
```

## 🧹 Temizlik
sql
### Tüm nesneleri silmek için:
```
DROP SECURITY POLICY SalesFilter;
DROP FUNCTION rls.fn_securitypredicate;
DROP SCHEMA rls;
DROP TABLE dbo.Customers, dbo.Sales, dbo.Orders, dbo.Parts;
DROP PROCEDURE dbo.sp_GetParts;
```

### Çalışma alanını silmek için:
-- Fabric Portal > Çalışma Alanı Ayarları > "Bu çalışma alanını kaldır"
## 📝 Önemli Notlar
### Test Kullanıcısı olarak bağlanmak için:

Tarayıcıda yeni gizli pencere açın veya "Switch User" özelliğini kullanın

Power BI Desktop'ta farklı kimlik bilgileriyle oturum açın

### Roller ve Etkileri:

#### Workspace Admin: Tüm verileri maskesiz görür, tüm güvenlik ayarlarını yapabilir

#### Workspace Viewer: DDM/RLS kurallarına tabidir, nesne oluşturamaz

### Performans Etkisi:

RLS ve DDM'nin sorgu performansına minimal etkisi vardır

Karmaşık predicate fonksiyonlarından kaçının

# 🔒 Güvenlik Terimleri - Kısa Açıklamalar
## 1. GDPR (Genel Veri Koruma Yönetmeliği)
Avrupa Birliği'nin veri gizliliği yasasıdır. Kişisel verilerin korunmasını ve işlenme kurallarını düzenler. Fabric'te DDM ve RLS ile uyum sağlanabilir.

## 2. HIPAA (Sağlık Sigortası Taşınabilirlik ve Sorumluluk Yasası)
ABD'de sağlık verilerinin korunmasını zorunlu kılan yasa. Sütun düzeyinde güvenlik ve maskeleme kritiktir.

## 3. RLS (Row-Level Security - Satır Düzeyinde Güvenlik)
Kullanıcıların bir tablodaki yalnızca kendilerine ait satırları görmesini sağlar. Örneğin, satış temsilcilerinin sadece kendi müşterilerini görmesi.

## 4. DDM (Dynamic Data Masking - Dinamik Veri Maskeleme)
Hassas verileri (TCKN, kredi kartı vb.) otomatik olarak kısmen gizler. Örn: 5123-XXXX-XXXX-9012.

## Diğer Önemli Terimler:

### PII (Personally Identifiable Information):
 Kişisel tanımlanabilir bilgiler (adres, telefon vb.).

### RBAC (Role-Based Access Control):
 Kullanıcı rollerine göre izin yönetimi.

### SOC 2:
 Bulut hizmetlerinde veri güvenliği standardı.

### Data Sovereignty:
 Verinin fiziksel olarak belirli bir ülkede saklanması zorunluluğu.

## 📌 Fabric'te Nasıl Uygulanır?
### GDPR/HIPAA için:
 DDM + RLS + Sütun izinleri birlikte kullanılır.

### SOC 2 uyumu için:
 Tüm erişim logları ve izin politikaları dokümante edilmelidir.