# Domain

> Bootstrap 2026-11-02 ile dolduruldu. Kurallar **bugünkü kod davranışını** anlatır; "AÇIK" olanlar
> İK kararı bekler ve `specs/backlog.md`'de izlenir. Kod düzeltilerek kural tahmin edilmez (AGENTS.md kural 8).

## Ubiquitous language

| Term | Meaning | Notes / not to be confused with |
|---|---|---|
| Personel (`Employee`) | id, ad, birim, işe giriş tarihi | Kullanıcı hesabı değildir (kimlik doğrulama yok) |
| İzin talebi (`LeaveRequest`) | personel, başlangıç ve bitiş tarihi (ikisi dahil), neden, durum | |
| Durum | `Beklemede` · `Onaylandi` · `Reddedildi` — kalıcı değer ASCII ve büyük/küçük harf duyarlı | **Planlanan, henüz kodda yok:** `Iptaledildi` (B-8) |
| Kıdem | Tam yıl olarak çalışma süresi | Bugünkü kod yalnız yıl farkına bakıyor (BR-2) |
| İzin hakkı | Kıdeme göre yıllık gün sayısı | Kalan ile karıştırılmamalı |
| Kullanılan | `Onaylandi` durumundaki taleplerin gün toplamı | `Beklemede` sayılmaz (BR-5) |
| Kalan (bakiye) | İzin hakkı − kullanılan | |

## Business rules
Format: kural — mevcut davranış (kod) — durum.

- **BR-1 İzin hakkı:** kıdem <1 yıl → 0 · 1–5 → **14** · 6–15 → 20 · 16+ → 26 gün. —
  **AÇIK (B-1):** koddaki yorum 1–5 yıl için 15 gün diyor; geçerli mevzuat/el kitabı doğrulanmalı.
- **BR-2 Kıdem:** `bugünün yılı − işe giriş yılı`; ay/gün dikkate alınmaz. — **AÇIK (B-2):** yıldönümü esas mı?
- **BR-3 Gün sayısı:** takvim günü, başlangıç ve bitiş dahil. — **AÇIK (B-3):** iş günü mü, resmî tatiller düşülür mü?
- **BR-4 Bakiye kontrolü:** handler `kalan < gün` ise reddeder (ERR-412); servis `kalan <= gün` ise
  sessizce kaydetmez ama kullanıcı başarı görür. — **AÇIK (B-4):** kalanın tamamı kullanılabilir mi? (muhtemel hata)
- **BR-5 Bekleyen talepler:** bakiyeden düşülmez. — **AÇIK (B-5)** (2016'dan beri karar yok).
- **BR-6 Geçmiş tarihli talep:** kabul edilir ("İK geriye dönük giriyor"). — Doğrulanacak (B-6).
- **BR-7 Yeni talep:** `Beklemede` durumuyla oluşur. — Doğrulandı (kod).
- **BR-8 Yıl sonu devri:** yok (yorum satırındaki taslak: en fazla 5 gün). — **AÇIK (B-7).**
- **BR-9 Onay / red:** arayüzde bu işlemi yapan ekran yok; durumlar yalnız seed verisinden gelir. — Doğrulandı (kod).

## Key domain invariants
- Bitiş tarihi başlangıçtan önce olamaz (bugün kodda kontrol edilmiyor — yeni kod uygular).
- Gün sayısı ve bakiye hesabı **tek yerde** yapılır (`docs/architecture.md`); iki kopya olmaz.
- Bir talebin durumu yalnızca tanımlı durumlardan biridir (`LeaveStatus` enum, `docs/conventions.md`).
- Kişisel veri (ad, izin nedeni) loga, hata mesajına ve test verisine girmez.

## Sensitive areas
- **İzin hakkı ve bakiye hesabı** — `getEntitlement`, `getSeniorityYears`, `calcDays`, `getUsedDays`,
  `getRemaining`, bakiye kontrolü. Çalışanın yasal hakkı ve parasal değeri; hata hukuki risktir.
- **Kişisel veri (KVKK)** — personel adı, birimi, işe giriş tarihi, izin nedeni. Sağlık nedeni
  özel nitelikli kişisel veridir.
- **Talep durumu geçişleri ve yetki** — onay, red, (planlanan) iptal; kim neyi yapabilir.
