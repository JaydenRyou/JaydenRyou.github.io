---
layout: single
title: "SPARK DataFrame (1)"
categories: [Study Notes]
tag: [SPARK]
author_profile: false
---

## Pandas DataFrame vs Spark DataFrame

Spark DataFrame과 Pandas DataFrame을 정리해보겠습니다.

권철민 강사님의 스파크 머신러닝 완벽가이드 강의를 참고하여 정리하는 것입니다.

**Spark DataFrame -> Pandas DataFrame**
```python
data_pdf = data_sdf.select('*').toPandas()
```
**Pandas DataFrame -> Spark DataFrame**
```python
data_sdf = spark.createDataFrame(data_pdf)
```

## Pandas head() vs Spark limit()
- pandas DataFrame의 head(N)는 Dataframe의 선두 N개 Record를 가지는 DataFrame을 반환
- spark DataFrame의 head(N)는 DataFrame의 선두 N개 Row Object를 list로 반환. 
- spark DataFrame의 limit(N)가 DataFrame의 선두 N개 Record를 가지는 DataFrame을 반환. 
- pandas DataFrame은 print() 적용 시 DataFrame의 내용을 출력하지만 spark DataFrame은 DataFrame의 schema만 출력

## Pandas info() vs Spark printSchema()
- pandas DataFrame의 info()는 컬럼명, data type과 not null 건수를 함께 출력
- spark DataFrame의 info() 메소드가 없으며 대신 printSchema() 메소드로 스키마(컬럼명, data type)만 출력
- not null 건수를 위해서는 별도의 SQL성 쿼리 작성 필요.

**컬럼별 Null 갯수 찾기**
```python
from pyspark.sql.functions import col, isnan, when, count

data_sdf.select([count(when(col(c).isNull() | isnan(c), c)).alias(c) for c in data_sdf.columns]).show()
```

## Pandas describe() vs Spark describe()
- spark dataframe은 pandas dataframe과 비슷하게 describe()를 통해 모든 컬럼값들의 건수/평균/표준편차/최소값/최대값 등의 정보를 확인할 수 있음. 다만 percentile값을 만들지 않음. 
- pandas dataframe과 다르게 describe()시 숫자형 컬럼 뿐만 아니라 문자형 컬럼에 대해서도 건수/평균/표준편차/최소값/최대값 조사

**number 형 컬럼에 대해 describe() 수행하기**
```python
num_columns = [column for column, dtype in data_sdf.dtypes() if dtype != 'string']

data_sdf.select(num_columns).describe().show()
```

## Pandas shape vs Spark shape
- pandas DataFrame의 shape는 row건수와 column 개수를 매우 빠르게 제공.
- spark DataFrame은 shape를 제공하지 않음. column 개수는 spark DataFrame의 columns로 확인. row건수는 count() 메소드(쿼리성)로 확인.

```python
# Row 갯수
data_sdf.count()
# Col 갯수
len(data_sdf.columns)
```

## Spark의 select() 메소드 알아보기
- select() 메소드는 SQL의 Select 절과 유사하게 한개 이상의 컬럼들의 값을 DataFrame형태로 반환. 
- 한개의 컬럼명, 또는 여러개의 컬럼명을 인자로 입력할 수 있음.
- 개별 컬럼명을 문자열 형태로 또는 DataFrame의 컬럼 속성으로 지정
- DataFrame의 컬럼 속성으로 지정시에는 DataFrame.컬럼명, DataFrame[컬럼명], col(컬럼명) 으로 지정 가능.

```python
data_sdf.select('name').show() # SELECT col1 FROM data_sdf
data_sdf.select(data_sdf['name']).show()
data_sdf.select(col('name')).show()
data_sdf.select(data_sdf.name).show()
```

## Spark DataFrame filter() 메소드 알아보기
- filter()는 SQL의 where와 유사하게 DataFrame내의 특정 조건을 만족하는 레코드를 DataFrame으로 반환. 
- filter()내의 조건 컬럼은 컬럼 속성으로 지정 가능. 조건문 자체는 SQL 과 유사한 문자열로 지정 할 수 있음(조건 컬럼은 문자열 지정이 안됨. )
- where() 메소드는 filter()의 alias이며 SQL where와 직관적인 동일성을 간주하기 위해 생성. 
- 복합 조건 and는 & 를, or를 | 를 사용. 개별 조건은 ()로 감싸야 함.

```python
data_sdf.filter(data_sdf['Name'] == 'Hyoyeol').show() # SELECT Name FROM data_sdf WHERE Name = 'Hyoyeol'
data_sdf.where(data_sdf['Name'] == 'Hyoyeol').show()
data_sdf.filter("Name = 'Hyoyeol'").show()
```

```python
data_sdf.filter(col('Name').like('Hyo%')).show() # SELECT Name FROM data_sdf WHERE Name LIKE 'Hyo%'
data_sdf.filter("Name like C%").show() # SELECT Name FROM data_sdf WHERE Name LIKE 'C%'
```


