# Veri Madenciligi Projesi - Calisma Plani

> **Proje Turu:** Kategorik ve sayisal degiskenler iceren bir veri setinde veri on isleme, ozellik secimi, boyut indirgeme ve model performansinin karsilastirmali analizi

---

## Proje Ozeti

| Bilgi | Deger |
|-------|-------|
| **Veri Seti** | Wisconsin Breast Cancer Dataset |
| **Hedef Degisken** | diagnosis (M=Malignant, B=Benign) |
| **Problem Tipi** | Binary Classification |
| **Baslangic Tarihi** | Aralik 2024 |
| **Durum** | Tamamlandi |

---

## 1. Problem Tanimi ve Amac


### Amac Ornegi
> Bu calismanin amaci, meme kanseri teshisinde (Malignant vs Benign) yuksek dogruluk oranina sahip siniflandirma modelleri gelistirmek ve ozellik secimi ile boyut indirgeme yontemlerinin model performansi uzerindeki etkilerini karsilastirmali olarak incelemektir.

### Notlar
```
Wisconsin Breast Cancer veri seti, meme kitlesinin ince igne aspirasyonu (FNA) goruntusunden
hesaplanan ozellikler icerir. Her ozellik icin ortalama (mean), standart hata (se) ve
en kotu (worst) degerleri bulunmaktadir.

Ozellikler:
- radius: cekirdek merkezinden cevre noktasina uzaklik ortalamalari
- texture: gri tonlama degerlerinin standart sapmasi
- perimeter: cevre uzunlugu
- area: alan
- smoothness: yaricap uzunluklarindaki yerel degisim
- compactness: cevre^2 / alan - 1
- concavity: konturun icbukeylik derecesi
- concave points: konturun icbukey kisimlari sayisi
- symmetry: simetri
- fractal_dimension: fraktal boyut
```

---

## 2. Veri Setinin Taninmasi (EDA)


| Metrik | Deger |
|--------|-------|
| Gozlem Sayisi | 569 |
| Degisken Sayisi | 31 (30 ozellik + 1 hedef) |
| Hedef Dagilimi | B: 357 (%62.7), M: 212 (%37.3) |

### 2.2 Degisken Turleri
-

| Tur | Sayi | Degiskenler |
|-----|------|-------------|
| Sayisal | 30 | radius_mean, texture_mean, perimeter_mean, area_mean, smoothness_mean, compactness_mean, concavity_mean, concave points_mean, symmetry_mean, fractal_dimension_mean, radius_se, texture_se, perimeter_se, area_se, smoothness_se, compactness_se, concavity_se, concave points_se, symmetry_se, fractal_dimension_se, radius_worst, texture_worst, perimeter_worst, area_worst, smoothness_worst, compactness_worst, concavity_worst, concave points_worst, symmetry_worst, fractal_dimension_worst |
| Kategorik | 0 | - |
| Hedef | 1 | diagnosis |

### 2.3 Eksik Deger Analizi


| Degisken | Eksik Sayi | Eksik Oran (%) |
|----------|------------|----------------|
| Tum degiskenler | 0 | 0% |

> **Not:** Veri setinde eksik deger bulunmamaktadir. Toplam hucre sayisi: 17,639

### 2.4 Dagilim Analizi
### Notlar
```
Yuksek Korelasyonlu Ozellik Ciftleri (|r| > 0.9):
- radius_mean <-> perimeter_mean: 0.998
- radius_mean <-> area_mean: 0.987
- radius_worst <-> perimeter_worst: 0.994
- radius_worst <-> area_worst: 0.984
- perimeter_mean <-> area_mean: 0.987

Bu yuksek korelasyonlar, PCA kullanmak icin guclu bir motivasyon saglar.
```

---

## 3. Veri On Isleme

### 3.1 Eksik Deger Doldurma (Imputation)

#### Sayisal Degiskenler
| Yontem | Aciklama | Kullanildi | Performans |
|--------|----------|------------|------------|
| Mean | Ortalama ile doldur | [ ] | Gerekli degil |
| Median | Medyan ile doldur (outlier varsa) | [ ] | Gerekli degil |
| KNN Imputation | K en yakin komsu ile doldur | [ ] | Gerekli degil |
| MICE | Coklu imputation | [ ] | Gerekli degil |

> **Not:** Veri setinde eksik deger bulunmadigindan imputation uygulanmadi.

#### Kategorik Degiskenler
| Yontem | Aciklama | Kullanildi |
|--------|----------|------------|
| Mode | En sik degerle doldur | [ ] |
| "Unknown" | Yeni kategori ekle | [ ] |

### 3.2 Aykiri Deger Analizi


| Degisken | Aykiri Sayi | Oran (%) | Islem |
|----------|-------------|----------|-------|
| area_se | 65 | 11.42% | Birakildi |
| radius_se | 38 | 6.68% | Birakildi |
| perimeter_se | 38 | 6.68% | Birakildi |
| area_worst | 35 | 6.15% | Birakildi |
| smoothness_se | 30 | 5.27% | Birakildi |
| compactness_se | 28 | 4.92% | Birakildi |
| fractal_dimension_se | 28 | 4.92% | Birakildi |
| symmetry_se | 27 | 4.75% | Birakildi |
| area_mean | 25 | 4.39% | Birakildi |
| fractal_dimension_worst | 24 | 4.22% | Birakildi |

> **Karar:** Aykiri degerler medikal veriler icin anlamli olabilir (tumor ozellikleri), bu nedenle silinmedi.

### 3.3 Kategorik Degisken Donusumu (Encoding)

| Degisken | Kategori Sayisi | Yontem | Tamamlandi |
|----------|-----------------|--------|------------|
| diagnosis | 2 (M, B)       | Label Encoding | [x] |

**Encoding Sonucu:**
- B (Benign/Iyi Huylu) -> 0
- M (Malignant/Kotu Huylu) -> 1

### 3.4 Ozellik Olceklendirme

| Model | Uygun Scaler |
|-------|--------------|
| Logistic| StandardScaler |
 Regression  
| PCA | StandardScaler |
| SVM | StandardScaler |
| KNN | StandardScaler |
| Random Forest | Gerekli degil |



### Notlar
```
StandardScaler kullanildi: Her ozellik icin ortalama=0, standart sapma=1 olacak sekilde
donusum yapildi. Bu, PCA ve mesafe tabanli algoritmalar (KNN, SVM) icin gereklidir.
```

---

## 4. Ozellik Secimi (Feature Selection)

### 4.1 Filtre Yontemleri
- [x] Korelasyon analizi (hedef ile)
- [ ] Chi-square testi
- [x] ANOVA F-testi
- [ ] Mutual Information

| Yontem | En Onemli 5 Ozellik |
|--------|---------------------|
| Korelasyon | concave points_worst, perimeter_worst, radius_worst, area_worst, concave points_mean |
| ANOVA | concave points_worst (F=964.39), perimeter_worst (F=897.94), concave points_mean (F=861.68), radius_worst (F=860.78), perimeter_mean (F=697.24) |

### 4.2 ANOVA F-Test Sonuclari

| Sira | Ozellik | F-Score | P-Value |
|------|---------|---------|---------|
| 1 | concave points_worst | 964.39 | 1.97e-124 |
| 2 | perimeter_worst | 897.94 | 5.77e-119 |
| 3 | concave points_mean | 861.68 | 7.10e-116 |
| 4 | radius_worst | 860.78 | 8.48e-116 |
| 5 | perimeter_mean | 697.24 | 8.44e-101 |
| 6 | area_worst | 661.60 | 2.83e-97 |
| 7 | radius_mean | 646.98 | 8.47e-96 |
| 8 | area_mean | 573.06 | 4.73e-88 |
| 9 | concavity_mean | 533.79 | 9.97e-84 |
| 10 | concavity_worst | 436.69 | 2.46e-72 |
| 11 | compactness_mean | 313.23 | 3.94e-56 |
| 12 | compactness_worst | 304.34 | 7.07e-55 |
| 13 | radius_se | 268.84 | 9.74e-50 |
| 14 | perimeter_se | 253.90 | 1.65e-47 |
| 15 | area_se | 243.65 | 5.90e-46 |

### Secilen Ozellikler
- [x] Top K ozellik belirlendi (K = **15**)
- [x] Secilen ozellikler: concave points_worst, perimeter_worst, concave points_mean, radius_worst, perimeter_mean, area_worst, radius_mean, area_mean, concavity_mean, concavity_worst, compactness_mean, compactness_worst, radius_se, perimeter_se, area_se

### Notlar
```
ANOVA F-Testi, her ozelligin hedef degisken ile istatistiksel iliskisini olcer.
Yuksek F-Score = Ozellik siniflari iyi ayirt ediyor
Dusuk P-Value (< 0.05) = Istatistiksel olarak anlamli

En onemli ozellikler "worst" (en kotu) degerler - bu, tumorun en agresif
bolumlerinin teshiste belirleyici oldugunu gosteriyor.
```

---

## 5. Boyut Indirgeme (PCA)



| Bilesen | Varyans Orani | Kumulatif |
|---------|---------------|-----------|
| PC1 | 0.4427 | 0.4427 |
| PC2 | 0.1897 | 0.6324 |
| PC3 | 0.0939 | 0.7264 |
| PC4 | 0.0660 | 0.7924 |
| PC5 | 0.0550 | 0.8473 |
| PC6 | 0.0402 | 0.8876 |
| PC7 | 0.0225 | 0.9101 |
| PC8 | 0.0159 | 0.9260 |
| PC9 | 0.0139 | 0.9399 |
| PC10 | 0.0117 | 0.9516 |

### Bilesen Secimi
| Esik | Gerekli Bilesen Sayisi |
|------|------------------------|
| %90 varyans | 7 |
| %95 varyans | 10 |

- [x] Optimal bilesen sayisi belirlendi: **10** (%95 varyans)
- [x] PCA donusumu uygulandi
- [x] 2D gorsellestirme yapildi

### Notlar
```
PCA ile 30 ozellik -> 10 bilesene indirildi.
Toplam aciklanan varyans: %95.16

PC1 tek basina verinin %44.27'sini acikliyor - bu, ozelliklerin buyuk kisminin
birbiriyle iliskili oldugunu gosteriyor (yuksek korelasyon bulgulariyla uyumlu).

2D gorsellestirmede Benign ve Malignant siniflarin buyuk olcude ayrilabilir
oldugu goruldu.
```

---

## 6. Modelleme

### 6.1 Veri Bolme
- [x] Train-Test split (%80-%20)
- [x] Stratified sampling uygulandi

### 6.2 Gozetimli Ogrenme (Classification)

#### Kullanilacak Modeller
| Model | Kullanildi | Parametre Ayari |
|-------|------------|-----------------|
| Logistic Regression | [x] | max_iter=1000, random_state=42 |
| Random Forest | [x] | n_estimators=100, random_state=42 |
| KNN | [x] | n_neighbors=5 |
| SVM | [x] | kernel='rbf', probability=True, random_state=42 |

#### Sonuclar - Tum Ozellikler (30 ozellik)

| Model | Accuracy | Precision | Recall | F1 | AUC |
|-------|----------|-----------|--------|-----|-----|
| Logistic Regression | 0.9649 | 0.9750 | 0.9286 | 0.9512 | 0.9960 |
| Random Forest | 0.9737 | 1.0000 | 0.9286 | 0.9630 | 0.9929 |
| KNN | 0.9561 | 0.9744 | 0.9048 | 0.9383 | 0.9816 |
| SVM | 0.9737 | 1.0000 | 0.9286 | 0.9630 | 0.9954 |

#### Sonuclar - Secilmis Ozellikler (15 ozellik - ANOVA)

| Model | Accuracy | Precision | Recall | F1 | AUC |
|-------|----------|-----------|--------|-----|-----|
| Logistic Regression | 0.9737 | 1.0000 | 0.9286 | 0.9630 | 0.9993 |
| Random Forest | 0.9649 | 1.0000 | 0.9048 | 0.9500 | 0.9912 |
| KNN | 0.9386 | 0.9730 | 0.8571 | 0.9114 | 0.9899 |
| SVM | 0.9561 | 1.0000 | 0.8810 | 0.9367 | 0.9987 |

#### Sonuclar - PCA Sonrasi (10 bilesen)

| Model | Accuracy | Precision | Recall | F1 | AUC |
|-------|----------|-----------|--------|-----|-----|
| Logistic Regression | **0.9825** | 1.0000 | 0.9524 | 0.9756 | 0.9970 |
| Random Forest | 0.9386 | 0.9268 | 0.9048 | 0.9157 | 0.9934 |
| KNN | 0.9561 | 0.9744 | 0.9048 | 0.9383 | 0.9838 |
| SVM | 0.9649 | 1.0000 | 0.9048 | 0.9500 | 0.9967 |

### Ozellik Seti Karsilastirmasi

| Veri Seti | Ozellik Sayisi | En Iyi Model | Accuracy |
|-----------|----------------|--------------|----------|
| Tum Ozellikler | 30 | Random Forest / SVM | 0.9737 |
| Secilmis Ozellikler | 15 | Logistic Regression | 0.9737 |
| PCA | 10 | **Logistic Regression** | **0.9825** |

**En Iyi Yaklasim:** PCA ile Lojistik Regresyon

### Genel En Iyi Model

| Kriter | Model | Veri Seti | Skor |
|--------|-------|-----------|------|
| En Yuksek Accuracy | Logistic Regression | PCA | 0.9825 |
| En Yuksek F1 | Logistic Regression | PCA | 0.9756 |
| En Yuksek AUC | Logistic Regression | ANOVA Secilmis | 0.9993 |

### Notlar
```
En Iyi Performans:
- Model: Lojistik Regresyon
- Veri Seti: PCA (10 bilesen)
- Accuracy: %98.25

Classification Report (En Iyi Model):
              precision    recall  f1-score   support
      Benign       0.97      1.00      0.99        72
   Malignant       1.00      0.95      0.98        42
    accuracy                           0.98       114

Onemli Bulgular:
1. PCA + Logistic Regression en yuksek dogrulugu verdi
2. Tum modeller %93+ dogruluk elde etti
3. Random Forest, PCA verisinde diger setlere gore dusuk performans gosterdi
4. Logistic Regression hem basit hem de etkili
```

---

## 8. Sonuc ve Yorum

### Temel Bulgular
1. **PCA ile boyut indirgeme** en yuksek model performansini sagladi (30 ozellik -> 10 bilesen)
2. **Lojistik Regresyon** en basit ve en etkili model olarak one cikti (%98.25 accuracy)
3. Tum modeller **%93+ dogruluk** ile klinik kullanim icin yeterli performans gosterdi
4. **Yuksek korelasyonlu ozellikler** (|r| > 0.9) PCA'nin neden iyi calistigini acikliyor

### Hangi Yontem Neden Daha Iyi Calisti?
```
PCA + Logistic Regression kombinasyonu en iyi sonucu verdi cunku:

1. PCA, yuksek korelasyonlu ozellikleri ortogonal bilesenlere donusturdu
2. Bu, multicollinearity (coklu dogrusal baglanti) problemini cozdu
3. Logistic Regression, dogrusal olarak ayrilabilir veride cok etkili
4. 2D PCA gorsellestirmesi siniflarin buyuk olcude ayrilabilir oldugunu gosterdi
5. Daha az ozellik = daha az overfitting riski

ANOVA secimi iyi calismasina ragmen, yuksek korelasyonlu ozellikler hala
veri setinde kaldi. PCA bu problemi tamamen cozdu.
```

### Veri Setine Gore Yontem Seciminin Onemi
```
Bu proje, farkli veri hazirlama yontemlerinin ayni modeller uzerinde
farkli sonuclar verdigini gostermektedir:

- Tum Ozellikler: Random Forest ve SVM iyi (tree-based ve kernel yontemleri
  yuksek korelasyona daha dayanikli)

- ANOVA Secilmis: Logistic Regression en iyi (gereksiz ozellikler elendi
  ama korelasyon hala var)

- PCA: Logistic Regression en iyi (korelasyon tamamen giderildi,
  dogrusal model en etkili)

Sonuc: Veri setinin ozelliklerine gore on isleme stratejisi secilmeli!
```

### Gercek Hayata Katki
```
Klinik Uygulama Potansiyeli:
- %98+ dogruluk orani, yardimci tani sistemi olarak kullanilabilir
- Hizli ve dusuk maliyetli on tarama
- Uzman doktor kararini destekler (nihai karar hekimde)

Sinirliliklar:
- Veri seti tek bir kaynaktan (Wisconsin)
- Gercek klinik ortamda farkli goruntu kalitesi olabilir
- Model duzenli olarak yeni verilerle guncellenmeli

Oneriler:
- Coklu hastane verileriyle dogrulama yapilmali
- False Negative orani kritik (kanser kacirma) - Recall metrigi onemli
- Model aciklanabilirligi icin SHAP/LIME kullanilabilir
```

---



**Proje Durumu: TAMAMLANDI**
