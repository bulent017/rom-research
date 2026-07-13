# MobileITM — AOSP / Custom ROM Araştırma Raporu

Bu depo, MobileITM için AOSP tabanlı özel Android işletim sistemi (custom ROM), cihaz
sıkılaştırma, kendi SDK/API katmanı, GMS/EDLA lisanslama, donanım tedariki ve ürün
senaryoları üzerine hazırlanan araştırma raporunu içerir. Site **MkDocs + Material** ile
üretilir ve **GitHub Pages**'te yayınlanır.

## İçerik

Rapor `docs/` altında 10 bölümden oluşur (Genel Bakış + 1–10). Bölüm sırası ve başlıkları
`mkdocs.yml` içindeki `nav` bölümünde tanımlıdır.

## Yerelde çalıştırma (önizleme)

```bash
# 1) Sanal ortam (önerilir)
python3 -m venv .venv
source .venv/bin/activate

# 2) Bağımlılıklar
pip install -r requirements.txt

# 3) Canlı önizleme (http://127.0.0.1:8000)
mkdocs serve

# 4) Statik siteyi derle (site/ klasörüne)
mkdocs build --strict
```

## GitHub Pages'e yayınlama

### Yol 1 — Otomatik (GitHub Actions, önerilir)

Bu depoda `mkdocs.yml` ve `.github/workflows/deploy.yml` **kökte** olduğu için Actions
workflow'u tanınır ve her `main` push'unda siteyi derleyip yayınlar.

1. Depoyu `main` dalına push edin.
2. GitHub → **Settings → Actions → General → "Allow all actions and reusable workflows" → Save**
   (Actions kapalıysa açın).
3. GitHub → **Settings → Pages → Source → "GitHub Actions"** seçin.
   (Workflow `configure-pages` ile Pages'i otomatik de etkinleştirebilir.)
4. Site: `https://bulent017.github.io/rom-research/`

### Yol 2 — Elle (gh-pages dalı)

```bash
mkdocs gh-deploy --force
```

Sonra **Settings → Pages → Deploy from a branch → `gh-pages` / `(root)` → Save**.

## Notlar

- Rapor 2026-07 itibarıyla erişilen açık kaynaklara dayanır. Fiyat/süre/lisans rakamları
  yaklaşıktır; bağlayıcı kararlar öncesi ilgili tarafla (Google, OEM, ODM) doğrulanmalıdır.
- Tüm kaynaklar `docs/10-kaynakca.md` içinde toplanmıştır.
