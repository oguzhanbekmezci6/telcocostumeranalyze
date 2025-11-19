#  LightGBM ile Telekomünikasyon Sektöründe Müşteri Kaybı (Churn) Tahmin Modeli

## Proje Özeti (Business Case)

Bu projenin temel amacı, telekomünikasyon sektörüne ait müşteri verilerini kullanarak, bir müşterinin şirketten **ayrılma (churn)** olasılığını yüksek doğrulukla önceden tahmin etmektir. Erken ve doğru tahmin, şirketin yüksek riskli müşterilere **proaktif müdahale stratejileri** uygulamasını ve müşteri kaybından kaynaklanan gelirin azalmasını önlemesini sağlayacaktır.

---

## ⚙️ Metodoloji ve Kullanılan Teknikler

Bu analiz, sadece temel bir modelleme çalışması değil, aynı zamanda **İş Alanı Bilgisi (Domain Knowledge)** ve gelişmiş makine öğrenimi tekniklerini birleştiren bütünsel bir veri bilimi projesidir.

### 1. Veri Kaynağı ve Ön İşleme
* **Veri Seti:** Yaygın olarak kullanılan Telco Müşteri Kaybı veri seti 
* **Kayıp Değer Yönetimi:** `TotalCharges` sütunundaki eksik değerleri (NaN), veri setinin **medyanı** ile doldurdum
* **Kategorik Dönüşüm:** Tüm kategorik değişkenleri, modelin işleyebileceği formata (`pd.get_dummies`) dönüştürdüm

### 2. Mühendislik 
Modelin tahmin gücünü artırmak için 8 yeni özellik oluşturdum : 
* `AvgMonthlyCharge`: Müşterinin ortalama aylık harcaması (`TotalCharges` / `tenure`).
* `RiskyPayment`: Kağıtsız fatura kullanan ancak otomatik ödeme yapmayan müşteriler (yüksek riskli ödeme davranışı).
* `tenure_group`: Kalıcı sürenin 12 aylık gruplara ayrılması.
* `RiskyFiber`: Fiber Optik kullanan ve Aylık Sözleşmeli müşteriler (en riskli grup).

### 3. Modelleme
* **Algoritma:** Yüksek performanslı ve hız sunan **LightGBM (Light Gradient Boosting Machine)** algoritması 
* **Sınıf Dengesizliği Çözümü:** Churn (Ayrılan) sınıfının azınlıkta olması nedeniyle, modeli **`class_weight='balanced'`** parametresiyle eğittim . 
* **Optimizasyon:** Aşırı öğrenmeyi (Overfitting) engellemek için **Erken Durdurma (Early Stopping)** mekanizması kullandım.

---

## Model Sonuçları ve İş Çıkarımları

Model, projenin hedefini karşılayan güçlü bir performans sergiledi .

### Ana Performans Metrikleri
| Metrik | Değer | İş Anlamı |
| :--- | :--- | :--- |
| **ROC AUC Skoru** | **0.8411** | Modelin, ayrılan ve kalan müşterileri ayırt etme gücü. 
| **Recall (Ayrılan Sınıfı)** | **0.78** | Gerçekten ayrılacak olan müşterilerin %78'i model tarafından doğru bir şekilde tespit edilebildi.
| **Accuracy (Genel Doğruluk)** | **%76.22** | Tüm tahminlerin genel doğruluk oranı. 

 
**En Kritik Faktörler (İş Odaklı Öncelikler):**
1.  **TotalCharges** ve **tenure (Kalıcı Süre):** Müşterinin finansal değeri ve bağlılığı, kaybı tahmin etmedeki en güçlü sinyal gözüküyor . 
2.  **MonthlyCharges (Aylık Ücretler):** Yüksek faturaya sahip müşteriler, fiyat hassasiyeti nedeniyle yüksek risk altında .
3.  **AvgMonthlyCharge ve RiskyPayment:** Geliştirdiğim bu iki özellik, yüksek riskli ödeme davranışını ve müşteri başına maliyet verimliliğini göstererek modelin hassasiyetini artırdı.

---
