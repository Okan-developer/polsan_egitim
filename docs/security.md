# Security

> Bootstrap 2026-11-02 ile dolduruldu. Her plan ve review bu kurallara uyar.

## Secrets
- Secrets never enter the repo, specs, prompts, or chat. `.env` is gitignored; provide `.env.example`.
- Agents never print secret values, even when debugging.
- Konfigürasyon (ör. port) ortam değişkeniyle verilir; repoda parola/token yok.

## Input & output
- Formdan gelen her alan `web` katmanında doğrulanır: eksik/geçersiz id, tarih, `parseInt` hatası →
  açık Türkçe hata mesajı; sessiz yönlendirme yok.
- HTML'e yazılan **her** değer escape edilir (XSS) — ad, birim, neden, durum dahil.
- Hata mesajlarında stack trace, iç kod yolu ve kişisel veri yok.
- KVKK: izin nedeni (özellikle sağlık) özel nitelikli veridir; loga ve test verisine girmez.

## AuthN / AuthZ
- **Bugün yok — bilinen açık (B-9).** Uygulama yalnız iç ağda çalışır; her erişen her şeyi görür.
- Eklendiğinde: varsayılan red (default-deny); yetki `app` katmanında uygulanır; durum değiştiren
  her POST CSRF korumalı olur. Kimin onay/red/iptal yapabileceği o spec'te İK ile kararlaştırılır.

## Dependencies
- Kaynak yalnız Maven Central (ya da şirket aynası); sürümler sabitlenir (aralık yok).
- **Runtime bağımlılık eklemek ADR gerektirir** (`docs/decisions/`); test kapsamı (JUnit) serbest.
- Kontrol: bilinen CVE yok, lisans ticari kullanıma uygun, aktif bakım.

## Review lens
Security is a mandatory dimension of every independent review (see `prompts/review.md`), not a
separate afterthought phase.
