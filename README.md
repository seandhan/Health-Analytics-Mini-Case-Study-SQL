[![forthebadge](https://github.com/seandhan/Health-Analytics-Mini-Case-Study-SQL/blob/main/images/USES-SQL%20SERVER-.svg)]()
[![forthebadge](https://forthebadge.com/images/badges/made-with-python.svg)](https://forthebadge.com)
[![forthebadge](https://forthebadge.com/images/badges/made-with-markdown.svg)](https://forthebadge.com)

<h1 align="center">Health Analytics Mini Case Study (SQL) ☤ </h1>


<p align="center">The primary objective of this project is to conduct an in-depth analysis of data from a sales records database, with the intent of extracting meaningful insights that can be employed to enhance decision-making processes. 
  </p>



# Health Analytics Mini Case Study (SQL)
---

## Project Description

The General Manager of Analytics at Health Co has urgently requested help analyzing their `health.user_logs` dataset. 

We have been asked to quickly analyse the dataset and answer questions about active users for an upcoming board meeting.

---

## Table of Contents

- [Skills and Tools Used](#skills-and-tools-used)
- [Business Questions](#business-questions)
- [Dataset Info](#dataset-info)
- [Data Source](#data-source)
- [Code](#code)
- [Database Exploration](#database-exploration)
- [Lessons Learnt](#Lessons-Learnt)
- [Contact Information](#contact-information)

---

## Skills and Tools Used

- SQL
- Python
- Pandas
- Pyodbc
- Jupyter Notebook

---


## Business Questions

Let’s cover the business questions that we need to help the GM answer!

1. How many unique users exist in the logs dataset?
2. How many total measurements do we have per user on average?
3. What about the median number of measurements per user?
4. How many users have 3 or more measurements?
5. How many users have 1,000 or more measurements?

Looking at the logs data - what is the number and percentage of the active user base who:

6. Have logged blood glucose measurements?
7. Have at least 2 types of measurements?
8. Have all 3 measures - blood glucose, weight and blood pressure?

For users that have blood pressure measurements:

9. What is the median systolic/diastolic blood pressure values?

---

## Dataset Info


The provided `health.user_logs` dataset contains the following variables:


| id                                       | log\_date                | measure        | measure\_value | systolic | diastolic |
| ---------------------------------------- | ------------------------ | -------------- | -------------- | -------- | --------- |


---

## Data Source

The  `health.user_logs` dataset was provided as an excel file. Click [here](https://github.com/seandhan/Health-Analytics-Mini-Case-Study-SQL/blob/main/user_logs.csv)


---

### Dataset preparation

For this analysis the excel file was imported in Microsoft SQL Server and MSSQL queries were used to explore the data. A `health` database was created with `user_logs` table. 

See the following steps for more detailed procedure for importing Excel files into SQL Server to create databases.
>
>**Step 1: Prepare Your Excel File**
>
>Ensure your Excel file is organized with a header row and data in subsequent rows.
>Verify that the column names and data types in the Excel file match the table structure you want to create in SQL Server.
>
>**Step 2: Set Up SQL Server**
>
>Ensure you have SQL Server installed and configured on your system.
>Create a new database or identify an existing one where you want to import the data.
>
>**Step 3: Install Required Drivers**
>
>Check if you have the necessary ODBC or OLE DB drivers installed on your system. If not, you may need to install them. You can usually find these drivers in the SQL Server installation package.
>
>**Step 4: Open SQL Server Management Studio (SSMS)**
>
>Launch SQL Server Management Studio (SSMS) on your computer.
>
>**Step 5: Connect to SQL Server**
>
>Connect to your SQL Server instance by providing the server name and appropriate authentication method (Windows or SQL Server Authentication).
>
>**Step 6: Import Data Using the SQL Server Import and Export Wizard**
>
>In SSMS, right-click on the database where you want to import the data, and choose "Tasks" > "Import Data."
>
>**Step 7: SQL Server Import and Export Wizard - Introduction**
>
>In the SQL Server Import and Export Wizard, you'll see an introduction screen. Click "Next" to proceed.
>
>**Step 8: Data Source**
>
>Choose the "Data Source" as "Microsoft Excel." Click "Browse" to locate your Excel file.
Specify the version of the Excel file (Excel 97-2003 or Excel 2007+).
Select the appropriate Excel worksheet if your file contains multiple sheets. Click "Next."
>
>**Step 9: Destination**
>
>Choose the "Destination" as "SQL Server Native Client."
Enter your SQL Server connection details, including the server name, authentication method, and database name.
Click "Next" to proceed.
>
>**Step 10: Copy Data from Excel**
>
>In the "Copy data from one or more tables or views" section, you can choose to copy data from the Excel file into an existing table in your database or create a new table.
If creating a new table, you can map the Excel columns to SQL Server table columns. Ensure that data types match correctly. You can also set options for handling duplicate records.
Click "Next" to continue.
>
>**Step 11: Review and Execute**
>
>Review the settings you've configured. If everything looks correct, click "Next."
>
>**Step 12: Complete the Wizard**
>
>The wizard will now run the import process, and you'll see a summary of the operation.
Check for any error messages or warnings. If there are any issues, you can review the details and correct them.
Once the import is successful, click "Finish" to complete the wizard.
>
>Step 13: Verify Data
>
>Open SSMS, connect to your SQL Server database, and query the newly imported data to ensure it has been successfully transferred.
Your Excel data is now available in your SQL Server database, and you can use it to create tables, views, or perform other necessary operations. After importing, consider any additional data cleaning or transformation tasks as required for your specific use case.

---

## Code

The code used to the explore this database can be found in the Jupyter notebook. Click [HERE](https://github.com/seandhan/Health-Analytics-Mini-Case-Study-SQL/blob/main/Health%20Analytics%20Mini%20Case%20Study.ipynb) 

---

## Database Exploration

There are 3 unique measures


![Unique measures.png](https://github.com/seandhan/Health-Analytics-Mini-Case-Study-SQL/blob/main/images/Unique%20measures.png)


### Overview

The database summary is as follows:

![Table summary.png](https://github.com/seandhan/Health-Analytics-Mini-Case-Study-SQL/blob/main/images/Table%20summary.png)


---

## 1. How many unique users exist in the logs dataset?

![q1.png](https://github.com/seandhan/Health-Analytics-Mini-Case-Study-SQL/blob/main/images/q1.png)

There are **554** unique users in the dataset.

---
## 2. How many total measurements do we have per user on average?

![q2.png](https://github.com/seandhan/Health-Analytics-Mini-Case-Study-SQL/blob/main/images/q2.png)

The average measurements per user is **79** measurements.

---

## 3. What about the median number of measurements per user?

![q3.png](https://github.com/seandhan/Health-Analytics-Mini-Case-Study-SQL/blob/main/images/q3.png)

The median measurement per user is **2**.

---
## 4. How many users have 3 or more measurements?

![q4.png](https://github.com/seandhan/Health-Analytics-Mini-Case-Study-SQL/blob/main/images/q4.png)

**209** users have 3 or more measurements.

---
## 5. How many users have 1,000 or more measurements?

![q5.png](https://github.com/seandhan/Health-Analytics-Mini-Case-Study-SQL/blob/main/images/q5.png)

There are **5** users with 1,000 or more measurements..

---
## 6. What is the number and percentage of the active user base who have logged blood glucose measurements?

![q6.png](https://github.com/seandhan/Health-Analytics-Mini-Case-Study-SQL/blob/main/images/q6.png)

There are **325** users or **40%** of user base who have logged blood glucose measurements.

---
## 7. What is the number and percentage of the active user base who have at least 2 types of measurements?

![q7.png](https://github.com/seandhan/Health-Analytics-Mini-Case-Study-SQL/blob/main/images/q7.png)

Out of **554** active users, **204** users have at least 2 types of measurements making up **37%** of total active user base.

---
## 8. What is the number and percentage of the active user base who have all 3 measures - blood glucose, weight and blood pressure?

![q8.png](https://github.com/seandhan/Health-Analytics-Mini-Case-Study-SQL/blob/main/images/q8.png)

Out of **554** active users, **50** users have taken all 3 measures making up **9%** of total active user base.

---
## 9. For users that have blood pressure measurements, what is the median systolic/diastolic blood pressure values?

![q9.png](https://github.com/seandhan/Health-Analytics-Mini-Case-Study-SQL/blob/main/images/q9.png)

The median for systolic and diastolic are 126 and 79.

---

## Lessons Learnt

1. Microsoft SQL Server

2. Debugging Code

3. Summary statisitcs (mean & median)

4. Sorting & filtering Data


## Contact Information

- Email: [sean_dhanasar@msn.com](mailto:sean_dhanasar@msn.com)
- LinkedIn: [Sean Dhanasar](https://www.linkedin.com/in/sdhanasar)


