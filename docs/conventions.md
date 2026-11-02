# Conventions

> Bootstrap 2026-11-02 ile dolduruldu. Kurallar **yeni ve değişen kod** için geçerli; eski kod ilgili
> spec'te dokunulduğunda kurala uydurulur, toplu temizlik yapılmaz.

## Language
- Chat language (interviews, sessions): Türkçe (`tr`)
- Document language (docs/, specs, plans, ADRs, review and verify reports): Türkçe (`tr`)
- Always English (protocol, not prose): ANEW core files, template headings and field labels,
  `Status:` values (Draft / Approved / In progress / Shipped), `Approved by / on:`, `Source:`.
- Code identifiers, branch names, commit messages: kod tanımlayıcıları ve dal adları İngilizce;
  commit mesajı Türkçe olabilir, Conventional Commits tipi İngilizce (`feat:`, `fix:` …).
  Kullanıcıya görünen metinler Türkçe.
- Agent messages (handoff summaries, refusals, `Next:` lines): chat language; command names, file
  paths and protocol fields verbatim.

## Language & framework versions
- Java: **JDK 21 (LTS)**, `--release 21`. Bu makinede: `C:\Users\oozkan\.jdks\dragonwell-ex-21.0.9`.
- HTTP: JDK `com.sun.net.httpserver` (framework yok).
- Build/test hedefi: **Maven Wrapper (`mvnw`) + JUnit Jupiter 5** — ilk spec kurar; o zamana kadar
  `javac`. Kaynak dizini `src/` kalır (`pom.xml` `sourceDirectory`); `src/main/java`'ya taşıma ayrı refactor spec'i.
- Loglama: `java.util.logging` (ek kütüphane yok).

## Naming
- Java standart isimlendirmesi; dosya başına bir public tip; paketler `leavemanager.<katman>`.
- Durum değerleri `LeaveStatus` enum'unda; kalıcı değer ASCII (`Onaylandi`, `Iptaledildi`).
  Durumlar string olarak karşılaştırılmaz.
- Testler: `<Sınıf>Test` (unit), `<Senaryo>HttpIT` (HTTP entegrasyon).

## Error handling
- Hatalar yutulmaz: boş `catch` ve "loglanacak" yorumu yasak.
- İş kuralı ihlali (bakiye yetersiz, personel yok, geçersiz tarih aralığı) `boolean` dönmez — tipli
  sonuç veya domain exception.
- Web katmanı kullanıcıya anlaşılır Türkçe mesaj gösterir, ayrıntıyı JUL ile loglar; stack trace ve
  kişisel veri kullanıcıya gösterilmez.

## Data rules
- Tarih `LocalDate`; saat dilimi `Europe/Istanbul`; "bugün" `Clock` üzerinden alınır.
- Gün sayısı `int`; aralıklar iki uç dahil — bu kural tek bir metotta yaşar.
- İzin hakkı ve kıdem tabloları tek yerde, adı konmuş sabitler olarak.
- Loglarda kişiler yalnız id ile anılır (KVKK); ad ve izin nedeni loga yazılmaz.

## Known traps
- Türkçe locale'de `toLowerCase()`/`toUpperCase()` (`"I"` → `"ı"`) — `Locale.ROOT` kullan ya da enum karşılaştır — from bootstrap
- `LocalDate.now()` güne ve saat dilimine bağlı — `Clock` enjekte et — from bootstrap
- `ChronoUnit.DAYS.between` bitiş gününü saymaz — iki uç dahil hesabı tek metoda bırak — from bootstrap
- 29 Şubat işe girişte yıldönümü hesabı — sınır değer testi yaz — from bootstrap
- `HttpServer.stop()` çağrılmazsa port açık kalır — testte port 0, `finally` içinde `stop(0)` — from bootstrap
- Windows'ta `PATH`'teki `java` (1.8) ≠ `JAVA_HOME` (21 olmalı) — derleme/test hep `$JAVA_HOME/bin` ile — from bootstrap

## Enforced by tooling
- `javac --release 21 -Xlint:all -Werror` (check `build` adımı): derleme hataları ve tüm lint uyarıları.
- Satır sonları `.gitattributes` ile LF.
