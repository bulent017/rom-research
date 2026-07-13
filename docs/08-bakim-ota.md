# 8. Bakım, Güvenlik Yamaları ve OTA

!!! danger "Asıl maliyet ilk build değil, süreklilik"
    Custom ROM işinin en çok hafife alınan kısmı budur. Bir kez derleyip satmak yetmez;
    OS'un sahibi olduğunuz an, **güvenlik yamalarını takip etmek ve sahadaki tüm cihazlara
    dağıtmak** sizin sorumluluğunuza geçer. Bu, bir defalık proje değil, **sürekli işleyen
    bir hattır** ve fiyatlamaya **abonelik** olarak yansıtılmalıdır.

## 8.1 Aylık güvenlik yaması döngüsü (ASB)

Google her ay **Android Security Bulletin (ASB)** yayınlar ve düzeltmeleri AOSP'ye
işler. Sıkılaştırılmış cihaz satıyorsanız:

1. Google'ın aylık yamalarını kendi kaynak ağacınıza **merge** edersiniz.
2. Yeni imajı **kendi anahtarlarınızla** yeniden imzalarsınız ([Bölüm 2](02-custom-rom-nasil-yapilir.md)).
3. **OTA paketi** üretip sahadaki cihazlara dağıtırsınız.

!!! info "Google desteği sonsuz değil"
    Google, sertifikalı bir Android sürümüne genelde **~3 yıl** güvenlik yaması sağlar;
    sonrasında backport sorumluluğu tamamen size/OEM'e geçer
    ([Jason Bayton](https://bayton.org/blog/2024/01/certifying-android-devices/)). Uzun
    ömürlü kurumsal/signage cihazlarda bu, planlanması gereken gerçek bir yüktür.

## 8.2 A/B (seamless) güncelleme mimarisi

Production filoları için **A/B seamless updates** standarttır:

- Cihazda sistem/boot/vendor bölümlerinin **iki kopyası** (slot A ve slot B) bulunur.
- Güncelleme, cihaz çalışırken **kullanılmayan slota** yazılır; sonraki açılışta o slota
  geçilir.
- Güncelleme başarısız olursa cihaz **eski çalışan slota geri döner** → tuğlalaşma
  (brick) riski minimuma iner. Sahadaki cihaz için kritik.

Kaynak: [Android — OTA updates](https://source.android.com/docs/core/ota),
[Esper — How Android OTA updates work](https://www.esper.io/blog/firmware-over-the-air-fota-updates-on-android).

## 8.3 Delta (artımlı) güncellemeler

Tam imaj yerine, mevcut bölümün üstüne uygulanan **ikili fark (delta)** paketleri
gönderilir → bant genişliği ve indirme boyutu ciddi düşer. Binlerce cihazlık filoda
maliyet farkı büyüktür. ([Esper](https://www.esper.io/blog/firmware-over-the-air-fota-updates-on-android))

## 8.4 Kendi OTA sunucunuz

İyi haber: OTA sunucusu **karmaşık değildir**. Custota gibi çözümler yalnızca **HTTP Range
başlığını destekleyen statik bir web sunucusu** (Apache/Nginx/Caddy) ister.

Seçenekler:

| Çözüm | Not |
|---|---|
| [Custota](https://github.com/chenxiaolong/Custota) | A/B OTA istemcisi; **avbroot** ile custom anahtarla imzalı OTA'yı sorunsuz kurar |
| [avbroot](https://github.com/chenxiaolong/avbroot) | A/B OTA'ları custom anahtarla yeniden imzalama aracı |
| [OpenOTA](https://github.com/mitchellurgero/OpenOTA) | PHP tabanlı basit self-hosted OTA checker |
| Ticari ([Esper](https://www.esper.io/blog/firmware-over-the-air-fota-updates-on-android)) | Aşamalı dağıtım, otomatik geri alma, uyumluluk raporu — büyük filo |

!!! tip "MDM backend'inizle birleştirin"
    MDM altyapınız zaten cihazlarla konuşuyor. OTA dağıtım panelini **buna entegre
    etmek** mantıklıdır: hangi cihaz grubuna hangi sürüm, hangi saatte, kademeli dağıtım
    (%1 → %10 → %100), başarısızlıkta otomatik durdurma.

## 8.5 Production dağıtım disiplini

Büyük filolarda dikkat edilecekler:

- **Aşamalı (staged) dağıtım:** Önce küçük bir pilot gruba, sorun yoksa yaygınlaştır.
- **Otomatik geri alma:** Filo genelinde hata oranı eşiği aşılırsa dağıtımı durdur.
- **CDN/mirror:** Eşzamanlı indirmeler altyapıyı zorlar; CDN veya LAN paylaşımı kullan.
- **Cihaz grubu bazlı raporlama:** Hangi cihaz hangi sürümde, uyumluluk durumu.

## 8.6 Fiyatlamaya etkisi

Bu bölümdeki her şey, ürünün **tek seferlik değil abonelikli** olması gerektiğini gösterir:

- Donanım: tek seferlik (maliyet + düşük marj)
- **Yazılım bakımı + güvenlik yaması + OTA + yönetim: aylık abonelik** (asıl marj)

Bu model hem signage (Senaryo A) hem sıkılaştırılmış telefon (Senaryo B) için geçerlidir
ve MobileITM'in mevcut MDM abonelik iş modeliyle **doğal olarak örtüşür**.

---

!!! note "Bu bölümün kaynakları"
    - [Android — OTA updates](https://source.android.com/docs/core/ota)
    - [Android — Sign builds for release](https://source.android.com/docs/core/ota/sign_builds)
    - [Esper — How Android OTA updates work](https://www.esper.io/blog/firmware-over-the-air-fota-updates-on-android)
    - [Custota — A/B OTA updater (GitHub)](https://github.com/chenxiaolong/Custota)
    - [avbroot (GitHub)](https://github.com/chenxiaolong/avbroot)
    - [OpenOTA (GitHub)](https://github.com/mitchellurgero/OpenOTA)
    - [Jason Bayton — Certifying Android devices (destek süreleri)](https://bayton.org/blog/2024/01/certifying-android-devices/)
