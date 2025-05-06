# KQL (Kusto Query Language) Nedir?
## Tanım ve Temel Özellikler
KQL (Kusto Query Language), Microsoft'un geliştirdiği gerçek zamanlı veri analizi için optimize edilmiş bir sorgu dilidir. Özellikle büyük ölçekli log ve zaman serisi verileriyle çalışmak üzere tasarlanmıştır.

## Ne İçin Kullanılır?
### Log Analizi:

Örnek: Sunucu hatalarını tespit etme
```
kql
EventLogs 
| where Level == "Error" 
| summarize count() by Source
```
### Zaman Serisi Analizi:

Örnek: 5 dakikalık ortalama CPU kullanımı
```
kql
PerfData 
| where Time > ago(1h) 
| summarize avg(CPU) by bin(Time, 5m)
```

### Anomali Tespiti:
```
kql
NetworkTraffic 
| where BytesSent > (3 * stdev(BytesSent))
```

## Diğer Dillerden Farkları
Özellik	KQL	SQL	Python (Pandas)
Optimizasyon	Gerçek zamanlı	Transactionel	Genel amaçlı
Sözdizimi	Pipe-based (`	`)	Tabular	Fonksiyonel
Zaman Serisi	Yerleşik destek	Sınırlı	Kütüphanelerle
Ölçeklenme	PB ölçeğinde	TB ölçeğinde	Bellek sınırlı

## Ne Zaman Kullanılır?
### ✔ Azure Veri Hizmetleri ile çalışırken:

Azure Data Explorer

Microsoft Fabric EventHouse

Azure Monitor Logs

### ✔ Performans avantajı gerektiren durumlarda:

Saniyede milyonlarca kayıt işleme

Gerçek zamanlı (sub-second) sorgular

### ✔ Zaman serisi odaklı analizlerde:

IoT sensör verileri

Finansal piyasa verileri

Kullanıcı etkinlik logları

## Güçlü Yönleri
```
kql
// Örnek: Çoklu veri kaynağı birleştirme
let Errors = EventLogs | where Level == "Error";
let Traces = TraceLogs | where Message contains "timeout";
union Errors, Traces
| take 100
```

## Avantajlar:

Hız: Sorgular genellikle <1 saniyede tamamlanır

Esneklik: JSON gibi karmaşık veri yapılarını işleyebilir

Yerleşik ML: series_decompose_anomalies gibi fonksiyonlar

## Sınırlamalar
⚠ Transaction desteği yok: Veri modifikasyonu için uygun değil
⚠ JOIN kısıtlamaları: Büyük tablolarda performans sorunları
⚠ Öğrenme eğrisi: SQL'den farklı pipe-based syntax

## Gerçek Dünya Senaryoları
### Siber Güvenlik:
```
kql
SigninLogs 
| where ResultType == "50057" // Hesapları devre dışı bırakma
| summarize Attempts=count() by UserPrincipalName
| top 10 by Attempts
```

### E-Ticaret Analitiği:
```
kql
PageViews 
| where Timestamp > ago(7d)
| summarize Users=dcount(UserId) by PageName
| order by Users desc
```

### Makine Öğrenmesi Öncesi Veri Hazırlık:
```
kql
SensorReadings
| where not(isnull(Value))
| summarize avg(Value) by DeviceId, bin(Timestamp, 1h)
```

KQL özellikle Azure ekosisteminde ve büyük ölçekli log analizlerinde SQL'den çok daha verimli çalışır. Ancak karmaşık transaction işlemleri için SQL, veri bilimi projeleri için ise Python daha uygundur.