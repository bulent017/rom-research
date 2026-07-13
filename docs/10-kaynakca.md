# 10. Kaynakça

Bu rapor, 2026-07 itibarıyla erişilen aşağıdaki açık kaynaklara dayanır. Lisans koşulları,
fiyatlar ve süreçler zamanla değişebilir; bağlayıcı kararlar öncesi ilgili tarafla (Google,
OEM, ODM) doğrudan teyit alınmalıdır.

## Resmî AOSP / Android dokümanları

- [Android — Kurulum / Genel bakış](https://source.android.com/docs/setup/about)
- [Android — Build numbers (branch/etiket referansı)](https://source.android.com/docs/setup/reference/build-numbers)
- [Android — Sign builds for release](https://source.android.com/docs/core/ota/sign_builds)
- [Android — OTA updates](https://source.android.com/docs/core/ota)
- [Android — Verified Boot (AVB)](https://source.android.com/docs/security/features/verifiedboot/avb)
- [Android — AVB 2.0 / avbtool README](https://android.googlesource.com/platform/external/avb/+/master/README.md)
- [Android — File-Based Encryption (FBE)](https://source.android.com/docs/security/features/encryption/file-based)
- [Android — Vendor API level](https://source.android.com/docs/core/architecture/api-flags)
- [Android — Implement the service](https://source.android.com/docs/core/architecture/configuration/archive/service)

## Custom ROM süreci ve imzalama

- [Creating Release Keys and Signing Builds (wladimir-tm4pda)](https://wladimir-tm4pda.github.io/porting/release_keys.html)
- [Guide To Sign Android Build With Private Release Keys (gist)](https://gist.github.com/neilchetty/22ae18f5404c460de2a9372812803026)
- [dhina17 — How to start Android custom ROM development](https://blog.dhina17.dev/how-to-start-android-custom-rom-development)
- [emteria — Building an AOSP ROM](https://emteria.com/blog/aosp-rom)
- [Vibrothermography — AOSP with a locked bootloader](https://thermal.cnde.iastate.edu/aosp_build_instructions.xhtml)

## Sıkılaştırma (hardening)

- [GrapheneOS — Features](https://grapheneos.org/features)
- [James Bottomley — Securing a Rooted Android Phone](https://blog.hansenpartnership.com/securing-a-rooted-android-phone/)
- [James Bottomley — Android Verified Boot yazıları](https://blog.hansenpartnership.com/tag/android-verified-boot/)
- [Synacktiv — Exploring GrapheneOS hardened_malloc](https://www.synacktiv.com/en/publications/exploring-grapheneos-secure-allocator-hardened-malloc)
- [Madaidan's Insecurities — Android](https://madaidans-insecurities.github.io/android.html)
- [ConnectCore — Generate AVB keys](https://docs.digi.com/resources/documentation/digidocs/embedded/android/dea11/cc8x/android-trustfence_t_secure-boot-generate-avb-keys)
- [Samsung Knox — FBE/FDE](https://docs.samsungknox.com/admin/knox-platform-for-enterprise/kbas/kba-360039577713/)

## Kendi SDK / API katmanı

- [Samsung Knox — About the Knox SDK](https://docs.samsungknox.com/dev/knox-sdk/about-the-knox-sdk/)
- [Knox Standard SDK (SEAP)](https://seap.samsung.com/sdk/knox-standard-android)
- [Knox — Networking Framework mimarisi](https://seap.samsung.com/html-docs/android-vpn-service/Content/framework-architecture.htm)
- [Building a Custom System Service in Android 15 (AOSP) — Medium](https://medium.com/@aruncse2k20/building-a-custom-system-service-in-android-15-aosp-4ff26fcb1dbb)
- [AOSP – Creating a System Service (devarea)](https://devarea.com/aosp-creating-a-system-service/)
- [NXP Community — add custom system service to AOSP](https://community.nxp.com/t5/i-MX-Processors/How-to-add-custom-system-service-to-AOSP/m-p/927478)

## OEMConfig / Android Enterprise

- [Google — Use OEMConfig](https://support.google.com/work/android/answer/9388447)
- [Zebra — OEMConfig FAQ](https://techdocs.zebra.com/oemconfig/11-5/faq/)
- [Microsoft Intune — OEMConfig kullanımı](https://learn.microsoft.com/en-us/intune/device-configuration/templates/configure-oemconfig-android)
- [Android Management API — enterprises.policies](https://developers.google.com/android/management/reference/rest/v1/enterprises.policies)
- [Jason Bayton — Android Enterprise FAQ](https://bayton.org/android/android-enterprise-faq/)

## Lisanslama (GMS / MADA / EDLA)

- [Jason Bayton — Certifying Android devices](https://bayton.org/blog/2024/01/certifying-android-devices/)
- [Jason Bayton — GMS vs EDLA](https://bayton.org/android/android-enterprise-faq/gms-vs-edla-enterprise/)
- [BenQ — What is Google EDLA](https://www.benq.com/en-us/education/edtech-blog/what-is-google-edla.html)
- [Deeplight — GMS & Android 14 EDLA](https://www.dlcen.com/edla/759.html)
- [emteria — GMS certification guide](https://emteria.com/learn/google-mobile-services)
- [einfochips — How to obtain GMS license](https://www.einfochips.com/blog/how-to-obtain-googles-gms-license-for-android-devices/)
- [Zhongle — GMS certification](https://www.zhongletest.com/en/certification/177.html)
- [Android Certified Partners](https://www.android.com/certified/partners/)

## Donanım / ODM / BSP

- [Sunchip — RK3568 Android board](https://www.sunchip-tech.com/products/rk3568-android-board/)
- [Sunchip — RK3588 Android board](https://www.sunchip-tech.com/products/rk3588-android-board/)
- [Geniatech RK3588 quad-display signage player](https://aijourn.com/geniatech-introduces-rk3588-quad-display-signage-player/)
- [Rikomagic DS08 — RK3588 8K signage (CNX Software)](https://www.cnx-software.com/2025/12/05/rikomagic-ds08-rockchip-rk3588-powered-8k-digital-signage-player-with-rtc-and-watchdog/)
- [Ranboda — Rockchip TV box](https://www.ranboda.com/product-category/tv-box/rockchip-tv-box/)
- [TinkerBoard — RK3588 device repo (GitHub)](https://github.com/TinkerBoard-Android/rockchip-android-device-rockchip-rk3588)
- [rockchip-linux/kernel (GitHub)](https://github.com/rockchip-linux/kernel)
- [FriendlyELEC — FriendlyThings for Rockchip](https://wiki.friendlyelec.com/wiki/index.php/FriendlyThings_for_Rockchip)
- [Collabora — Open-source boot chain for RK3588](https://www.collabora.com/news-and-blog/blog/2024/02/21/almost-a-fully-open-source-boot-chain-for-rockchips-rk3588/)
- [Android Enterprise — Device manufacturers dizini](https://androidenterprisepartners.withgoogle.com/device-manufacturers/)
- [Google — Resources for OEMs and carriers](https://developers.google.com/android/work/oem-resources)

## OTA / filo yönetimi

- [Esper — How Android OTA updates work](https://www.esper.io/blog/firmware-over-the-air-fota-updates-on-android)
- [Custota — A/B OTA updater (GitHub)](https://github.com/chenxiaolong/Custota)
- [avbroot — custom key OTA re-signing (GitHub)](https://github.com/chenxiaolong/avbroot)
- [OpenOTA (GitHub)](https://github.com/mitchellurgero/OpenOTA)

## İşverenin paylaştığı kaynaklar

- [LinkedIn — Android + AOSP + AI Learning Roadmap 2026 (Ankita Vamja)](https://www.linkedin.com/pulse/android-aosp-ai-learning-roadmap-2026-ankita-vamja-mdxgc/)
- [Android — Kurulum / Genel bakış (TR)](https://source.android.com/docs/setup/about?hl=tr)
- [dhina17 — How to start Android custom ROM development](https://blog.dhina17.dev/how-to-start-android-custom-rom-development)
- [emteria — AOSP ROM](https://emteria.com/blog/aosp-rom)

---

!!! warning "Doğrulama notu"
    Fiyat, süre ve lisans koşullarına dair rakamlar (ör. NRE ~$85k, EDLA ~6–8 hafta,
    Google ~3 yıl yama desteği) açık kaynaklara dayalı **yaklaşık değerlerdir**. Bağlayıcı
    planlama öncesi Google Android Partner ekibi ve hedef OEM/ODM ile doğrudan
    doğrulanmalıdır.
