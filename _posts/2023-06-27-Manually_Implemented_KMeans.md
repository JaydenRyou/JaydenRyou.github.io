---
layout: single
title: "Manually Implemented K-Means"
categories: Project
tag: [python, Sklearn, K-Means]
author_profile: false
---


# 프로젝트 개요

프로젝트의 목표는 KMeans 알고리즘을 수동으로 구현하고 Sklearn 라이브러리의 KMeans 모델과 비교하는 것입니다.

데이터는 Kaggle의 Customer Segmentation Classification을 사용했습니다.

데이터 전처리 후 EDA를 수행하고 KMeans 알고리즘을 수동으로 구현합니다.

Sklearn의 KMeans를 사용하여 얻은 중심점과 수동으로 구현한 KMeans의 중심점 구하여 비교합니다. 

모델 평가는 두 구현에서 얻는 중심점의 유클리드 거리를 계산하는 것입니다. 만약 중심점이 유사하다면, 두 중심점들의 거리가 작을 것으로 예상됩니다.

이 프로젝트를 통해 KMeans 알고리즘을 이해하고 구현하며 Sklearn의 KMeans와 성능 비교를 하며 KMeans 알고리즘의 이해를 깊이하는 것입니다.


```python
import numpy as np 
import pandas as pd
import matplotlib.pyplot as plt
from sklearn.compose import ColumnTransformer
from sklearn.preprocessing import OneHotEncoder, StandardScaler
np.random.seed(317)
%matplotlib inline
```

# Data EDA


```python
train_df = pd.read_csv(...\Classifying Customers into Segments\Train.csv')
test_df = pd.read_csv(...\Classifying Customers into Segments\Test.csv')
```


```python
train_df.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 8068 entries, 0 to 8067
    Data columns (total 11 columns):
     #   Column           Non-Null Count  Dtype  
    ---  ------           --------------  -----  
     0   ID               8068 non-null   int64  
     1   Gender           8068 non-null   object 
     2   Ever_Married     7928 non-null   object 
     3   Age              8068 non-null   int64  
     4   Graduated        7990 non-null   object 
     5   Profession       7944 non-null   object 
     6   Work_Experience  7239 non-null   float64
     7   Spending_Score   8068 non-null   object 
     8   Family_Size      7733 non-null   float64
     9   Var_1            7992 non-null   object 
     10  Segmentation     8068 non-null   object 
    dtypes: float64(2), int64(2), object(7)
    memory usage: 693.5+ KB



```python
train_df.nunique()
```




    ID                 8068
    Gender                2
    Ever_Married          2
    Age                  67
    Graduated             2
    Profession            9
    Work_Experience      15
    Spending_Score        3
    Family_Size           9
    Var_1                 7
    Segmentation          4
    dtype: int64



'ID' 컬럼은 8068개의 unique한 값을 가지고 있습니다. 군집화에 유용한 정보를 주지 못하기 때문에 train_df와 test_df에서 제거할 것입니다.

비지도학습을 구현할 것이기 때문에 train_df의 'Segmentation' 컬럼을 제거할 것입니다.

또한, 'Segmentation'의 unique한 값은 4이므로, k = 4를 사용하겠습니다.


```python
train_df = train_df.drop(['ID', 'Segmentation'], axis = 1)
test_df = test_df.drop('ID', axis = 1)
```

군집화에 목표를 둔 프로젝트로서 train_df와 test_df를 결합하겠습니다.


```python
combined_df = pd.concat([train_df, test_df], axis = 0)
```


```python
print(combined_df.shape)
combined_df.isna().sum()
```

    (10695, 9)





    Gender                0
    Ever_Married        190
    Age                   0
    Graduated           102
    Profession          162
    Work_Experience    1098
    Spending_Score        0
    Family_Size         448
    Var_1               108
    dtype: int64



결측값을 처리하기 전에, 데이터 시각화를 위해 categorical values의 비율을 pie 차트를 사용하고, numerical values는 histogram을 사용하겠습니다.


```python
categorical_columns = [col for col in combined_df.columns if combined_df[col].dtypes == 'object']
numerical_columns = [col for col in combined_df.columns if combined_df[col].dtypes != 'object']
```


```python
print('numerical_col: ', numerical_columns)
print('categorical_col: ', categorical_columns)
```

    numerical_col:  ['Age', 'Work_Experience', 'Family_Size']
    categorical_col:  ['Gender', 'Ever_Married', 'Graduated', 'Profession', 'Spending_Score', 'Var_1']



```python
plt.figure(figsize = (8, 10))
for i, col in enumerate(categorical_columns):
    plt.subplot(3, 2, i+1)
    combined_df[col].value_counts().plot(kind = 'pie', autopct = '%2.f%%', shadow = True)
    centre_circle = plt.Circle((0, 0), 0.80, fc = 'white')
    fig = plt.gcf()
    fig.gca().add_artist(centre_circle)
    
plt.tight_layout()
plt.show()
```



![output_15_0]({{site.url}}\images\2023-06-27-Manually_Implemented_KMeans\output_15_0.png)
    



```python
plt.figure(figsize = (8, 8))
for i, col in enumerate(numerical_columns):
    plt.subplot(2, 2, i+1)
    plt.hist(combined_df[col], bins = combined_df[col].nunique(), edgecolor = 'black')
    plt.title(col)
plt.tight_layout()
plt.show()
```


 ![output_16_0]({{site.url}}\images\2023-06-27-Manually_Implemented_KMeans\output_16_0.png)
    


결측값 처리를 위해 가장 빈도수 높은 값을 넣겠습니다.


```python
most_freq_values = combined_df.mode().iloc[0]
combined_df.fillna(most_freq_values, inplace = True)
```

'Spending_Score' 컬럼은 ordinal variable이기 때문에 dictionary를 만들어 알맞은 값으로 변형하겠습니다.


```python
Spending_Score_mapping = {
    'Low': 1,
    'Average': 2,
    'High': 3
}
```


```python
combined_df['Spending_Score'] = combined_df['Spending_Score'].map(Spending_Score_mapping)
```

데이터 전처리를 위해 categorical features는 OneHotEncoder를 numerical features는 StandardScaler를 사용하겠습니다.


```python
categorical_columns.remove('Spending_Score')

num_transformer = StandardScaler()
cat_transformer = OneHotEncoder(drop = 'first')

preprocessor = ColumnTransformer(
    transformers = [
        ('num', num_transformer, numerical_columns),
        ('cat', cat_transformer, categorical_columns)
    ])
X_transformed = preprocessor.fit_transform(combined_df)
pd.DataFrame(X_transformed)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
    
    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>0</th>
      <th>1</th>
      <th>2</th>
      <th>3</th>
      <th>4</th>
      <th>5</th>
      <th>6</th>
      <th>7</th>
      <th>8</th>
      <th>9</th>
      <th>10</th>
      <th>11</th>
      <th>12</th>
      <th>13</th>
      <th>14</th>
      <th>15</th>
      <th>16</th>
      <th>17</th>
      <th>18</th>
      <th>19</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>-1.282499</td>
      <td>-0.447327</td>
      <td>0.787219</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>-0.328606</td>
      <td>-0.447327</td>
      <td>0.126415</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1.400325</td>
      <td>-0.447327</td>
      <td>-1.195194</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1.400325</td>
      <td>-0.755089</td>
      <td>-0.534390</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>-0.209369</td>
      <td>-0.447327</td>
      <td>2.108828</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>10690</th>
      <td>-0.865170</td>
      <td>2.014771</td>
      <td>0.787219</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>10691</th>
      <td>-0.507461</td>
      <td>-0.447327</td>
      <td>-1.195194</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>10692</th>
      <td>0.565669</td>
      <td>-0.447327</td>
      <td>-0.534390</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>10693</th>
      <td>0.207959</td>
      <td>-0.447327</td>
      <td>1.448024</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>10694</th>
      <td>-0.030514</td>
      <td>2.014771</td>
      <td>0.126415</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
    </tr>
  </tbody>
</table>
<p>10695 rows × 20 columns</p>
</div>




```python
X_transformed = pd.DataFrame(X_transformed)
X_transformed['cluster'] = np.zeros(len(X_transformed))
X_transformed
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
    
    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>0</th>
      <th>1</th>
      <th>2</th>
      <th>3</th>
      <th>4</th>
      <th>5</th>
      <th>6</th>
      <th>7</th>
      <th>8</th>
      <th>9</th>
      <th>...</th>
      <th>11</th>
      <th>12</th>
      <th>13</th>
      <th>14</th>
      <th>15</th>
      <th>16</th>
      <th>17</th>
      <th>18</th>
      <th>19</th>
      <th>cluster</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>-1.282499</td>
      <td>-0.447327</td>
      <td>0.787219</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>-0.328606</td>
      <td>-0.447327</td>
      <td>0.126415</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1.400325</td>
      <td>-0.447327</td>
      <td>-1.195194</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1.400325</td>
      <td>-0.755089</td>
      <td>-0.534390</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>...</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>-0.209369</td>
      <td>-0.447327</td>
      <td>2.108828</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>10690</th>
      <td>-0.865170</td>
      <td>2.014771</td>
      <td>0.787219</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>10691</th>
      <td>-0.507461</td>
      <td>-0.447327</td>
      <td>-1.195194</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>10692</th>
      <td>0.565669</td>
      <td>-0.447327</td>
      <td>-0.534390</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>10693</th>
      <td>0.207959</td>
      <td>-0.447327</td>
      <td>1.448024</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>10694</th>
      <td>-0.030514</td>
      <td>2.014771</td>
      <td>0.126415</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
    </tr>
  </tbody>
</table>
<p>10695 rows × 21 columns</p>
</div>



# Manually Implemented K-Means

4가지 함수를 만들어 수동으로 KMeans를 구현하겠습니다.

1. initiate_centroids(X): 랜덤하게 4개의 초기 중심점을 할당합니다.
2. euclidean_distance(a, b): 두 점의 euclidean distance를 구합니다.
3. assign_cluster(centroid, X): 데이터셋 X를 4개의 군집화를 통해 묶습니다.
4. update_centrodis(X): 각 군집의 중심점을 업데이트 합니다.


```python
def initiate_centroids(X):
    centroids = X.sample(4)
    return centroids

def euclidean_distance(a, b):
    return np.square(np.sum((a-b)**2))

def assign_cluster(centroid, X):
    for idx_data in range(len(X)):
        distance = []
        for i in range(4):
            distance.append(euclidean_distance(centroid.iloc[i], X.iloc[idx_data]))
        cent_distance = np.array(distance)
        cluster = np.argmin(cent_distance)
        X.loc[idx_data, 'cluster'] = cluster
    return X

def update_centroids(X):
    centroids = []
    for i in range(4):
        centroids.append(X[X.cluster == i].mean(axis = 0))
    return pd.DataFrame(centroids)
```

알고리즘은 임의로 4개의 초기 중심점을 선택한 후, 데이터셋의 각 데이터 포인트를 euclidean distance 기반으로 4개의 군집 중 하나에 할당합니다. 그런 다음 각 군집에 할당된 모든 데이터 포인트의 중심점으로 업데이트 됩니다.

알고리즘은 데이터 포인트를 가장 가까운 중심점에 할당하고 중심점을 업데이트하는 과정을 반복하며, 수렴할 때까지 진행합니다. 즉, 이전 중심점과 새로운 중심점 사이의 차이가 임계값 이하일 때까지 반복합니다.


```python
init_centroids = initiate_centroids(X_transformed)
X = assign_cluster(init_centroids,X_transformed)
centroids = update_centroids(X)
while(True):
    assign_cluster(centroids, X)
    updated_centroids = update_centroids(X)
    if euclidean_distance(centroids.values, updated_centroids.values) == 0:
        break
    centroids = updated_centroids
```

수동으로 구현한 KMeans 중심점입니다.


```python
pd.DataFrame(updated_centroids)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
    
    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>0</th>
      <th>1</th>
      <th>2</th>
      <th>3</th>
      <th>4</th>
      <th>5</th>
      <th>6</th>
      <th>7</th>
      <th>8</th>
      <th>9</th>
      <th>...</th>
      <th>11</th>
      <th>12</th>
      <th>13</th>
      <th>14</th>
      <th>15</th>
      <th>16</th>
      <th>17</th>
      <th>18</th>
      <th>19</th>
      <th>cluster</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>-0.739639</td>
      <td>-0.409659</td>
      <td>0.837213</td>
      <td>0.382313</td>
      <td>0.347680</td>
      <td>0.411903</td>
      <td>0.184264</td>
      <td>0.111634</td>
      <td>0.062878</td>
      <td>0.032616</td>
      <td>...</td>
      <td>0.022192</td>
      <td>0.004707</td>
      <td>0.048083</td>
      <td>0.081036</td>
      <td>0.123067</td>
      <td>0.202757</td>
      <td>0.013786</td>
      <td>0.526227</td>
      <td>0.034297</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>-0.428388</td>
      <td>1.889441</td>
      <td>-0.146604</td>
      <td>0.432039</td>
      <td>0.469795</td>
      <td>0.644013</td>
      <td>0.104099</td>
      <td>0.098166</td>
      <td>0.121359</td>
      <td>0.049622</td>
      <td>...</td>
      <td>0.092772</td>
      <td>0.007551</td>
      <td>0.041532</td>
      <td>0.064725</td>
      <td>0.100863</td>
      <td>0.120280</td>
      <td>0.013484</td>
      <td>0.663970</td>
      <td>0.024272</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1.609705</td>
      <td>-0.443384</td>
      <td>-0.674806</td>
      <td>0.560064</td>
      <td>0.904431</td>
      <td>0.672184</td>
      <td>0.037907</td>
      <td>0.051789</td>
      <td>0.082755</td>
      <td>0.095035</td>
      <td>...</td>
      <td>0.010144</td>
      <td>0.417512</td>
      <td>0.018153</td>
      <td>0.017619</td>
      <td>0.054992</td>
      <td>0.067806</td>
      <td>0.004805</td>
      <td>0.832355</td>
      <td>0.012280</td>
      <td>2.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>-0.005273</td>
      <td>-0.364107</td>
      <td>-0.238897</td>
      <td>0.714572</td>
      <td>0.689534</td>
      <td>0.749624</td>
      <td>0.029544</td>
      <td>0.081122</td>
      <td>0.171007</td>
      <td>0.102153</td>
      <td>...</td>
      <td>0.017777</td>
      <td>0.008513</td>
      <td>0.037306</td>
      <td>0.042313</td>
      <td>0.108413</td>
      <td>0.130696</td>
      <td>0.009765</td>
      <td>0.666750</td>
      <td>0.024787</td>
      <td>3.0</td>
    </tr>
  </tbody>
</table>
<p>4 rows × 21 columns</p>
</div>



# Sklearn K-Means


```python
from sklearn.cluster import KMeans
km = KMeans(n_clusters = 4,
           init = init_centroids.iloc[:,:-1])
y_km = km.fit_predict(X_transformed.iloc[:,:-1].values)
```

    C:\Users\hyoye\anaconda3\lib\site-packages\sklearn\cluster\_kmeans.py:870: FutureWarning: The default value of `n_init` will change from 10 to 'auto' in 1.4. Set the value of `n_init` explicitly to suppress the warning
      warnings.warn(
    C:\Users\hyoye\anaconda3\lib\site-packages\sklearn\cluster\_kmeans.py:1362: RuntimeWarning: Explicit initial center position passed: performing only one init in KMeans instead of n_init=10.
      super()._check_params_vs_input(X, default_n_init=10)


Sklearn KMeans 중심점입니다.


```python
pd.DataFrame(km.cluster_centers_)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
    
    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>0</th>
      <th>1</th>
      <th>2</th>
      <th>3</th>
      <th>4</th>
      <th>5</th>
      <th>6</th>
      <th>7</th>
      <th>8</th>
      <th>9</th>
      <th>10</th>
      <th>11</th>
      <th>12</th>
      <th>13</th>
      <th>14</th>
      <th>15</th>
      <th>16</th>
      <th>17</th>
      <th>18</th>
      <th>19</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>-0.616786</td>
      <td>-0.365220</td>
      <td>1.231231</td>
      <td>0.593028</td>
      <td>0.417615</td>
      <td>0.386789</td>
      <td>0.090642</td>
      <td>0.089541</td>
      <td>0.092110</td>
      <td>0.089541</td>
      <td>0.379450</td>
      <td>0.013578</td>
      <td>0.004037</td>
      <td>0.049541</td>
      <td>0.096514</td>
      <td>0.112661</td>
      <td>0.226789</td>
      <td>0.013578</td>
      <td>0.498716</td>
      <td>0.032661</td>
    </tr>
    <tr>
      <th>1</th>
      <td>-0.363739</td>
      <td>1.822231</td>
      <td>-0.257487</td>
      <td>0.476938</td>
      <td>0.511776</td>
      <td>0.684004</td>
      <td>0.089794</td>
      <td>0.087341</td>
      <td>0.130520</td>
      <td>0.058391</td>
      <td>0.146712</td>
      <td>0.088322</td>
      <td>0.009323</td>
      <td>0.037291</td>
      <td>0.049068</td>
      <td>0.096663</td>
      <td>0.104514</td>
      <td>0.011286</td>
      <td>0.705103</td>
      <td>0.021590</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1.437711</td>
      <td>-0.461720</td>
      <td>-0.379602</td>
      <td>0.606344</td>
      <td>0.952826</td>
      <td>0.708418</td>
      <td>0.040260</td>
      <td>0.051240</td>
      <td>0.095161</td>
      <td>0.115087</td>
      <td>0.007320</td>
      <td>0.012200</td>
      <td>0.323302</td>
      <td>0.018300</td>
      <td>0.021553</td>
      <td>0.060187</td>
      <td>0.070354</td>
      <td>0.003660</td>
      <td>0.821065</td>
      <td>0.013420</td>
    </tr>
    <tr>
      <th>3</th>
      <td>-0.320555</td>
      <td>-0.455834</td>
      <td>-0.546186</td>
      <td>0.507342</td>
      <td>0.526634</td>
      <td>0.714656</td>
      <td>0.115462</td>
      <td>0.111431</td>
      <td>0.143680</td>
      <td>0.037144</td>
      <td>0.114886</td>
      <td>0.023323</td>
      <td>0.005471</td>
      <td>0.042327</td>
      <td>0.042327</td>
      <td>0.125828</td>
      <td>0.135618</td>
      <td>0.012957</td>
      <td>0.634322</td>
      <td>0.029657</td>
    </tr>
  </tbody>
</table>
</div>



# 모델 평가

수동 구현한 KMeans와 Sklearn의 KMeans의 중심점 거리는 0.47로 데이터셋에 20개의 컬럼이 있는데도 좋은 성능을 보여줬습니다. 또한 초기 중심점과의 거리는 148.43으로 알고리즘이 잘 작동되었다고 생각됩니다. 

그러나 Sklearn의 KMeans는 바로 실행되었지만 저의 수동 알고리즘 수행 시간은 약 3분 정도 걸렸습니다. 찾아보니 Sklearn의 KMeans 알고리즘은 Lloyd's algorithm을 C언어로 구현하여 속도가 빠르다고 합니다.

**수동으로 구현한 KMeans 중심점과 Sklearn KMeans 중심점의 euclidean distance**


```python
euclidean_distance(updated_centroids.iloc[:,:-1].values, km.cluster_centers_)
```




    0.4735496801988983



**초기 중심점과 Sklearn KMeans 중심점의 euclidean distance**


```python
euclidean_distance(init_centroids.iloc[:,:-1].values, km.cluster_centers_)
```




    148.42659292257596

