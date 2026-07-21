# **Takım İsmi**

Takım Sentio

# Ürün İle İlgili Bilgiler

## Takım Elemanları

| Ad Soyad | Rol | Slack | Durum |
|---|---|---|---|
| KUBILAY DEMIRSOY | Scrum Master / Developer | `kdemirsoy1970` (U0A39LSDNQ5) | Aktif |
| MUHAMMED YUSUF YÜRTMEN | Product Owner | `muhammetyusuf.1343` (U0A3GMH91B6) | Pasif |
| SENANUR DEMIRCI | Team Member / Developer | `senanurdemirci9b` (U0A39M0HYBF) | Pasif |
| MEHMET CAN GÜLSEROĞLU | Team Member / Developer | `mcagul99` (U0A3B384YVC) | Pasif |
| ZEHRA CEREN ENGIN | Team Member / Developer | `zcerenengin` (U0A3B33DC6A) | Pasif |


## Ürün İsmi

--Tweet Duygu Analizi (Sentiment Analysis)--

## Ürün Açıklaması

- Sosyal medya paylaşımlarının duygu tonunu (pozitif / negatif) otomatik olarak sınıflandıran bir makine öğrenmesi modeli geliştiriyoruz. Ürün, 18.727 etiketli tweet üzerinde eğitiliyor ve iki farklı yaklaşımı karşılaştırıyor: klasik NLP boru hattı (TF-IDF + doğrusal model) ve transformer tabanlı derin öğrenme. Marka takibi, müşteri geri bildirimi analizi ve kriz yönetimi gibi senaryolarda kullanılabilir.

## Ürün Özellikleri

- Tweet metnini otomatik temizleme (RT etiketi, @kullanıcı, URL, hashtag, emoji, harf uzatma, kısaltmalar)
- Olumsuzluk işaretleme — "not good" ile "good" ayrımını koruyan `NOT_` önek mekanizması
- Hafif ve hızlı klasik model (470 KB, milisaniyede tahmin)
- Tahminle birlikte güven skoru (`p_positive`) döndürme
- Model karşılaştırma paneli: accuracy, F1, ROC-AUC, karışıklık matrisi, ROC eğrisi
- Modelin hangi kelimelere ağırlık verdiğini gösteren yorumlanabilirlik grafiği
- Eğitilmiş modeli kaydetme ve tek satırlık `predict_sentiment()` fonksiyonu ile çıkarım

## Hedef Kitle

- Sosyal medya ve marka yöneticileri
- Müşteri deneyimi (CX) ekipleri
- Pazar araştırması yapan analistler
- NLP alanında çalışan öğrenciler ve araştırmacılar
- Veri bilimi alanına yeni giren geliştiriciler

# Sprint 2

  ```
  kdemirsoy1970  10:00
  📅 Gün 5 — Daily Scrum

  ✅ Dün: CountVectorizer ve TfidfVectorizer kurulumu tamamlandı,
     ilk deneme eğitimleri alındı.
  🎯 Bugün: Vektörleştiriciyi Pipeline içine taşıyacağım.
  🚧 Engel: Vektörleştiriciyi tüm veriye fit ettiğimi fark ettim —
     test bilgisi eğitime sızıyor. Skorlar bu yüzden iyimser çıkmış
     olabilir. Pipeline'a geçip dünkü sonuçları yeniden ölçeceğim.
  ```

  <details>
  <summary><b>Sprint 2 Daily Scrum özeti — 10 iş günü</b> (açmak için tıklayın)</summary>

  | Gün | Dün ne yapıldı | Bugün ne yapılacak | Engel |
  |---|---|---|---|
  | 1 | Sprint 1 kapanışı | Sprint planlama, story'lerin task'lere bölünmesi, metin temsili yöntemlerinin araştırılması | — |
  | 2 | Sprint planlama tamamlandı | `build_freqs()` ve `tweet_to_freq()` fonksiyonlarının yazılması | — |
  | 3 | Frekans sözlüğü çıkarıldı (17.919 kelime-sınıf çifti) | Baseline modelin eğitilmesi | — |
  | 4 | Baseline eğitildi, 0.789 accuracy | Ortak `evaluate()` fonksiyonunun yazılması | 🚧 Baseline beklenenden düşük çıktı. İki özelliğe sıkıştırılmış temsilden fazlası beklenmemeli sonucuna varıldı, referans olarak kabul edildi. |
  | 5 | `evaluate()` tamamlandı | CountVectorizer / TfidfVectorizer kurulumu | 🚧 Vektörleştirici tüm veriye fit ediliyordu — veri sızıntısı tespit edildi. |
  | 6 | Sızıntı `Pipeline` ile giderildi, sonuçlar yeniden ölçüldü | LR + BoW ve LR + TF-IDF eğitimi, karakter n-gram eklenmesi | — |
  | 7 | İki model de ~0.907 accuracy verdi | LinearSVC ve ComplementNB eklenmesi | 🚧 LinearSVC `predict_proba` desteklemiyor, ROC-AUC hesaplanamıyor. `CalibratedClassifierCV` ile sarmalanarak çözüldü. |
  | 8 | Dört klasik model karşılaştırıldı | LightGBM entegrasyonu | 🚧 Seyrek matris ağaç modeline uygun değil; TruncatedSVD ile 300 boyuta indirildi. Eğitim tahmin edilenin iki katı sürdü. |
  | 9 | LightGBM sonuçlandı, en zayıf ikinci model | RandomizedSearchCV ile hiperparametre araması | 🚧 Arama ~15 dk sürdü, Colab oturumunun düşmemesi için gözlem altında tutuldu. |
  | 10 | Arama tamamlandı | Sonuçların tabloya dökülmesi, Sprint Review hazırlığı | 🚧 Aramanın elle seçilen parametrelerin gerisinde kalması — Review'a gündem olarak taşındı. |

  **Katılım durumu:** Sprint boyunca `#takim-sentio-daily` kanalına 10/10 gün mesaj bırakan tek üye Kubilay Demirsoy olmuştur. Diğer üyelerden Daily Scrum yanıtı alınamamış, 3. günde ve 7. günde kanal üzerinden hatırlatma yapılmış, dönüş sağlanmamıştır.

  </details>

- **Sprint board update**: Bu sprint'te takip, Miro yerine **GitHub Issues + Projects** üzerinden yürütüldü. Story'ler issue, task'ler ise issue içindeki checklist maddeleri olarak tanımlandı. Sprint sonunda tüm issue'lar `Done` sütununa taşındı.

- **Ürün Durumu**:

  Sprint sonunda çalışan bir duygu sınıflandırıcısı elde edildi. Sonuçların tamamı **doğrulama kümesi** (2.374 tweet) üzerindedir; test kümesine bu sprint'te dokunulmadı.

  | Model | Accuracy | F1 | ROC-AUC |
  |---|---|---|---|
  | LR + poz/neg frekans (2 boyut) | 0.789 | 0.793 | 0.859 |
  | LightGBM + TF-IDF (SVD 300) | 0.868 | 0.875 | 0.942 |
  | ComplementNB + TF-IDF | 0.884 | 0.887 | 0.949 |
  | LR (RandomizedSearchCV ile ayarlanmış) | 0.901 | 0.906 | 0.966 |
  | LinearSVC + TF-IDF | 0.905 | 0.910 | 0.968 |
  | LR + TF-IDF (kelime + karakter n-gram) | 0.907 | 0.911 | 0.968 |
  | **LR + Bag-of-Words** | **0.907** | **0.912** | 0.963 |

  ### 1. Sınıf ayrımının veri düzeyinde doğrulanması

  Modeli eğitmeden önce, kelimelerin sınıflara göre dağılımına bakarak öğrenilecek bir sinyal olup olmadığı kontrol edildi:

  ```
  Sözlük büyüklüğü (kelime, sınıf) çifti: 17919
  'love' pozitifte: 844 | negatifte: 35
  ```

  ![En pozitif ve en negatif kelimeler]

  <img width="1183" height="484" alt="En pozitif en negatif kelimeler" src="https://github.com/user-attachments/assets/f91f0de6-9237-4c89-ace8-025d3454a54b" />


  Log-oran sıralaması beklendiği gibi çıktı: pozitif tarafta *awesome, happy, amaze, welcome, thanks*; negatif tarafta *sad, hate, suck, hurt, bore*. Veri setinin tutarlı etiketlendiğinin ilk göstergesi oldu.

  ### 2. Baseline — LR + poz/neg frekans (2 boyutlu temsil)

  ![Baseline karışıklık matrisi ve ROC](ProjectManagement/Sprint2Documents/baseline_confusion_roc.jpeg)

  Tüm tweet'i iki sayıya sıkıştıran temsil, 2.374 örnekte **502 hata** yaptı (215 yanlış pozitif, 287 yanlış negatif). AUC 0.86. Sonraki modellerin karşılaştırılacağı referans noktası olarak kaydedildi.

  ### 3. Sprint'in ana çıktısı — LR + TF-IDF

  ![LR TF-IDF karışıklık matrisi ve ROC](ProjectManagement/Sprint2Documents/lr_tfidf_confusion_roc.jpeg)

  Aynı doğrulama kümesinde hata sayısı **502'den 221'e** düştü (107 yanlış pozitif, 114 yanlış negatif). AUC 0.86'dan 0.97'ye çıktı. Hataların iki sınıf arasında dengeli dağılması, modelin bir sınıfa eğilim göstermediğini doğruladı.

  En iyi modelin (LR + Bag-of-Words) detaylı sınıflandırma raporu:

  ```
  === LR + BoW [val] ===
  Accuracy: 0.9073 | F1: 0.9117 | ROC-AUC: 0.9631

                precision    recall  f1-score   support

      negative      0.899     0.906     0.902      1124
      positive      0.915     0.909     0.912      1250

      accuracy                          0.907      2374
     macro avg      0.907     0.907     0.907      2374
  weighted avg      0.907     0.907     0.907      2374
  ```

  ### 4. Hiperparametre araması

  ```
  Fitting 5 folds for each of 25 candidates, totalling 125 fits
  En iyi CV F1: 0.8919
  En iyi parametreler: {'clf__C': 21.52, 'clf__class_weight': None,
                        'vec__max_df': 0.9, 'vec__min_df': 1,
                        'vec__ngram_range': (1, 2)}
  ```

  ### 5. Model yorumlanabilirliği

  ![En ayırt edici özellikler](ProjectManagement/Sprint2Documents/en_ayirt_edici_ozellikler.jpeg)

  Sınıflandırıcının en yüksek ağırlık verdiği kelimeler, 1. adımdaki veri düzeyi analiziyle büyük ölçüde örtüştü. Bu, modelin veriye ezber yapmak yerine anlamlı bir sinyal öğrendiğini gösteriyor. Ürünün "kara kutu değil, açıklanabilir" özelliği bu çıktıyla karşılandı.

- **Sprint Review**:

  Sprint hedefine ulaşıldı; çalışan ve ~%91 doğrulukla tahmin yapan bir model ortaya çıktı. Toplantıda öne çıkan konular:

  - **Hiperparametre araması beklenenin tersi sonuç verdi.** 25 aday ve 125 eğitimlik arama, elle seçilen varsayılan parametrelerin *gerisinde* kaldı (F1 0.906 vs 0.912). Sebep, arama uzayına karakter n-gram ve Bag-of-Words seçeneklerinin dahil edilmemesiydi. Bu negatif sonucun gizlenmemesine, README'de olduğu gibi raporlanmasına karar verildi.
  - **LightGBM metin verisinde beklendiği gibi zayıf kaldı.** Yüksek boyutlu seyrek matrisi SVD ile 300 boyuta indirmek bilgi kaybettiriyor; doğrusal modeller seyrekliği doğrudan kullanabildiği için avantajlı. Ağaç tabanlı modellerin bu problem için uygun olmadığı sonucuna varıldı.
  - **Karakter n-gramlarının katkısı sınırlı çıktı.** ROC-AUC'yi 0.963'ten 0.968'e taşıdı ama accuracy'de fark yaratmadı. Twitter'ın yazım hatası bolluğu düşünüldüğünde daha fazlası bekleniyordu.
  - **Klasik yaklaşımın tavanı görüldü.** Dört farklı model ve bir hiperparametre araması sonrasında sonuçlar 0.90–0.91 bandında sıkıştı. Sprint 3'te transformer tabanlı bir modelin denenmesine karar verildi.
  - Vektörleştiricinin `Pipeline` içine alınarak veri sızıntısının önlenmesi, sprint'in en kritik teknik kararı olarak kaydedildi.

  Sprint Review katılımcıları: Kubilay Demirsoy

- **Sprint Retrospective**:

  * **Takım katılımı sprint'in en büyük riski oldu.** Beş kişilik takımın dört üyesi Daily Scrum'a ve task devralmaya katılmadı. Sprint hedefi tek geliştirici tarafından tamamlandı ancak bu sürdürülebilir değil. Durum eğitim koordinasyonuna bildirildi; Sprint 3 planlaması, tek kişilik efora göre kapsam belirlenerek yapılacak.
  * Ortak `evaluate()` fonksiyonunun erken yazılması çok zaman kazandırdı. Benzer yardımcı fonksiyonların ilerideki sprintlerde de en başta yazılmasına karar verildi.
  * Hiperparametre araması için harcanan ~15 dakikalık hesaplama süresi çıktısına değmedi. Arama uzayı, sezgisel denemelerle iyi sonuç veren yapılandırmaları da kapsayacak şekilde tasarlanmalı.
  * Model seçiminin yalnızca doğrulama kümesinde yapılması ve test kümesine dokunulmaması disiplinine sprint boyunca uyuldu. Bu kuralın Sprint 3'te de korunmasına karar verildi.
  * Engel bildiren mesajların 🚧 emojisi ile işaretlenmesi işe yaradı, geçmişe dönük takip kolaylaştı.
  * Task tahminleri genel olarak tuttu; yalnızca LightGBM entegrasyonu tahmin edilenin iki katı sürdü.

---

# Sprint 3

[Sprint 3 içeriği doldurulacak]

---

## Kurulum ve Çalıştırma

**Google Colab (önerilen):**

`sentiment.ipynb` dosyasını Colab'da açın, `Çalışma zamanı > Türünü değiştir > T4 GPU` seçin ve ilk hücreden başlayın. İkinci hücre `tweet_data.csv` için yükleme kutusu açacaktır.

**Yerel kurulum:**

```bash
git clone https://github.com/[kullanıcı-adınız]/my_project.git
cd my_project
pip install -r requirements.txt
jupyter notebook sentiment.ipynb
```

**Kayıtlı modeli doğrudan kullanma:**

```python
import joblib
pipe = joblib.load("sentiment_classic.joblib")
pipe.predict_proba(["what a wonderful day"])[0, 1]   # -> 0.99
```

## Proje Yapısı

```
sentiment.ipynb            # ana defter (tüm çıktılarıyla birlikte)
sentiment_classic.joblib   # eğitilmiş LR + BoW modeli (470 KB)
tweet_data.csv             # veri seti (1.8 MB)
requirements.txt
.gitignore
README.md
ProjectManagement/
  └── Sprint2Documents/
        ├── DailyScrumMeetingNotesSprint2.docx
        ├── log_oran_kelimeler.jpeg
        ├── baseline_confusion_roc.jpeg
        ├── lr_tfidf_confusion_roc.jpeg
        └── en_ayirt_edici_ozellikler.jpeg
```
