# AKRB hesaplaması

## AAPS AKRB değerini nasıl hesaplar?

Karbonhidratlar bir yemeğin veya düzeltmenin bir parçası olarak girildiğinde, AAPS bunları aktif karbonhidratlara (AKRB) ekler. AAPS, KŞ değerlerinde gözlemlenen sapmalara dayalı olarak karbonhidratları emer (kaldırır). Emilim oranı, karbonhidrat duyarlılık faktörüne bağlıdır. Bu bir profil değeri değildir, ancak İDF/Kİ olarak hesaplanır ve 1 g karbonhidratın KŞ'nizi kaç mg/dl yükselteceğini gösterir.

The formula is: `absorbed_carbs = deviation * ic / isf` It means:
* increasing ic will increase carbs absorbed every 5 minutes thus shorten total time of absorption
* increasing isf will decrease carbs absorbed every 5 minutes thus prolong total time of absorption
* changing profile % increase/decrease both values thus has no impact on carbs absorption time

Örneğin, profil İDF'niz 100 ve Kİ'niz 5 ise, KDF'niz 20 olacaktır. Kan şekerinizin yükseldiği her 20 mg/dl için, 1 g karbonhidrat AAPS tarafından emilir. Pozitif AKRB de bu hesaplamayı etkiler. Dolayısıyla, AAPS, KŞ'nizin AİNS nedeniyle 20 mg/dl düşmesini beklerse ve bunun yerine düz kalırsa, 1 g karbonhidrat emer.

Karbonhidratlar, hangi hassasiyet algoritmasının kullanıldığına bağlı olarak aşağıda açıklanan yöntemlerle de emilecektir.

### Oref1

Emilmeyen karbonhidratlar belirtilen süreden sonra kesilir.

![Oref1](../images/cob_oref0_orange_II.png)

### AAPS, Ağırlıklı Ortalama

emilim, belirtilen süreden sonra `AKRB == 0` olarak hesaplanır

![AAPS, WheitedAverage](../images/cob_aaps2_orange_II.png)

KŞ sapmalarından hesaplanan değer yerine minimum karbonhidrat emilimi (min_5m_carbimpact) kullanılırsa, AKRB grafiğinde turuncu bir nokta görünür.

(COB-calculation-detection-of-wrong-cob-values)=

## Yanlış AKRB değerlerinin tespiti

AAPS, bir önceki öğünden AKRB ile bolus yapmak üzereyseniz, algoritma mevcut AKRB hesaplamasının yanlış olabileceğini düşünür ve sizi uyarır. Bu durumda bolus sihirbazından sonraki onay ekranında size ek bir ipucu verecektir.

### AAPS, yanlış AKRB değerlerini nasıl tespit eder?

Normalde AAPS, karbonhidrat emilimini KŞ sapmaları yoluyla tespit eder. Karbonhidratları girdiyseniz, ancak AAPS bunların KŞ sapmaları aracılığıyla tahmini emilimini göremezse, bunun yerine emilimi hesaplamak için [min_5m_carbimpact](../Configuration/Config-Builder.md?highlight=min_5m_carbimpact#absorption-settings) yöntemini kullanır. ('fallback' olarak adlandırılır). Bu yöntem, KŞ sapmalarını dikkate almadan yalnızca minimum karbonhidrat emilimini hesapladığı için yanlış AKRB değerlerine yol açabilir.

![Hint on wrong COB value](../images/Calculator_SlowCarbAbsorption.png)

Yukarıdaki ekran görüntüsünde, karbonhidrat emiliminin %41'i sapmalardan tespit edilen değer yerine min_5m_carbimpact tarafından matematiksel olarak hesaplanmıştır.  Bu, belki de algoritma tarafından hesaplanandan daha az aktif karbonhidratınız olduğu anlamına gelir.

### Bu uyarı ile nasıl başa çıkılır?

- Tedaviyi iptal etmeyi düşünün - Tamam yerine İptal'e basın.
- Bolus sihirbazı ile AKRB'yi dikkate almadan (tiki kaldırıp hesaplamaya dahil etmeyerek) yaklaşan öğününüzü tekrar hesaplayın.
- Düzeltme bolusuna ihtiyacınız olduğundan, eminseniz manuel olarak girin.
- Her durumda aşırı doz almamaya dikkat edin!

### Algoritma neden AKRB'yi doğru algılamıyor?

- Belki karbonhidratları fazla tahmin ederek girdiniz.
- Bir önceki öğünden sonra aktivite / egzersiz yaptınız
- I:C oranının ayarlanması gerekiyor
- min_5m_carbimpact değeri yanlış (SMB ile 8, AMA ile 3 önerilir)

## Girilen karbonhidratların manuel olarak düzeltilmesi

Karbonhidratları gereğinden fazla veya az girdiyseniz, bunu [burada](Screenshots-carb-correction) açıklandığı gibi tedaviler sekmesi ve eylemler sekmesi aracılığıyla düzeltebilirsiniz.
