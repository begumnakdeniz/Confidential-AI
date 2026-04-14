[README.md](https://github.com/user-attachments/files/26723432/README.md)
# Sırdaş Yapay Zeka · Sektörel PII Maskeleme

> Client-side PII detection and masking tool for Healthcare, Banking, and Chatbot/AI sectors.  
> All processing happens entirely in the browser — no data ever leaves the device.

![Hero](screenshots/01-hero.png)

---

## İçindekiler

- [Nedir?](#nedir)
- [Özellikler](#özellikler)
- [Sektör Modülleri](#sektör-modülleri)
- [Nasıl Çalışır?](#nasıl-çalışır)
- [Ekran Görüntüleri](#ekran-görüntüleri)
- [Versiyon Geçmişi](#versiyon-geçmişi)
- [Kullanım](#kullanım)
- [Teknik Detaylar](#teknik-detaylar)
- [Lisans](#lisans)

---

## Nedir?

**Sırdaş Yapay Zeka**, metinlerdeki kişisel verileri (PII — Personally Identifiable Information) tespit edip, her birini benzersiz bir token ile değiştiren ve SHA-256 ile hashlayan bir araçtır. Sağlık, Bankacılık ve Chatbot/AI olmak üzere üç sektöre özel tespit motoru içerir.

Kullanım amacı: hasta kayıtlarını, finansal işlem verilerini veya kullanıcı mesajlarını herhangi bir üçüncü taraf AI API'sine göndermeden önce güvenli hale getirmek.

---

## Özellikler

- 🔍 **Sektörel tespit** — Her sektörün ihtiyacına göre özelleştirilmiş detector pipeline
- 🎭 **Token maskeleme** — `[PERSON_1]`, `[IBAN_CODE_1]` gibi tip bilgisi taşıyan tokenlar
- 🔐 **SHA-256 hashleme** — Native Web Crypto API ile, her değer için kısa ve benzersiz hash
- 🗄 **Mapping Vault** — Token → orijinal değer eşlemesi sadece lokal bellekte, buluta gitmez
- 📋 **Üç çıktı görünümü** — Tespit vurgusu, maskeli metin (kopyalamaya hazır), vault tablosu
- ✅ **Sıfır bağımlılık** — Framework yok, build adımı yok, kurulum yok
- 🇹🇷 **Türkçe karakter desteği** — Ç, Ğ, İ, Ö, Ş, Ü tüm regex pattern'larında desteklenir

---

## Sektör Modülleri

### 🏥 Sağlık

**Nerede kullanılır:** Hastane bilgi sistemleri, e-Nabız entegrasyonları, klinik karar destek yazılımları  
**Neden gerekli:** KVKK ve HIPAA kapsamında tıbbi veriler özel kategori kişisel veri sayılır  
**Nasıl çalışır:** Anamnez notları, laboratuvar sonuçları ve reçeteler AI'ya iletilmeden önce anonimleştirilir

| Veri Tipi | Açıklama |
|-----------|----------|
| 👤 Hasta / Hekim Adı | İsim soyisim tespiti |
| 🪪 T.C. Kimlik No | 11 haneli TC kimlik numarası |
| 📅 Tarih | Doğum tarihi, muayene tarihi (GG.AA.YYYY) |
| 🩺 Tanı Kodu (ICD-10) | `J06.9`, `L4-L5` gibi ICD formatları |
| 💊 İlaç Adı & Dozu | Voltaren 75mg, Amoxicillin 500mg 3x1 |
| 🩸 Kan Grubu | A Rh+, 0 Rh-, AB+ formatları |
| 🆔 Hasta / Poliklinik No | PKL-, HST-, MRN- prefix'li kayıt numaraları |
| 📞 Telefon | Türkiye formatlı telefon numaraları |
| 📧 E-posta | Standart e-posta adresleri |
| 📍 Konum / Hastane | Şehir ve hastane isimleri |

---

### 🏦 Bankacılık

**Nerede kullanılır:** Bankacılık uygulamaları, kredi değerlendirme sistemleri, müşteri hizmetleri chatbotları  
**Neden gerekli:** BDDK ve PCI-DSS yönetmelikleri — finansal veri ihlalleri ağır cezai yaptırım doğurur  
**Nasıl çalışır:** Müşteri şikayetleri ve işlem kayıtları AI ile analiz edilmeden önce tüm finansal veriler maskelenir

| Veri Tipi | Açıklama |
|-----------|----------|
| 👤 Müşteri Adı | Context-aware prefix tespiti (Müşteri: X, Başvuran: X) |
| 🪪 T.C. Kimlik | 11 haneli TC kimlik numarası |
| 🏦 IBAN | TR prefix'li Türk IBAN formatı |
| 🏧 Hesap / Kart No | 10-16 haneli hesap ve kart numaraları |
| 💰 Tutar (16 para birimi) | TL ₺ USD $ EUR € GBP £ CHF JPY SAR AED CAD AUD CNY RUB |
| 🆔 Banka Müşteri No | 25+ Türk bankası için özel prefix pattern'ları (aşağıya bkz.) |
| 🌐 IP Adresi | IPv4 formatı |
| 🧾 İşlem / Referans No | TXN-, EFT-, HAV-, REF- prefix'li kodlar |
| 📞 Telefon | Türkiye formatlı telefon numaraları |
| 📧 E-posta | Standart e-posta adresleri |

**Desteklenen Türk Bankaları (Müşteri No Tespiti):**

| Banka | Prefix |
|-------|--------|
| Ziraat Bankası | Standalone 10 hane |
| İş Bankası | `CIF-` |
| Garanti BBVA | `GRT-` |
| Akbank | `AKB-` |
| YapıKredi | `YKB-`, `YKN-` |
| Halkbank | `HLK-`, `HAL-` |
| Vakıfbank | `VKF-`, `VKB-` |
| QNB Finansbank | `FNB-`, `QNB-` |
| DenizBank | `DNZ-` |
| TEB | `TEB-` |
| ING Bank | `ING-` |
| HSBC | `HSB-`, `HST-` |
| Odeabank | `ODB-` |
| Fibabanka | `FIB-` |
| Şekerbank | `SKR-`, `SKB-` |
| Burgan Bank | `BRG-` |
| Anadolubank | `ADB-`, `AND-` |
| Alternatif Bank | `ALT-` |
| Kuveyt Türk | `KUV-`, `KT-` |
| Albaraka Türk | `ALB-` |
| Türkiye Finans | `TRF-` |
| Ziraat Katılım | `ZKT-` |
| Vakıf Katılım | `VKT-` |
| Emlak Katılım | `EKT-` |
| PTT Bank | `PTT-` |
| Türk Eximbank | `EXM-` |

---

### 🤖 Chatbot & AI

**Nerede kullanılır:** Müşteri hizmetleri chatbotları, AI asistanlar, otomatik e-posta yanıtlayıcılar  
**Neden gerekli:** Kullanıcı mesajları üçüncü taraf LLM API'lerine gönderilmeden önce PII temizlenmeli  
**Nasıl çalışır:** Gerçek zamanlı mesaj akışında isim, telefon, konum gibi veriler otomatik maskelenerek AI'ya iletilir

| Veri Tipi | Açıklama |
|-----------|----------|
| 👤 Kullanıcı Adı | İsim soyisim |
| 📞 Telefon | Türkiye formatlı telefon numaraları |
| 📧 E-posta | Standart e-posta adresleri |
| 📍 Konum | Şehir, semt isimleri |
| 🪪 T.C. Kimlik | 11 haneli TC kimlik numarası |
| 🌐 URL / Web | http/https ve www. adresleri |
| 📮 Posta Kodu | 5 haneli posta kodu |
| 🖥️ IP Adresi | IPv4 formatı |
| 📦 Sipariş No | SP-, SIP-, ORD- prefix'li kodlar |

---

## Nasıl Çalışır?

```
Ham Metin
    │
    ▼
┌─────────────────────────┐
│   Sektör Detector       │  ← Sektöre özel regex pipeline
│   Pipeline              │
└────────────┬────────────┘
             │  Tespit edilen entity listesi
             ▼
┌─────────────────────────┐
│   Overlap Resolution    │  ← Çakışan tespitler önceliğe göre çözülür
└────────────┬────────────┘
             │
             ▼
┌─────────────────────────┐
│   Token Ataması         │  ← [PERSON_1], [IBAN_CODE_1] ...
│   SHA-256 Hashleme      │  ← Web Crypto API (native browser)
└────────────┬────────────┘
             │
    ┌────────┴────────┐
    ▼                 ▼
Maskeli Metin     Mapping Vault
(AI'ya gönderilir) (Sadece lokal bellek)
```

**Örnek dönüşüm:**

```
GİRDİ:  Hasta: Ahmet Kaya, T.C. 34501234560, İlaç: Voltaren 75mg
ÇIKTI:  Hasta: [PERSON_1]#7B6AAE08, T.C. [NRP_1]#C8629239, İlaç: [DRUG_1]#5AA91422
```

---

## Ekran Görüntüleri

### Sektör Seçimi
Uygulama açıldığında üç sektör kartı karşılar. Her kart, o sektörde tespit edilen veri tiplerini gösterir.

![Sector Selection](screenshots/02-sector-select.png)

---

### Sektör Bilgi Çubuğu & Giriş Alanı
Sektör seçilince "Nerede / Neden / Nasıl" bilgi çubuğu açılır. Hazır örnek metinler tek tıkla yüklenir.

![Input Area](screenshots/03-input.png)

---

### Maskeli Çıktı
Analiz sonucunda tüm PII'lar renkli tokenlarla değiştirilir. Her token yanında SHA-256 hash kısa kodu gösterilir. Metin doğrudan AI API'ye gönderilebilir hale gelir.

![Masked Output](screenshots/04-masked-output.png)

---

### Mapping Vault
Vault sekmesinde her token için orijinal değer, veri tipi ve tam SHA-256 hash görüntülenir. Bu tablo yalnızca tarayıcı belleğinde tutulur, hiçbir yere gönderilmez.

![Vault](screenshots/05-vault.png)

---

## Versiyon Geçmişi

| Versiyon | Açıklama |
|----------|----------|
| `v1` | Temel PII tespiti — isim, telefon, e-posta |
| `v2` | IBAN, T.C. Kimlik, konum ve pasaport eklendi |
| `v3` | SHA-256 hashleme, Mapping Vault görünümü, 5 örnek metin |
| `v4 (sektörel)` | Sektörel mimari — Sağlık, Bankacılık, Chatbot/AI modülleri. 25+ Türk bankası müşteri no desteği, 16 para birimi, ICD-10 tanı kodları, ilaç & kan grubu tespiti |

---

## Kullanım

Herhangi bir `.html` dosyasını tarayıcıda açın. Kurulum gerekmez.

```bash
# Repoyu klonla
git clone https://github.com/kullanici-adi/sirdas-yapay-zeka.git

# İstediğin versiyonu aç
open v4-sektörel/sirdas-sektorel.html
```

veya dosyayı direkt tarayıcıya sürükleyin.

---

## Teknik Detaylar

| Başlık | Detay |
|--------|-------|
| Dil | Vanilla HTML / CSS / JavaScript |
| Hashleme | `window.crypto.subtle.digest("SHA-256")` — Native Web Crypto API |
| Çerçeve | Yok — sıfır bağımlılık |
| Build | Yok — tek dosya, direkt çalışır |
| Veri akışı | Tamamen client-side, ağ isteği yok |
| Türkçe desteği | `[A-ZÇĞİÖŞÜ][a-zçğıöşü]` pattern'ları tüm detector'larda |
| Overlap çözümü | Yüksek öncelikli detector kazanır (URL > EMAIL > IBAN > ...) |
| Token formatı | `[TIP_N]#HASH8` — ör. `[PERSON_1]#7B6AAE08` |

---

## Lisans

[GNU GPL v3](LICENSE)
