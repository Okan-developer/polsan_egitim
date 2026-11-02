# Architecture

> Bootstrap 2026-11-02 ile dolduruldu. "Bugün" mevcut durumu, "Hedef" kararlaştırılan yapıyı anlatır.

## System overview
LeaveManager, şirket içi personel izin takip sistemidir: izin hakkı, kullanılan/kalan bakiye ve
izin talepleri. Tek JVM'de çalışan bir **monolit**; HTTP sunucusu JDK'nın `com.sun.net.httpserver`'ı,
arayüz sunucu tarafında üretilen düz HTML (JavaScript yok), veri bellekte (her açılışta seed).
**Bugün:** her şey `src/Main.java` içinde (veri, iş kuralı, HTTP, HTML). **Hedef:** aynı monolit,
paketlerle ayrılmış katmanlar — 228 satırlık bir uygulama için hexagonal/modüler monolit fazla tören.
Geçiş büyük bir yeniden yazım değil: mevcut kod refactor spec'leriyle adım adım taşınır, yeni kod
doğrudan hedef yapıya yazılır.

## Modules / components and ownership

| Module | Single responsibility | Owns |
|---|---|---|
| `leavemanager.domain` | Personel, talep, durum, izin hakkı/kıdem/gün/bakiye hesapları — saf Java | İş kuralları (BR-n) |
| `leavemanager.app` | Kullanım senaryoları (talep oluşturma, iptal …); bakiye kontrolünün **tek** yeri | Senaryo akışları |
| `leavemanager.store` | Depolama; bugün bellek, ileride veritabanı yalnız burada değişir | Personel ve talep verisi |
| `leavemanager.web` | HTTP handler'ları, form ayrıştırma, HTML üretimi ve escape | Sunum |

## Communication rules
- Doğrudan metot çağrısı: `web → app → domain`, `app → store`, `store → domain` (tipler).
- Olay/mesaj altyapısı yok ve gerekmiyor.
- Saat `java.time.Clock` olarak dışarıdan verilir (uygulama girişinde `Europe/Istanbul`).

## Forbidden dependencies (make them testable)
1. `leavemanager.domain` hiçbir `leavemanager.*` paketine, `com.sun.net.httpserver`'a ve I/O'ya
   (`java.io`, `java.net`, `java.nio`) bağımlı olamaz.
2. `leavemanager.web`, `leavemanager.store`'a doğrudan erişemez — yalnız `leavemanager.app` üzerinden.
3. Hiçbir paket `leavemanager.web`'e bağımlı olamaz.
4. `LocalDate.now()` / `Clock.systemDefaultZone()` yalnız uygulama girişinde (`main`) çağrılır.

## Deliberately out of scope
- Kalıcı veritabanı (2015'teki "Oracle'a geçiş" planı karar değildir).
- Kimlik doğrulama / yetkilendirme — bilinen açık, ayrı spec (B-9).
- Çoklu sunucu, ölçekleme, harici entegrasyon (bordro, LDAP).
