# DBMS-Lab2

Here's the MySQL syntax for creating the tables based on your provided Oracle SQL syntax:

```sql
-- Create SALESMAN table
CREATE TABLE SALESMAN (
    SALESMAN_ID INT(4),
    NAME VARCHAR(20),
    CITY VARCHAR(20),
    COMMISSION VARCHAR(20),
    PRIMARY KEY (SALESMAN_ID)
);

-- Create CUSTOMER1 table
CREATE TABLE CUSTOMER1 (
    CUSTOMER_ID INT(4),
    CUST_NAME VARCHAR(20),
    CITY VARCHAR(20),
    GRADE INT(3),
    PRIMARY KEY (CUSTOMER_ID),
    SALESMAN_ID INT(4),
    FOREIGN KEY (SALESMAN_ID) REFERENCES SALESMAN (SALESMAN_ID) ON DELETE SET NULL
);

-- Create ORDERS table
CREATE TABLE ORDERS (
    ORD_NO INT(5),
    PURCHASE_AMT DECIMAL(10, 2),
    ORD_DATE DATE,
    PRIMARY KEY (ORD_NO),
    CUSTOMER_ID INT(4),
    SALESMAN_ID INT(4),
    FOREIGN KEY (CUSTOMER_ID) REFERENCES CUSTOMER1 (CUSTOMER_ID) ON DELETE CASCADE,
    FOREIGN KEY (SALESMAN_ID) REFERENCES SALESMAN (SALESMAN_ID) ON DELETE CASCADE
);
```

Here's the MySQL syntax for inserting data into the tables:

```sql
-- Insert data into SALESMAN table
INSERT INTO SALESMAN VALUES (1000, 'JOHN', 'BANGALORE', '25 %');
INSERT INTO SALESMAN VALUES (2000, 'RAVI', 'BANGALORE', '20 %');
INSERT INTO SALESMAN VALUES (3000, 'KUMAR', 'MYSORE', '15 %');
INSERT INTO SALESMAN VALUES (4000, 'SMITH', 'DELHI', '30 %');
INSERT INTO SALESMAN VALUES (5000, 'HARSHA', 'HYDRABAD', '15 %');

-- Insert data into CUSTOMER1 table
INSERT INTO CUSTOMER1 VALUES (10, 'PREETHI', 'BANGALORE', 100, 1000);
INSERT INTO CUSTOMER1 VALUES (11, 'VIVEK', 'MANGALORE', 300, 1000);
INSERT INTO CUSTOMER1 VALUES (12, 'BHASKAR', 'CHENNAI', 400, 2000);
INSERT INTO CUSTOMER1 VALUES (13, 'CHETHAN', 'BANGALORE', 200, 2000);
INSERT INTO CUSTOMER1 VALUES (14, 'MAMATHA', 'BANGALORE', 400, 3000);

-- Insert data into ORDERS table
INSERT INTO ORDERS VALUES (50, 5000, '2017-05-04', 10, 1000);
INSERT INTO ORDERS VALUES (51, 450, '2017-01-20', 10, 2000);
INSERT INTO ORDERS VALUES (52, 1000, '2017-02-24', 13, 2000);
INSERT INTO ORDERS VALUES (53, 3500, '2017-04-13', 14, 3000);
INSERT INTO ORDERS VALUES (54, 550, '2017-03-09', 12, 2000);
```

In MySQL, dates should be formatted as 'YYYY-MM-DD' for insertion. I've adjusted the date format in the INSERT INTO ORDERS statements to comply with this requirement.

Here are the queries converted into MySQL syntax:

1. Count the customers with grades above Bangalore’s average.

```sql
SELECT GRADE, COUNT(DISTINCT CUSTOMER_ID)
FROM CUSTOMER1
GROUP BY GRADE
HAVING GRADE > (SELECT AVG(GRADE) FROM CUSTOMER1 WHERE CITY = 'BANGALORE');
```

2. Find the name and numbers of all salesmen who had more than one customer.

```sql
SELECT SALESMAN_ID, NAME
FROM SALESMAN A
WHERE 1 < (SELECT COUNT(*)
           FROM CUSTOMER1
           WHERE SALESMAN_ID = A.SALESMAN_ID);
```

3. List all salesmen and indicate those who have and don’t have customers in their cities (Use UNION operation).

```sql
SELECT SALESMAN.SALESMAN_ID, NAME, CUST_NAME, COMMISSION
FROM SALESMAN, CUSTOMER1
WHERE SALESMAN.CITY = CUSTOMER1.CITY
UNION
SELECT SALESMAN_ID, NAME, 'NO MATCH', COMMISSION
FROM SALESMAN
WHERE NOT CITY = ANY
(SELECT CITY FROM CUSTOMER1)
ORDER BY 2 DESC;
```

4. Create a view that finds the salesman who has the customer with the highest order of a day.

```sql
CREATE VIEW ELITSALESMAN AS
SELECT B.ORD_DATE, A.SALESMAN_ID, A.NAME
FROM SALESMAN A, ORDERS B
WHERE A.SALESMAN_ID = B.SALESMAN_ID
AND B.PURCHASE_AMT = (SELECT MAX(PURCHASE_AMT)
                      FROM ORDERS C
                      WHERE C.ORD_DATE = B.ORD_DATE);
```

5. Demonstrate the DELETE operation by removing salesman with id 1000. All his orders must also be deleted.

```sql
DELETE FROM SALESMAN
WHERE SALESMAN_ID = 1000;
```

For the deletion operation to cascade to child tables, the foreign key definitions in the child tables (ORDERS and CUSTOMER1) should have been defined with ON DELETE CASCADE.
