--------------------------------------------------------------------------------

# **TABLES STRUCTURE**

--------------------------------------------------------------------------------

NOTE: these are just extracts from bigger tables, to show their structure

  # table name: client 


|client_id|first_name|middle_name|last_name|date_of_birth|
|---------|----------|-----------|---------|-------------|
|33       |Susan     |Mapenzi    |Marigu   |1974-06-11   |
|35       |Paul      |           |Pogba    |1993-03-15   |
|36       |Hafsa     |Wangui     |Munga    |1987-05-07   |
|37       |Everlyne  |           |Maten'ge |1973-02-27   |
|38       |Barack    |           |Obama    |1961-08-04   |
|39       |Prudence  |Salim      |Okeyo    |1985-02-16   |
|40       |Rosemary  |Pauline    |Kinyua   |1977-01-27   |
|42       |Elizabeth |           |Mbaji    |1975-10-03   |
|43       |Johny     |Paul       |Orengo   |1971-07-29   |
|44       |Merceline |Lucy       |Njenga   |1982-04-21   |

  # table name: loan
  
|loan_id|client_id|vehicle_id|principal_amount|submitted_on_date|
|-------|---------|----------|----------------|-----------------|
|75676  |40784    |24        |106500          |2019-05-02       |
|75659  |40760    |26        |108400          |2020-12-05       |
|75685  |40807    |27        |101500          |2019-05-02       |
|75657  |40796    |28        |271482          |2019-06-21       |
|75662  |40803    |29        |114400          |2019-05-02       |
|75660  |40737    |30        |95300           |2019-05-02       |
|75656  |40815    |31        |78500           |2019-05-02       |
|75666  |40834    |32        |111800          |2019-05-02       |
|75658  |40811    |33        |107050          |2019-05-02       |
|75663  |40840    |34        |101800          |2019-05-02       |

  # table name: vehicle
 
|vehicle_id|make    |model_name      |
|----------|--------|----------------|
|24        |Haojin  |HJ 150CC-11A    |
|26        |Honda   |Ace CB 125CC ES |
|27        |TVS     |HLX 125CC ES    |
|29        |TVS     |HLX 150CC X     |
|30        |TVS     |HLX 100CC KS    |
|31        |Haojin  |HJ 125CC-A      |
|32        |Boxer   |BM 150CC (4)    |   
|33        |Ferrari |Enzo 6000CC     |
|34        |Boxer   |BM 150cc-2      |
|35        |Boxer   |BM 150cc-3      |

--------------------------------------------------------------------------------
-- QUESTIONS
--------------------------------------------------------------------------------
Note:
The SQL code below is write with respect to PostgreSQL syntax and keywords.

Using the tables above, please write the SQL code that would answer each of the following questions (write the code below each of them).

1. -- Select all the clients called Paul in first_name or middle_name and who are more than 25 years old.
   -- In the results, create a column with the client's age in years. 
   -- Order them from older to younger.
```sql
WITH client_age AS 
(
    SELECT client_id, first_name, middle_name, last_name, date_of_birth, age(CURRENT_DATE, date_of_birth) AS age, 
        EXTRACT(year FROM age(CURRENT_DATE, date_of_birth)) as age_2 
    FROM client 
    ORDER BY age DESC
)
SELECT client_id, first_name, middle_name, last_name, date_of_birth, age 
FROM client_age
WHERE LOWER(first_name) = 'paul' AND  age_2 > 25  or LOWER(middle_name) = 'paul' AND  age_2 > 25     
```
Results:

|client_id |first_name    |middle_name   |last_name   |date_of_birth    |age                    |
|----------|--------------|--------------|------------|-----------------|-----------------------|
|43        |Johny         |Paul          |Orengo      |1971-07-29       |50 years 2 mons 10 days|
|35        |Paul          |NULL          |Pogba       |1993-03-15       |28 years 6 mons 24 days|

Explanation:

Only two clients will meet the criteria of having either first name or last name be paul while they are 25 years and above.
    
2. -- Add a column to the table from question (1) that contains the number of loans each customer made.
   -- If there is no loan, this column should show 0.

Note:
The question was not clear and had two interpretations which are:
    1. Add the column to the table created with the condition applied.
    2. Add the column to the table created without the condition.
The two implementations are provided below:-

Table with Condition Apply
```sql
WITH client_age AS 
(
    SELECT client_id, first_name, middle_name, last_name, date_of_birth, age(CURRENT_DATE, date_of_birth) AS age, 
        EXTRACT(year FROM age(CURRENT_DATE, date_of_birth)) as age_2 
    FROM client
    ORDER BY age DESC
)
SELECT client_age.client_id, client_age.first_name, client_age.middle_name, client_age.last_name, client_age.date_of_birth, client_age.age, COALESCE(loan.principal_amount, 0) as new_principal_amount
FROM client_age 
LEFT JOIN public.loan as loan on client_age.client_id = loan.client_id
WHERE LOWER(client_age.first_name) = 'paul' AND  client_age.age_2 > 25  or LOWER(client_age.middle_name) = 'paul' AND  client_age.age_2 > 25;
```
Output:

| client_id | first_name | middle_name | last_name | date_of_birth | age                     | new_principal_amount |
|-----------|------------|-------------|-----------|---------------|-------------------------|----------------------|
| 43        | Johny      | Paul        | Orengo    | 1971-07-29    | 50 years 2 mons 10 days | 0                    |
| 35        | Paul       | NULL        | Pogba     | 1993-03-15    | 28 years 6 mons 24 days | 0                    |


Table without condition applied
```sql
WITH client_age AS 
(
    SELECT client_id, first_name, middle_name, last_name, date_of_birth, age(CURRENT_DATE, date_of_birth) AS age, 
        EXTRACT(year FROM age(CURRENT_DATE, date_of_birth)) as age_2 
    FROM client
    ORDER BY age DESC
)
SELECT client_age.client_id, client_age.first_name, client_age.middle_name, client_age.last_name, client_age.date_of_birth, client_age.age, COALESCE(loan.principal_amount, 0) as new_principal_amount
FROM client_age 
LEFT JOIN public.loan as loan on client_age.client_id = loan.client_id;
```

Output:

| client_id | first_name | middle_name | last_name | date_of_birth | age                     | new_principal_amount |
|-----------|------------|-------------|-----------|---------------|-------------------------|----------------------|
| 39        | Prudence   | Salim       | Okeyo     | 1985-02-16    | 36 years 7 mons 20 days | 0                    |
| 33        | Susan      | Mapenzi     | Marigu    | 1974-06-11    | 47 years 3 mons 27 days | 0                    |
| 40        | Rosemary   | Pauline     | Kinyua    | 1977-01-27    | 44 years 8 mons 12 days | 0                    |
| 37        | Everlyne   | NULL        | Maten'ge  | 1973-02-27    | 48 years 7 mons 9 days  | 0                    |
| 38        | Barack     | NULL        | Obama     | 1961-08-04    | 60 years 2 mons 4 days  | 0                    |
| 42        | Elizabeth  | NULL        | Mbaji     | 1975-10-03    | 46 years 5 days         | 0                    |
| 36        | Hafsa      | Wangui      | Munga     | 1987-05-07    | 34 years 5 mons 1 day   | 0                    |
| 44        | Merceline  | Lucy        | Njenga    | 1982-04-21    | 39 years 5 mons 17 days | 0                    |
| 43        | Johny      | Paul        | Orengo    | 1971-07-29    | 50 years 2 mons 10 days | 0                    |
| 35        | Paul       | NULL        | Pogba     | 1993-03-15    | 28 years 6 mons 24 days | 0                    |

3. -- Select the 100cc, 125cc and 150cc bikes from the vehicle table.
   -- Add an engine_size column to the output (that contains the engine size).

```sql
SELECT vehicle_id, make, model_name,
CASE 
    WHEN LOWER(model_name) LIKE '%100cc%' THEN '100CC'
    WHEN LOWER(model_name) LIKE '%125cc%' THEN '125CC'
    WHEN LOWER(model_name) LIKE '%150cc%' THEN '150CC'
END AS rating
FROM public.vehicle
```
Output:

| vehicle_id | make    | model_name      | rating |
|------------|---------|-----------------|--------|
| 24         | Haojin  | HJ 150CC-11A    | 150CC  |
| 26         | Honda   | Ace CB 125CC ES | 125CC  |
| 27         | TVS     | HLX 125CC ES    | 125CC  |
| 29         | TVS     | HLX 150CC X     | 150CC  |
| 30         | TVS     | HLX 100CC KS    | 100CC  |
| 31         | Haojin  | HJ 125CC-A      | 125CC  |
| 32         | Boxer   | BM 150CC (4)    | 150CC  |
| 33         | Ferrari | Enzo 6000CC     | NULL   |
| 34         | Boxer   | BM 150cc-2      | 150CC  |
| 35         | Boxer   | BM 150cc-3      | 150CC  |

4. -- Calculate the total principal_amount per client full name (one column that includes all the names for each client) and per vehicle make.

```sql
SELECT 
    CONCAT(first_name, ' ',  middle_name, ' ', last_name) as full_name, vehicle.make as make, loan.principal_amount
FROM client
LEFT JOIN loan ON client.client_id = loan.client_id
LEFT JOIN vehicle on loan.vehicle_id = vehicle.vehicle_id
```

Output:

| full_name               | make | principal_amount |
|-------------------------|------|------------------|
| Prudence Salim Okeyo    | NULL | NULL             |
| Susan Mapenzi Marigu    | NULL | NULL             |
| Rosemary Pauline Kinyua | NULL | NULL             |
| Everlyne  Maten'ge      | NULL | NULL             |
| Barack  Obama           | NULL | NULL             |
| Elizabeth  Mbaji        | NULL | NULL             |
| Hafsa Wangui Munga      | NULL | NULL             |
| Merceline Lucy Njenga   | NULL | NULL             |
| Johny Paul Orengo       | NULL | NULL             |
| Paul  Pogba             | NULL | NULL             |

6. -- Select the loan table and add an extra column that shows the chronological loan order for each client based on the submitted_on_date column: 
   -- 1 if it's the client's first sale, 2 if it's the client's second sale etc.
   -- Call it loan_order

```sql
WITH client AS 
(
    SELECT loan_id, client_id, vehicle_id, principal_amount, submitted_on_date
    FROM    loan
    ORDER BY client_id, submitted_on_date
)
SELECT loan_id, client_id, vehicle_id, principal_amount, submitted_on_date, ROW_NUMBER () OVER (partition BY client_id) as loan_order
FROM client
```

Output:

| loan_id | client_id | vehicle_id | principal_amount | submitted_on_date | loan_order |
|---------|-----------|------------|------------------|-------------------|------------|
| 75660   | 40737     | 30         | 95300            | 02/05/2019        | 1          |
| 75659   | 40760     | 26         | 108400           | 05/12/2020        | 1          |
| 75676   | 40784     | 24         | 106500           | 02/05/2019        | 1          |
| 75657   | 40796     | 28         | 271482           | 21/06/2019        | 1          |
| 75662   | 40803     | 29         | 114400           | 02/05/2019        | 1          |
| 75685   | 40807     | 27         | 101500           | 02/05/2019        | 1          |
| 75658   | 40811     | 33         | 107050           | 02/05/2019        | 1          |
| 75656   | 40815     | 31         | 78500            | 02/05/2019        | 1          |
| 75666   | 40834     | 32         | 111800           | 02/05/2019        | 1          |
| 75663   | 40840     | 34         | 101800           | 02/05/2019        | 1          |