# 7. Ürün Senaryoları

Bu bölüm, işverenin fikirlerini iki somut ürün senaryosuna indirger ve her birini uçtan
uca (donanım → OS → yönetim → gelir) ele alır.

## 7.1 Senaryo A — Hafif + Kilitli Signage OS (önerilen 1. faz)

!!! success "Neden önce bu?"
    En düşük riskli, en net gelir modeli. **GMS gerekmez** ([Bölüm 5](05-lisanslama-gms-edla.md)),
    **sürücü sorumluluğu ODM'de** ([Bölüm 6](06-donanim-tedarik.md)), ve en pahalı yazılım
    parçası (**uzaktan yönetim**) elinizde zaten var.

**Ürün:** Havalimanı/AVM ekranlarının arkasına takılan, MobileITM markalı, hafif Android
kutusu. Açılır açılmaz reklam/duyuru döngüsüne girer, başka hiçbir şey çalışmaz.

**Yığın (stack):**

| Katman | Seçim |
|---|---|
| Donanım | Rockchip RK3568/RK3588 tabanlı ODM kutu (beyaz etiket) |
| OS | Debloat edilmiş AOSP (Play Services yok), MobileITM branding |
| Kilit | Özel launcher + lock task (tek uygulama = signage player) |
| Yönetim | Mevcut MDM backend'iniz → içerik/zamanlama/uzaktan reboot |
| İçerik | Reklam döngüsü, dijital tabela, uzaktan güncelleme |
| Güncelleme | Kendi OTA sunucunuz ([Bölüm 8](08-bakim-ota.md)) |

**Gelir modeli (sektör standardı):**

1. **Donanım satışı** — tek seferlik (kutu genelde maliyetine yakın satılır).
2. **Aylık abonelik** — içerik yönetimi, reklam döngüsü, uzaktan yönetim, uptime garantisi.
   *Asıl para buradadır.*
3. **Reklam payı** — havalimanı/AVM gibi lokasyonlarda ekran ağını reklam envanteri olarak
   işletmek (işverenin "cihazdan para kazanma" vizyonu).

**Neden düşük risk:** Play Store lisansı yok; donanım/sürücü ODM'de; cihaz tek model;
yazılımın en zor parçası (uzaktan yönetim) hazır.

**İlk somut adım:** ODM'den 1–2 örnek kutu + BSP iste (örnek başı ~50–100$), çalışan bir
custom firmware çıkar, bir müşteride 5–10 ekranlık pilot kur. Toplu sipariş ve marka
baskısı ancak pilot tutarsa.

## 7.2 Senaryo B — Sıkılaştırılmış Kurumsal Telefon (2. faz)

**Ürün:** "Bizzat cihaz olarak" sıkılaştırılmış telefon — açılışta parola, kilitli
bootloader, ADB kapalı, gereksiz her şey atılmış, MDM ajanı sisteme gömülü.

**Yığın:**

| Katman | Seçim |
|---|---|
| Donanım | OEM ortağı (General Mobile / Hikvision tipi) veya Ar-Ge için Pixel |
| OS | AOSP tabanlı sıkılaştırılmış ROM ([Bölüm 3](03-sikilastirma.md)) |
| Boot | Verified Boot + kendi/OEM anahtarıyla relock, pre-boot auth (FBE/CE) |
| SDK | `MobileITMManager` sistem servisi + platform imzalı SDK ([Bölüm 4](04-sdk-api-katmani.md)) |
| Yönetim | MDM ajanı priv-app olarak gömülü |
| GMS | Müşteri isterse → OEM üzerinden; istemezse saf AOSP |

**Hedef müşteri:** Savunma, kamu, finans, saha operasyonları, güvenli iletişim isteyen
kurumlar — yani "cihaz kaybolsa/çalınsa bile veri sızmasın" diyenler.

**Neden 2. faz:** OEM ortaklığı (NRE, sertifikasyon, anahtar lisanslama) ve gerçek relock
doğrulaması gerektirir; Senaryo A'dan öğrenilenler ve kurulan OTA/imzalama hattı burada
yeniden kullanılır.

**Güçlü pazarlama pozisyonu:** OEM görüşmesine "bir fikrimiz var" diye değil, "elimizde
çalışan sıkılaştırılmış OS + kendi SDK'mız var, donanım arıyoruz" diye gitmek — bunu
Senaryo A ve Pixel prototipi sağlar.

## 7.3 Kapsam dışı bırakılması önerilenler

=== "Akıllı saat / BLE"

    İşveren gündeme getirdi ama **erteleyin**. Wear OS **Google lisansıyla** dağıtılır,
    serbestçe alınamaz; saf AOSP'nin saat profili ürünleşmiş değildir. Piyasadaki ucuz
    akıllı saatlerin çoğu Android değil **RTOS** çalıştırır ve BLE ile bağlanır. Yani bu,
    AOSP yetkinliğinin doğrudan devamı değil, **ayrı bir gömülü sistem projesidir.**
    En erken 3. faz.

=== "Rastgele telefonlara ROM"

    BSP ve relock kısıtları nedeniyle ticari olarak **elenmelidir**
    (bkz. [Bölüm 6](06-donanim-tedarik.md)).

## 7.4 İki senaryonun ortak altyapısı

Her iki üründe de şu bileşenler **paylaşılır** — yani Senaryo A'ya yapılan yatırım
Senaryo B'ye taşınır:

- AOSP build + imzalama hattı ([Bölüm 2](02-custom-rom-nasil-yapilir.md))
- Kendi OTA sunucusu ([Bölüm 8](08-bakim-ota.md))
- MDM ajanının priv-app entegrasyonu
- `MobileITMManager` SDK'sı ([Bölüm 4](04-sdk-api-katmani.md))
- Aylık güvenlik yaması disiplini ([Bölüm 8](08-bakim-ota.md))

---

!!! note "Bu bölümün kaynakları"
    - Signage donanım tedariki: [Bölüm 6 kaynakları](06-donanim-tedarik.md)
    - Gelir modeli: signage sektörü standart pratiği (donanım + SaaS abonelik);
      [Esper — FOTA/fleet yönetimi](https://www.esper.io/blog/firmware-over-the-air-fota-updates-on-android)
    - Wear OS lisanslama: [Android Certified Partners](https://www.android.com/certified/partners/)
