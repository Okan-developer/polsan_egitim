# Spec 0001 — Test altyapısı ve karakterizasyon testleri

- Status: Draft
- Mode: lite
- Plan: `specs/plans/0001-plan.md`
- Self-critique: pending (öz-eleştiri boşlukları spec onayıyla birlikte onaylanacak)

## Intent
LeaveManager'ın kodu sahipsiz ve hiç testi yok; izin hakkı ve bakiye gibi hassas kurallara yapılacak
her değişiklik kimsenin bilmediği bir davranışı bozabilir. Bu iş projeye tek komutla (`./scripts/check`)
çalışan bir test altyapısı kurar ve uygulamanın **bugünkü davranışını — hatalı kısımları dahil —**
testlerle sabitler. Başarı: BR-1…BR-9 kurallarının her biri ve ekranlarda gözlemlenen davranış en az
bir testle korunuyor; sonraki düzeltmeler (B-1…B-10) ve `Iptaledildi` (B-8) neyi değiştirdiklerini
görerek ilerleyebiliyor. Bu işte **hiçbir davranış düzeltilmez**; bilinen hatalar backlog maddesine
referansla olduğu gibi kayda geçer.

## Requirements
1. **R-1 Test altyapısı.** Testler `./scripts/check` içinde çalışır: hızlı profil (`--fast`) kural
   testlerini, tam profil ek olarak uçtan uca ekran testlerini çalıştırır. Bir test kırmızıysa check kırmızıdır.
   Hızlı/tam ayrımı test adlandırmasıyla (seçici) yapılır; yeni eklenen bir test mutlaka bir profilde çalışır.
2. **R-2 Kural karakterizasyonu.** İzin hakkı (BR-1), kıdem (BR-2), gün sayısı (BR-3), kullanılan ve
   kalan (BR-5), yeni talebin durumu (BR-7) bugünkü davranışlarıyla, sınır değerleriyle sabitlenir.
3. **R-3 Ekran karakterizasyonu.** Ana sayfa (özet sayıları, bakiye tablosu), talep listesi, yeni talep
   formu ve kaydı (bakiye kontrolü BR-4, geçmiş tarih BR-6, hatalı girdi), bilinmeyen adres uçtan uca,
   HTTP üzerinden sabitlenir.
4. **R-4 Bilinen hatalar olduğu gibi.** Hatalı olduğu bilinen davranışı sabitleyen her test, adında veya
   açıklamasında ilgili backlog maddesini (B-n) anar. Hiçbir hata bu işte düzeltilmez.
5. **R-5 Test edilebilirlik, davranış değişmeden.** Testler "bugün"ü sabitleyebilmeli, sunucuyu 5000
   dışında bir portta açabilmeli ve her testte veriyi baştan kurabilmelidir. Uygulama normal
   başlatıldığında davranış birebir aynı kalır: sistem saati, port 5000, aynı seed verisi.
6. **R-6 Determinizm.** Testler sabit bir referans günle (**2026-11-02**), `Europe/Istanbul` ve `tr-TR`
   ile çalışır; çalıştırıldıkları güne, makineye ve sıraya bağlı değildir.

## User experience
none — no user interface. Mevcut ekranlar değişmez; bu iş yalnızca onları test altına alır.

## Constraints & out of scope
- **Sensitive area: İzin hakkı ve bakiye hesabı** (`docs/domain.md`). Kişisel veri (KVKK) de dolaylı:
  test verisi kurgusaldır; seed'deki sağlık nedeni test verisine kopyalanmaz.
- Araç kararı bootstrap'ta verildi: Maven Wrapper + JUnit 5, JDK 21 (`docs/conventions.md`). Erişim:
  Maven Central'a doğrudan (proxy/ayna yok).
- Üretim kodunda yalnız R-5 için gereken asgari dikiş noktaları; paketlere bölme yok.
- Seed isimleri kurgusal demo verisi kabul edilir; testler seed'i yalnız "açılışta seed aynı" davranışı
  için sayı/yapı düzeyinde kullanır.
- **Out of scope:** `src/main/java` yapısına taşıma, paketlere bölme ve mimari bağımlılık testi,
  HTML escape / hata yönetimi / girdi doğrulama düzeltmeleri (B-10), herhangi bir kural düzeltmesi
  (B-1…B-7), kod kapsamı raporu, `Iptaledildi` (B-8).
- Split: değerlendirildi; tek yetenek ("test edilebilirlik"), 10 kriter — tek spec önerildi.

## Acceptance criteria
- [ ] AC-1 — `./scripts/check --fast` kural testlerini, `./scripts/check` ek olarak ekran testlerini çalıştırır; ikisi de yeşil, her profilin çalışan test sayısı çıktıda görünür ve hiçbir test iki profilin dışında kalmaz.
- [ ] AC-2 — Bir kural testi kasıtlı kırıldığında `./scripts/check --fast` ve `./scripts/check` kırmızı döner.
- [ ] AC-3 — İzin hakkı (BR-1): kıdem 0 → 0, 1 → 14, 5 → 14, 6 → 20, 15 → 20, 16 → 26 gün (bugünkü değerler; 14/15 sorusu B-1).
- [ ] AC-4 — Kıdem (BR-2, B-2): referans gün 2026-11-02 iken 2025-12-31'de işe giren 1 yıl (14 gün), 2026-01-01'de işe giren 0 yıl (0 gün) kıdemli sayılır; 2020-02-29'da işe giren 6 yıl sayılır.
- [ ] AC-5 — Gün sayısı (BR-3, B-3): başlangıç = bitiş → 1 gün; Cuma–Pazartesi → 4 gün (hafta sonu düşülmez).
- [ ] AC-6 — Kullanılan / kalan (BR-5, B-5): yalnız `Onaylandi` talepler kullanılandan düşülür; `Beklemede` ve `Reddedildi` düşülmez.
- [ ] AC-7 — Bakiye kontrolü (BR-4, BR-7, B-4) uçtan uca: istenen gün < kalan → talep `Beklemede` olarak, Türkçe karakterli nedeniyle (ör. "Düğün") birlikte listede görünür; istenen gün = kalan → ERR-412 gösterilmez, listeye yönlendirilir, **talep oluşmaz**; istenen gün > kalan → ERR-412 mesajı gösterilir, talep oluşmaz.
- [ ] AC-8 — Ekranlar uçtan uca, seed verisiyle referans günde: ana sayfa Bekleyen 4 / Onaylanan 1 / Reddedilen 3 ve her personelin hak/kullanılan/kalan satırını gösterir; talep listesi 8 talebi gün sayısı ve durumuyla gösterir; bilinmeyen adres 404 döner.
- [ ] AC-9 — Girdi davranışı uçtan uca (BR-6, B-10): geçmiş tarihli talep kabul edilir; bitişi başlangıçtan önce olan talep negatif gün sayısıyla kabul edilir (B-11); tarih veya personel alanı geçersiz/eksik gönderildiğinde talep oluşmaz ve listeye yönlendirilir (bugünkü sessiz davranış).
- [ ] AC-10 — Varsayılan çalışma değişmez ve iz bırakmaz: README komutlarıyla başlatılan uygulama port 5000'de aynı seed'le açılır; testler port 5000'i kullanmaz, arka arkaya iki çalışma aynı sonucu verir; `Residue: clean (fast + full)`.

## Definition of Done
- [ ] Every acceptance criterion mapped to proof (test or reproducible observation)
- [ ] `scripts/check` green
- [ ] Independent review done; real findings fixed, noise rejected with written rationale, deferred ones in `specs/backlog.md`
- [ ] Docs / ADRs updated if behavior or architecture changed
- [ ] Spec moved to `specs/done/` (it becomes immutable there)

## Scorecard (fill at ship — honest numbers make the process improvable)
| Metric | Value |
|---|---|
| Spec revisions | |
| Fix rounds | |
| Review findings: real / noise / deferred | |
| Regressions introduced | |
| Bugs escaped to production | |
