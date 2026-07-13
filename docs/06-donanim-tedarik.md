# 6. Donanım ve Tedarik Yolları

!!! danger "En büyük teknik engel: sürücüler (BSP)"
    "Çok farklı telefon var, hepsine ROM yazamayız" sezgisi **doğrudur**. Sebep: Android'in
    üst katmanı (framework) açık kaynaktır ama **donanımı çalıştıran katman değildir**.
    Her cihaz için modem, GPU, kamera, NFC gibi bileşenlerin **kapalı kaynak sürücü/firmware
    blob'ları** ve cihaza özel çekirdek gerekir. Bunlar bir arada **BSP (Board Support
    Package)** olarak anılır. BSP olmadan cihaz düzgün çalışmaz; **BSP yoksa maliyet fırlar.**
    ([emteria](https://emteria.com/blog/aosp-rom))

Bu yüzden "her telefona ROM" ticari olarak çalışmaz. Üç geçerli tedarik yolu vardır.

## 6.1 Yol A — ODM cihaz (signage / gömülü için ideal)

**ODM (Original Design Manufacturer):** Sizin adınıza cihaz tasarlayıp üreten fabrika.
Signage kutuları için **Rockchip ekosistemi** olgun bir tedarik zinciridir.

- Popüler yongalar: **RK3566 / RK3568 / RK3588** (4K/8K, Android 11–14, NPU, RTC, watchdog).
- Üreticiler beyaz etiketle (**MobileITM markası**) satar, **BSP ve firmware
  özelleştirmesini** verir; bazıları ~500 adetlik siparişte firmware'i size göre uyarlar.
- Örnek tedarikçiler:
  [Sunchip RK3568/RK3588 kartları](https://www.sunchip-tech.com/products/rk3568-android-board/),
  [Geniatech quad-display signage player](https://aijourn.com/geniatech-introduces-rk3588-quad-display-signage-player/),
  [Rikomagic DS08 (8K signage, RTC + watchdog)](https://www.cnx-software.com/2025/12/05/rikomagic-ds08-rockchip-rk3588-powered-8k-digital-signage-player-with-rtc-and-watchdog/),
  [Ranboda Rockchip TV box](https://www.ranboda.com/product-category/tv-box/rockchip-tv-box/).
- Geliştirici kaynağı hazır: örn.
  [TinkerBoard RK3588 device tree](https://github.com/TinkerBoard-Android/rockchip-android-device-rockchip-rk3588),
  [rockchip-linux/kernel](https://github.com/rockchip-linux/kernel),
  [FriendlyELEC FriendlyThings SDK](https://wiki.friendlyelec.com/wiki/index.php/FriendlyThings_for_Rockchip)
  (GPIO/UART/SPI/I2C erişimi için hazır Android SDK).

!!! success "Avantajı"
    Donanım **tek model** ve **sürücüler fabrikadan çalışır** gelir. Sürücü/BSP sorumluluğu
    ODM'dedir; siz sadece üst katmanı (branding, kiosk, MDM, içerik) özelleştirirsiniz.
    Bu, en düşük teknik riskli yoldur.

## 6.2 Yol B — OEM ortaklığı (kurumsal telefon için)

**General Mobile modeli.** Sertifikalı bir OEM ile anlaşırsınız; size cihazın tam **BSP**'sini
verir. Farklar:

- İmzalama ya OEM anahtarlarıyla yapılır ya da anahtarlar **size lisanslanır** ("şifresini
  de almış" denen şey budur).
- Cihazlar **fabrikadan sizin ROM'unuzla ve kilitli bootloader'la** çıkar → relock/anahtar
  sorunu doğmaz.
- Müşteri **GMS istiyorsa** tek uygulanabilir yol budur (bkz. [Bölüm 5](05-lisanslama-gms-edla.md)).
- Sürücü sorumluluğu OEM'de kalır.

Google'ın kurumsal cihaz ekosistemi ve ortak listeleri:
[Android Enterprise device manufacturers](https://androidenterprisepartners.withgoogle.com/device-manufacturers/),
[Android Certified Partners](https://www.android.com/certified/partners/),
[Resources for OEMs and carriers](https://developers.google.com/android/work/oem-resources).

!!! tip "Hikvision zaten bir OEM adayı olabilir"
    Elinizdeki **Hikvision DS-MDT202** üzerinde `sys.oem_unlock_allowed=0` (kilitli) ve
    bir MediaTek MDM katmanı (`com.mediatek.mdmconfig`) mevcut. Cihazlarını zaten
    kullandığınız Hikvision'dan **BSP + imza anahtarı + OEM-unlock erişimi** talep etmek,
    sıfırdan başlamaktan çok daha kısa bir OEM ortaklığı yolu olabilir.

## 6.3 Yol C — Referans cihaz (Ar-Ge ve doğrulama)

**Google Pixel.** ROM öğrenmesi, sıkılaştırma provası ve **gerçek relock testi** için tek
erişilebilir cihaz:

- Vendor blob'ları Google'dan **resmî** indirilir → BSP derdi minimum.
- **Custom AVB anahtarıyla relock** ve güçlü secure element (Titan M) destekler.
- Uzun güvenlik desteği (yeni modellerde ~7 yıl).
- GrapheneOS'un neden yalnızca Pixel desteklediğinin sebebi budur (bkz.
  [Bölüm 3](03-sikilastirma.md)).

!!! warning "Ama ticari ölçekte Pixel sürdürülebilir değil"
    Prototip/demo/doğrulama için ideal; ama binlerce cihazlık üretim tedariki için
    uygun değildir. Pixel = **laboratuvar cihazı**, ürün değil.

## 6.4 Yolların karşılaştırması

| Kriter | ODM (Rockchip) | OEM ortaklığı | Pixel (referans) |
|---|---|---|---|
| Hedef ürün | Signage / gömülü | Kurumsal telefon | Ar-Ge / doğrulama |
| BSP / sürücü | ODM verir | OEM verir | Google (hazır) |
| Bootloader relock | ODM ile çözülür | OEM ile (fabrikada) | ✅ (custom key) |
| GMS mümkün mü? | Gerekmez | ✅ (OEM üzerinden) | Var ama üründe değil |
| Marka baskısı | ✅ (beyaz etiket) | ✅ | ❌ |
| Ölçek/tedarik | ✅ Yüksek | ✅ Yüksek | ❌ Düşük |
| İlk yatırım | Düşük–orta | Orta–yüksek (NRE) | En düşük |
| Teknik risk | **Düşük** | Orta | Düşük |

## 6.5 Sonuç

- **Signage ürünü → ODM (Rockchip).** Sürücü riski yok, marka baskısı var, tedarik hazır.
- **Kurumsal telefon → OEM ortaklığı** (muhtemelen Hikvision veya General Mobile tipi).
- **Öğrenme/doğrulama → Pixel.**

"Rastgele telefonlara ROM basıp satma" fikri, BSP ve relock kısıtları nedeniyle
**elenmelidir.**

---

!!! note "Bu bölümün kaynakları"
    - [emteria — Building an AOSP ROM (BSP maliyeti)](https://emteria.com/blog/aosp-rom)
    - [Sunchip — RK3568 Android board](https://www.sunchip-tech.com/products/rk3568-android-board/)
    - [Geniatech RK3588 signage player](https://aijourn.com/geniatech-introduces-rk3588-quad-display-signage-player/)
    - [Rikomagic DS08 (CNX Software)](https://www.cnx-software.com/2025/12/05/rikomagic-ds08-rockchip-rk3588-powered-8k-digital-signage-player-with-rtc-and-watchdog/)
    - [TinkerBoard RK3588 device repo](https://github.com/TinkerBoard-Android/rockchip-android-device-rockchip-rk3588)
    - [FriendlyELEC — FriendlyThings SDK](https://wiki.friendlyelec.com/wiki/index.php/FriendlyThings_for_Rockchip)
    - [Android Enterprise — Device manufacturers](https://androidenterprisepartners.withgoogle.com/device-manufacturers/)
    - [Android Certified Partners](https://www.android.com/certified/partners/)
