#  LightGBM ile Telekomünikasyon Sektöründe Müşteri Kaybı (Churn) Tahmin Modeli

## Proje Özeti (Business Case)

Bu projenin temel amacı, telekomünikasyon sektörüne ait müşteri verilerini kullanarak, bir müşterinin şirketten **ayrılma (churn)** olasılığını yüksek doğrulukla önceden tahmin etmektir. Erken ve doğru tahmin, şirketin yüksek riskli müşterilere **proaktif müdahale stratejileri** uygulamasını ve müşteri kaybından kaynaklanan gelirin azalmasını önlemesini sağlayacaktır.

---

## ⚙️ Metodoloji ve Kullanılan Teknikler

Bu analiz, sadece temel bir modelleme çalışması değil, aynı zamanda **İş Alanı Bilgisi (Domain Knowledge)** ve gelişmiş makine öğrenimi tekniklerini birleştiren bütünsel bir veri bilimi projesidir.

### 1. Veri Kaynağı ve Ön İşleme
* **Veri Seti:** Yaygın olarak kullanılan Telco Müşteri Kaybı veri seti kullanılmıştır.
* **Kayıp Değer Yönetimi:** `TotalCharges` sütunundaki eksik değerler (NaN), veri setinin **medyanı** ile doldurularak modelleme sürecine hazır hale getirilmiştir.
* **Kategorik Dönüşüm:** Tüm kategorik değişkenler, modelin işleyebileceği formata (`pd.get_dummies`) dönüştürülmüştür.

### 2. İleri Düzey Öznitelik Mühendisliği (Feature Engineering)
Modelin tahmin gücünü artırmak ve iş dünyası için anlamlı sinyaller üretmek amacıyla 8 yeni özellik oluşturulmuştur:
* `AvgMonthlyCharge`: Müşterinin ortalama aylık harcaması (`TotalCharges` / `tenure`).
* `RiskyPayment`: Kağıtsız fatura kullanan ancak otomatik ödeme yapmayan müşteriler (yüksek riskli ödeme davranışı).
* `tenure_group`: Kalıcı sürenin 12 aylık gruplara ayrılması.
* `RiskyFiber`: Fiber Optik kullanan ve Aylık Sözleşmeli müşteriler (en riskli grup).

### 3. Modelleme
* **Algoritma:** Yüksek performanslı ve hız sunan **LightGBM (Light Gradient Boosting Machine)** algoritması seçilmiştir.
* **Sınıf Dengesizliği Çözümü:** Churn (Ayrılan) sınıfının azınlıkta olması nedeniyle, model **`class_weight='balanced'`** parametresiyle eğitilerek dengesizlik problemi giderilmiş ve Ayrılan müşterileri yakalama yeteneği maksimize edilmiştir.
* **Optimizasyon:** Aşırı öğrenmeyi (Overfitting) engellemek için **Erken Durdurma (Early Stopping)** mekanizması kullanılmıştır.

---

## Model Sonuçları ve İş Çıkarımları

Model, projenin hedefini karşılayan güçlü bir performans sergilemiştir.

### Ana Performans Metrikleri
| Metrik | Değer | İş Anlamı |
| :--- | :--- | :--- |
| **ROC AUC Skoru** | **0.8411** | Modelin, ayrılan ve kalan müşterileri ayırt etme gücü mükemmeldir. |
| **Recall (Ayrılan Sınıfı)** | **0.78** | Gerçekten ayrılacak olan müşterilerin %78'i model tarafından doğru bir şekilde tespit edilebilmektedir. **Bu, müşteri kaybını önleme potansiyelini gösterir.** |
| **Accuracy (Genel Doğruluk)** | **%76.22** | Tüm tahminlerin genel doğruluk oranı. |

### En Önemli Kayıp Faktörleri (Feature Importance)

Aşağıdaki grafik, modelin tahmin yaparken en çok hangi özelliklere ağırlık verdiğini göstermektedir. 

**En Kritik Faktörler (İş Odaklı Öncelikler):**
1.  **TotalCharges** ve **tenure (Kalıcı Süre):** Müşterinin finansal değeri ve bağlılığı, kaybı tahmin etmedeki en güçlü sinyallerdir. Kısa süreler ve düşük toplam harcama en büyük riski işaret eder.
2.  **MonthlyCharges (Aylık Ücretler):** Yüksek faturaya sahip müşteriler, fiyat hassasiyeti nedeniyle yüksek risk altındadır.
3.  **AvgMonthlyCharge ve RiskyPayment:** Geliştirilen bu iki özellik, yüksek riskli ödeme davranışını ve müşteri başına maliyet verimliliğini göstererek modelin hassasiyetini artırmıştır.

---
