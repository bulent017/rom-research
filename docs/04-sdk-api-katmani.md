# 4. Kendi SDK / API Katmanı (Knox Benzeri)

!!! abstract "Hedef"
    İşverenin "Samsung SDK'sı gibi kendi API'miz olsun" isteği: cihaza, uygulamaların
    (özellikle sizin MDM ajanınızın) çağırabileceği, **platform yetkisiyle çalışan özel
    yetenekler** eklemek. Örn. `MobileITMManager.setKioskMode(true)`,
    `MobileITMManager.disableUsbData()`, `MobileITMManager.rotateSignageContent(...)`.

Bu, custom ROM'un **en değerli çıktısıdır**: OS'a sahip olmadan yapamayacağınız yetenekleri
temiz bir API arkasına koyup ürünleştirmek.

## 4.1 Samsung Knox nasıl kurgulanmış? (referans mimari)

Knox, bir "hardware + software" güvenlik platformudur ve üstünde **1100+ API metodu** sunan
bir SDK ailesi vardır. Mimarinin çalışma mantığı:

- **MDM istemci uygulaması** Knox SDK'sını çağırır.
- Samsung **MDM framework**'ü bu çağrıları alır ve **Knox Framework sistem servisini**
  (platform seviyesinde çalışan servis) tetikler.
- Yetenekler (VPN engine, container/workspace, DualDAR şifreleme vb.) ayrı sistem
  bileşenleri olarak devreye girer; izolasyon **uygulama katmanından çekirdek ve donanıma**
  kadar iner.

Kaynak: [Samsung Knox — About the Knox SDK](https://docs.samsungknox.com/dev/knox-sdk/about-the-knox-sdk/),
[Knox Standard SDK (SEAP)](https://seap.samsung.com/sdk/knox-standard-android),
[Knox Networking Framework mimarisi](https://seap.samsung.com/html-docs/android-vpn-service/Content/framework-architecture.htm).

**Çıkarım:** "Knox gibi SDK" demek, aslında **(1) bir sistem servisi + (2) onun önüne
konan temiz bir SDK katmanı** demektir. Aşağıda bunun AOSP'de nasıl yapıldığı var.

## 4.2 AOSP'de kendi sistem servisini yazmak

Teknik iskelet (özet):

1. **AIDL arayüzü** tanımlanır: servisin dışarıya açtığı metotların sözleşmesi. Build
   sistemi bundan Binder "stub/proxy" kodunu üretir.
2. **Servis implementasyonu** yazılır ve `system_server` içinde başlatılır (veya ayrı bir
   sistem süreci olarak). Örn. `SystemServer.java` içine servis kaydı eklenir.
3. **Manager sınıfı** (ör. `MobileITMManager`) yazılır: uygulamalar düşük seviyeli Binder
   yerine bu temiz, nesne yönelimli API'yı kullanır (Android'in `getSystemService()`
   deseninin aynısı).
4. **Modül** AOSP'ye eklenir: dizin + `Android.bp` (Soong build).

Kaynak: [Building a Custom System Service in Android 15 (AOSP)](https://medium.com/@aruncse2k20/building-a-custom-system-service-in-android-15-aosp-4ff26fcb1dbb),
[AOSP – Creating a System Service (devarea)](https://devarea.com/aosp-creating-a-system-service/),
[NXP Community — add custom system service](https://community.nxp.com/t5/i-MX-Processors/How-to-add-custom-system-service-to-AOSP/m-p/927478).

## 4.3 Genel (public) API olarak yayınlamak

SDK'nızı üçüncü tarafların da kullanmasını istiyorsanız, yeni public sınıf/metotlar
AOSP'nin API yüzey denetimine takılır:

- Public API imzaları `frameworks/base/core/api/current.txt` içinde tutulur.
- Yeni API eklediğinizde `make update-api` ile bu imza dosyaları güncellenir; build,
  beklenmedik API değişikliklerini bu dosyaya göre denetler.

Kaynak: [Android — Vendor API level](https://source.android.com/docs/core/architecture/api-flags),
[Android — Implement the service](https://source.android.com/docs/core/architecture/configuration/archive/service).

## 4.4 Platform imzası: SDK'nın ayrıcalıklı çalışması

Sisteme dokunan yetenekler (kiosk, USB kontrolü, politika zorlama) **platform imzalı**
ya da **priv-app** olmayı gerektirir. Yani:

- Servisiniz ve MDM ajanınız, [Bölüm 2](02-custom-rom-nasil-yapilir.md)'deki **platform
  anahtarınızla** imzalanır → sistem yetkileriyle çalışır.
- MDM ajanınız `priv-app` olarak imaja gömülür → silinemez, öldürülemez.

Bu, "General Mobile'dan ROM + **imza anahtarı** alan firma" örneğindeki anahtarın neden
kritik olduğunu da açıklar: **imza anahtarı olmadan sistem seviyesinde çalışan bir SDK
üretemezsiniz.**

## 4.5 Alternatif / tamamlayıcı: OEMConfig

Kendi baştan sona ROM'unuz yoksa ama bir **OEM ortağınız** varsa, OEM'e özel yetenekleri
standart bir yolla açmanın yolu **OEMConfig**'tir:

- OEM, cihaza özel yönetim yeteneklerini tanımlayan bir **şema** oluşturur, bunu bir
  uygulamaya gömer ve Google Play'e koyar.
- EMM (MDM) konsolu bu şemayı okur ve IT yöneticisine sunar; ayarlar **managed
  configurations** ile cihaza iner.
- Böylece her EMM ayrı DPC yazmak zorunda kalmaz; Zebra, Samsung gibi üreticiler bunu
  kullanır.

Kaynak: [Google — Use OEMConfig](https://support.google.com/work/android/answer/9388447),
[Zebra — OEMConfig FAQ](https://techdocs.zebra.com/oemconfig/11-5/faq/),
[Microsoft Intune — OEMConfig](https://learn.microsoft.com/en-us/intune/device-configuration/templates/configure-oemconfig-android).

!!! tip "MobileITM için iki katmanlı strateji"
    1. **Kendi ROM'unuzda:** `MobileITMManager` sistem servisi + platform imzalı SDK →
       tam kontrol, "Knox benzeri" ürün.
    2. **Başkasının cihazında (ör. mevcut Hikvision):** OEM'in sunduğu OEMConfig/OEM API'ları
       üzerinden yönetim → ROM'a gerek kalmadan zenginleşme. Elinizdeki DS-MDT202'de
       zaten `com.mediatek.mdmconfig` gibi bir MTK MDM katmanı mevcut; bunun ne sunduğu
       incelenmeye değer.

## 4.6 Gerçekçi kapsam uyarısı

Knox'un **1100+ API'si** yıllarca süren bir mühendislik yatırımıdır. MobileITM'in hedefi
"Knox'u kopyalamak" değil, **kendi ürününüz için gereken 10–20 kritik yeteneği** (kiosk,
USB/ADB kontrolü, signage içerik yönetimi, güçlü sıfırlama, sertifika yönetimi) temiz bir
SDK arkasına koymak olmalı. Bu, ulaşılabilir ve ürünleştirilebilir bir hedeftir.

---

!!! note "Bu bölümün kaynakları"
    - [Samsung Knox — About the Knox SDK](https://docs.samsungknox.com/dev/knox-sdk/about-the-knox-sdk/)
    - [Knox Standard SDK (SEAP)](https://seap.samsung.com/sdk/knox-standard-android)
    - [Building a Custom System Service in Android 15 (AOSP)](https://medium.com/@aruncse2k20/building-a-custom-system-service-in-android-15-aosp-4ff26fcb1dbb)
    - [AOSP – Creating a System Service (devarea)](https://devarea.com/aosp-creating-a-system-service/)
    - [Android — Vendor API level](https://source.android.com/docs/core/architecture/api-flags)
    - [Google — Use OEMConfig](https://support.google.com/work/android/answer/9388447)
    - [Zebra — OEMConfig FAQ](https://techdocs.zebra.com/oemconfig/11-5/faq/)
