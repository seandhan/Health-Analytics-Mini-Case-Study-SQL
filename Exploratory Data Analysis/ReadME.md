# 1. Exploratory Data Analysis

---
<details>
<summary>Import Libraries & Database connection</summary>
<span style = "font-family: Arial; font-weight:bold;font-size:2em;color:black;">Importing Libraries


```python
import pyodbc
import pandas as pd
import warnings

warnings.filterwarnings('ignore')
```

<span style = "font-family: Arial; font-weight:bold;font-size:2em;color:black;">Database connection


```python
# Define your MSSQL server connection parameters
Server   = "SeanSD\SQLEXPRESS"
Database = "health"
UID      = ""
PWD      = ""

connection_str = ("Driver={SQL Server Native Client 11.0};"
                  "Server=" + Server + ";"
                  "Database=" + Database + ";"
                  "Trusted_Connection=yes;")

try:
    connection = pyodbc.connect(connection_str)
    print ("Connected to SQL database Server:", Server, "\nDatabase: " + Database)
except pyodbc.Error as ex:
    sqlstate = ex.args[1]
    print ("Unable to connect: ", sqlstate)
```

    Connected to SQL database Server: SeanSD\SQLEXPRESS 
    Database: health
    
</details>

---

## A look at the dataset

Let's take a look at the first 10 rows from the `user_logs` table.


```python
query_str = """
SELECT TOP (10) *
FROM user_logs;
"""
pd.read_sql(query_str, connection)
```




<div>

*Output:*


<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>id</th>
      <th>log_date</th>
      <th>measure</th>
      <th>measure_value</th>
      <th>systolic</th>
      <th>diastolic</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>fa28f948a740320ad56b81a24744c8b81df119fa</td>
      <td>2020-11-15</td>
      <td>weight</td>
      <td>46.039590</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1a7366eef15512d8f38133e7ce9778bce5b4a21e</td>
      <td>2020-10-10</td>
      <td>blood_glucose</td>
      <td>97.000000</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>bd7eece38fb4ec71b3282d60080d296c4cf6ad5e</td>
      <td>2020-10-18</td>
      <td>blood_glucose</td>
      <td>120.000000</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>0f7b13f3f0512e6546b8d2c0d56e564a2408536a</td>
      <td>2020-10-17</td>
      <td>blood_glucose</td>
      <td>232.000000</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>d14df0c8c1a5f172476b2a1b1f53cf23c6992027</td>
      <td>2020-10-15</td>
      <td>blood_pressure</td>
      <td>140.000000</td>
      <td>140.0</td>
      <td>113.0</td>
    </tr>
    <tr>
      <th>5</th>
      <td>0f7b13f3f0512e6546b8d2c0d56e564a2408536a</td>
      <td>2020-10-21</td>
      <td>blood_glucose</td>
      <td>166.000000</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>6</th>
      <td>0f7b13f3f0512e6546b8d2c0d56e564a2408536a</td>
      <td>2020-10-22</td>
      <td>blood_glucose</td>
      <td>142.000000</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>7</th>
      <td>87be2f14a5550389cb2cba03b3329c54c993f7d2</td>
      <td>2020-10-12</td>
      <td>weight</td>
      <td>129.060013</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>8</th>
      <td>0efe1f378aec122877e5f24f204ea70709b1f5f8</td>
      <td>2020-10-07</td>
      <td>blood_glucose</td>
      <td>138.000000</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>9</th>
      <td>054250c692e07a9fa9e62e345231df4b54ff435d</td>
      <td>2020-10-04</td>
      <td>blood_glucose</td>
      <td>210.000000</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>



## Unique measures


```python
query_str = """SELECT DISTINCT measure FROM user_logs;"""

pd.read_sql(query_str, connection)
```




<div>

*Output:*


<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>measure</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>weight</td>
    </tr>
    <tr>
      <th>1</th>
      <td>blood_pressure</td>
    </tr>
    <tr>
      <th>2</th>
      <td>blood_glucose</td>
    </tr>
  </tbody>
</table>
</div>



## Structure Summary


```python
# Table you want to examine
table_name = 'user_logs'

# Get column information for the specific table
cursor = connection.cursor()
columns_query = f"""
SELECT COLUMN_NAME, DATA_TYPE
FROM information_schema.columns
WHERE table_name = '{table_name}'
"""
cursor.execute(columns_query)
columns = cursor.fetchall()

# Get row count for the specific table
row_count_query = f"""
SELECT COUNT(*) FROM {table_name}
"""
cursor.execute(row_count_query)
row_count = cursor.fetchone()[0]

print(f"Table: \033[1m{table_name}\033[0m\n")
print(f"Columns: \033[1m{len(columns)}\033[0m")
print(f"Rows: \033[1m{row_count}\033[0m\n")

# Display column information
for column in columns:
    column_name, data_type = column
    print(f"\tColumn: \033[1m{column_name}\033[0m ({data_type})")

# Initialize a dictionary to store the count of missing values for each column
missing_value_counts = {}

# Loop through the columns and calculate the count of missing values
for column in columns:
    column_name = column.COLUMN_NAME
    missing_value_query = f"""
    SELECT COUNT(*) FROM {table_name}
    WHERE {column_name} IS NULL
    """
    cursor.execute(missing_value_query)
    missing_value_count = cursor.fetchone()[0]
    if missing_value_count > 0:
        missing_value_counts[column_name] = missing_value_count

# Display the columns with missing values
print(f"\nColumns with Missing Values in {table_name}:")
for column_name, missing_count in missing_value_counts.items():
    print(f"\t\033[1m{column_name}\033[0m {missing_count}")
    
# Close the cursor
cursor.close()

```

*Output:*

![table_summary](https://github.com/seandhan/Health-Analytics-Mini-Case-Study-SQL/blob/main/table%20summary.png)
    

## Total record count

Let's also take a look at the total record count.


```python
query_str = """
SELECT 
  COUNT(*) AS 'count'
FROM user_logs;
"""
pd.read_sql(query_str, connection)
```




<div>

*Output:*


<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>count</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>43891</td>
    </tr>
  </tbody>
</table>
</div>



## Unique column count

We'll take a look at how many unique id's are present in the dataset. That'll give us a count of the total number of users.


```python
query_str = """
SELECT COUNT(DISTINCT id) AS 'count'
FROM user_logs;
"""
pd.read_sql(query_str, connection)
```




<div>

*Output:*


<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>count</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>554</td>
    </tr>
  </tbody>
</table>
</div>



## Single column frequency counts

Let's take a look at the measure column and see frequency and the percentage count of each value across the table.


```python
query_str = """
SELECT 
  measure,
  COUNT(*) AS frequency,
  ROUND(100.0* COUNT(*)/SUM(COUNT(*)) OVER(), 2) AS percentage
FROM user_logs
GROUP BY measure
ORDER BY frequency DESC;
"""
pd.read_sql(query_str, connection)
```




<div>

*Output:*


<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>measure</th>
      <th>frequency</th>
      <th>percentage</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>blood_glucose</td>
      <td>38692</td>
      <td>88.15</td>
    </tr>
    <tr>
      <th>1</th>
      <td>weight</td>
      <td>2782</td>
      <td>6.34</td>
    </tr>
    <tr>
      <th>2</th>
      <td>blood_pressure</td>
      <td>2417</td>
      <td>5.51</td>
    </tr>
  </tbody>
</table>
</div>



Let's also see the frequency of unique id's that appear in the dataset and limit the output to just 5.


```python
query_str = """
SELECT TOP (5)
  id,
  COUNT(*) AS frequency,
  ROUND(100.0* COUNT(*)/SUM(COUNT(*)) OVER(),
  2) AS percentage
FROM user_logs
GROUP BY id
ORDER BY frequency DESC;
"""
pd.read_sql(query_str, connection)
```




<div>

*Output:*


<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>id</th>
      <th>frequency</th>
      <th>percentage</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>054250c692e07a9fa9e62e345231df4b54ff435d</td>
      <td>22325</td>
      <td>50.86</td>
    </tr>
    <tr>
      <th>1</th>
      <td>0f7b13f3f0512e6546b8d2c0d56e564a2408536a</td>
      <td>1589</td>
      <td>3.62</td>
    </tr>
    <tr>
      <th>2</th>
      <td>ee653a96022cc3878e76d196b1667d95beca2db6</td>
      <td>1235</td>
      <td>2.81</td>
    </tr>
    <tr>
      <th>3</th>
      <td>abc634a555bbba7d6d6584171fdfa206ebf6c9a0</td>
      <td>1212</td>
      <td>2.76</td>
    </tr>
    <tr>
      <th>4</th>
      <td>576fdb528e5004f733912fae3020e7d322dbc31a</td>
      <td>1018</td>
      <td>2.32</td>
    </tr>
  </tbody>
</table>
</div>



## Individual column distribution

Let's now take a look at the most frequent values across each column.

`Measure Value Column`


```python
query_str = """
SELECT TOP(10)
  measure_value,
  COUNT(*) AS frequency
FROM user_logs
GROUP BY measure_value
ORDER BY frequency DESC;
"""
pd.read_sql(query_str, connection)
```




<div>

*Output:*


<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>measure_value</th>
      <th>frequency</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0.0</td>
      <td>572</td>
    </tr>
    <tr>
      <th>1</th>
      <td>401.0</td>
      <td>433</td>
    </tr>
    <tr>
      <th>2</th>
      <td>117.0</td>
      <td>390</td>
    </tr>
    <tr>
      <th>3</th>
      <td>118.0</td>
      <td>346</td>
    </tr>
    <tr>
      <th>4</th>
      <td>123.0</td>
      <td>342</td>
    </tr>
    <tr>
      <th>5</th>
      <td>122.0</td>
      <td>331</td>
    </tr>
    <tr>
      <th>6</th>
      <td>126.0</td>
      <td>326</td>
    </tr>
    <tr>
      <th>7</th>
      <td>120.0</td>
      <td>323</td>
    </tr>
    <tr>
      <th>8</th>
      <td>115.0</td>
      <td>319</td>
    </tr>
    <tr>
      <th>9</th>
      <td>108.0</td>
      <td>319</td>
    </tr>
  </tbody>
</table>
</div>



`Systolic column`


```python
query_str = """
SELECT TOP(10)
  systolic,
  COUNT(*) AS frequency
FROM user_logs
GROUP BY systolic
ORDER BY frequency DESC;
"""
pd.read_sql(query_str, connection)
```




<div>

*Output:*


<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>systolic</th>
      <th>frequency</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>NaN</td>
      <td>26023</td>
    </tr>
    <tr>
      <th>1</th>
      <td>0.0</td>
      <td>15451</td>
    </tr>
    <tr>
      <th>2</th>
      <td>120.0</td>
      <td>71</td>
    </tr>
    <tr>
      <th>3</th>
      <td>123.0</td>
      <td>70</td>
    </tr>
    <tr>
      <th>4</th>
      <td>128.0</td>
      <td>66</td>
    </tr>
    <tr>
      <th>5</th>
      <td>127.0</td>
      <td>64</td>
    </tr>
    <tr>
      <th>6</th>
      <td>119.0</td>
      <td>60</td>
    </tr>
    <tr>
      <th>7</th>
      <td>130.0</td>
      <td>60</td>
    </tr>
    <tr>
      <th>8</th>
      <td>135.0</td>
      <td>57</td>
    </tr>
    <tr>
      <th>9</th>
      <td>136.0</td>
      <td>55</td>
    </tr>
  </tbody>
</table>
</div>



There are many null and zero values. We'll explore this later.

`Diastolic column`


```python
query_str = """
SELECT  TOP(10)
  diastolic,
  COUNT(*) AS frequency
FROM user_logs
GROUP BY diastolic
ORDER BY frequency DESC;
"""
pd.read_sql(query_str, connection)
```




<div>

*Output:*


<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>diastolic</th>
      <th>frequency</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>NaN</td>
      <td>26023</td>
    </tr>
    <tr>
      <th>1</th>
      <td>0.0</td>
      <td>15449</td>
    </tr>
    <tr>
      <th>2</th>
      <td>80.0</td>
      <td>156</td>
    </tr>
    <tr>
      <th>3</th>
      <td>79.0</td>
      <td>124</td>
    </tr>
    <tr>
      <th>4</th>
      <td>81.0</td>
      <td>119</td>
    </tr>
    <tr>
      <th>5</th>
      <td>78.0</td>
      <td>110</td>
    </tr>
    <tr>
      <th>6</th>
      <td>77.0</td>
      <td>109</td>
    </tr>
    <tr>
      <th>7</th>
      <td>73.0</td>
      <td>109</td>
    </tr>
    <tr>
      <th>8</th>
      <td>83.0</td>
      <td>106</td>
    </tr>
    <tr>
      <th>9</th>
      <td>76.0</td>
      <td>102</td>
    </tr>
  </tbody>
</table>
</div>



This is somewhat similar output if compared to systolic column.

## Deep dive into the specific values

Since there are many 0 values in the measure_value column and some large number of nulls in systolic, diastolic. 

Let's take a look to see if the measure_value = 0 only when there is a specific measure value. We can use the WHERE clause here.


```python
query_str = """
SELECT 
  measure,
  COUNT(*) AS frequency
FROM user_logs
WHERE measure_value = 0
GROUP BY measure
ORDER BY frequency DESC;
"""
pd.read_sql(query_str, connection)
```




<div>

*Output:*


<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>measure</th>
      <th>frequency</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>blood_pressure</td>
      <td>562</td>
    </tr>
    <tr>
      <th>1</th>
      <td>blood_glucose</td>
      <td>8</td>
    </tr>
    <tr>
      <th>2</th>
      <td>weight</td>
      <td>2</td>
    </tr>
  </tbody>
</table>
</div>




```python
query_str = """
SELECT 
  measure,
  COUNT(*) AS frequency,
  ROUND(100.0* COUNT(*)/SUM(COUNT(*)) OVER(),
  2) AS percentage
FROM user_logs
GROUP BY measure
ORDER BY frequency DESC;
"""
pd.read_sql(query_str, connection)
```




<div>

*Output:*


<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>measure</th>
      <th>frequency</th>
      <th>percentage</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>blood_glucose</td>
      <td>38692</td>
      <td>88.15</td>
    </tr>
    <tr>
      <th>1</th>
      <td>weight</td>
      <td>2782</td>
      <td>6.34</td>
    </tr>
    <tr>
      <th>2</th>
      <td>blood_pressure</td>
      <td>2417</td>
      <td>5.51</td>
    </tr>
  </tbody>
</table>
</div>



It appears that `measure_value` = 0 when the `measure` is blood_pressure.

Let's explore further.


```python
query_str = """
SELECT TOP(10)
  measure,
  measure_value,
  systolic,
  diastolic
FROM user_logs
WHERE measure = 'blood_pressure'
AND measure_value = 0;
"""
pd.read_sql(query_str, connection)
```




<div>

*Output:*


<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>measure</th>
      <th>measure_value</th>
      <th>systolic</th>
      <th>diastolic</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>blood_pressure</td>
      <td>0.0</td>
      <td>115.0</td>
      <td>76.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>blood_pressure</td>
      <td>0.0</td>
      <td>115.0</td>
      <td>76.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>blood_pressure</td>
      <td>0.0</td>
      <td>105.0</td>
      <td>70.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>blood_pressure</td>
      <td>0.0</td>
      <td>136.0</td>
      <td>87.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>blood_pressure</td>
      <td>0.0</td>
      <td>164.0</td>
      <td>84.0</td>
    </tr>
    <tr>
      <th>5</th>
      <td>blood_pressure</td>
      <td>0.0</td>
      <td>190.0</td>
      <td>94.0</td>
    </tr>
    <tr>
      <th>6</th>
      <td>blood_pressure</td>
      <td>0.0</td>
      <td>125.0</td>
      <td>79.0</td>
    </tr>
    <tr>
      <th>7</th>
      <td>blood_pressure</td>
      <td>0.0</td>
      <td>136.0</td>
      <td>84.0</td>
    </tr>
    <tr>
      <th>8</th>
      <td>blood_pressure</td>
      <td>0.0</td>
      <td>135.0</td>
      <td>89.0</td>
    </tr>
    <tr>
      <th>9</th>
      <td>blood_pressure</td>
      <td>0.0</td>
      <td>138.0</td>
      <td>85.0</td>
    </tr>
  </tbody>
</table>
</div>



It appears that when blood_pressure is measured, the systolic and diastolic columns are populated but the measure_value is blank. 

Lets explore when the measure is blood_pressure but measure_value!=0.


```python
query_str = """
SELECT TOP(10)
  measure,
  measure_value,
  systolic,
  diastolic
FROM user_logs
WHERE measure = 'blood_pressure'
AND measure_value is NOT NULL
"""
pd.read_sql(query_str, connection)
```




<div>

*Output:*


<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>measure</th>
      <th>measure_value</th>
      <th>systolic</th>
      <th>diastolic</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>blood_pressure</td>
      <td>140.0</td>
      <td>140.0</td>
      <td>113.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>blood_pressure</td>
      <td>114.0</td>
      <td>114.0</td>
      <td>80.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>blood_pressure</td>
      <td>132.0</td>
      <td>132.0</td>
      <td>94.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>blood_pressure</td>
      <td>105.0</td>
      <td>105.0</td>
      <td>68.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>blood_pressure</td>
      <td>149.0</td>
      <td>149.0</td>
      <td>85.0</td>
    </tr>
    <tr>
      <th>5</th>
      <td>blood_pressure</td>
      <td>156.0</td>
      <td>156.0</td>
      <td>88.0</td>
    </tr>
    <tr>
      <th>6</th>
      <td>blood_pressure</td>
      <td>142.0</td>
      <td>142.0</td>
      <td>84.0</td>
    </tr>
    <tr>
      <th>7</th>
      <td>blood_pressure</td>
      <td>131.0</td>
      <td>131.0</td>
      <td>71.0</td>
    </tr>
    <tr>
      <th>8</th>
      <td>blood_pressure</td>
      <td>128.0</td>
      <td>128.0</td>
      <td>77.0</td>
    </tr>
    <tr>
      <th>9</th>
      <td>blood_pressure</td>
      <td>114.0</td>
      <td>114.0</td>
      <td>76.0</td>
    </tr>
  </tbody>
</table>
</div>



So, it looks like whenever blood_pressure is measured, measure_value is populated with systolic and sometimes it is equal to 0.

Let's check the same for the null values of systolic and diastolic.


```python
query_str = """
SELECT TOP(10)
  measure,
  count(*)
FROM user_logs
WHERE systolic is NULL
GROUP BY measure
"""
pd.read_sql(query_str, connection)
```




<div>

*Output:*


<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>measure</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>weight</td>
      <td>443</td>
    </tr>
    <tr>
      <th>1</th>
      <td>blood_glucose</td>
      <td>25580</td>
    </tr>
  </tbody>
</table>
</div>



This confirms that systolic only has non-null values when `measure` = **blood_pressure**. 

Let's see if it is similiar for the diastolic column.


```python
query_str = """
SELECT TOP(10)
  measure,
  count(*)
FROM user_logs
WHERE diastolic is NULL
GROUP BY measure;
"""
pd.read_sql(query_str, connection)
```




<div>

*Output:*


<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>measure</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>weight</td>
      <td>443</td>
    </tr>
    <tr>
      <th>1</th>
      <td>blood_glucose</td>
      <td>25580</td>
    </tr>
  </tbody>
</table>
</div>



Non-null values are only present when ``measure='blood_pressure'``.

---

**Close connection**


```python
# Close the database connection
connection.close()
```

---

