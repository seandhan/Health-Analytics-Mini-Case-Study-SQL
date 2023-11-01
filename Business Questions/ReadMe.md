<details>
<summary>Database connection</summary>
<br>


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

# Business Questions

## How many unique users exist in the logs dataset?


```python
query_str = """SELECT
  COUNT (DISTINCT id) AS unique_users
FROM user_logs"""

pd.read_sql(query_str, connection)
```




<div>

*Output:*

  
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>unique_users</th>
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



---

**Lets create a table for questions 2-8**


```python
try:
    # Create a cursor for the connection
    cursor = connection.cursor()

    # Drop the table if it exists
    drop_table_sql = """
    IF OBJECT_ID('user_measure_count', 'U') IS NOT NULL
        DROP TABLE user_measure_count;
    """
    # Execute the SQL code to drop the table if it exists
    cursor.execute(drop_table_sql)

    # Create a table with the required structure
    create_table_sql = """
    CREATE TABLE user_measure_count (
        id NVARCHAR(255),
        measure_count INT,
        unique_measure_count INT
    );
    """
    # Execute the SQL code to create the table
    cursor.execute(create_table_sql)

    # Populate the table with data
    fill_table_sql = """
    INSERT INTO user_measure_count (id, measure_count, unique_measure_count)
    SELECT
        id,
        COUNT(*) AS measure_count,
        COUNT(DISTINCT measure) AS unique_measure_count
    FROM user_logs
    GROUP BY id;
    """
    # Execute the SQL code to fill the table
    cursor.execute(fill_table_sql)

    # Commit the transaction
    connection.commit()

    # You can now perform further analysis on the created table using pandas
    pd.read_sql("SELECT TOP 5 * FROM user_measure_count", connection)

except Exception as e:
    # Handle any exceptions here
    print(f"An error occurred: {str(e)}")
    # Roll back the transaction to ensure data consistency
    connection.rollback()

finally:
    # Close the cursor
    cursor.close()
    
# You can now perform further analysis on the created table using pandas
pd.read_sql("SELECT TOP 5 * FROM user_measure_count", connection) 
```





*Output:*

  
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>id</th>
      <th>measure_count</th>
      <th>unique_measure_count</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>004beb6711843b214e80d73df57a3680fdf9700a</td>
      <td>3</td>
      <td>2</td>
    </tr>
    <tr>
      <th>1</th>
      <td>007fe1259a4283a991e1f2835ddcdedacf78dde9</td>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>2</th>
      <td>008dd1dc1728bb0b420188963905d259c5533149</td>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>3</th>
      <td>00ae4fa0241952312d518cee728a387bf156f514</td>
      <td>4</td>
      <td>1</td>
    </tr>
    <tr>
      <th>4</th>
      <td>0115244529929acd03b01315cff7eabfb9f126af</td>
      <td>1</td>
      <td>1</td>
    </tr>
  </tbody>
</table>
</div>



---

## How many total measurements do we have per user on average?


```python
num_2_query_str = """
SELECT 
    ROUND(AVG(CAST(measure_count AS FLOAT)),2) AS avg_measurement
    FROM
    user_measure_count;
"""

pd.read_sql(num_2_query_str, connection)
```




<div>

*Output:*

  
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>avg_measurement</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>79.23</td>
    </tr>
  </tbody>
</table>
</div>



---

## What about the median number of measurements per user?


```python
num_3_query_str = """
SELECT TOP 1
    PERCENTILE_CONT(0.5) WITHIN GROUP (ORDER BY measure_count)
        OVER () AS median_value

FROM
  user_measure_count;
"""
pd.read_sql(num_3_query_str, connection)
```




<div>

*Output:*

  
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>median_value</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2.0</td>
    </tr>
  </tbody>
</table>
</div>



---

## How many users have 3 or more measurements?


```python
num_4_query_str = """
SELECT 
  COUNT(*) AS more_than_equal_3_count
FROM user_measure_count
WHERE measure_count >= 3
"""
pd.read_sql(num_4_query_str, connection)
```




<div>

*Output:*

  
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>more_than_equal_3_count</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>209</td>
    </tr>
  </tbody>
</table>
</div>



---

## How many users have 1,000 or more measurements?


```python
num_5_query_str = """
SELECT 
  COUNT(*) AS more_than_equal_1000_count
FROM user_measure_count
WHERE measure_count >= 1000
"""
pd.read_sql(num_5_query_str, connection)
```




<div>

*Output:*

  
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>more_than_equal_1000_count</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>5</td>
    </tr>
  </tbody>
</table>
</div>



---

## What is the number and percentage of the active user base who have logged blood glucose measurements?


```python
num_6_query_str = """
WITH user_test_stats AS (
SELECT
  measure,
  COUNT(DISTINCT id) AS unique_num_tests,
  ROUND(100.0 * COUNT(DISTINCT id) / SUM(COUNT(DISTINCT id)) OVER (),2) AS percentage
FROM user_logs
GROUP BY measure
)

SELECT * FROM user_test_stats WHERE measure  = 'blood_glucose';
"""


pd.read_sql(num_6_query_str, connection)
```




<div>

*Output:*

  
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>measure</th>
      <th>unique_num_tests</th>
      <th>percentage</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>blood_glucose</td>
      <td>325</td>
      <td>40.22</td>
    </tr>
  </tbody>
</table>
</div>



---

## What is the number and percentage of the active user base who have at least 2 types of measurements?


```python
num_7_query_str = """
WITH measure_more_than_2 AS (
SELECT *
FROM user_measure_count
WHERE unique_measure_count >= 2
)

SELECT
  COUNT(DISTINCT m.id) AS unique_user,
  ROUND(100.0 * COUNT(DISTINCT m.id) / COUNT(DISTINCT u.id),2) AS unique_user_percentage
FROM user_measure_count AS u
LEFT JOIN measure_more_than_2 AS m
  ON u.id = m.id;
"""


pd.read_sql(num_7_query_str, connection)
```




<div>

*Output:*

  
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>unique_user</th>
      <th>unique_user_percentage</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>204</td>
      <td>36.82</td>
    </tr>
  </tbody>
</table>
</div>



---

## Have all 3 measures - blood glucose, weight and blood pressure?


```python
num_8_query_str = """
WITH all_measures AS (
SELECT *
FROM user_measure_count
WHERE unique_measure_count = 3)

SELECT
  COUNT(DISTINCT m.id) AS unique_user,
  ROUND(100.0*COUNT(DISTINCT m.id) / COUNT(DISTINCT u.id),2) AS unique_user_percentage
FROM user_measure_count AS u
LEFT JOIN all_measures AS m
  ON u.id = m.id
"""

pd.read_sql(num_8_query_str, connection)
```




<div>

*Output:*

  
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>unique_user</th>
      <th>unique_user_percentage</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>50</td>
      <td>9.03</td>
    </tr>
  </tbody>
</table>
</div>



## What is the median systolic/diastolic blood pressure values?


```python
num_9_query_str = """
SELECT
    TOP 1
    'blood_pressure' AS measure_name,
    PERCENTILE_CONT(0.5) WITHIN GROUP (ORDER BY systolic) OVER () AS systolic_median,
    PERCENTILE_CONT(0.5) WITHIN GROUP (ORDER BY diastolic)  OVER () AS diastolic_median
 FROM user_logs
 WHERE measure = 'blood_pressure';
"""


pd.read_sql(num_9_query_str, connection)
```




<div>

*Output:*

  
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>measure_name</th>
      <th>systolic_median</th>
      <th>diastolic_median</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>blood_pressure</td>
      <td>126.0</td>
      <td>79.0</td>
    </tr>
  </tbody>
</table>
</div>



**Close connection**


```python
# Close the database connection
connection.close()
```

---

---

<a id="Question_7"></a>

<a id="Question_8"></a>

<a id="Question_9"></a>
