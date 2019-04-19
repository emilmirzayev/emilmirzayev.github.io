---
title: "Pandasda seçim və dilimləmə"
date: 2019-04-19
tags: [python, pandas, data science]
excerpt: "Pandas seçim operatorları, seçim məntiqi və onların tətbiqi"
---
## Ön söz

Uzun aradan sonra, Pandas haqqında növbəti yazını yazmaq imkanım oldu. Hal-hazırda işlədiyim layihədə Pandas və Numpy
paketlərini olduqca intensiv istifadə edirəm. Pandas Pythonu nə qədər tamamlasa da, bəzi məqamlarda Python ilə fərqli "ağıla" sahibdir. Bu, ən çox da özünü `Boolean` məntiqi ilə işləyərkən göstərir. Pandas özü Numpy "üzərində" qurulduğu üçün, sonuncunun xassələrini daşıyır. Gəlin, bir örnək üzərində çalışaq.

```python
import numpy as np
import pandas as pd

np.random.state(state = 0)

df = pd.DataFrame(np.random.randint(0, 5, (6, 3)), 
                  columns = ["x", "y", "z"])

df

   x  y  z
0  4  0  3
1  3  3  1
2  3  2  4
3  0  0  4
4  2  1  0
5  1  1  0
```
Bu rəqəmlər tesadüfi generasiya olundumağına baxmayaraq, sizdə də eyni bu rəqəmlər generasiya olunacaq. Səbəb isə, `np.random.seed(seed = 0)` hissəsidir. Bu, generasiya alqoritminə başlanğıc toxum verir.

## Dilimləmə/seçmə
Pandasda verilənləri bir neçə üsul ilə dilimləmək olar.
1. İndeks üzərindən
2. Ad (Label) üzərindən
3. Maska ilə

Gəlin, qısa olaraq, hər birinə aid misallara baxaq.

```python
df.iloc[:3]

   x  y  z
0  4  0  3
1  3  3  1
2  3  2  4

df.loc[0:2, ["x", "y"]]

   x  y
0  4  0
1  3  3
2  3  2

# loc digər növ
df.loc[:, ["x", "y"]]
   x  y
0  4  0
1  3  3
2  3  2
3  0  0
4  2  1
5  1  1

df["x"]         # df.x

0    4
1    3
2    3
3    0
4    2
5    1

df.loc[[True, True, False, False, False, False]]

   x  y  z
0  4  0  3
1  3  3  1
```

Burdakı misallardan da göründüyü kimi, `.iloc` yalnız indeks üzərindən, `.loc` isə, əsasən ad üzərindən olmaqla yanaşı, həm də index + ad üzərindən də dilimləmə etməyə şərait yaradır. Burada ən maraqlı və faydalı olan isə **maska** ilə seçimdir. Ən çox istifadə olunan seçim də məhz budur. Ona görə, bu mövzumuz bunun üzərində olacaq.

Pandas Numpy üzərində yazıldığı və `DataFrame` obyektlərini daxildə Numpy array (matris) kimi saxladığı üçün, bu cür maskalar ilə seçim etmək imkanı var. Buna həm də **Boolean indexing** deyilir. Çünki seçimlər `True, False` məntiqi üzərindən gedir. Yalnızca `True` göstəricilərinin indeksinə uyğun gələn sıralar geri qaytarılır. Yuxarıdaki misalda yalnızca ilk iki sırada `True` olduğu üçün, onlar seçilmişdir.

Gəlin, `x` 3-ə bərabər olan sətirləri seçək. Bunun üçün bizə 3-ə bərabər olan `x` göstəricilərinin sıraları lazımdır.

```python
mask = df.x == 3

mask
0    False
1     True
2     True
3    False
4    False
5    False
Name: x, dtype: bool

df[mask]

   x  y  z
1  3  3  1
2  3  2  4

df.loc[mask]

   x  y  z
1  3  3  1
2  3  2  4

df[df[x] == 3]

   x  y  z
1  3  3  1
2  3  2  4
```
Burada biz ilk öncə, `x` qiymətlərinin 3-ə bərabər olduğu indeksləri tapdıq, daha sonra isə, həmin indekslərə görə, öz seçimimizi etdik. Sonuncu üsul isə, bu iki işi eyni anda görür. Sırf bu halda, sonuncu yazdığımı istifadə etmək daha məqsədəuyğundur.

## Boolean sehri

Gəlin indi seçimi bir az çətinləşdirək. `x`-i 3-ə, `y`-i isə 2-yə barəbər olan sətirləri seçək (yalnızca bir sətirdir). Bunun üçün bizə Pythonun doğma Boolean operatorları kömək edə bilər. ` df[(df.x == 3) and (df.y == 2)]` Bu cür edə bilərik məsələn..

```python
df[(df.x == 3) and (df.y == 2)]

---------------------------------------------------------------------------
ValueError                                Traceback (most recent call last)
<ipython-input-49-c1b8b2f1e03c> in <module>
----> 1 df[(df.x == 3) and (df.y == 2)]

~\Anaconda3\lib\site-packages\pandas\core\generic.py in __nonzero__(self)
   1476         raise ValueError("The truth value of a {0} is ambiguous. "
   1477                          "Use a.empty, a.bool(), a.item(), a.any() or a.all()."
-> 1478                          .format(self.__class__.__name__))
   1479
   1480     __bool__ = __nonzero__

ValueError: The truth value of a Series is ambiguous. Use a.empty, a.bool(), a.item(), a.any() or a.all().
```
İlk baxışdan bu davranış qəribə gələ bilər. Gəlin görək burda nə baş verdi. Mesajda deyilir ki, Pandas burdaki məntiqi bimənalı kimi qəbul edə bilmir. Səbəb isə çox sadədir. Pandas vektor və matrislər üzərində çalışdığı üçün, adi `and, or` məntiqi onu çaşdırır. Pandasda bu operatorların yerinə binar operatorlar istifadə edilir. Onlar bunlardır:

1. **`&`** AND
2. **`|`** OR
3. **`~`** NOT

İndi isə bu cür yoxlayaq.

```python
df[(df.x == 3) & (df.y == 2)]   # df["x"]; df["y"]

   x  y  z
2  3  2  4
```

İşə yaradı. Eyni üsul ilə digər operatorları da yoxlamaq olar. Hətta, onları birləşdirib daha mürəkkəb məntiq ilə seçim etmək olar.

```python
# OR
df[(df.x == 3) | (df.y == 2)]

   x  y  z
1  3  3  1
2  3  2  4

# NOT OR
df[~((df.x == 3) | (df.y == 2))]

   x  y  z
0  4  0  3
3  0  0  4
4  2  1  0
5  1  1  0
```
Burada sonuncu misala diqqət edin. **~** operatoru üst səviyyə **NOT** məntiqini vermək üçün mötərizə xaricində amma, indeks daxilində qalıb. Bizim dilə tərcümə etsək, belə olar. Mənə X-i 3-ə, və ya Y-i 2-yə bərabər olanlardan başqa digər bütün sətirləri ver.

## Pandas və Pythonda Boolean fərqi
Pythonun öz doğma **and, or** operatorları skalyar vahidlərlə işləmək üçün nəzərdə tutulub. Yəni, onlar müqayisə aparanda obyektləri vahid kimi qəbul edirlər. Örnək.

```python
a = [1, 2, 3]

b = [2, 2, 3]

c = [1, 2, 3]

a == b
False

a == c
True
```
Burada Python hər 3 listin bütün elementlərini bir-biri ilə müqayisə edir, və sonda yalnız bir nəticə çıxarır. Bərabərdir, ya yox.
Pandas isə, skalyar yox, məhz vektorial vahidlərlə işləmək üçün nəzərdə tutulub. Ona görə də, o, verilənlərə həm də element-element baxa bilməlidir. Gəlin, yuxarıdakı listləri Pandas obyektinə çevirib müqayisə edək.

```
pd.Series(a) == pd.Series(b)

0    False
1     True
2     True
dtype: bool

pd.Series(a) == pd.Series(c)

0    True
1    True
2    True
dtype: bool

```
Pythonun öz operatorları və Pandas operatorlarını bu cür cədvəldə müqayisə edə bilərsiniz. Bu cür olduqda, məntiq daha aydın olacaq.

| Operator| Mənası|
| ------------- |:-------------:|
| ifadə1 **and** ifadə2| AND |
| ifadə1 **or** ifadə2| OR |
| **not** ifadə1 | NOT |

Pandas isə, onları bu cür şəkildəyişməyə uğradır

| Operator| Mənası|
| ------------- |:-------------:|
| ifadə1 **`&`** ifadə2| elementvari AND |
| ifadə1 **`|`** ifadə2| elementvari OR |
| **`~`** ifadə1 | elementvari NOT |

Bu cür mürəkkəb seçimlər və dilimləmə zamanı, Pandasın digər çox güclü bir funksiyası olan `pd.eval()` da istifadə etmək olar. Şəxsən mən demək olar yalnızca ondan istifadə edirəm. Növbəti məqaləmdə isə onun haqqında yazacam.