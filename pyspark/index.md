# [Tips] Pyspark


pyspark와 관련한 tips 정리

<!--more-->

### DataFrame join할 때, 같은 이름의 컬럼이 있는 경우 문제 발생 가능
- 1. 같은 이름으로 join하는 경우 표현식을 쓰지 않고 문자열로 `on` 조건을 걸면 된다. : `df1.join(df2, "same_col_name", "[how]")`
- 2. join하는 컬럼이름은 달라도 같은 이름의 컬럼이 존재하는 경우 join하기 전에 이름을 바꾸자.

### 특정 조건일 떄, 특정 컬럼의 값 바꾸기 `when`, `otherwise` 를 같이 이용하자
```python
df = df.withColumn(
    "col_name",
    when(col("col_1") == 1, 2).otherwise(col("col_name"))
)
```

### pandas를 pyspark dataframe으로 바꾸는 법
```python
spark.createDataFrame(df)
```

### 새로운 컬럼을 만들 때, 특정 상수로 채우기 `lit` 이용
```python
df.withColumn("new_col", lit(상수))
```

### 자주 쓰는 데이터 & 데이터의 크기가 그렇게 크지 않는 경우: `toPandas()` 이용해서 pandas로 진행하자

### groupby하고 특정 컬럼값의 unique한 갯수 세기 `countDistinct`
```python
df.groupby('groupby할 컬럼').agg(countDistinct('컬럼').alias('원하는 컬럼이름'))
```

### 여러 개의 값들에 해당하는 row를 뽑아내기 `isin`
```python
df[df['컬럼'].isin([값1, 값2, ...])]
```

### column-wise sum 하는 법
- fancy한 방법을 찾지는 못함
- `lit(0)`으로 0인 컬럼 만들고 더하고 싶은 컬럼들을 for문으로 반복해서 더함

### usere defined function
- `udf`를 이용
- spark.ml에서 logit 모형을 만들었는데 예측 `probability` 컬럼의 schema가 VectorUDT였다. 이는 `[0.9, 0.1]`의 형태를 갖고 있었는데 이를 처리하기 위해 아래와 같은 방법으로 진행하였다.

```python
from pyspark.sql.functions.F import udf

def get_prob(x):
  return float(x[0])

# wrap the function and s-tore as a variable
my_udf = udf(get_prob, FloatType())

user_df = user_df.withColumn('probability', my_udf(user_df.probability))
```

### 날짜(DATE) `ORDER BY`
- pyspark에서 sql쿼리 사용할 때, 날짜를 정렬해야하는 경우
  - `ASC`: 날짜가 가장 오래된 것부터 가장 최근 순으로 정렬
  - `DESC`: 반대

```python
df = spark.sql("""
    SELECT 
        *
    FROM (
        SELECT 
            *,
            ROW_NUMBER() OVER (PARTITION BY id, sex ORDER BY dt ASC) as idx
        FROM user_df
    ) u
    WHERE u.idx == "1"
""")
```
