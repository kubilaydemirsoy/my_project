# my_project — Tweet Duygu Analizi

18 bin İngilizce tweet üzerinde pozitif/negatif sınıflandırması. Önce klasik NLP boru hattını
kurdum (temizlik → TF-IDF → doğrusal model), sonra aynı veriyle bir transformer'ı ince ayar
yapıp ikisini yan yana koydum. Merak ettiğim şey sadece skor değildi: kelime torbası nerede
tıkanıyor, transformer o farkı gerçekten kapatıyor mu, kapatıyorsa ne kadar?

Kısa cevap: kapatıyor, ~6 puan F1 farkla. Ama klasik model 470 KB, transformer 500 MB — ve
klasik modelin eğitimi saniyeler sürüyor.

## Veri

`tweet_data.csv` — 18.727 satır, sütunlar: `textID`, `tweet_text`, `sentiment`.
Etiketler `positive` / `negative`, dağılım %52.6 – %47.4. Ciddi bir dengesizlik yok,
o yüzden accuracy anlamlı; yine de F1 ve ROC-AUC da raporluyorum.

Veri ilk bakışta temiz görünüyor ama içinde **112 tam tekrar eden tweet** vardı. Bunları
atmazsanız aynı metin hem eğitime hem teste düşebiliyor ve skor haksız yere şişiyor.
Temizlik sonrası 18.615 kayıt kaldı.

Ayrım: %70 eğitim (13.448) / %15 doğrulama (2.374) / %15 test (2.793), `stratify` ile.
Model seçimi ve hiperparametre ayarı doğrulama kümesinde yapıldı, teste sadece en sonda
bir kez bakıldı.

## Sonuçlar

Doğrulama kümesi:

| Model | Accuracy | F1 | ROC-AUC |
|---|---|---|---|
| LR + poz/neg frekans (2 boyut) | 0.789 | 0.793 | 0.859 |
| LightGBM + TF-IDF (SVD 300) | 0.868 | 0.875 | 0.942 |
| ComplementNB + TF-IDF | 0.884 | 0.887 | 0.949 |
| LR (RandomizedSearchCV ile ayarlanmış) | 0.901 | 0.906 | 0.966 |
| LinearSVC + TF-IDF | 0.905 | 0.910 | 0.968 |
| LR + TF-IDF (kelime+karakter) | 0.907 | 0.911 | 0.968 |
| **LR + Bag-of-Words** | **0.907** | **0.912** | 0.963 |
| RoBERTa (hazır, hiç eğitilmemiş) | 0.921 | 0.926 | 0.971 |
| **RoBERTa (fine-tuned)** | **0.954** | **0.957** | **0.988** |

Test kümesi (tek seferlik final ölçüm):

| Model | Accuracy | F1 | ROC-AUC |
|---|---|---|---|
| LR + Bag-of-Words | 0.888 | 0.892 | 0.957 |
| RoBERTa (fine-tuned) | 0.947 | 0.950 | 0.984 |

Fine-tuning Colab T4'te 2 epoch, yaklaşık 2 dakika sürdü (early stopping 3. epoch'a
gerek bırakmadı, eğitim kaybı 0.165).

## Not ettiğim şeyler

**Hiperparametre araması işe yaramadı.** 25 aday, 5 katlı CV — ve çıkan model elle seçtiğim
varsayılanlardan yarım puan *geride* kaldı (0.906 vs 0.912). CV skorunu optimize etmek her
zaman doğrulama skorunu iyileştirmiyor; arama uzayının dışında kalan `min_df` / n-gram
kombinasyonları daha iyiymiş. Negatif sonuç ama bence raporlanması gereken bir sonuç.

**Boosting metinde çalışmıyor.** LightGBM'i "acaba" diye denedim, en zayıf ikinci model çıktı.
Yüksek boyutlu seyrek matrisi SVD ile 300 boyuta indirmek bilgi kaybettiriyor; doğrusal
modeller seyrekliği doğrudan kullanabildiği için avantajlı. Ağaç modelleri sayısal/tablosal
özelliklerin de olduğu problemlerde parlıyor, saf metinde değil.

**Karakter n-gramları beklediğim kadar katkı yapmadı.** TF-IDF'e `char_wb` (3,5) eklemek
AUC'yi 0.963'ten 0.968'e çıkardı ama accuracy'de fark yok. Twitter'ın yazım hatası
bolluğunda daha fazlasını umuyordum.

**Hiç eğitilmemiş transformer, en iyi klasik modelimi geçti.** `cardiffnlp/twitter-roberta`
tam da bu alanda (124M tweet) ön-eğitilmiş olduğu için tek bir gradyan adımı atmadan 0.926 F1
veriyor. Alan uyumu, model büyüklüğünden daha önemli olabiliyor.

**Olumsuzluk işaretleme.** `not`, `no`, `never` gibi kelimeleri stopword listesinden çıkardım
ve olumsuzluktan sonraki üç tokene `NOT_` öneki ekledim. Bu olmadan "not good" ile "good"
aynı vektöre düşüyor — kelime torbasının en can sıkıcı zaafı.

**Kalan hataların hepsi model hatası değil.** Klasik model test setinde 314 hata yapıyor
(%11.2). En emin yanlışlara tek tek baktığımda önemli bir kısmı ya yanlış etiketlenmiş ya
alaycı tweetler. Örneğin "Happy Mother's Day to every mommy out there" veri setinde negatif
etiketli — model haklı, etiket yanlış. Bu veri setinde ~%95 muhtemelen gerçekçi bir tavan.

## Defterde ne var

`sentiment.ipynb` sırayla:

1. Veri keşfi — dağılımlar, tweet uzunlukları, tekrar/eksik kontrolü, kelime bulutları
2. Normalizasyon — RT, @kullanıcı, URL, hashtag, emoji, harf uzatma, kısaltmalar, olumsuzluk
3. Temsil — poz/neg frekans, Bag-of-Words, TF-IDF (kelime + karakter n-gram)
4. Klasik modeller — LR, LinearSVC, ComplementNB, LightGBM + hiperparametre araması
5. Transformer — hazır model ile tahmin, ardından fine-tuning
6. Hata analizi — en emin yanlışlar, en ayırt edici özellikler
7. Model kaydetme + `predict_sentiment()` çıkarım fonksiyonu

Çıktılar defterin içinde kayıtlı, tekrar çalıştırmadan da görebilirsiniz.

## Çalıştırma

**Colab (önerilen — Bölüm 5 GPU ister):**

Defteri açın, `Çalışma zamanı > Türünü değiştir > T4 GPU` seçin, ilk hücreden başlayın.
İkinci hücre `tweet_data.csv` için yükleme kutusu açar.

**Yerelde:**

```bash
git clone https://github.com/<kullanıcı-adınız>/my_project.git
cd my_project
pip install -r requirements.txt
jupyter notebook sentiment.ipynb
```

Kayıtlı modeli doğrudan kullanmak isterseniz:

```python
import joblib
pipe = joblib.load("sentiment_classic.joblib")
pipe.predict_proba(["what a wonderful day"])[0, 1]   # -> 0.99
```

Dikkat: `sentiment_classic.joblib` scikit-learn 1.9.0 ile kaydedildi. Farklı bir sürümle
yüklerseniz uyarı alırsınız; `requirements.txt`'teki sürümlerle sorun çıkmaz.

## Sonraki adımlar

- Karar eşiğini 0.5'te bırakmak yerine doğrulama kümesinde F1'i maksimize edecek şekilde seçmek
- Klasik model + transformer olasılıklarını harmanlamak (basit soft-voting)
- Nötr sınıfını ekleyip üç sınıflı hale getirmek
- En emin yanlışları elle gözden geçirip etiket gürültüsünü temizlemek, sonra yeniden eğitmek

## Dosyalar

```
sentiment.ipynb            # ana defter (çıktılarıyla)
sentiment_classic.joblib   # eğitilmiş LR + BoW modeli (470 KB)
tweet_data.csv             # veri (1.8 MB)
requirements.txt
.gitignore
README.md
```

Fine-tune edilmiş RoBERTa modeli (~500 MB) repoda yok — `.gitignore` ile dışarıda tutuldu.
Defterin 5. bölümünü çalıştırırsanız kendiniz üretebilirsiniz.
