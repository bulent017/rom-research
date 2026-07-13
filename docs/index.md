# MobileITM — AOSP / Custom ROM Araştırma Raporu

!!! abstract "Bu rapor ne hakkında?"
    MobileITM bugün bir **MDM (Mobil Cihaz Yönetimi)** firması olarak, cihazları
    *Device Owner* yetkisiyle **politika** üzerinden yönetiyor — yani başkasının
    işletim sisteminin üstüne kural koyuyor. Bu rapor, bir üst adımı araştırıyor:
    **işletim sisteminin kendisine sahip olarak** (custom ROM / AOSP tabanlı kendi
    Android'imizi üreterek) cihaz üretmek, sıkılaştırmak ve bundan ürün/gelir
    çıkarmak mümkün mü, nasıl yapılır, maliyeti ve engelleri nedir?

    Rapor **production ortamında (gerçek cihazlarda)** çalışacak çözümlere odaklanır;
    emülatör/geliştirme ortamı kapsam dışıdır.

## Yönetici özeti (TL;DR)

- **Yapılmak istenen tek cümlede:** Politikayla değil, **OS'un sahibi olarak** cihaz
  üretmek. İşverenin saydığı tüm örnekler (sıkılaştırılmış telefon, hafif signage OS,
  kendi Knox-benzeri SDK, açılışta parola, cihazdan gelir) aynı merdivenin basamakları.

- **Üç ayrı kavram, karıştırılmamalı:**
  **Sıkılaştırma** (güvenlik) ≠ **Hafiflik** (ayak izi) ≠ **Kilitleme** (kiosk).
  Detay: [Bölüm 1](01-baglamm-ve-hedefler.md).

- **Teknik olarak mümkün ve oturmuş bir yol var:** AOSP'yi kaynaktan derle → kendi
  release/AVB anahtarlarınla imzala → verified boot'u kendi anahtarınla kilitle →
  kendi OTA sunucunla güncelle. Adımlar [Bölüm 2](02-custom-rom-nasil-yapilir.md) ve
  sıkılaştırma [Bölüm 3](03-sikilastirma.md)'te.

- **En büyük ticari engel:** Kendi ROM'unu koyunca cihaz **Google servislerini (GMS)**
  kaybeder. Geri kazanmak Google ile **MADA/EDLA** anlaşması ister ve pratikte sadece
  **~100 onaylı OEM/ODM ortağı** üzerinden mümkün. Signage'de GMS gerekmez (avantaj).
  Detay: [Bölüm 5](05-lisanslama-gms-edla.md).

- **En büyük teknik engel:** Sürücüler / **BSP** (Board Support Package). "Her telefona
  ROM" modeli ticari olarak çalışmaz; cihaz ya seçili bir referans model (Pixel), ya
  ODM kutu (Rockchip vb.), ya da OEM ortağı üzerinden gelmeli.
  Detay: [Bölüm 6](06-donanim-tedarik.md).

- **Önerilen sıra:** Önce **hafif + kilitli signage OS** (en düşük risk, GMS gerekmez,
  sürücü sorumluluğu ODM'de, MDM altyapınız yeniden kullanılır) → sonra **sıkılaştırılmış
  kurumsal telefon** (OEM ortaklığıyla). Detay: [Bölüm 7](07-urun-senaryolari.md) ve
  [Bölüm 9](09-degerlendirme-yol-haritasi.md).

- **Gözden kaçan maliyet:** Asıl yük ilk derleme değil, **aylık güvenlik yaması + kendi
  OTA hattının** sürekli bakımı. Fiyatlamaya abonelik olarak yansımalı.
  Detay: [Bölüm 8](08-bakim-ota.md).

## Karar tablosu — hangi ürün, hangi yol?

| Kriter | Signage OS (hafif+kilitli) | Sıkılaştırılmış telefon |
|---|---|---|
| GMS/Play Store gerekir mi? | ❌ Gerekmez (avantaj) | ⚠️ Müşteri isterse → OEM ortağı şart |
| Sürücü/BSP kaynağı | ODM (Rockchip vb.) verir | OEM ortağı verir / Pixel referans |
| Donanım sertifikasyonu | ODM'de | OEM'de |
| Bootloader relock | ODM ile çözülür | Pixel'de mümkün / OEM ile |
| İlk pazara çıkış riski | **Düşük** | Orta–Yüksek |
| MDM altyapısının yeniden kullanımı | Yüksek | Yüksek |
| Önerilen sıra | **1. Faz** | 2. Faz |

## Nasıl okunmalı?

Rapor 10 bölümden oluşur. Acele eden yönetici için bu sayfa + [Bölüm 9 (Değerlendirme
ve Yol Haritası)](09-degerlendirme-yol-haritasi.md) yeterlidir. Teknik ekip için
[Bölüm 2–4](02-custom-rom-nasil-yapilir.md), ticari/satınalma için
[Bölüm 5–6](05-lisanslama-gms-edla.md) kritiktir.

!!! note "Kaynaklar"
    Her bölümün sonunda o bölümde kullanılan kaynaklar listelenir; tüm kaynakların
    toplu listesi [Bölüm 10 — Kaynakça](10-kaynakca.md)'dadır. Rapor 2026-07 itibarıyla
    erişilen açık kaynaklara dayanır; lisans koşulları ve fiyatlar zamanla değişebilir,
    kritik kararlar öncesi ilgili tarafla (Google, OEM, ODM) doğrudan teyit alınmalıdır.
