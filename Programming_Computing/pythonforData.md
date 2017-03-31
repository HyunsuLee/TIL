# 파이썬으로 데이터 분석하기.
- nippe DNA chip 분석중 scatter plot 좀 그리려다 R로 그리기에는 너무 느려 python으로 test.

## library들 부르기
```python
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import csv
```

## data 부르기 및 확인
```python
comp = pd.read_csv('composite.csv')
comp.head()
```

## data subsetting
```python
comp_sub = comp.iloc[:, 3:14] #row 는 다 가져오고, col은 3~14번째껄로.
```

## matrix 형태의 scatter plotting
```python
# 사이즈 단위는 inch인듯. 100dpi로 output해줌. diagonal에 density plot해주는 option. 기본은 histogram.
pd.tools.plotting.scatter_matrix(comp_sub, alpha=0.2, figsize = (32,32), diagonal = 'kde')
# save. png가 제일 빠르다. tiff는 그리고 용량도 큼. pdf, jpg도 비교적 느림.
plt.savefig('scatter_matrix.png')
```
