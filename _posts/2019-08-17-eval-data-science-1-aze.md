---
title: "İrəli Data Science. Pandas Eval 1"
date: 2019-08-17
tags: [python, pandas, advanced data science, numpy, eval, numexpr, hints]
excerpt: "Pandas Eval sinfi və onun data science üçün istifadəsi 1."
---
Pandas öz çoxfunksionallığı ilə artıq nəinki Python istifadəçiləri arasında, habelə ümumən Data Science ekosistemində olan hamı tərəfindən tanınır. Bu dəfə, onun irəli funksiyalarından biri olan `pandas.eval` haqqında yazacağam.

`pandas.eval` nədir? `pandas.eval` rəsmi [dokumentasiyaya]("https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.eval.html") istinadən sətir halında yazılmış hesablama əmrlərini yerinə yetirən `pandas` metodudur.

Gəlin ilk öncə çox sadə bir misal ilə başlayaq.

```python
In: import pandas as pd

In: pd.eval('10 + 1')
Out: 11

In: pd.eval('1e6 ** 2 + 23 ** 45')
Out: 1.8956258430116204e+61

In: eval("10 + 1")
Out: 11

In: eval("1e6 ** 2 + 23 ** 45")
Out: 1.8956258430116204e+61
```
İlk baxışda bu funksiyanın etdiyi iş Pythonun özünün `eval` funksiyası ilə eyni olduğunu görürük və bu 50 faiz doğrudur.
`pandas.eval` özlüyündə `numexpr` alətini işlədir və nəticədə hesablamalar zamanı həm sürətdə qazanc, həm də rahatlıq verir. Sürətdə qazanc hesablanan dəyişənlərin ölçüsü artdıqca artır. Yəni, daha böyük verilənlər ilə hesablama apardıqda daha böyük qazanc olur.

Tutaq ki, bizə iki `DataFrame` obyekti verilib:
```python
import numpy as np
import pandas as pd

np.random.seed(0)

df1 = pd.DataFrame(np.random.randint(0, 10, (10, 5)), 
                    columns = list("ABCDE"))

df2 = pd.DataFrame(np.random.randint(0, 10, (10, 5)), 
                    columns = list("EFGHI"))
```
Bizə isə birinci df1 üçün yeni Z sütunu hesablamaq lazımdır. Hesablama isə təkcə df1 elementlərindən yox, həm də df2 elementlərindən istifadə edərək edilməlidir. Bunun üçün:

```python
df1["Z"] = pd.eval("df1.A + 2 * df2.H + (df1.E + df2.F)** 2")

Out:
   A  B  C  D  E    Z
0  0  4  5  5  6   81
1  8  4  1  4  9  336
2  8  1  1  7  9  338
3  9  3  6  7  2   36
4  0  3  5  9  4   37
5  4  6  4  4  3   40
6  4  4  8  4  3   91
7  7  5  5  0  1   94
8  5  9  3  0  5  180
9  0  1  2  4  2  106
```

`pandas.eval` təkcə sadə riyazi əməlləri yox, həm də müqayisə, şərt, digər qeyri-riyazi funksiyalar, habelə generatorları dəstəkləyir.

```python
In: pd.eval("df1.A > df2.E")
Out:
0    False
1     True
2     True
3     True
4    False
5     True
6     True
7    False
8     True
9    False
dtype: bool

In: pd.eval("df1.A ==  df2.E")
Out:
0     True
1    False
2    False
3    False
4    False
5    False
6    False
7    False
8    False
9    False
dtype: bool
```
`pandas.DataFrame.Eval` da özlüyündə `pandas.eval` işlədir və əgər hesablamalar yalnız bir `DataFrame` üzərində gedirsə, istifadə oluna bilər. Bu zaman sütun vahidləri üçün referans yalnız sütun adı ilə olacaqdır:

```python
In: df1.eval("A ** 2")
Out:
0     0
1    64
2    64
3    81
4     0
5    16
6    16
7    49
8    25
9     0
dtype: int32

In: df1.eval("X = A ** 2")
Out:
   A  B  C  D  E    Z   X
0  0  4  5  5  6   81   0
1  8  4  1  4  9  336  64
2  8  1  1  7  9  338  64
3  9  3  6  7  2   36  81
4  0  3  5  9  4   37   0
5  4  6  4  4  3   40  16
6  4  4  8  4  3   91  16
7  7  5  5  0  1   94  49
8  5  9  3  0  5  180  25
9  0  1  2  4  2  106   0
```
ikinci misalda göründüyü kimi, nəticələri birbaşa yeni bir sütuna mənimsətmək mümkündür. **Lakin**, bu ilkin df1i dəyişməyəcək, sadəcə yeni df1 surəti qaytaracaq. `inplace = True` ilə bunu dəyişmək olar.
