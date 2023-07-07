---
layout: single
title: "Pandas DataFrame vs Spark DataFrame (1)"
categories: [Study Notes]
tag: [Spark, SQL]
author_profile: false
---

### titanic_train.csv 파일을 로드하고, 이를 DataFrame으로 변환


```python
# spark.read.csv() 메소드를 이용하여 csv 파일을 로드하고 DataFrame으로 변환
titanic_sdf = spark.read.csv('/FileStore/tables/titanic_train.csv', header=True, inferSchema=True)

print('titanic_sdf type: ', type(titanic_sdf))
titanic_sdf.show()
```

    titanic_sdf type:  <class 'pyspark.sql.dataframe.DataFrame'>
    +-----------+--------+------+--------------------+------+----+-----+-----+----------------+-------+-----+--------+
    |PassengerId|Survived|Pclass|                Name|   Sex| Age|SibSp|Parch|          Ticket|   Fare|Cabin|Embarked|
    +-----------+--------+------+--------------------+------+----+-----+-----+----------------+-------+-----+--------+
    |          1|       0|     3|Braund, Mr. Owen ...|  male|22.0|    1|    0|       A/5 21171|   7.25| null|       S|
    |          2|       1|     1|Cumings, Mrs. Joh...|female|38.0|    1|    0|        PC 17599|71.2833|  C85|       C|
    |          3|       1|     3|Heikkinen, Miss. ...|female|26.0|    0|    0|STON/O2. 3101282|  7.925| null|       S|
    |          4|       1|     1|Futrelle, Mrs. Ja...|female|35.0|    1|    0|          113803|   53.1| C123|       S|
    |          5|       0|     3|Allen, Mr. Willia...|  male|35.0|    0|    0|          373450|   8.05| null|       S|
    |          6|       0|     3|    Moran, Mr. James|  male|null|    0|    0|          330877| 8.4583| null|       Q|
    |          7|       0|     1|McCarthy, Mr. Tim...|  male|54.0|    0|    0|           17463|51.8625|  E46|       S|
    |          8|       0|     3|Palsson, Master. ...|  male| 2.0|    3|    1|          349909| 21.075| null|       S|
    |          9|       1|     3|Johnson, Mrs. Osc...|female|27.0|    0|    2|          347742|11.1333| null|       S|
    |         10|       1|     2|Nasser, Mrs. Nich...|female|14.0|    1|    0|          237736|30.0708| null|       C|
    |         11|       1|     3|Sandstrom, Miss. ...|female| 4.0|    1|    1|         PP 9549|   16.7|   G6|       S|
    |         12|       1|     1|Bonnell, Miss. El...|female|58.0|    0|    0|          113783|  26.55| C103|       S|
    |         13|       0|     3|Saundercock, Mr. ...|  male|20.0|    0|    0|       A/5. 2151|   8.05| null|       S|
    |         14|       0|     3|Andersson, Mr. An...|  male|39.0|    1|    5|          347082| 31.275| null|       S|
    |         15|       0|     3|Vestrom, Miss. Hu...|female|14.0|    0|    0|          350406| 7.8542| null|       S|
    |         16|       1|     2|Hewlett, Mrs. (Ma...|female|55.0|    0|    0|          248706|   16.0| null|       S|
    |         17|       0|     3|Rice, Master. Eugene|  male| 2.0|    4|    1|          382652| 29.125| null|       Q|
    |         18|       1|     2|Williams, Mr. Cha...|  male|null|    0|    0|          244373|   13.0| null|       S|
    |         19|       0|     3|Vander Planke, Mr...|female|31.0|    1|    0|          345763|   18.0| null|       S|
    |         20|       1|     3|Masselmani, Mrs. ...|female|null|    0|    0|            2649|  7.225| null|       C|
    +-----------+--------+------+--------------------+------+----+-----+-----+----------------+-------+-----+--------+
    only showing top 20 rows
    
    


```python
import pandas as pd

# pandas DataFrame을 spark DataFrame으로 부터 생성
titanic_pdf = titanic_sdf.select('*').toPandas()

print('titanic_pdf type: ', type(titanic_pdf))
titanic_pdf
```

    titanic_pdf type:  <class 'pandas.core.frame.DataFrame'>
    




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
      <th>PassengerId</th>
      <th>Survived</th>
      <th>Pclass</th>
      <th>Name</th>
      <th>Sex</th>
      <th>Age</th>
      <th>SibSp</th>
      <th>Parch</th>
      <th>Ticket</th>
      <th>Fare</th>
      <th>Cabin</th>
      <th>Embarked</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>0</td>
      <td>3</td>
      <td>Braund, Mr. Owen Harris</td>
      <td>male</td>
      <td>22.0</td>
      <td>1</td>
      <td>0</td>
      <td>A/5 21171</td>
      <td>7.2500</td>
      <td>None</td>
      <td>S</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>1</td>
      <td>1</td>
      <td>Cumings, Mrs. John Bradley (Florence Briggs Th...</td>
      <td>female</td>
      <td>38.0</td>
      <td>1</td>
      <td>0</td>
      <td>PC 17599</td>
      <td>71.2833</td>
      <td>C85</td>
      <td>C</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>1</td>
      <td>3</td>
      <td>Heikkinen, Miss. Laina</td>
      <td>female</td>
      <td>26.0</td>
      <td>0</td>
      <td>0</td>
      <td>STON/O2. 3101282</td>
      <td>7.9250</td>
      <td>None</td>
      <td>S</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4</td>
      <td>1</td>
      <td>1</td>
      <td>Futrelle, Mrs. Jacques Heath (Lily May Peel)</td>
      <td>female</td>
      <td>35.0</td>
      <td>1</td>
      <td>0</td>
      <td>113803</td>
      <td>53.1000</td>
      <td>C123</td>
      <td>S</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5</td>
      <td>0</td>
      <td>3</td>
      <td>Allen, Mr. William Henry</td>
      <td>male</td>
      <td>35.0</td>
      <td>0</td>
      <td>0</td>
      <td>373450</td>
      <td>8.0500</td>
      <td>None</td>
      <td>S</td>
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
    </tr>
    <tr>
      <th>886</th>
      <td>887</td>
      <td>0</td>
      <td>2</td>
      <td>Montvila, Rev. Juozas</td>
      <td>male</td>
      <td>27.0</td>
      <td>0</td>
      <td>0</td>
      <td>211536</td>
      <td>13.0000</td>
      <td>None</td>
      <td>S</td>
    </tr>
    <tr>
      <th>887</th>
      <td>888</td>
      <td>1</td>
      <td>1</td>
      <td>Graham, Miss. Margaret Edith</td>
      <td>female</td>
      <td>19.0</td>
      <td>0</td>
      <td>0</td>
      <td>112053</td>
      <td>30.0000</td>
      <td>B42</td>
      <td>S</td>
    </tr>
    <tr>
      <th>888</th>
      <td>889</td>
      <td>0</td>
      <td>3</td>
      <td>"Johnston, Miss. Catherine Helen ""Carrie"""</td>
      <td>female</td>
      <td>NaN</td>
      <td>1</td>
      <td>2</td>
      <td>W./C. 6607</td>
      <td>23.4500</td>
      <td>None</td>
      <td>S</td>
    </tr>
    <tr>
      <th>889</th>
      <td>890</td>
      <td>1</td>
      <td>1</td>
      <td>Behr, Mr. Karl Howell</td>
      <td>male</td>
      <td>26.0</td>
      <td>0</td>
      <td>0</td>
      <td>111369</td>
      <td>30.0000</td>
      <td>C148</td>
      <td>C</td>
    </tr>
    <tr>
      <th>890</th>
      <td>891</td>
      <td>0</td>
      <td>3</td>
      <td>Dooley, Mr. Patrick</td>
      <td>male</td>
      <td>32.0</td>
      <td>0</td>
      <td>0</td>
      <td>370376</td>
      <td>7.7500</td>
      <td>None</td>
      <td>Q</td>
    </tr>
  </tbody>
</table>
<p>891 rows × 12 columns</p>
</div>



### Pandas DataFrame head() vs Spark DataFrame limit()

**Pandas DataFrame의 head(N) 적용시 Pandas DataFrame 반환**


```python
# pandas DataFrame.head()
print(type(titanic_pdf.head(5)))
titanic_pdf.head(5)
```

    <class 'pandas.core.frame.DataFrame'>
    




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
      <th>PassengerId</th>
      <th>Survived</th>
      <th>Pclass</th>
      <th>Name</th>
      <th>Sex</th>
      <th>Age</th>
      <th>SibSp</th>
      <th>Parch</th>
      <th>Ticket</th>
      <th>Fare</th>
      <th>Cabin</th>
      <th>Embarked</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>0</td>
      <td>3</td>
      <td>Braund, Mr. Owen Harris</td>
      <td>male</td>
      <td>22.0</td>
      <td>1</td>
      <td>0</td>
      <td>A/5 21171</td>
      <td>7.2500</td>
      <td>None</td>
      <td>S</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>1</td>
      <td>1</td>
      <td>Cumings, Mrs. John Bradley (Florence Briggs Th...</td>
      <td>female</td>
      <td>38.0</td>
      <td>1</td>
      <td>0</td>
      <td>PC 17599</td>
      <td>71.2833</td>
      <td>C85</td>
      <td>C</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>1</td>
      <td>3</td>
      <td>Heikkinen, Miss. Laina</td>
      <td>female</td>
      <td>26.0</td>
      <td>0</td>
      <td>0</td>
      <td>STON/O2. 3101282</td>
      <td>7.9250</td>
      <td>None</td>
      <td>S</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4</td>
      <td>1</td>
      <td>1</td>
      <td>Futrelle, Mrs. Jacques Heath (Lily May Peel)</td>
      <td>female</td>
      <td>35.0</td>
      <td>1</td>
      <td>0</td>
      <td>113803</td>
      <td>53.1000</td>
      <td>C123</td>
      <td>S</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5</td>
      <td>0</td>
      <td>3</td>
      <td>Allen, Mr. William Henry</td>
      <td>male</td>
      <td>35.0</td>
      <td>0</td>
      <td>0</td>
      <td>373450</td>
      <td>8.0500</td>
      <td>None</td>
      <td>S</td>
    </tr>
  </tbody>
</table>
</div>



**Spark DataFrame에 head(N) 적용시 Row object를 가지는 list 반환**


```python
# Spark DataFrame.head()
print(type(titanic_sdf.head(5)))
titanic_sdf.head(5)
```

    <class 'list'>
    




    [Row(PassengerId=1, Survived=0, Pclass=3, Name='Braund, Mr. Owen Harris', Sex='male', Age=22.0, SibSp=1, Parch=0, Ticket='A/5 21171', Fare=7.25, Cabin=None, Embarked='S'),
     Row(PassengerId=2, Survived=1, Pclass=1, Name='Cumings, Mrs. John Bradley (Florence Briggs Thayer)', Sex='female', Age=38.0, SibSp=1, Parch=0, Ticket='PC 17599', Fare=71.2833, Cabin='C85', Embarked='C'),
     Row(PassengerId=3, Survived=1, Pclass=3, Name='Heikkinen, Miss. Laina', Sex='female', Age=26.0, SibSp=0, Parch=0, Ticket='STON/O2. 3101282', Fare=7.925, Cabin=None, Embarked='S'),
     Row(PassengerId=4, Survived=1, Pclass=1, Name='Futrelle, Mrs. Jacques Heath (Lily May Peel)', Sex='female', Age=35.0, SibSp=1, Parch=0, Ticket='113803', Fare=53.1, Cabin='C123', Embarked='S'),
     Row(PassengerId=5, Survived=0, Pclass=3, Name='Allen, Mr. William Henry', Sex='male', Age=35.0, SibSp=0, Parch=0, Ticket='373450', Fare=8.05, Cabin=None, Embarked='S')]



**Spark DataFrame의 limit(N)가 DataFrame의 선두 N개 Record를 가지는 DataFrame을 반환**


```python
print(type(titanic_sdf.limit(5)))
titanic_sdf.limit(5).show()
```

    <class 'pyspark.sql.dataframe.DataFrame'>
    +-----------+--------+------+--------------------+------+----+-----+-----+----------------+-------+-----+--------+
    |PassengerId|Survived|Pclass|                Name|   Sex| Age|SibSp|Parch|          Ticket|   Fare|Cabin|Embarked|
    +-----------+--------+------+--------------------+------+----+-----+-----+----------------+-------+-----+--------+
    |          1|       0|     3|Braund, Mr. Owen ...|  male|22.0|    1|    0|       A/5 21171|   7.25| null|       S|
    |          2|       1|     1|Cumings, Mrs. Joh...|female|38.0|    1|    0|        PC 17599|71.2833|  C85|       C|
    |          3|       1|     3|Heikkinen, Miss. ...|female|26.0|    0|    0|STON/O2. 3101282|  7.925| null|       S|
    |          4|       1|     1|Futrelle, Mrs. Ja...|female|35.0|    1|    0|          113803|   53.1| C123|       S|
    |          5|       0|     3|Allen, Mr. Willia...|  male|35.0|    0|    0|          373450|   8.05| null|       S|
    +-----------+--------+------+--------------------+------+----+-----+-----+----------------+-------+-----+--------+
    
    

### Pandas DataFrame print() vs Spark DataFrame print()

**Pandas DataFrame에 print() 적용시 DataFrame 내용 출력**


```python
print(titanic_pdf.head(10))
```

       PassengerId  Survived  Pclass  ...     Fare Cabin  Embarked
    0            1         0       3  ...   7.2500  None         S
    1            2         1       1  ...  71.2833   C85         C
    2            3         1       3  ...   7.9250  None         S
    3            4         1       1  ...  53.1000  C123         S
    4            5         0       3  ...   8.0500  None         S
    5            6         0       3  ...   8.4583  None         Q
    6            7         0       1  ...  51.8625   E46         S
    7            8         0       3  ...  21.0750  None         S
    8            9         1       3  ...  11.1333  None         S
    9           10         1       2  ...  30.0708  None         C
    
    [10 rows x 12 columns]
    

**Spark DataFrame에 print() 적용시 DataFrame의 schema 출력**


```python
print(titanic_sdf.limit(10))
```

    DataFrame[PassengerId: int, Survived: int, Pclass: int, Name: string, Sex: string, Age: double, SibSp: int, Parch: int, Ticket: string, Fare: double, Cabin: string, Embarked: string]
    

### Pandas DataFrame info() vs Spark DataFrame printSchema()

**Pandas DataFrame에 info() 적용시 컬렴명, not null 건수, data type 출력**


```python
titanic_pdf.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 891 entries, 0 to 890
    Data columns (total 12 columns):
     #   Column       Non-Null Count  Dtype  
    ---  ------       --------------  -----  
     0   PassengerId  891 non-null    int32  
     1   Survived     891 non-null    int32  
     2   Pclass       891 non-null    int32  
     3   Name         891 non-null    object 
     4   Sex          891 non-null    object 
     5   Age          714 non-null    float64
     6   SibSp        891 non-null    int32  
     7   Parch        891 non-null    int32  
     8   Ticket       891 non-null    object 
     9   Fare         891 non-null    float64
     10  Cabin        204 non-null    object 
     11  Embarked     889 non-null    object 
    dtypes: float64(2), int32(5), object(5)
    memory usage: 66.3+ KB
    

**Spark DataFrame에 printSchema() 적용시 (컬럼명, data type) schema 출력**


```python
titanic_sdf.printSchema()
```

    root
     |-- PassengerId: integer (nullable = true)
     |-- Survived: integer (nullable = true)
     |-- Pclass: integer (nullable = true)
     |-- Name: string (nullable = true)
     |-- Sex: string (nullable = true)
     |-- Age: double (nullable = true)
     |-- SibSp: integer (nullable = true)
     |-- Parch: integer (nullable = true)
     |-- Ticket: string (nullable = true)
     |-- Fare: double (nullable = true)
     |-- Cabin: string (nullable = true)
     |-- Embarked: string (nullable = true)
    
    

**not null 건수를 위해 별도 SQL 쿼리 작성 필요**


```python
from pyspark.sql.functions import count, col, when, isnan

titanic_sdf.select([count(when(col(c).isNull() | isnan(c), c)).alias(c) for c in titanic_sdf.columns]).show()
```

    +-----------+--------+------+----+---+---+-----+-----+------+----+-----+--------+
    |PassengerId|Survived|Pclass|Name|Sex|Age|SibSp|Parch|Ticket|Fare|Cabin|Embarked|
    +-----------+--------+------+----+---+---+-----+-----+------+----+-----+--------+
    |          0|       0|     0|   0|  0|177|    0|    0|     0|   0|  687|       2|
    +-----------+--------+------+----+---+---+-----+-----+------+----+-----+--------+
    
    

### Pandas DataFrame describe() vs Spark DataFrame describe()

**Pandas DataFrame에 describe() 적용시 숫자형 컬럼에 대해 count/mean/std/min/percentile/max 값 출력**


```python
titanic_pdf.describe()
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
      <th>PassengerId</th>
      <th>Survived</th>
      <th>Pclass</th>
      <th>Age</th>
      <th>SibSp</th>
      <th>Parch</th>
      <th>Fare</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>891.000000</td>
      <td>891.000000</td>
      <td>891.000000</td>
      <td>714.000000</td>
      <td>891.000000</td>
      <td>891.000000</td>
      <td>891.000000</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>446.000000</td>
      <td>0.383838</td>
      <td>2.308642</td>
      <td>29.699118</td>
      <td>0.523008</td>
      <td>0.381594</td>
      <td>32.204208</td>
    </tr>
    <tr>
      <th>std</th>
      <td>257.353842</td>
      <td>0.486592</td>
      <td>0.836071</td>
      <td>14.526497</td>
      <td>1.102743</td>
      <td>0.806057</td>
      <td>49.693429</td>
    </tr>
    <tr>
      <th>min</th>
      <td>1.000000</td>
      <td>0.000000</td>
      <td>1.000000</td>
      <td>0.420000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>223.500000</td>
      <td>0.000000</td>
      <td>2.000000</td>
      <td>20.125000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>7.910400</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>446.000000</td>
      <td>0.000000</td>
      <td>3.000000</td>
      <td>28.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>14.454200</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>668.500000</td>
      <td>1.000000</td>
      <td>3.000000</td>
      <td>38.000000</td>
      <td>1.000000</td>
      <td>0.000000</td>
      <td>31.000000</td>
    </tr>
    <tr>
      <th>max</th>
      <td>891.000000</td>
      <td>1.000000</td>
      <td>3.000000</td>
      <td>80.000000</td>
      <td>8.000000</td>
      <td>6.000000</td>
      <td>512.329200</td>
    </tr>
  </tbody>
</table>
</div>



**Spark DataFrame에 describe() 적용시 숫자형 컬럼 뿐만 아니라 문자형 컬럼에 대해서도 count/mean/std/min/max 값 출력 (percentile 값 출력 X)**


```python
titanic_sdf.describe().show()
```

    +-------+-----------------+-------------------+------------------+--------------------+------+------------------+------------------+-------------------+------------------+-----------------+-----+--------+
    |summary|      PassengerId|           Survived|            Pclass|                Name|   Sex|               Age|             SibSp|              Parch|            Ticket|             Fare|Cabin|Embarked|
    +-------+-----------------+-------------------+------------------+--------------------+------+------------------+------------------+-------------------+------------------+-----------------+-----+--------+
    |  count|              891|                891|               891|                 891|   891|               714|               891|                891|               891|              891|  204|     889|
    |   mean|            446.0| 0.3838383838383838| 2.308641975308642|                null|  null| 29.69911764705882|0.5230078563411896|0.38159371492704824|260318.54916792738| 32.2042079685746| null|    null|
    | stddev|257.3538420152301|0.48659245426485753|0.8360712409770491|                null|  null|14.526497332334035|1.1027434322934315| 0.8060572211299488|471609.26868834975|49.69342859718089| null|    null|
    |    min|                1|                  0|                 1|"Andersson, Mr. A...|female|              0.42|                 0|                  0|            110152|              0.0|  A10|       C|
    |    max|              891|                  1|                 3|van Melkebeke, Mr...|  male|              80.0|                 8|                  6|         WE/P 5735|         512.3292|    T|       S|
    +-------+-----------------+-------------------+------------------+--------------------+------+------------------+------------------+-------------------+------------------+-----------------+-----+--------+
    
    


```python
# 숫자형 컬럼에 대해서 describe() 수행할 수 있도록 select 컬럼, filtering 적용

num_columns = [column for column, dtype in titanic_sdf.dtypes if dtype != 'string']
titanic_sdf.select(num_columns).describe().show()
```

    +-------+-----------------+-------------------+------------------+------------------+------------------+-------------------+-----------------+
    |summary|      PassengerId|           Survived|            Pclass|               Age|             SibSp|              Parch|             Fare|
    +-------+-----------------+-------------------+------------------+------------------+------------------+-------------------+-----------------+
    |  count|              891|                891|               891|               714|               891|                891|              891|
    |   mean|            446.0| 0.3838383838383838| 2.308641975308642| 29.69911764705882|0.5230078563411896|0.38159371492704824| 32.2042079685746|
    | stddev|257.3538420152301|0.48659245426485753|0.8360712409770491|14.526497332334035|1.1027434322934315| 0.8060572211299488|49.69342859718089|
    |    min|                1|                  0|                 1|              0.42|                 0|                  0|              0.0|
    |    max|              891|                  1|                 3|              80.0|                 8|                  6|         512.3292|
    +-------+-----------------+-------------------+------------------+------------------+------------------+-------------------+-----------------+
    
    

### Spark DataFrame의 shape

**Pandas DataFrame은 shape을 통해 row 건수와 column 개수 반환 (Spark DataFrame은 shape 제공 X)**


```python
# spark DataFrame row 건수
print('row 건수: ', titanic_sdf.count())
# spark DataFrame column 개수
print('column 개수: ', len(titanic_sdf.columns))
```

    row 건수:  891
    column 개수:  12
    

### Spark DataFrame의 select() 메소드
* select() 메소드는 SQL의 Select 절과 유사하게 한개 이상의 컬럼들의 값을 DataFrame형태로 반환. 
* 한개의 컬럼명, 또는 여러개의 컬럼명을 인자로 입력할 수 있음.
* 개별 컬럼명을 문자열 형태로 또는 DataFrame의 컬럼 속성으로 지정
* DataFrame의 컬럼 속성으로 지정시에는 DataFrame.컬럼명, DataFrame[컬럼명], col(컬럼명) 으로 지정 가능.
* select 절에 list를 입력해도 되나, 원칙적으로 개별 컬럼을 넣는 것이 바람직


```python
dict_01 = {'Name': ['Chulmin', 'Wansoo','Myunghyun','Hyunjoo', 'Chulman'],
           'Year': [2011, 2016, 2015, 2015, 2011],
           'Gender': ['Male', 'Male', 'Male', 'Female', 'Male']
          }
# Dictionary를 Pandas DataFrame으로 변환
data_pdf = pd.DataFrame(dict_01)

# Pandas DataFrame을 Spark DataFrame으로 변환
data_sdf = spark.createDataFrame(data_pdf)
```

**Pandas DataFrame의 단일 컬럼 출력**


```python
data_pdf['Name']
```




    0      Chulmin
    1       Wansoo
    2    Myunghyun
    3      Hyunjoo
    4      Chulman
    Name: Name, dtype: object



**Pandas DataFrame의 여러 컬럼 출력**


```python
data_pdf[['Name', 'Year']]
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
      <th>Name</th>
      <th>Year</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Chulmin</td>
      <td>2011</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Wansoo</td>
      <td>2016</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Myunghyun</td>
      <td>2015</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Hyunjoo</td>
      <td>2015</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Chulman</td>
      <td>2011</td>
    </tr>
  </tbody>
</table>
</div>



**Spark DataFrame의 단일 컬럼 출력**


```python
from pyspark.sql.functions import col

# data_sdf.select('Name') = data_sdf.select(data_sdf.Name) = data_sdf.select(col('Name')) = data_sdf.select(data_sdf['Name'])
data_sdf.select(col('Name')).show() # SELECT Name FROM data_sdf
```

    +---------+
    |     Name|
    +---------+
    |  Chulmin|
    |   Wansoo|
    |Myunghyun|
    |  Hyunjoo|
    |  Chulman|
    +---------+
    
    

**Spark DataFrame의 여러 컬럼 출력**


```python
from pyspark.sql.functions import col

# data_sdf.select('Name', 'Year') = data_sdf.select(['Name','Year']) = data_sdf.select(data_sdf['Name'], data_sdf['Year'])
# data_sdf.select(col('Name'), col('Year')) = data_sdf.select(data_sdf.Name, data_sdf.Year)
data_sdf.select(col('Name'), col('Year')).show() # SELECT Name, Year FROM data_sdf
```

    +---------+----+
    |     Name|Year|
    +---------+----+
    |  Chulmin|2011|
    |   Wansoo|2016|
    |Myunghyun|2015|
    |  Hyunjoo|2015|
    |  Chulman|2011|
    +---------+----+
    
    

**select() 절에서 컬럼 가공 후 DataFrame 생성**


```python
from pyspark.sql.functions import lower, upper, col

data_sdf.select('*', upper(col('Name'))).show()
data_sdf.select(upper(col('Name'))).show()
```

    +---------+----+------+-----------+
    |     Name|Year|Gender|upper(Name)|
    +---------+----+------+-----------+
    |  Chulmin|2011|  Male|    CHULMIN|
    |   Wansoo|2016|  Male|     WANSOO|
    |Myunghyun|2015|  Male|  MYUNGHYUN|
    |  Hyunjoo|2015|Female|    HYUNJOO|
    |  Chulman|2011|  Male|    CHULMAN|
    +---------+----+------+-----------+
    
    +-----------+
    |upper(Name)|
    +-----------+
    |    CHULMIN|
    |     WANSOO|
    |  MYUNGHYUN|
    |    HYUNJOO|
    |    CHULMAN|
    +-----------+
    
    

### Spark DataFrame filter() 메소드 알아보기
* filter()는 SQL의 where와 유사하게 DataFrame내의 특정 조건을 만족하는 레코드를 DataFrame으로 반환. 
* filter()내의 조건 컬럼은 컬럼 속성으로 지정 가능. 조건문 자체는 SQL 과 유사한 문자열로 지정 할 수 있음(조건 컬럼은 문자열 지정이 안됨. )
* where() 메소드는 filter()의 alias이며 SQL where와 직관적인 동일성을 간주하기 위해 생성. 
* 복합 조건 and는 & 를, or를 | 를 사용. 개별 조건은 ()로 감싸야 함.


```python
# data_sdf.where(data_sdf['Name'] == 'Chulmin)
# data_sdf.filter("Name == 'Chulmin'")
data_sdf.filter(data_sdf['Name'] == 'Chulmin').show() # SELECT * FROM data_sdf WHERE Name = 'Chulmin'
```

    +-------+----+------+
    |   Name|Year|Gender|
    +-------+----+------+
    |Chulmin|2011|  Male|
    +-------+----+------+
    
    


```python
from pyspark.sql.functions import col

data_sdf.filter( (data_sdf['Gender'] == 'Male') & (col('Year') > 2011) ).show() # SELECT * FROM data_sdf WHERE GENDER = 'Male' AND Year > 2011
data_sdf.filter( (data_sdf['Gender'] == 'Male') | (col('Year') < 2011) ).show() # SELECT * FROM data_sdf WHERE GENDER = 'Male' OR Year < 2011
```

    +---------+----+------+
    |     Name|Year|Gender|
    +---------+----+------+
    |   Wansoo|2016|  Male|
    |Myunghyun|2015|  Male|
    +---------+----+------+
    
    +---------+----+------+
    |     Name|Year|Gender|
    +---------+----+------+
    |  Chulmin|2011|  Male|
    |   Wansoo|2016|  Male|
    |Myunghyun|2015|  Male|
    |  Chulman|2011|  Male|
    +---------+----+------+
    
    

**문자열 컬럼의 like 수행**


```python
data_sdf.filter(col('Name').like('Chul%')).show() # SELECT * FROM data_sdf WHERE Name LIKE 'Chul%'

# SQL의 like 조건문을 string으로 filter() 수행
data_sdf.filter("Name like 'Chul%'").show()
```

    +-------+----+------+
    |   Name|Year|Gender|
    +-------+----+------+
    |Chulmin|2011|  Male|
    |Chulman|2011|  Male|
    +-------+----+------+
    
    +-------+----+------+
    |   Name|Year|Gender|
    +-------+----+------+
    |Chulmin|2011|  Male|
    |Chulman|2011|  Male|
    +-------+----+------+
    
    


```python
# filtering 후에 특정 컬럼으로만 DataFrame 생성하려면 select() 적용. 
data_sdf.filter(upper(data_sdf['Name']).like('%M%')).select('Year', 'Gender').show()
```

    +----+------+
    |Year|Gender|
    +----+------+
    |2011|  Male|
    |2015|  Male|
    |2011|  Male|
    +----+------+
    
    
