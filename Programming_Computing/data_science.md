# Clustering
## Hieracrhical Clustering

### 방법
1. 각 요소들을 하나의 클러스터로 취급
2. 클러스터 사이에 거리가 짧은 것부터 하나씩 클러스터를 묶는다.
3. 2번을 반복해서 1개의 클러스터가 되면 끝.

### 종류
1. single: 클러스터내의 요소들간에 가장 가까운 거리를 기준으로 묶는것.  
ex) {1,2,3}과 {4,5,6}간의 single 거리는 1
2. complete: 가장 먼거리가 기준  
ex) 위의 예에서는 6-1=5
3. average: median 값간의 거리
ex) 위의 예에서는 5-2=3

### 다차원 요소들의 거리는 어떻게 측정하는가?
1. 유클리드 거리: root(|X1-X2|^2) - 요소들간의 직선거리
2. 맨하탄 거리: |X1-X2| - 맨하탄 블록은 따라 가듯 각 feature사이의 절대 거리들의 합.


## K-means

### 방법
1. 임의의 클러스터 갯수를 정하고, 클러스터의 노드를 랜덤하게 위치시킴.
2. 요소 입장에서 제일 가까운 노드에 클러스터를 할당함.
3. 한 클러스터안에 할당된 요소들의 중앙노드(요소들의 평균, centroid)를 구함.
4. centroid가 다시 클러스터의 노드가 되고, 2번부터 반복.


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

