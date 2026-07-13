# 1. Proje Bağlamı ve Hedefler

## 1.1 Mevcut durum: Politika katmanındayız

MobileITM bugün bir **MDM firması** olarak çalışıyor. Projeniz cihaza **Device Owner**
olarak kurulur ve Android'in **DevicePolicyManager** API'ları üzerinden politika uygular:
uygulama kısıtlama, kiosk modu, uzaktan kilit/sıfırlama, ayar kontrolü vb.

Bu, Android'in **en üst kontrol katmanıdır** ama bir tavanı vardır: siz **başkasının
işletim sisteminin misafirisiniz**. İşletim sistemini üreten (Google + OEM) neye izin
verdiyse onu yapabilirsiniz. Bir uygulamayı "kaldırırsınız" ama kod imajda kalır ve
fabrika ayarında geri gelir; boot zincirine, çekirdeğe, sistem servislerine dokunamazsınız.

## 1.2 İşverenin istediği: Bir üst katman — OS'un sahipliği

İşverenin verdiği örneklerin hepsi aslında **tek bir şeyin** farklı görünümleri:

> **Politikayla değil, işletim sisteminin kendisine sahip olarak cihaz üretmek.**

Somut örnekler ve karşılık geldikleri teknik yetenek:

| İşverenin ifadesi | Teknik karşılığı | Neden politikayla olmaz? |
|---|---|---|
| "Politikadan daha sıkı, cihaz olarak sıkılaştırılmış" | Verified Boot + kendi anahtarınla relock, SELinux, ADB kapatma | Boot zinciri ve çekirdek OS sahibine ait |
| "Açılırken şifre isteyen cihaz" | Pre-boot auth + Credential Encrypted storage | Boot/şifreleme katmanı imajda tanımlı |
| "Samsung SDK'sı gibi kendi API'miz" | Kendi **system service** + platform imzalı SDK | Sistem servisi platform anahtarı ister |
| "Hafif Android OS + MobileITM markası (signage)" | Debloat edilmiş AOSP imajı + kendi launcher | Uygulama imajdan çıkarılır, gizlenmez |
| "General Mobile'dan ROM + imza alıp sıkılaştıran firma" | OEM'den BSP + imza anahtarı lisanslama | İmza anahtarı olmadan sistem imzalanamaz |
| "Cihazdan para kazanmak (reklam döndürme)" | Kendi OS + kendi içerik/yönetim düzlemi | Ürün + abonelik modeli |

## 1.3 Kritik kavram ayrımı: üç farklı eksen

İşveren bunları bazen aynı anlamda kullanıyor, ama **teknik olarak üç ayrı eksendir**.
Doğru ürünü tasarlamak için ayırmak şart:

=== "Sıkılaştırma (Hardened)"

    **Ne:** Güvenlik. Saldırı yüzeyini daraltmak, OS'u kurcalanamaz kılmak.

    **Örnekler:** Verified boot + relock, açılışta parola, ADB/USB kapalı, SELinux
    sıkılaştırma, root yok, ekran görüntüsü engeli.

    **Ürün:** Kurumsal güvenli telefon, savunma/saha cihazı.

=== "Hafiflik (Lightweight)"

    **Ne:** Ayak izini/kaynak kullanımını azaltmak. Gereksiz bileşenleri imajdan çıkarmak.

    **Örnekler:** Play Services yok, tüketici uygulamaları yok, minimal servis seti,
    düşük RAM/depolama ayak izi.

    **Ürün:** Signage kutusu, tek-amaçlı IoT cihazı.

=== "Kilitleme (Locked / Kiosk)"

    **Ne:** Cihazı tek amaca kilitlemek; kullanıcı dışarı çıkamaz.

    **Örnekler:** Tek uygulama modu (lock task), özel launcher, ayarlara erişim yok,
    fiziksel tuş kısıtları.

    **Ürün:** Hem signage hem kurumsal telefon (ikisinde de var).

!!! tip "Örtüşme var ama eşit değil"
    Bir uygulamayı imajdan çıkarmak hem **hafifletir** hem **saldırı yüzeyini azaltır**.
    Ama bir telefon **tam özellikli** olup yine de **sıkılaştırılmış** olabilir; bir
    signage kutusu **hafif ve kilitli** ama kriptografik olarak çok da "sıkı" olmayabilir.
    Ürün tanımında hangi ekseni önceliklendirdiğinizi netleştirin.

## 1.4 Ortak payda ve raporun tezi

Üç eksenin de ortak paydası aynı: **OS katmanının size ait olması.** Bu rapor şu tezi
savunur:

1. Bu teknik olarak mümkün ve oturmuş bir yoldur (bkz. [Bölüm 2](02-custom-rom-nasil-yapilir.md)).
2. Ama iki sert engel vardır — **GMS lisanslama** ([Bölüm 5](05-lisanslama-gms-edla.md))
   ve **sürücü/BSP** ([Bölüm 6](06-donanim-tedarik.md)) — ve bu engeller **cihaz seçimini**
   belirler.
3. Bu yüzden en akılcı giriş, engellerin en zayıf olduğu üründür: **hafif + kilitli
   signage OS** (bkz. [Bölüm 7](07-urun-senaryolari.md)).

## 1.5 MobileITM'in elindeki avantaj

Bu işe sıfırdan girmiyorsunuz. En pahalı yazılım parçası olan **uzaktan cihaz yönetimi
(MDM backend + ajan)** zaten sizde. Custom ROM'a geçtiğinizde bu ajan artık normal bir
uygulama değil, **sistem uygulaması (priv-app)** olur: silinemez, öldürülemez, platform
yetkisinde çalışır. Yani ROM işi, mevcut ana varlığınızı (MDM) daha güçlü bir zemine
taşıma fırsatıdır — yeni bir işe başlangıç değil.

---

!!! note "Bu bölümün kaynakları"
    - Android DevicePolicyManager / Device Owner kavramları — [Android Enterprise FAQ, Jason Bayton](https://bayton.org/android/android-enterprise-faq/)
    - Politika vs OS sahipliği ayrımı — bu sohbette yapılan canlı analize dayanır (bkz. giriş).
