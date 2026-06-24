# CVMatch – Akıllı CV ve İş İlanı Eşleştirme Sistemi

CV'nizi yapay zeka ile analiz eden, iş ilanlarıyla eşleştiren, ATS uyumluluğunu ölçen ve optimize edilmiş CV çıktısı üreten bir web uygulaması.

---

## Ne Yapar?

- PDF formatındaki CV'den metni otomatik olarak çıkarır
- OpenRouter API üzerinden yapay zeka ile CV ve iş ilanını analiz eder
- Zorunlu ve tercih edilen beceriler için ayrı ayrı eşleştirme yapar
- Beceri, deneyim ve eğitim kategorilerine göre ağırlıklı ATS uyum skoru hesaplar
- Eksik becerileri tespit eder, her biri için öğrenme önerileri sunar
- CV'yi iş ilanına göre ATS uyumlu biçimde optimize eder
- Optimize edilmiş CV'yi DOCX formatında dışa aktarır

---

## Proje Yapısı

```
root/
├── ats_analyzer.py      → Tüm uygulama (tek dosya)
└── requirements.txt   → Gerekli Python kütüphaneleri
```

---

## Dosya Ne İş Yapar?

### `ats_analyzer.py`

Tüm uygulama tek bir dosyada toplanmıştır. İçerdiği fonksiyonlar:

| Fonksiyon | Açıklama |
|-----------|----------|
| `extract_text_from_pdf()` | PyMuPDF ile PDF'ten ham metin çıkarır |
| `call_openrouter()` | OpenRouter API'ye istek gönderir, rate limit durumunda otomatik retry yapar |
| `extract_cv_data()` | CV metninden beceri, deneyim, eğitim ve dil bilgilerini çıkarır |
| `extract_job_data()` | İş ilanından zorunlu ve tercih edilen becerileri çıkarır |
| `calculate_ats_score()` | rapidfuzz ile fuzzy matching kullanarak ağırlıklı ATS skoru hesaplar |
| `find_missing_skills()` | Eksik beceriler için AI destekli öğrenme önerileri üretir |
| `optimize_cv()` | CV'yi iş ilanına göre ATS uyumlu hale getirir |
| `generate_docx()` | python-docx ile stillendirilmiş DOCX dosyası oluşturur |
| `render_dashboard()` | Metrik kartları, renkli badge'ler ve tablo içeren analiz panelini render eder |
| `main()` | 4 sekmeli Streamlit arayüzünü yönetir |

---

## Skor Hesaplama

ATS skoru 4 kategoride ağırlıklı olarak hesaplanır:

| Kategori | Ağırlık | Açıklama |
|----------|---------|----------|
| Zorunlu Beceri Eşleşmesi | %55 | İş ilanındaki zorunlu becerilerle eşleşme oranı |
| Tercih Edilen Beceriler | %15 | Tercih edilen becerilerle eşleşme oranı |
| Deneyim | %20 | CV'deki deneyim yılının ilanda istenenle karşılaştırması |
| Eğitim | %10 | Eğitim seviyesinin ilandaki gereksinimle karşılaştırması |

Skor renklendirmesi:

- 🟢 **80 ve üzeri** → Güçlü uyum
- 🟡 **60–79** → Optimize edilebilir
- 🔴 **0–59** → Optimize edilmeli

---

## Kurulum

**1. Repoyu klonla:**
```bash
git clone https://github.com/denzworks/cvmatch.git
cd cvmatch
```

**2. Kütüphaneleri yükle:**
```bash
pip install -r requirements.txt
```

**3. API anahtarını ayarla:**

[openrouter.ai/keys](https://openrouter.ai/keys) adresine ücretsiz kayıt ol ve API anahtarını kopyala.

Ortam değişkeni olarak tanımla (önerilen):
```bash
# Windows
set OPENROUTER_API_KEY=sk-or-v1-...

# Linux / macOS
export OPENROUTER_API_KEY=sk-or-v1-...
```

Alternatif olarak uygulamayı başlattıktan sonra sol panelden de girebilirsin.

**4. Uygulamayı başlat:**
```bash
streamlit run ats_analyzer.py
```

---

## Kullanım

1. Sol panelden OpenRouter API Key'ini gir (ortam değişkeni tanımladıysan otomatik gelir)
2. **CV Yükle & Analiz** sekmesinde PDF formatındaki CV'ni yükle
3. İş ilanı metnini metin kutusuna yapıştır
4. **Analiz Et** butonuna bas (~30–60 saniye bekle)
5. **Analiz Sonuçları** sekmesinden ATS skorunu ve eşleşme detaylarını incele
6. **CV Optimizasyonu** sekmesinden **CV'yi Optimize Et** butonuna bas
7. **Export** sekmesinden DOCX olarak indir

---

## Sekmeler

| Sekme | İçerik |
|-------|--------|
| 📤 CV Yükle & Analiz | PDF yükleme, iş ilanı girişi, analiz başlatma |
| 📊 Analiz Sonuçları | ATS skoru, eşleşen/eksik beceriler, skor tablosu, öğrenme önerileri |
| ✨ CV Optimizasyonu | AI destekli optimize edilmiş CV önizlemesi |
| 📥 Export | DOCX indirme, metin kopyalama |

---

## Gereksinimler

- Python 3.8+
- İnternet bağlantısı (API çağrısı için)
- OpenRouter hesabı (ücretsiz)

### Kullanılan Kütüphaneler

```
streamlit>=1.45.0
requests>=2.32.0
PyMuPDF>=1.26.0
pandas>=2.3.0
numpy>=2.3.0
python-docx>=1.2.0
rapidfuzz>=3.14.0
```

---

## Kullanılan Model

Varsayılan model: `openrouter/free`

OpenRouter üzerinden erişilen bu model ücretsiz kullanılabilir. Rate limit durumunda uygulama otomatik olarak bekleyip yeniden dener.

---

## Notlar

- Uygulama yalnızca metin tabanlı PDF'leri okuyabilir. Tarayıcıyla oluşturulmuş görüntü tabanlı PDF'lerde metin çıkarılamaz.
- Ücretsiz model rate limit'i dakikada birkaç istekle sınırlıdır. Analiz sırasında 3 ayrı API çağrısı yapıldığından her istek arasına otomatik bekleme eklenmektedir.
- API anahtarını ortam değişkeni olarak tanımlamak, her oturumda yeniden girmek zorunda kalmamayı sağlar.
- Optimize edilmiş CV'yi DOCX olarak indirmeden önce adınızı Export sekmesindeki alana girmeniz dosya başlığını kişiselleştirir.
