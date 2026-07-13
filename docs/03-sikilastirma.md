# 3. Cihaz Sıkılaştırma Teknikleri

!!! abstract "Sıkılaştırma nedir?"
    Cihazın **saldırı yüzeyini daraltmak** ve OS'u **kurcalanamaz** hale getirmek. Yani
    kullanıcının/saldırganın cihaz üzerinde yapabileceklerini kısmak — özellikle cihaz
    fiziksel olarak birinin eline geçtiğinde bile veri ve bütünlüğün korunması. Tek
    cümleyle: **"cihaz elimden çıksa bile kimse içine giremesin, değiştiremesin, veri
    çıkaramasın."**

Bu bölümdeki tekniklerin çoğu **yalnızca OS'un sahibiyken** uygulanabilir; MDM/politika
ile bir kısmı yapılabilir ama boot/relock/SELinux gibi katmanlar için ROM sahipliği şarttır.
En iyi açık referans **GrapheneOS**'tur; aşağıda hem AOSP tabanı hem GrapheneOS'un eklediği
ileri teknikler işlenir.

## 3.1 Boot güvenliği: Verified Boot (AVB)

**Amaç:** Cihaz her açılışta OS'un imzasını doğrular; kurcalanmış (imzası tutmayan) OS
**açılmaz**. Bu, "bizzat cihaz olarak sıkılaştırma"nın temelidir.

**Nasıl çalışır:** Bootloader, `vbmeta` bölümünü okur; `vbmeta` boot ve system
bölümlerinin hash'lerini/imzasını içerir. Zincir kök anahtardan aşağı doğru her katmanı
doğrular. ([Android — Verified Boot / AVB](https://source.android.com/docs/security/features/verifiedboot/avb))

**Kendi anahtarınızla kurma (özet akış):**

```bash
# 1) Kendi RSA anahtarınızı üretin (2048 veya 4096)
openssl genrsa -out avb_key.pem 4096

# 2) Public anahtarı cihazın anlayacağı forma çevirin
avbtool extract_public_key --key avb_key.pem --output pkmd.bin

# 3) (Bootloader açıkken) kendi anahtarınızı custom slot'a yazın
fastboot flash avb_custom_key pkmd.bin

# 4) İmajlarınızı kendi anahtarınızla imzalayıp vbmeta üretin, cihaza yazın
# 5) Bootloader'ı GERİ KİLİTLEYİN
fastboot flashing lock
```

Kaynak: [James Bottomley — Securing a Rooted Android Phone](https://blog.hansenpartnership.com/securing-a-rooted-android-phone/),
[ConnectCore — Generate AVB keys](https://docs.digi.com/resources/documentation/digidocs/embedded/android/dea11/cc8x/android-trustfence_t_secure-boot-generate-avb-keys),
[avbtool README](https://android.googlesource.com/platform/external/avb/+/master/README.md).

!!! danger "Relock donanıma bağlıdır — her cihazda olmaz"
    Kendi anahtarınızla **geri kilitleme**, ancak cihaz `fastboot flash avb_custom_key`
    ve custom key ile relock'a **izin veriyorsa** mümkündür. Pratikte bunu güvenilir
    biçimde destekleyen cihazlar **Pixel** ve az sayıda modeldir. Samsung/Xiaomi gibi
    çoğu telefon **custom anahtarla relock'a izin vermez** (unlocked kalır, her açılışta
    uyarı; Samsung'da Knox e-fuse yanar). Bu, [Bölüm 6](06-donanim-tedarik.md)'daki cihaz
    seçimini belirleyen faktördür. Ayrıca relock çoğu cihazda **veri bölümünü siler**.

## 3.2 Açılışta kimlik doğrulama ve şifreleme (FBE)

**Amaç:** "Cihaz daha açılırken şifre istesin" ve **şifre girilene kadar veri okunamasın.**

Android 10+ **File-Based Encryption (FBE)** kullanır. İki depolama sınıfı vardır:

- **DE (Device Encrypted):** Boot sonrası hemen erişilebilir (Direct Boot bileşenleri).
- **CE (Credential Encrypted):** **Yalnızca kullanıcı parola/PIN/desen ile kimlik
  doğruladıktan sonra** çözülür. Kullanıcı verisi burada durur.

Parola belirlendiğinde, anahtar TEE (Trusted Execution Environment) tarafından
imzalanan bir anahtarla yeniden şifrelenir; yani doğru kimlik girilene dek veri **matematiksel
olarak** kilitlidir. ([Android — File-Based Encryption](https://source.android.com/docs/security/features/encryption/file-based),
[Samsung Knox — FBE/FDE](https://docs.samsungknox.com/admin/knox-platform-for-enterprise/kbas/kba-360039577713/))

Sıkılaştırılmış cihazda bu; **güçlü parola zorunluluğu**, yanlış deneme limiti,
GrapheneOS'taki gibi **süreli otomatik yeniden başlatma** (veriyi "at rest" durumuna
alır) ve **duress PIN** (girilince cihazı geri dönüşsüz siler) ile güçlendirilir.
([GrapheneOS — Features](https://grapheneos.org/features))

## 3.3 Erişim yüzeyini kapatma

OS sahibi olarak imaj/derleme seviyesinde:

- **ADB / USB hata ayıklama** kapalı (`ro.adb.secure=1`, geliştirici seçenekleri kilitli)
- **Fastboot / kurtarma** erişimi kısıtlı
- **USB veri aktarımı** engelli (yalnızca şarj)
- **OEM unlock** devre dışı (elinizdeki Hikvision DS-MDT202'de olduğu gibi:
  `sys.oem_unlock_allowed=0`)
- Gereksiz **portlar/servisler** kapalı

## 3.4 Yazılım daraltma (debloat) = hem hafiflik hem güvenlik

Gereksiz uygulama/servis **imajdan tamamen çıkarılır** (MDM'deki gibi sadece
gizlenmez). Her çıkarılan bileşen bir saldırı yüzeyi eksiği demektir:

- Tüketici uygulamaları (tarayıcı, mağaza, medya) çıkarılır
- Kullanılmayan sistem servisleri devre dışı
- Signage'de: telefon/SMS/NFC yığınları bile çıkarılabilir

## 3.5 Çekirdek ve çalışma zamanı sıkılaştırma (GrapheneOS referansı)

GrapheneOS, stok AOSP'nin üstüne şunları ekler — bunlar "ne kadar ileri gidilebilir"in
haritasıdır:

- **Hardened malloc:** Bellek bozulması açıklarını sömürmeyi zorlaştıran güvenli bellek
  ayırıcı; ayırma sonrası bellek sıfırlanır (zero-on-free), guard bölgeleri ve rastgele
  canary'ler.
- **Çekirdek sıkılaştırma:** arm64'te 4 seviyeli sayfa tabloları ile yüksek ASLR entropisi;
  bellek etiketleme (memory tagging); serbest bırakılan bellek anında sıfırlanır; zorunlu
  **çekirdek modülü imzalama** (RSA-4096 + SHA-256).
- **Kontrol akışı koruması:** ARMv9'da BTI + PAC + ShadowCallStack.
- **Sandbox:** Güçlendirilmiş **SELinux** ve **seccomp-bpf** politikaları.
- **Dinamik kod yükleme kısıtları:** JIT kapatılıp tam AOT derlemeye geçiş; bellekten/
  depodan dinamik kod yükleme kapatılabilir.
- **İzin denetimleri:** Ağ ve sensör izinlerini uygulama bazında **tamamen kapatma**;
  Storage/Contact "scopes".

Kaynak: [GrapheneOS — Features](https://grapheneos.org/features),
[Synacktiv — hardened_malloc analizi](https://www.synacktiv.com/en/publications/exploring-grapheneos-secure-allocator-hardened-malloc),
[Madaidan — Android security](https://madaidans-insecurities.github.io/android.html).

!!! tip "GrapheneOS neden sadece Pixel?"
    Çünkü Pixel; **custom AVB anahtarıyla relock**, güçlü **secure element (Titan M)**,
    bellek etiketleme ve uzun güvenlik desteği (7 yıl) sunan, bu sıkılaştırmaları
    donanımla destekleyen ender cihazdır. Sizin sıkılaştırma **öğrenme ve doğrulama**
    cihazınız da bu yüzden Pixel olmalı.

## 3.6 Politika (MDM) ile ROM sıkılaştırması nerede ayrışır?

| Teknik | MDM/politika ile? | ROM sahipliğiyle? |
|---|---|---|
| Uygulama gizleme/devre dışı | ✅ (gizlenir) | ✅ (imajdan silinir) |
| Kiosk / tek uygulama | ✅ | ✅ |
| Güçlü parola zorunluluğu | ✅ | ✅ |
| Verified boot + kendi anahtarınla relock | ❌ | ✅ |
| Açılışta pre-boot auth / özel şifreleme akışı | ❌ | ✅ |
| SELinux/seccomp politikası değiştirme | ❌ | ✅ |
| Çekirdek/malloc sıkılaştırma | ❌ | ✅ |
| ADB'yi imaj seviyesinde tamamen kaldırma | ❌ | ✅ |

Bu tablo, işverenin "politikadan daha sıkı, cihaz olarak sıkılaştırılmış" isteğinin
**neden ROM sahipliği gerektirdiğini** özetler.

---

!!! note "Bu bölümün kaynakları"
    - [Android — Verified Boot (AVB)](https://source.android.com/docs/security/features/verifiedboot/avb)
    - [Android — File-Based Encryption](https://source.android.com/docs/security/features/encryption/file-based)
    - [GrapheneOS — Features](https://grapheneos.org/features)
    - [James Bottomley — Securing a Rooted Android Phone](https://blog.hansenpartnership.com/securing-a-rooted-android-phone/)
    - [avbtool README (AVB 2.0)](https://android.googlesource.com/platform/external/avb/+/master/README.md)
    - [Synacktiv — hardened_malloc](https://www.synacktiv.com/en/publications/exploring-grapheneos-secure-allocator-hardened-malloc)
    - [Madaidan's Insecurities — Android](https://madaidans-insecurities.github.io/android.html)
    - [Samsung Knox — FBE/FDE](https://docs.samsungknox.com/admin/knox-platform-for-enterprise/kbas/kba-360039577713/)
