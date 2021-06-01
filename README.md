# 3 Pyspark Dataframe: Handling Missing Values

# 1. Dropping Columns, rows
~~~python
df_spark.drop("Name").show()

+----+----------+------+
| age|Experience|Salary|
+----+----------+------+
|  31|        10| 30000|
|  30|         8| 25000|
|  29|         4| 20000|
|  24|         3| 20000|
|  21|         1| 15000|
|  23|         2| 18000|
|null|      null| 40000|
|  34|        10| 38000|
|  36|      null|  null|
+----+----------+------+
~~~
### Droppin Null

~~~python
df_spark.na.drop().show()
+---------+---+----------+------+
|     Name|age|Experience|Salary|
+---------+---+----------+------+
|    Krish| 31|        10| 30000|
|Sudhanshu| 30|         8| 25000|
|    Sunny| 29|         4| 20000|
|     Paul| 24|         3| 20000|
|   Harsha| 21|         1| 15000|
|  Shubham| 23|         2| 18000|
+---------+---+----------+------+
~~~
### Threshold
~~~python
df_spark.na.drop(how="any", thresh = 3).show()
+---------+---+----------+------+
|     Name|age|Experience|Salary|
+---------+---+----------+------+
|    Krish| 31|        10| 30000|
|Sudhanshu| 30|         8| 25000|
|    Sunny| 29|         4| 20000|
|     Paul| 24|         3| 20000|
|   Harsha| 21|         1| 15000|
|  Shubham| 23|         2| 18000|
|     null| 34|        10| 38000|
+---------+---+----------+------+
~~~

### Subset
~~~python
df_spark.na.drop(how="any",subset=["Experience","Name"]).show()
+---------+---+----------+------+
|     Name|age|Experience|Salary|
+---------+---+----------+------+
|    Krish| 31|        10| 30000|
|Sudhanshu| 30|         8| 25000|
|    Sunny| 29|         4| 20000|
|     Paul| 24|         3| 20000|
|   Harsha| 21|         1| 15000|
|  Shubham| 23|         2| 18000|
+---------+---+----------+------+
~~~

# 2. Filling the missing values
~~~python
df_spark.na.fill('Missing values').show()
+--------------+----+----------+------+
|          Name| age|Experience|Salary|
+--------------+----+----------+------+
|         Krish|  31|        10| 30000|
|     Sudhanshu|  30|         8| 25000|
|         Sunny|  29|         4| 20000|
|          Paul|  24|         3| 20000|
|        Harsha|  21|         1| 15000|
|       Shubham|  23|         2| 18000|
|        Mahesh|null|      null| 40000|
|Missing values|  34|        10| 38000|
|Missing values|  36|      null|  null|
+--------------+----+----------+------+

df_spark.na.fill(0,['Experience','age']).show()
+---------+---+----------+------+
|     Name|age|Experience|Salary|
+---------+---+----------+------+
|    Krish| 31|        10| 30000|
|Sudhanshu| 30|         8| 25000|
|    Sunny| 29|         4| 20000|
|     Paul| 24|         3| 20000|
|   Harsha| 21|         1| 15000|
|  Shubham| 23|         2| 18000|
|   Mahesh|  0|         0| 40000|
|     null| 34|        10| 38000|
|     null| 36|         0|  null|
+---------+---+----------+------+

~~~

# 3. Handling Missing values by Mean, MEdian And Mode
~~~python
from pyspark.ml.feature import Imputer

imputer = Imputer(
    inputCols=['age', 'Experience', 'Salary'], 
    outputCols=["{}_imputed".format(c) for c in ['age', 'Experience', 'Salary']]
    ).setStrategy("mean")

imputer.fit(df_spark).transform(df_spark).show()
+---------+----+----------+------+-----------+------------------+--------------+
|     Name| age|Experience|Salary|age_imputed|Experience_imputed|Salary_imputed|
+---------+----+----------+------+-----------+------------------+--------------+
|    Krish|  31|        10| 30000|         31|                10|         30000|
|Sudhanshu|  30|         8| 25000|         30|                 8|         25000|
|    Sunny|  29|         4| 20000|         29|                 4|         20000|
|     Paul|  24|         3| 20000|         24|                 3|         20000|
|   Harsha|  21|         1| 15000|         21|                 1|         15000|
|  Shubham|  23|         2| 18000|         23|                 2|         18000|
|   Mahesh|null|      null| 40000|         28|                 5|         40000|
|     null|  34|        10| 38000|         34|                10|         38000|
|     null|  36|      null|  null|         36|                 5|         25750|
+---------+----+----------+------+-----------+------------------+--------------+
~~~