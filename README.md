# Spotify Veri Analizi ve Müzik Türü Tahmini

Bu proje, Spotify şarkı verilerini kullanarak çeşitli analizler yapmayı ve şarkıların ses özelliklerine dayanarak müzik türlerini tahmin etmeyi amaçlamaktadır.

## Projenin Amacı

-   Spotify'da yer alan popüler şarkıların özelliklerini analiz etmek.
-   Şarkıların yıllara göre değişimini, popülerliklerini, sürelerini ve diğer ses özelliklerini incelemek.
-   Elde edilen veriler ve özellikler kullanılarak bir makine öğrenmesi modeli eğitmek.
-   Bu model ile yeni şarkıların müzik türlerini tahmin etmek.

## Kullanılan Teknolojiler

-   **Python:** Ana programlama dili.
-   **Pandas:** Veri işleme ve analizi için kullanıldı.
-   **Numpy:** Sayısal işlemler için kullanıldı.
-   **Matplotlib & Plotly:** Veri görselleştirme için kullanıldı.
-   **Scikit-learn:** Makine öğrenmesi modeli oluşturma, eğitme ve değerlendirme için kullanıldı.
    -   Özellikle `RandomForestClassifier` modeli müzik türü tahmini için tercih edilmiştir.
-   **Joblib:** Eğitilmiş makine öğrenmesi modelini kaydetmek ve yüklemek için kullanıldı.

## Veri Seti

Projede iki ana veri seti kullanılmıştır:
1.  `songs_normalize.csv`: Şarkıların sanatçısı, adı, süresi, müstehcenlik durumu, yılı, popülaritesi ve çeşitli ses özellikleri (dans edilebilirlik, enerji, tonalite, ses yüksekliği vb.) ile birlikte müzik türünü içeren ana veri seti.
2.  `yenı.csv`: Eğitilmiş model ile müzik türü tahmini yapmak için kullanılan, benzer özelliklere sahip yeni şarkıların listesi.

## Yapılan Adımlar

1.  **Veri Yükleme ve Kütüphane Kurulumu:**
    -   Gerekli Python kütüphaneleri (Pandas, Numpy, Matplotlib, Plotly, Scikit-learn) import edildi.
    -   `songs_normalize.csv` veri seti Pandas DataFrame olarak yüklendi.

2.  **Veri Ön İşleme ve Temizleme:**
    -   Veri setindeki eksik (NaN) değerler içeren satırlar tespit edildi.
    -   Bu satırlar, modelin performansını olumsuz etkilememesi ve analizlerin doğruluğunu artırması amacıyla veri setinden çıkarıldı.
    -   Veri seti hakkında genel bilgiler (`info()`, `describe()`) incelendi.

3.  **Keşifsel Veri Analizi (EDA):**
    -   Yıllara göre şarkı sayısı dağılımı incelendi (Bar ve pasta grafikleri).
    -   En çok şarkısı bulunan ilk 10 sanatçı listelendi ve görselleştirildi.
    -   İlk 50 sanatçının toplam şarkı sayısındaki payı incelendi.
    -   Yalnızca bir popüler şarkısı olan sanatçıların oranı görselleştirildi.
    -   Sanatçıların ortalama popülerlikleri ve toplam şarkı sayıları arasındaki ilişki incelendi.
    -   Şarkı sürelerinin yıllara göre ortalama değişimi analiz edildi. En uzun ve en kısa şarkılar belirlendi.
    -   Şarkıların müstehcen (explicit) olup olmama durumlarının yıllara göre dağılımı incelendi.
    -   Şarkıların majör ve minör tonalitelerinin anahtarlara (key) göre dağılımı görselleştirildi.
    -   Popülerlik, dans edilebilirlik, enerji, konuşma oranı (speechiness), ses yüksekliği (loudness), akustiklik, canlılık (liveness), enstrümantallik, pozitiflik (valence), tempo gibi ses özelliklerinin dağılımları histogramlarla incelendi.

4.  **Model Geliştirme ve Eğitimi (Müzik Türü Tahmini):**
    -   **Özellik Seçimi:** "artist", "song" gibi kategorik ve model için doğrudan kullanılamayacak sütunlar çıkarılarak özellik (X) matrisi oluşturuldu. Hedef değişken (y) olarak "genre;" (müzik türü) sütunu seçildi.
    -   **Veri Ayırma:** Veri seti, %80 eğitim ve %20 test verisi olacak şekilde ayrıldı.
    -   **Model Seçimi ve Eğitimi:**
        -   Müzik türü tahmini için `RandomForestClassifier` (Rastgele Orman Sınıflandırıcısı) modeli kullanıldı.
        -   Bu model, birden fazla karar ağacının bir araya gelerek tahmin yapması prensibine dayanır ve genellikle karmaşık veri setlerinde iyi performans gösterir.
        -   Model, `n_estimators=100` (100 adet karar ağacı) ve `random_state=42` (tekrarlanabilirlik için) parametreleriyle eğitim verisi üzerinde eğitildi.

5.  **Model Değerlendirme:**
    -   Eğitilen `RandomForestClassifier` modelinin performansı test verisi üzerinde değerlendirildi.
    -   Doğruluk oranı (accuracy) yaklaşık **%73.38** olarak bulundu. Bu, modelin test setindeki şarkıların yaklaşık %73'ünün müzik türünü doğru tahmin edebildiğini gösterir.
    -   Detaylı sınıflandırma raporu (precision, recall, f1-score) incelendi. Bu rapor, her bir müzik türü için modelin ne kadar başarılı olduğunu ayrı ayrı gösterir. "pop;" türü için tahmin başarısının yüksek olduğu, ancak bazı azınlık sınıflar (veri setinde az örneği olan türler) için modelin genelleme yapmakta zorlandığı gözlemlendi.

6.  **Modelin Kaydedilmesi:**
    -   Eğitilmiş `RandomForestClassifier` modeli, daha sonra kullanılmak üzere `joblib` kütüphanesi ile `genre_prediction_model.pkl` dosyasına kaydedildi.

7.  **Yeni Veri Üzerinde Tahmin:**
    -   `yenı.csv` adlı yeni bir veri seti yüklendi.
    -   Kaydedilen model (`genre_prediction_model.pkl`) tekrar yüklendi.
    -   Yeni veri setindeki şarkıların özellikleri kullanılarak müzik türü tahminleri yapıldı.
    -   Tahmin edilen müzik türleri, yeni veri setine "predicted_genre" adlı bir sütun olarak eklendi ve sonuçlar `tahmin_edilen_sarkilar.csv` dosyasına kaydedildi.

## Özet Sonuçlar

-   Veri setinde NaN değer içeren satırlar temizlendikten sonra analizler ve modelleme yapılmıştır.
-   EDA aşamasında şarkı özelliklerinin dağılımları ve zaman içindeki değişimleri hakkında çeşitli bilgiler edinilmiştir. Örneğin, şarkı sürelerinin son yıllarda kısalma eğiliminde olduğu veya bazı sanatçıların veri setinde daha fazla şarkıyla temsil edildiği görülmüştür.
-   Rastgele Orman Sınıflandırıcısı ile geliştirilen müzik türü tahmin modeli, test verileri üzerinde yaklaşık %73.38 doğruluk oranına ulaşmıştır. Bu, modelin belirli bir başarıyla müzik türlerini tahmin edebildiğini göstermektedir, ancak özellikle az sayıda örneği olan türler için iyileştirmeler yapılabilir.

## Nasıl Çalıştırılır?

1.  Proje dosyalarını bilgisayarınıza indirin (`spotıfy_verı_analızı_müzık_türü_tahmını.ipynb`, `songs_normalize.csv`, `yenı.csv`).
2.  Jupyter Notebook veya Google Colab gibi bir Python ortamında `.ipynb` dosyasını açın.
3.  Gerekli kütüphanelerin kurulu olduğundan emin olun (pandas, numpy, matplotlib, plotly, scikit-learn, joblib).
4.  Hücreleri sırayla çalıştırın.
