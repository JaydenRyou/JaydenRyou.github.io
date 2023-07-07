---
layout: single
title: "Pandas DataFrame vs Spark DataFrame (2)"
categories: [Study Notes]
tag: [Spark, SQL]
author_profile: false
---

### titanic_train.csv 파일을 로드하고, 이를 DataFrame으로 변환


```python
titanic_sdf = spark.read.csv('/FileStore/tables/titanic_train.csv', header=True, inferSchema=True)
titanic_pdf = titanic_sdf.select('*').toPandas()
```

### Spark DataFrame의 orderBy() 메소드

**Pandas DataFrame의 sort_values()**


```python
# 단일 컬럼 오름차순 정렬
titanic_pdf_sorted_01 = titanic_pdf.sort_values(by=['Name'], ascending=True)

# 여러 컬럼 내림차순 정렬
titanic_pdf_sorted_02 = titanic_pdf.sort_values(by=['Pclass', 'Name'], ascending = False)

# Pclass는 오름차순 Name은 내림차순 정렬
titanic_pdf_sorted_03 = titanic_pdf.sort_values(by=['Pclass', 'Name'], ascending = [True, False])
```

**Spark DataFrame의 orderBy()**


```python
from pyspark.sql.functions import col

# orderBy에 컬럼명을 문자열로 지정
titanic_sdf.orderBy('Name', ascending = False).show()

# orderBy에 컬럼명을 컬럼형태로 지정
# 1) DataFrame.컬럼명
titanic_sdf.orderBy(titanic_sdf.Name, ascending = False).show()

# 2) DataFrame['컬럼명']
titanic_sdf.orderBy(titanic_sdf['Name'], ascending = False).show()

# 3) col('컬럼명')
titanic_sdf.orderBy(col('Name'), ascending = False).show() 
```

    +-----------+--------+------+--------------------+------+----+-----+-----+----------------+--------+-----+--------+
    |PassengerId|Survived|Pclass|                Name|   Sex| Age|SibSp|Parch|          Ticket|    Fare|Cabin|Embarked|
    +-----------+--------+------+--------------------+------+----+-----+-----+----------------+--------+-----+--------+
    |        869|       0|     3|van Melkebeke, Mr...|  male|null|    0|    0|          345777|     9.5| null|       S|
    |        154|       0|     3|van Billiard, Mr....|  male|40.5|    0|    2|        A/5. 851|    14.5| null|       S|
    |        362|       0|     2|del Carlo, Mr. Se...|  male|29.0|    1|    0|   SC/PARIS 2167| 27.7208| null|       C|
    |        283|       0|     3|de Pelsmaeker, Mr...|  male|16.0|    0|    0|          345778|     9.5| null|       S|
    |        287|       1|     3|de Mulder, Mr. Th...|  male|30.0|    0|    0|          345774|     9.5| null|       S|
    |        560|       1|     3|de Messemaeker, M...|female|36.0|    1|    0|          345572|    17.4| null|       S|
    |        423|       0|     3|  Zimmerman, Mr. Leo|  male|29.0|    0|    0|          315082|   7.875| null|       S|
    |        241|       0|     3|Zabour, Miss. Tha...|female|null|    1|    0|            2665| 14.4542| null|       C|
    |        112|       0|     3|Zabour, Miss. Hileni|female|14.5|    1|    0|            2665| 14.4542| null|       C|
    |        496|       0|     3|Yousseff, Mr. Ger...|  male|null|    0|    0|            2627| 14.4583| null|       C|
    |        355|       0|     3|   Yousif, Mr. Wazli|  male|null|    0|    0|            2647|   7.225| null|       C|
    |        204|       0|     3|Youseff, Mr. Gerious|  male|45.5|    0|    0|            2628|   7.225| null|       C|
    |        326|       1|     1|Young, Miss. Mari...|female|36.0|    0|    0|        PC 17760|135.6333|  C32|       C|
    |        831|       1|     3|Yasbeck, Mrs. Ant...|female|15.0|    1|    0|            2659| 14.4542| null|       C|
    |        621|       0|     3| Yasbeck, Mr. Antoni|  male|27.0|    1|    0|            2659| 14.4542| null|       C|
    |        556|       0|     1|  Wright, Mr. George|  male|62.0|    0|    0|          113807|   26.55| null|       S|
    |         56|       1|     1|   Woolner, Mr. Hugh|  male|null|    0|    0|           19947|    35.5|  C52|       S|
    |        426|       0|     3|Wiseman, Mr. Phil...|  male|null|    0|    0|      A/4. 34244|    7.25| null|       S|
    |        492|       0|     3| Windelov, Mr. Einar|  male|21.0|    0|    0|SOTON/OQ 3101317|    7.25| null|       S|
    |        352|       0|     1|Williams-Lambert,...|  male|null|    0|    0|          113510|    35.0| C128|       S|
    +-----------+--------+------+--------------------+------+----+-----+-----+----------------+--------+-----+--------+
    only showing top 20 rows
    
    +-----------+--------+------+--------------------+------+----+-----+-----+----------------+--------+-----+--------+
    |PassengerId|Survived|Pclass|                Name|   Sex| Age|SibSp|Parch|          Ticket|    Fare|Cabin|Embarked|
    +-----------+--------+------+--------------------+------+----+-----+-----+----------------+--------+-----+--------+
    |        869|       0|     3|van Melkebeke, Mr...|  male|null|    0|    0|          345777|     9.5| null|       S|
    |        154|       0|     3|van Billiard, Mr....|  male|40.5|    0|    2|        A/5. 851|    14.5| null|       S|
    |        362|       0|     2|del Carlo, Mr. Se...|  male|29.0|    1|    0|   SC/PARIS 2167| 27.7208| null|       C|
    |        283|       0|     3|de Pelsmaeker, Mr...|  male|16.0|    0|    0|          345778|     9.5| null|       S|
    |        287|       1|     3|de Mulder, Mr. Th...|  male|30.0|    0|    0|          345774|     9.5| null|       S|
    |        560|       1|     3|de Messemaeker, M...|female|36.0|    1|    0|          345572|    17.4| null|       S|
    |        423|       0|     3|  Zimmerman, Mr. Leo|  male|29.0|    0|    0|          315082|   7.875| null|       S|
    |        241|       0|     3|Zabour, Miss. Tha...|female|null|    1|    0|            2665| 14.4542| null|       C|
    |        112|       0|     3|Zabour, Miss. Hileni|female|14.5|    1|    0|            2665| 14.4542| null|       C|
    |        496|       0|     3|Yousseff, Mr. Ger...|  male|null|    0|    0|            2627| 14.4583| null|       C|
    |        355|       0|     3|   Yousif, Mr. Wazli|  male|null|    0|    0|            2647|   7.225| null|       C|
    |        204|       0|     3|Youseff, Mr. Gerious|  male|45.5|    0|    0|            2628|   7.225| null|       C|
    |        326|       1|     1|Young, Miss. Mari...|female|36.0|    0|    0|        PC 17760|135.6333|  C32|       C|
    |        831|       1|     3|Yasbeck, Mrs. Ant...|female|15.0|    1|    0|            2659| 14.4542| null|       C|
    |        621|       0|     3| Yasbeck, Mr. Antoni|  male|27.0|    1|    0|            2659| 14.4542| null|       C|
    |        556|       0|     1|  Wright, Mr. George|  male|62.0|    0|    0|          113807|   26.55| null|       S|
    |         56|       1|     1|   Woolner, Mr. Hugh|  male|null|    0|    0|           19947|    35.5|  C52|       S|
    |        426|       0|     3|Wiseman, Mr. Phil...|  male|null|    0|    0|      A/4. 34244|    7.25| null|       S|
    |        492|       0|     3| Windelov, Mr. Einar|  male|21.0|    0|    0|SOTON/OQ 3101317|    7.25| null|       S|
    |        352|       0|     1|Williams-Lambert,...|  male|null|    0|    0|          113510|    35.0| C128|       S|
    +-----------+--------+------+--------------------+------+----+-----+-----+----------------+--------+-----+--------+
    only showing top 20 rows
    
    +-----------+--------+------+--------------------+------+----+-----+-----+----------------+--------+-----+--------+
    |PassengerId|Survived|Pclass|                Name|   Sex| Age|SibSp|Parch|          Ticket|    Fare|Cabin|Embarked|
    +-----------+--------+------+--------------------+------+----+-----+-----+----------------+--------+-----+--------+
    |        869|       0|     3|van Melkebeke, Mr...|  male|null|    0|    0|          345777|     9.5| null|       S|
    |        154|       0|     3|van Billiard, Mr....|  male|40.5|    0|    2|        A/5. 851|    14.5| null|       S|
    |        362|       0|     2|del Carlo, Mr. Se...|  male|29.0|    1|    0|   SC/PARIS 2167| 27.7208| null|       C|
    |        283|       0|     3|de Pelsmaeker, Mr...|  male|16.0|    0|    0|          345778|     9.5| null|       S|
    |        287|       1|     3|de Mulder, Mr. Th...|  male|30.0|    0|    0|          345774|     9.5| null|       S|
    |        560|       1|     3|de Messemaeker, M...|female|36.0|    1|    0|          345572|    17.4| null|       S|
    |        423|       0|     3|  Zimmerman, Mr. Leo|  male|29.0|    0|    0|          315082|   7.875| null|       S|
    |        241|       0|     3|Zabour, Miss. Tha...|female|null|    1|    0|            2665| 14.4542| null|       C|
    |        112|       0|     3|Zabour, Miss. Hileni|female|14.5|    1|    0|            2665| 14.4542| null|       C|
    |        496|       0|     3|Yousseff, Mr. Ger...|  male|null|    0|    0|            2627| 14.4583| null|       C|
    |        355|       0|     3|   Yousif, Mr. Wazli|  male|null|    0|    0|            2647|   7.225| null|       C|
    |        204|       0|     3|Youseff, Mr. Gerious|  male|45.5|    0|    0|            2628|   7.225| null|       C|
    |        326|       1|     1|Young, Miss. Mari...|female|36.0|    0|    0|        PC 17760|135.6333|  C32|       C|
    |        831|       1|     3|Yasbeck, Mrs. Ant...|female|15.0|    1|    0|            2659| 14.4542| null|       C|
    |        621|       0|     3| Yasbeck, Mr. Antoni|  male|27.0|    1|    0|            2659| 14.4542| null|       C|
    |        556|       0|     1|  Wright, Mr. George|  male|62.0|    0|    0|          113807|   26.55| null|       S|
    |         56|       1|     1|   Woolner, Mr. Hugh|  male|null|    0|    0|           19947|    35.5|  C52|       S|
    |        426|       0|     3|Wiseman, Mr. Phil...|  male|null|    0|    0|      A/4. 34244|    7.25| null|       S|
    |        492|       0|     3| Windelov, Mr. Einar|  male|21.0|    0|    0|SOTON/OQ 3101317|    7.25| null|       S|
    |        352|       0|     1|Williams-Lambert,...|  male|null|    0|    0|          113510|    35.0| C128|       S|
    +-----------+--------+------+--------------------+------+----+-----+-----+----------------+--------+-----+--------+
    only showing top 20 rows
    
    +-----------+--------+------+--------------------+------+----+-----+-----+----------------+--------+-----+--------+
    |PassengerId|Survived|Pclass|                Name|   Sex| Age|SibSp|Parch|          Ticket|    Fare|Cabin|Embarked|
    +-----------+--------+------+--------------------+------+----+-----+-----+----------------+--------+-----+--------+
    |        869|       0|     3|van Melkebeke, Mr...|  male|null|    0|    0|          345777|     9.5| null|       S|
    |        154|       0|     3|van Billiard, Mr....|  male|40.5|    0|    2|        A/5. 851|    14.5| null|       S|
    |        362|       0|     2|del Carlo, Mr. Se...|  male|29.0|    1|    0|   SC/PARIS 2167| 27.7208| null|       C|
    |        283|       0|     3|de Pelsmaeker, Mr...|  male|16.0|    0|    0|          345778|     9.5| null|       S|
    |        287|       1|     3|de Mulder, Mr. Th...|  male|30.0|    0|    0|          345774|     9.5| null|       S|
    |        560|       1|     3|de Messemaeker, M...|female|36.0|    1|    0|          345572|    17.4| null|       S|
    |        423|       0|     3|  Zimmerman, Mr. Leo|  male|29.0|    0|    0|          315082|   7.875| null|       S|
    |        241|       0|     3|Zabour, Miss. Tha...|female|null|    1|    0|            2665| 14.4542| null|       C|
    |        112|       0|     3|Zabour, Miss. Hileni|female|14.5|    1|    0|            2665| 14.4542| null|       C|
    |        496|       0|     3|Yousseff, Mr. Ger...|  male|null|    0|    0|            2627| 14.4583| null|       C|
    |        355|       0|     3|   Yousif, Mr. Wazli|  male|null|    0|    0|            2647|   7.225| null|       C|
    |        204|       0|     3|Youseff, Mr. Gerious|  male|45.5|    0|    0|            2628|   7.225| null|       C|
    |        326|       1|     1|Young, Miss. Mari...|female|36.0|    0|    0|        PC 17760|135.6333|  C32|       C|
    |        831|       1|     3|Yasbeck, Mrs. Ant...|female|15.0|    1|    0|            2659| 14.4542| null|       C|
    |        621|       0|     3| Yasbeck, Mr. Antoni|  male|27.0|    1|    0|            2659| 14.4542| null|       C|
    |        556|       0|     1|  Wright, Mr. George|  male|62.0|    0|    0|          113807|   26.55| null|       S|
    |         56|       1|     1|   Woolner, Mr. Hugh|  male|null|    0|    0|           19947|    35.5|  C52|       S|
    |        426|       0|     3|Wiseman, Mr. Phil...|  male|null|    0|    0|      A/4. 34244|    7.25| null|       S|
    |        492|       0|     3| Windelov, Mr. Einar|  male|21.0|    0|    0|SOTON/OQ 3101317|    7.25| null|       S|
    |        352|       0|     1|Williams-Lambert,...|  male|null|    0|    0|          113510|    35.0| C128|       S|
    +-----------+--------+------+--------------------+------+----+-----+-----+----------------+--------+-----+--------+
    only showing top 20 rows
    
    

**여러 컬럼 지정하고 서로 다른 방식으로 정렬**


```python
# 1) .asc(), desc() 적용
titanic_sdf.orderBy(col('Name').asc(), col('Pclass').desc()).show()

# 2) ascending = [True, False]
titanic_sdf.orderBy('Name', 'Pclass', ascending = [True, False]).show()
```

    +-----------+--------+------+--------------------+------+----+-----+-----+----------+-------+-----+--------+
    |PassengerId|Survived|Pclass|                Name|   Sex| Age|SibSp|Parch|    Ticket|   Fare|Cabin|Embarked|
    +-----------+--------+------+--------------------+------+----+-----+-----+----------+-------+-----+--------+
    |        147|       1|     3|"Andersson, Mr. A...|  male|27.0|    0|    0|    350043| 7.7958| null|       S|
    |        519|       1|     2|"Angle, Mrs. Will...|female|36.0|    1|    0|    226875|   26.0| null|       S|
    |        291|       1|     1|"Barber, Miss. El...|female|26.0|    0|    0|     19877|  78.85| null|       S|
    |        625|       0|     3|"Bowen, Mr. David...|  male|21.0|    0|    0|     54636|   16.1| null|       S|
    |        508|       1|     1|"Bradley, Mr. Geo...|  male|null|    0|    0|    111427|  26.55| null|       S|
    |        346|       1|     2|"Brown, Miss. Ame...|female|24.0|    0|    0|    248733|   13.0|  F33|       S|
    |        209|       1|     3|"Carr, Miss. Hele...|female|16.0|    0|    0|    367231|   7.75| null|       Q|
    |        205|       1|     3|"Cohen, Mr. Gursh...|  male|18.0|    0|    0|  A/5 3540|   8.05| null|       S|
    |        238|       1|     2|"Collyer, Miss. M...|female| 8.0|    0|    2|C.A. 31921|  26.25| null|       S|
    |        490|       1|     3|"Coutts, Master. ...|  male| 9.0|    1|    1|C.A. 37671|   15.9| null|       S|
    |        349|       1|     3|"Coutts, Master. ...|  male| 3.0|    1|    1|C.A. 37671|   15.9| null|       S|
    |        557|       1|     1|"Duff Gordon, Lad...|female|48.0|    1|    0|     11755|   39.6|  A16|       C|
    |        600|       1|     1|"Duff Gordon, Sir...|  male|49.0|    1|    0|  PC 17485|56.9292|  A20|       C|
    |        573|       1|     1|"Flynn, Mr. John ...|  male|36.0|    0|    0|  PC 17474|26.3875|  E25|       S|
    |        437|       0|     3|"Ford, Miss. Dool...|female|21.0|    2|    2|W./C. 6608| 34.375| null|       S|
    |        148|       0|     3|"Ford, Miss. Robi...|female| 9.0|    2|    2|W./C. 6608| 34.375| null|       S|
    |        482|       0|     2|"Frost, Mr. Antho...|  male|null|    0|    0|    239854|    0.0| null|       S|
    |        157|       1|     3|"Gilnagh, Miss. K...|female|16.0|    0|    0|     35851| 7.7333| null|       Q|
    |        166|       1|     3|"Goldsmith, Maste...|  male| 9.0|    0|    2|    363291| 20.525| null|       S|
    |        721|       1|     2|"Harper, Miss. An...|female| 6.0|    0|    1|    248727|   33.0| null|       S|
    +-----------+--------+------+--------------------+------+----+-----+-----+----------+-------+-----+--------+
    only showing top 20 rows
    
    +-----------+--------+------+--------------------+------+----+-----+-----+----------+-------+-----+--------+
    |PassengerId|Survived|Pclass|                Name|   Sex| Age|SibSp|Parch|    Ticket|   Fare|Cabin|Embarked|
    +-----------+--------+------+--------------------+------+----+-----+-----+----------+-------+-----+--------+
    |        147|       1|     3|"Andersson, Mr. A...|  male|27.0|    0|    0|    350043| 7.7958| null|       S|
    |        519|       1|     2|"Angle, Mrs. Will...|female|36.0|    1|    0|    226875|   26.0| null|       S|
    |        291|       1|     1|"Barber, Miss. El...|female|26.0|    0|    0|     19877|  78.85| null|       S|
    |        625|       0|     3|"Bowen, Mr. David...|  male|21.0|    0|    0|     54636|   16.1| null|       S|
    |        508|       1|     1|"Bradley, Mr. Geo...|  male|null|    0|    0|    111427|  26.55| null|       S|
    |        346|       1|     2|"Brown, Miss. Ame...|female|24.0|    0|    0|    248733|   13.0|  F33|       S|
    |        209|       1|     3|"Carr, Miss. Hele...|female|16.0|    0|    0|    367231|   7.75| null|       Q|
    |        205|       1|     3|"Cohen, Mr. Gursh...|  male|18.0|    0|    0|  A/5 3540|   8.05| null|       S|
    |        238|       1|     2|"Collyer, Miss. M...|female| 8.0|    0|    2|C.A. 31921|  26.25| null|       S|
    |        490|       1|     3|"Coutts, Master. ...|  male| 9.0|    1|    1|C.A. 37671|   15.9| null|       S|
    |        349|       1|     3|"Coutts, Master. ...|  male| 3.0|    1|    1|C.A. 37671|   15.9| null|       S|
    |        557|       1|     1|"Duff Gordon, Lad...|female|48.0|    1|    0|     11755|   39.6|  A16|       C|
    |        600|       1|     1|"Duff Gordon, Sir...|  male|49.0|    1|    0|  PC 17485|56.9292|  A20|       C|
    |        573|       1|     1|"Flynn, Mr. John ...|  male|36.0|    0|    0|  PC 17474|26.3875|  E25|       S|
    |        437|       0|     3|"Ford, Miss. Dool...|female|21.0|    2|    2|W./C. 6608| 34.375| null|       S|
    |        148|       0|     3|"Ford, Miss. Robi...|female| 9.0|    2|    2|W./C. 6608| 34.375| null|       S|
    |        482|       0|     2|"Frost, Mr. Antho...|  male|null|    0|    0|    239854|    0.0| null|       S|
    |        157|       1|     3|"Gilnagh, Miss. K...|female|16.0|    0|    0|     35851| 7.7333| null|       Q|
    |        166|       1|     3|"Goldsmith, Maste...|  male| 9.0|    0|    2|    363291| 20.525| null|       S|
    |        721|       1|     2|"Harper, Miss. An...|female| 6.0|    0|    1|    248727|   33.0| null|       S|
    +-----------+--------+------+--------------------+------+----+-----+-----+----------+-------+-----+--------+
    only showing top 20 rows
    
    

**orderBy() 메소드와 동일한 sort() 제공**


```python
titanic_sdf.sort(col('Pclass').asc(), col('Name').desc()).show()
```

    +-----------+--------+------+--------------------+------+----+-----+-----+--------+--------+-----+--------+
    |PassengerId|Survived|Pclass|                Name|   Sex| Age|SibSp|Parch|  Ticket|    Fare|Cabin|Embarked|
    +-----------+--------+------+--------------------+------+----+-----+-----+--------+--------+-----+--------+
    |        326|       1|     1|Young, Miss. Mari...|female|36.0|    0|    0|PC 17760|135.6333|  C32|       C|
    |        556|       0|     1|  Wright, Mr. George|  male|62.0|    0|    0|  113807|   26.55| null|       S|
    |         56|       1|     1|   Woolner, Mr. Hugh|  male|null|    0|    0|   19947|    35.5|  C52|       S|
    |        352|       0|     1|Williams-Lambert,...|  male|null|    0|    0|  113510|    35.0| C128|       S|
    |        156|       0|     1|Williams, Mr. Cha...|  male|51.0|    0|    1|PC 17597| 61.3792| null|       C|
    |        378|       0|     1|Widener, Mr. Harr...|  male|27.0|    0|    2|  113503|   211.5|  C82|       C|
    |        857|       1|     1|Wick, Mrs. George...|female|45.0|    1|    1|   36928|164.8667| null|       S|
    |        319|       1|     1|Wick, Miss. Mary ...|female|31.0|    0|    2|   36928|164.8667|   C7|       S|
    |        103|       0|     1|White, Mr. Richar...|  male|21.0|    0|    1|   35281| 77.2875|  D26|       S|
    |        125|       0|     1|White, Mr. Perciv...|  male|54.0|    0|    1|   35281| 77.2875|  D26|       S|
    |        695|       0|     1|     Weir, Col. John|  male|60.0|    0|    0|  113800|   26.55| null|       S|
    |        367|       1|     1|Warren, Mrs. Fran...|female|60.0|    1|    0|  110813|   75.25|  D37|       C|
    |        259|       1|     1|    Ward, Miss. Anna|female|35.0|    0|    0|PC 17755|512.3292| null|       C|
    |        516|       0|     1|Walker, Mr. Willi...|  male|47.0|    0|    0|   36967| 34.0208|  D46|       S|
    |        171|       0|     1|Van der hoef, Mr....|  male|61.0|    0|    0|  111240|    33.5|  B19|       S|
    |         31|       0|     1|Uruchurtu, Don. M...|  male|40.0|    0|    0|PC 17601| 27.7208| null|       C|
    |        257|       1|     1|Thorne, Mrs. Gert...|female|null|    0|    0|PC 17585|    79.2| null|       C|
    |        582|       1|     1|Thayer, Mrs. John...|female|39.0|    1|    1|   17421|110.8833|  C68|       C|
    |        551|       1|     1|Thayer, Mr. John ...|  male|17.0|    0|    2|   17421|110.8833|  C70|       C|
    |        699|       0|     1|Thayer, Mr. John ...|  male|49.0|    1|    1|   17421|110.8833|  C68|       C|
    +-----------+--------+------+--------------------+------+----+-----+-----+--------+--------+-----+--------+
    only showing top 20 rows
    
    

### Spark DataFrame에 aggregation 메소드 적용

**Pandas DataFrame에 aggregation 적용시 DataFrame에 속한 전체 컬럼 모두 aggregation 적용**


```python
# 1) count()
print(titanic_pdf.count())
print('----------------------------------------')

# 2) max()
print(titanic_pdf.max())
print('----------------------------------------')

# 3) min()
print(titanic_pdf.min())
```

    PassengerId    891
    Survived       891
    Pclass         891
    Name           891
    Sex            891
    Age            714
    SibSp          891
    Parch          891
    Ticket         891
    Fare           891
    Cabin          204
    Embarked       889
    dtype: int64
    ----------------------------------------
    PassengerId                            891
    Survived                                 1
    Pclass                                   3
    Name           van Melkebeke, Mr. Philemon
    Sex                                   male
    Age                                   80.0
    SibSp                                    8
    Parch                                    6
    Ticket                           WE/P 5735
    Fare                              512.3292
    dtype: object
    ----------------------------------------
    PassengerId                                                   1
    Survived                                                      0
    Pclass                                                        1
    Name           "Andersson, Mr. August Edvard (""Wennerstrom"")"
    Sex                                                      female
    Age                                                        0.42
    SibSp                                                         0
    Parch                                                         0
    Ticket                                                   110152
    Fare                                                        0.0
    dtype: object
    

    <command-3087825352393063>:6: FutureWarning: Dropping of nuisance columns in DataFrame reductions (with 'numeric_only=None') is deprecated; in a future version this will raise TypeError.  Select only valid columns before calling the reduction.
      print(titanic_pdf.max())
    <command-3087825352393063>:10: FutureWarning: Dropping of nuisance columns in DataFrame reductions (with 'numeric_only=None') is deprecated; in a future version this will raise TypeError.  Select only valid columns before calling the reduction.
      print(titanic_pdf.min())
    

**Spark DataFrame에 aggregation 함수 적용시 count() 외 어떤 컬럼을 aggregation 할지 명시해줘야 함**


```python
from pyspark.sql.functions import max, count, sum

titanic_sdf_max = titanic_sdf.select(max('Age'))
titanic_sdf_max.show()
print(type(titanic_sdf_max)) # 값은 하나지만 DataFrame 반환
```

    +--------+
    |max(Age)|
    +--------+
    |    80.0|
    +--------+
    
    <class 'pyspark.sql.dataframe.DataFrame'>
    

### Spark DataFrame의 groupBy()

**Pandas DataFrame의 groupby(by = '컬럼명') 적용시 DataFrameGroupBy 객체를 반환하고 여기에 aggregation 메소드 적용**


```python
# pandas DataFrame에 groupby()메소드 호출 시 DataFrameGroupBy 객체 반환. 
titanic_pdf_groupby = titanic_pdf.groupby(by = 'Pclass')
print('Pandas groupby type: ', type(titanic_pdf_groupby))

# Group by 된 pandas DataFrameGroupBy 객체에 count()를 적용 시 group by 된 컬럼값 레벨로 모든 컬럼들의 count() 수행.
print('----------------------------------------')
print(titanic_pdf_groupby.count())

# Group by 된 pandas DataFrameGroupBy 객체에 특정 컬럼에 aggregation 을 적용하려면 해당 컬럼을 ['컬럼명'] 추출하여 aggregation 함수 적용.
print('----------------------------------------')
print(titanic_pdf_groupby['Age'].max())

# pandas DataFrameGroupBy 객체에 여러 컬럼에 동일 aggregation 을 적용하려면 해당 컬럼들을 [['컬럼명1', '컬럼명2']]로 추출하여 aggregation 함수 적용.
print('----------------------------------------')
print(titanic_pdf_groupby[['Age', 'Fare']].max())

# Group by 된 DataFrameGroupBy 객체에 서로 다른 컬럼에 서로 다른 aggregation 함수를 적용하려면 agg() 메소드를 사용. 
# agg()메소드 내부에 인자는 dictionary 형태로 적용 컬럼명과 적용 aggregation 함수 기재
print('----------------------------------------')
agg_format = {'Age':'max', 'Fare':'mean', 'SibSp':'sum'}
print(titanic_pdf_groupby.agg(agg_format))
```

    Pandas groupby type:  <class 'pandas.core.groupby.generic.DataFrameGroupBy'>
    ----------------------------------------
            PassengerId  Survived  Name  Sex  Age  SibSp  Parch  Ticket  Fare  \
    Pclass                                                                      
    1               216       216   216  216  186    216    216     216   216   
    2               184       184   184  184  173    184    184     184   184   
    3               491       491   491  491  355    491    491     491   491   
    
            Cabin  Embarked  
    Pclass                   
    1         176       214  
    2          16       184  
    3          12       491  
    ----------------------------------------
    Pclass
    1    80.0
    2    70.0
    3    74.0
    Name: Age, dtype: float64
    ----------------------------------------
             Age      Fare
    Pclass                
    1       80.0  512.3292
    2       70.0   73.5000
    3       74.0   69.5500
    ----------------------------------------
             Age       Fare  SibSp
    Pclass                        
    1       80.0  84.154687     90
    2       70.0  20.662183     74
    3       74.0  13.675550    302
    


```python
# pandas DataFrame의 value_counts()는 Series에 적용시 해당 series내의 값 별로 건수를 구함. 
print(titanic_pdf['Pclass'].value_counts())
```

    3    491
    1    216
    2    184
    Name: Pclass, dtype: int64
    

**Spark DataFrame도 groupBy('컬럼명') 수행 시 group_by_컬럼명 레벨로 group by 된 GroupedData 객체 반환하고 여기에 aggregation 메소드 적용**


```python
titanic_sdf.groupBy('Pclass').count().show()
print('Spark DataFrame groupBy type:', type(titanic_sdf.groupBy('Pclass')))
```

    +------+-----+
    |Pclass|count|
    +------+-----+
    |     1|  216|
    |     3|  491|
    |     2|  184|
    +------+-----+
    
    Spark DataFrame groupBy type: <class 'pyspark.sql.group.GroupedData'>
    


```python
# Spark DataFrame의 orderBy()메소드를 적용하여 group by 결과 건수 descending 으로 정렬 
titanic_sdf.groupBy('Pclass').count().orderBy('count', ascending = False).show()
```

    +------+-----+
    |Pclass|count|
    +------+-----+
    |     3|  491|
    |     1|  216|
    |     2|  184|
    +------+-----+
    
    

**GroupedData 에 count()가 아니고 다른 aggregation 메소드를 적용 시 Pandas DataFrame의 groupby와 유사하게 group by된 컬럼 레벨로 전체 컬럼에 대해서 aggregation을 적용**


```python
titanic_sdf.groupBy('Pclass').max().show()
```

    +------+----------------+-------------+-----------+--------+----------+----------+---------+
    |Pclass|max(PassengerId)|max(Survived)|max(Pclass)|max(Age)|max(SibSp)|max(Parch)|max(Fare)|
    +------+----------------+-------------+-----------+--------+----------+----------+---------+
    |     1|             890|            1|          1|    80.0|         3|         4| 512.3292|
    |     3|             891|            1|          3|    74.0|         8|         6|    69.55|
    |     2|             887|            1|          2|    70.0|         3|         3|     73.5|
    +------+----------------+-------------+-----------+--------+----------+----------+---------+
    
    


```python
# group by 레벨로 특정 컬럼에 aggregation 적용. max('컬럼명')과 같이 aggregation 메소드 내부에 인자로 컬러명 입력
titanic_sdf.groupBy('Pclass').max('Age').show() # select max(Age) from titainic_sdf group by Pclass

#GroupedData에서 aggregation 메소드 호출 시 오직 문자열 컬럼명만 가능. 컬럼형 인자 입력은 오류 발생. 
titanic_sdf.groupBy('Pclass').max(col('Age')).show()
```

    +------+--------+
    |Pclass|max(Age)|
    +------+--------+
    |     1|    80.0|
    |     3|    74.0|
    |     2|    70.0|
    +------+--------+
    
    


    [0;31m---------------------------------------------------------------------------[0m
    [0;31mTypeError[0m                                 Traceback (most recent call last) 
    [0;31mTypeError[0m: Column is not iterable



```python
# 여러 컬럼으로 Group by 규정할 때 개별 컬럼명을 입력하거나, list 형태로 입력 가능. 
titanic_sdf.groupBy('Pclass', 'Sex').max('Age').show() # select max(Age) from titanic_sdf group by Pclass, Sex
titanic_sdf.groupBy(['Pclass', 'Sex']).max('Age').show()
```

    +------+------+--------+
    |Pclass|   Sex|max(Age)|
    +------+------+--------+
    |     2|female|    57.0|
    |     3|  male|    74.0|
    |     1|  male|    80.0|
    |     3|female|    63.0|
    |     1|female|    63.0|
    |     2|  male|    70.0|
    +------+------+--------+
    
    +------+------+--------+
    |Pclass|   Sex|max(Age)|
    +------+------+--------+
    |     2|female|    57.0|
    |     3|  male|    74.0|
    |     1|  male|    80.0|
    |     3|female|    63.0|
    |     1|female|    63.0|
    |     2|  male|    70.0|
    +------+------+--------+
    
    


```python
### 여러개의 aggregation 함수를 적용할 경우는 agg()메소드 내에서 개별 aggregation 함수를 명시 해야함. 

from pyspark.sql.functions import max, avg, sum, min

# select max(age), min(age), sum(age), avg(age) from titanic_sdf group by pclass
titanic_sdf.groupBy('Pclass').agg(max('Age'), min('Age'), sum('Age'), avg('Age')).show() # select max(age), min(age), sum(age), avg(age) from titanic_sdf group by pclass
```

    +------+--------+--------+--------+------------------+
    |Pclass|max(Age)|min(Age)|sum(Age)|          avg(Age)|
    +------+--------+--------+--------+------------------+
    |     1|    80.0|    0.92| 7111.42|38.233440860215055|
    |     3|    74.0|    0.42| 8924.92| 25.14061971830986|
    |     2|    70.0|    0.67| 5168.83| 29.87763005780347|
    +------+--------+--------+--------+------------------+
    
    


```python
#아래와 같이 개별 aggregation 함수 결과 컬럼에 별도의 컬럼명을 alias('새로운 컬럼명')을 활용하여 부여 할 수 있음. 
# agg() 메소드 내에서 aggregation 함수 적용 시에는 col('컬럼명')과 같은 컬럼형으로 컬럼명을 지정해도 됨. 
# select max(age) as max_age, min(age) as min_age, sum(age) as sum_age, avg(age) as avg_age from titanic_sdf group by pclass
titanic_sdf.groupBy('Pclass').agg(
    max(col('Age')).alias('max_age'), min('Age').alias('min_age'), \
    sum('Age').alias('sum_age'), avg('Age').alias('avg_age') \
    ).show()
```

    +------+-------+-------+-------+------------------+
    |Pclass|max_age|min_age|sum_age|           avg_age|
    +------+-------+-------+-------+------------------+
    |     1|   80.0|   0.92|7111.42|38.233440860215055|
    |     3|   74.0|   0.42|8924.92| 25.14061971830986|
    |     2|   70.0|   0.67|5168.83| 29.87763005780347|
    +------+-------+-------+-------+------------------+
    
    


```python
# filter()를 적용하여 group by의 aggregation 결과 값을 기준으로 filtering 적용할 수 있음.
titanic_sdf.groupBy('Pclass').agg(max(col('Age')).alias('max_age'), min('Age').alias('min_age') , \
                                 sum('Age').alias('sum_age'), avg('Age').alias('avg_age') \
                                 ).filter(col('max_age') > 70).show()
```

    +------+-------+-------+-------+------------------+
    |Pclass|max_age|min_age|sum_age|           avg_age|
    +------+-------+-------+-------+------------------+
    |     1|   80.0|   0.92|7111.42|38.233440860215055|
    |     3|   74.0|   0.42|8924.92| 25.14061971830986|
    +------+-------+-------+-------+------------------+
    
    
