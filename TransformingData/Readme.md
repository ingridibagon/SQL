In this exercise, we extract data from the csv file clients_sex.csv. The file has three values separated by commas ID, NAME, and SEX. Sex values are not standardized with different Portuguese names for Feminine and Masculine. Some names have spaces in front and start with low-case letters. The idea is to create a table with the data from the csv file and clean and standardize the data using SQL and the SGDB from Oracle.
## Create a table from the csv file

1. Put the clients_sex.csv file in a folder in your computer for example 'C:\SQLexercises'
2. Using SQL PLUS connect to the Oracle database and use the path to the csv file
   ```
   CREATE OR REPLACE DIRECTORY  SQLexercises AS 'c:\SQLexercises';
   ```
3. Create a table using the data from the csv file
   ```
   CREATE TABLE client_sex
        (
            ID varchar2(3),
            NAME varchar2(50),   
            SEX varchar2(10)
        )
        ORGANIZATION EXTERNAL
        (
          TYPE ORACLE_LOADER
          DEFAULT DIRECTORY SQLexercises
          ACCESS PARAMETERS
              (
                RECORDS DELIMITED BY NEWLINE
                FIELDS TERMINATED BY ','
              )
        LOCATION ('clients_sex.csv')
        );
   ```
4. Select all the data from the table you just created
```
SELECT * FROM client_sex;

```
5. Create a copy of the table to perform the data transformation
   ```
   CREATE TABLE client_sex_T AS (SELECT * FROM client_sex);
   ```
6.  List the distinct values from column SEX
```
SELECT DISTINCT SEX FROM client_sex_T ORDER BY SEX;
```
7. Update column SEX to use F for all the variants of feminine
```
UPDATE client_sex_T
SET SEX='F'
WHERE SEX IN(' Fem', ' Feminino', ' mulher', 'f', 'Fem', 'Feminino')
```
8. Using a similar code to 7. to update SEX to use 'M' for all the variants of masculine, and outros for indefinido and outros.
9. Update the column NAME to trim space in front of the name and to put the initial capital letter in all names
    ```
    UPDATE client_sex_T
    SET NAME=INITCAP(NAME);

    UPDATE client_sex_T
    SET NAME=LTRIM(NAME);  
    ```


10. Subtotals by SEX
    ```
    SELECT SEX, COUNT(SEX)
    FROM client_sex_T
    GROUP BY ROLLUP(SEX);
    ```
11. Create a csv file  with the transformed data
   ```
set colsep ,
set headsep off
set pagesize 0
set trimspool on
SPOOL 'C:/SQLexercises/client_sexT.csv'
SELECT * FROM client_sex_T
SPOOL OFF
```

   
   
