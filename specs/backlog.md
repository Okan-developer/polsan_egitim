# Backlog — deferred findings

Findings a human triaged as **defer**: real, but fixed later by decision (`workflows/feature-development.md`,
step 7). Nothing lands here without that decision; noise is rejected in the triage record, not parked here.

- A criterion moved out at the ship gate (R-08, `→ moved to B-<n>`) lands here too, by the
  human's ship decision; the entry names the spec and criterion.
- One entry per finding, newest last. Delete the entry in the commit of the work that closes it.
- An entry that grows into new behavior becomes a spec (`specs/active/`), not a fix.

<!-- Format (labels stay English):
- **B-<n>** — <finding, one line> — Source: spec <NNNN>, review round <n> (<file:line>) — Severity: <…>
  — Recommendation: <the reviewer's action> — Deferred by: <name>, <YYYY-MM-DD>, <why>
  A decision deferred at bootstrap (its REPORT) uses Source: bootstrap <YYYY-MM-DD>.
-->

- **B-1** — İzin hakkı 1–5 yıl: kod 14 gün, yorum 15 gün (BR-1) — Source: bootstrap 2026-11-02 (src/Main.java `getEntitlement`) — Severity: High
  — Recommendation: İK geçerli tabloyu doğrulasın; önce karakterizasyon testi, sonra `/change` — Deferred by: bootstrap görüşmesi, 2026-11-02, İK kararı gerekli
- **B-2** — Kıdem yalnız yıl farkıyla hesaplanıyor, yıldönümü dikkate alınmıyor (BR-2) — Source: bootstrap 2026-11-02 (src/Main.java `getSeniorityYears`) — Severity: High
  — Recommendation: yıldönümü esaslı hesap; 29 Şubat sınır testi — Deferred by: bootstrap görüşmesi, 2026-11-02, İK kararı gerekli
- **B-3** — Gün sayısı takvim günü; yorum "iş günü" diyor (BR-3) — Source: bootstrap 2026-11-02 (src/Main.java `calcDays`) — Severity: High
  — Recommendation: İK iş günü/resmî tatil kuralını belirlesin — Deferred by: bootstrap görüşmesi, 2026-11-02, İK kararı gerekli
- **B-4** — Bakiye kontrolü tutarsız: servis `<=`, handler `<`; kalanın tamamı istenince talep sessizce kaybolur (BR-4) — Source: bootstrap 2026-11-02 (src/Main.java `createRequest`, `/new`) — Severity: High
  — Recommendation: tek kontrol `app` katmanında; büyük olasılıkla `/fix-bug` — Deferred by: bootstrap görüşmesi, 2026-11-02, İK teyidi gerekli
- **B-5** — Bekleyen talepler bakiyeden düşülmüyor (BR-5) — Source: bootstrap 2026-11-02 (src/Main.java `getRemaining`) — Severity: Medium
  — Recommendation: İK karar versin (önerim: kalan gösteriminde ayrı "bekleyen" sütunu) — Deferred by: bootstrap görüşmesi, 2026-11-02, 2016'dan beri karar yok
- **B-6** — Geçmiş tarihli talepler kontrolsüz kabul ediliyor (BR-6) — Source: bootstrap 2026-11-02 (src/Main.java `createRequest`) — Severity: Low
  — Recommendation: İK teyidi; kabul ediliyorsa kural olarak belgele — Deferred by: bootstrap görüşmesi, 2026-11-02, İK teyidi gerekli
- **B-7** — Yıl sonu izin devri yok; yorum satırında taslak duruyor (BR-8) — Source: bootstrap 2026-11-02 (src/Main.java `recalculateYearEnd`) — Severity: Medium
  — Recommendation: İK kuralı belirlesin, sonra spec; ölü kod o spec'te silinir — Deferred by: bootstrap görüşmesi, 2026-11-02, İK kararı gerekli
- **B-8** — Yeni durum `Iptaledildi` (talep iptali) — Source: bootstrap 2026-11-02 (kullanıcı talebi) — Severity: Info
  — Recommendation: ilk özellik adayı; spec'te netleşecekler: hangi durumlardan (önerim `Beklemede`, `Onaylandi`), onaylı iptalde günler bakiyeye döner mi (önerim evet), iptal yetkisi kimde — Deferred by: bootstrap görüşmesi, 2026-11-02, spec gerektirir
- **B-9** — Kimlik doğrulama/yetkilendirme yok; onay/red ekranı yok — Source: bootstrap 2026-11-02 (src/Main.java) — Severity: High
  — Recommendation: "kim neyi onaylar" İK kararıyla ayrı spec; CSRF ile birlikte — Deferred by: bootstrap görüşmesi, 2026-11-02, spec gerektirir
- **B-10** — HTML escape yok (XSS), hatalar yutuluyor, form girdisi doğrulanmıyor, personel bulunamazsa `true` — Source: bootstrap 2026-11-02 (src/Main.java) — Severity: High
  — Recommendation: karakterizasyon testlerinden sonra `/fix-bug` turları — Deferred by: bootstrap görüşmesi, 2026-11-02, önce test altyapısı
