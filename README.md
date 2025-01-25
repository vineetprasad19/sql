# sql
                             **Important concepts of Oracle SQL that every Data Engineer/Database Admin applying for Realational database should know. **

**Indexes**
UNIQUE: ensures all values in the columns are unique.
B-TREE INDX: ideal for range-based queries (lastname, dates)
BITMAP: for key values and rowids. Ideal for columns with low cardinality columns. It’s a space-efficient index. (gender or marital_status)
COMPOSITE: COMBINING MULTIPLE COLUMNS INTO SINGLE INDX. Reduce the needs for multiple indexes.
Function-based, Reverse key, invisible, LOCAL, Global

**TRIGGERS** 
are stored PL/SQL blocks that execute automatically in response to specific events on a table, view, or database. 
DML Triggers: Fired on INSERT, UPDATE, or DELETE operations. Can be row-level or statement-level.
DDL Triggers: Fired on DDL events like CREATE, ALTER, or DROP.
Database Triggers: Fired on database-wide events like user login, logout, or system startup/shutdown. Like to Track user logins.
pseudo-records in row-level triggers?
:OLD (holds the previous values of the row) and :NEW (holds the new values of the row).
Level of triggers on row-level and statement-level triggers are BEFORE, AFTER, INSTEAD OF.

**RANK** Assigns rank with gaps after ties.1, 1, 3, 4 
**DENSE_RANK** Assigns rank without gaps after ties.1, 1, 2, 3 

**Joins**
Full outer join:  
The full outer join returns all matching rows from both tables and placing NULLs in the places where no matching row exists.
Cartesian Join
A CROSS JOIN produces a cartesian product between the two tables, returning all possible combinations of all rows. It has no ON clause because we're just joining everything to everything.

Table 1 contains 10 rows and Table 2 contains 20 rows with 5 rows matching on specific columns.

Then CROSS JOIN will return 10*20=200 rows in result set.
FULL OUTER JOIN will return 25 rows in result set. 10 matching and 15 null records.
INNER JOIN will return matching rows, hence, 5 rows in result set.

HASH Join: Used for Large datasets without indexes and for equi join. It scales the performance. Not dependent on indexes, but memory usage is high. 
Nested Loop Join: Used for Small datasets or indexed join columns. Works on Any type of join condition. Performamnce Degrades with large datasets. Performs best with indexes on the inner table and uses Minimal memory usage.

**User-defined Function:** atleast 1 RETURN statement Wt/ot size should be used in proc. Retrun a single value.
**Function** Can be invoked via expression, parameter, bind/host variable, or part of SELECT query.
create or replace function get_full_name
(p_emp_id number)
return varchar2
is v_full_name varchar2(200);
begin
  select first_name||' '||last_name into v_full_name   from employees
  where employee_id=p_emp_id;
  return v_full_name;
end; 

**CREATE RECURSIVE VIEW**
subordinates(emp_id, manager_id, emp_name, level) AS
(
  SELECT emp_id, manager_id, emp_name, 0 AS level
  FROM emp
  WHERE manager_id IS NULL
  UNION ALL
  SELECT e.emp_id, e.manager_id, e.emp_name, s.level + 1
  FROM emp e
  INNER JOIN subordinates s ON s.emp_id = e.manager_id
);

**CURSOR** is a POINTER used to retrieve and manipulate query result sets row by row. 
Implicit and Explicit Cursors. 

**Explicit Cursor:** Predefined, static query structure.
**Ref Cursor:** It allows dynamic queries and is commonly used to pass query results between procedures and functions. It can be opened many times with different query.

Declare,Open,FETCH,Close
cursor attributes in PL/SQL are %FOUND, %NOTFOUND, %ROWCOUNT, and %ISOPEN

**Oracle Hints**
HINTS: hints are useful when you want to override the default execution plan and optimize query performance for specific scenarios. 
Parallel, No_Parallel, append, Hash, Ordered, USE_NL, PARTITION ,ALL_ROWS: Optimizes for throughput., RESULT_CACHE for function (stored in a SGA shared global area.)
Access Path Hints:
Specify how Oracle should access the data.
INDEX(table_name index_name): Forces the use of a specific index.
FULL(table_name): Forces a full table scan.

**DYNAMIC SQL:**
is to create a SQL statement whose structure may change during run time, we need this when SQL is unknown at compile time. It is executed via Execute immediate or the DBMS_SQL package at executes at run time and not at compile time.

DDL (Create, alter, drop, truncate table) and DCL (Grant and Revoke) are not allowed inside PLSQL block, but it allowed using execute immediate statement

**transaction** 
in PL/SQL is a series of SQL statements that are executed as a unit of work. The transaction guarantees that either all changes made by these statements are successfully saved (committed) or, in the event of a failure, none of the changes are applied (rolled back).

**savepoint** 
is a marker set within a transaction that allows you to roll back part of the transaction to that specific point without affecting the entire transaction.

**%Type:** 
This datatype is used for specified tables to define the variable as its column name datatype.

**%ROWTYPE** 
if the programmer doesn’t know the datatype of the specified column but still needs to assign it to the variable. It is used to declare a variable that has the structure of the records in a table.

**Types of LOOPS:**
Simple Loop: executes a block of code repeatedly until explicitly terminated with an EXIT statement. 
WHILE Loop: executes a block of code as long as a specified condition evaluates to TRUE. 
FOR Loop: executes a block of code for a fixed number of iterations, iterating over a specified range of values. 
Nested Loops: are loops within loops, used when operations involve multiple levels of repetition. 
Cursor Loops: are used to iterate through the result set of a query. 

**Loops: For, while, nested**

DECLARE
  counter NUMBER := 1;
BEGIN
  LOOP
    DBMS_OUTPUT.PUT_LINE('This is iteration number ' || counter);
    IF counter = 3 THEN
      EXIT;
    END IF;
    counter := counter + 1;
  END LOOP;
END;

SET SERVEROUTPUT ON;
DECLARE
counter NUMBER;
BEGIN
DBMS_OUTPUT.PUT_LINE('FOR LOOP WITH REVERSE KEYWORD');
FOR counter IN REVERSE 1..5 LOOP
    DBMS_OUTPUT.PUT_LINE('REVERSE VALUE: '|| counter);
END LOOP;
END;

**For Loop Cursor:** 
No need to open, fetch and Close cursor as oracle will do it for us.

Declare
 CURSOR c1 is select cl, cl2 from tb1 where dpt=1;
BEGIN
for i in c1
loop
  dbms_output.put_line(i.cl||''||i.cl2);
end loop
end;

**Remove duplicates:** 
ROW_NUMBER() function assigns a unique number to each row within a partition. we can delete rows where this number is greater than 1. 
A subquery can help identify duplicate rows based on a unique column combination. Using Min and MAX funtion.

**ROWSET**
is a collection of rows retrieved from a database query where each row represents a single record and columns represent fields or attributes of that record. Rowsets are often used in Fetching query results ,Iterating through rows , AND Binding data to user interfaces .
Declare Cursor, Begin, Open Cursor, LOOP, fetch, exit, end loop, close cursor, end

**LISTAGG Function:** 
Concatenate employee names from the employees table into a single comma-separated string.
SELECT LISTAGG(first_name, ', ') WITHIN GROUP (ORDER BY first_name) AS employee_names FROM employees;

**composite datatype** 
is a type that can store multiple values together as a single unit. It allows us to group related data into a structured format. There are 2 types.

**PL/SQL Records:** is a collection of fields, where each field can have a different data type.
**PL/SQL Collections:** are groups of elements that can be manipulated as a single unit. They allow storing and handling multiple values of the same data type. 3 types Associative Arrays (Index by tables is a key(Primary)-value pairs), Nested Tables (Unordered collections and use for bulk processing, it needs initialization to use and it can be extened and can be stored in a db), VARRAY's (Ordered collection with max number of elements as its a fixed size array and can be stored in a d and its used for bulk processing)

**Oracle Data Dictionary**
The Oracle Data Dictionary is a collection of read-only tables and views that provide metadata about the database. It stores information about the database schema, objects, users, storage, and performance. 
ALL_TABLES, ALL_OBJECTS, USER_TABLES, USER_INDEXES, V$SESSION, V$SQL, V$LOCK DBA_TABLES, DBA_USERS, DBA_DATA_FILES, etc.

**Oracle SUPPLIED Packages:** 
**DBMS_OUTPUT** (Package enables to send messages via PUT_LINE, GET_LINE, ENBLE/DISABLE), **UTL_FILE** (to read and write OS text file by creating directory), **UTL_MAIL** (to send emails by setting SMTP_OUT_SERVER initilization parameter, by default this package is not installed so need to install then grant),  DBMS_ALERT, DBMS_LOCK, DBMS_SQL, DBMS_SESSION, DBMS_SCEDULER

**CONNECT BY** specifies the relationship between parent rows and child rows of the hierarchy. Both the CONNECT BY condition and the PRIOR expression can take the form of an uncorrelated subquery (Independent of the outer query,  The result of the subquery is used by the outer query, executed once and its fast).

Both **CURRVAL** and **NEXTVAL** are pseudocolumns used to access values from a sequence.

**LEVEL** is a pseudocolumn that returns the ordinal number of the recursive step in the Hierarchic clause that returned the row. For all the rows returned by the START WITH clause, LEVEL return the value 1 .

**Tablespaces**
Logical Storage Layer: Tablespaces abstract the physical details of storage, enabling DBA's to manage storage without needing to deal with individual files directly.
Data File Association: Each tablespace consists of one or more data files on the physical disk. These files store the actual data.
**Flexible Management:** 
Tablespaces can be resized, added, or modified without impacting the objects stored in them.
**Segmentation:** 
Data is organized into segments, extents, and blocks within a tablespace.

**PLSQL VARIABLES:** Scalar (Holds single value and its Char,Data,Number,Boolean),LOB, reference, composite (can use More than 1 datatypes)
Non-PLSQL Variables: BIND (Created in a session or environment. Its also called as host variable and used in sql statement and plsql blocks. It is referenced with a preceding colon)

**Unconformed Dimension:**
Differs in definition or attributes across fact tables or data marts. Is not consistent or shared, often tailored to a specific use case or business process.

**SQL Profiles vs. SQL Plan Baselines**
SQL PROFILES: Enhances optimizer decisions by correcting statistics. It Allows the optimizer to choose from multiple plans. No Plan Locking Optimizer can still generate new plans.Creates Automatically via SQL Tuning Advisor.Stored InData Dictionary (DBA_SQL_PROFILES). 
SQL Plan Baselines: Ensures plan stability by using only pre-approved plans. It Restricts the optimizer to specific plans. .Yes Plan Locking Only stored plans are used. Creates Automatically or manually via cursor cache or SQL Tuning Sets. Stored in SQL Management Base (DBA_SQL_PLAN_BASELINES).
Historical Plans (SQL Plan Management)
The optimizer might store and reuse multiple plans for a single query in specific scenarios, such as SQL plan baselines or manual plan interventions.

**PRAGMA** is a compiler directive that provides instructions to the PL/SQL compiler. Pragmas are not executed at runtime; they are purely for compilation purposes. 
Commonly used pragmas are PRAGMA **EXCEPTION_INIT**(exception_name, error_code);, 
**AUTONOMOUS_TRANSACTION** (Changes made in the block can be committed or rolled back without affecting the parent transaction.), and 
SERIALLY_REUSABLE (useful for stateless packages which means it makes persistent to not persistent for the session - Resets the parameter value.)., 
PRAGMA INLINE(subprogram_name, 'YES' | 'NO'); (Requests the compiler to inline the code of a subprogram, replacing a function or procedure call with the actual code - to optimize performance by reducing call overhead.)

**SCD1:** overwrites old data with new data.
**SCD2:** tracks historical data by creating multiple records for a given natural keY
factless fact table:  does not contain any measurable numerical data (facts). Instead, it is used to capture events or relationships between dimensions, It is the data of what happened? rather than how much? or how many? kind of thing

**Star:** Denormalized with direct dimensions
**Snowflake** : Normalized dimensions, Optimizes storage and handles complex data
**Galaxy**: Multiple fact tables with shared dimensions, use for Multi-process or multi-subject analysis
**Inmon’s CIF**: Normalized warehouse with dimensional marts,
**Kimball’s Approach**: Dimensional marts.
**Columnar Warehousing**: Column-based storage for fast analytics, High-performance analytical workloads.
**Federated Warehouse**: Distributed data systems

**AWR report**
Load Profile: Summarizes workload statistics 
Top Wait Events: Displays the most significant wait events contributing.
SQL Statistics: Highlights most consuming resource.
Instance Efficiency Ratios: Shows key efficiency ratios like 
I/O Statistics: Helps identify potential bottlenecks in storage performance (Details disk and tablespace I/O performance. )
**Advisory Recommendations**
Implement suggestions from Buffer Cache, PGA, or Shared Pool advisors.

**Slicing**: 
>>> a = ['spam', 'egg', 'bacon', 'tomato', 'ham', 'lobster']
>>> a[2:5]
['bacon', 'tomato', 'ham']
If and else
num = 5
if num > 10:
    print("num is totally bigger than 10.")
elif num < 10:
    print("num is smaller than 10.")
else:
    print("num is indeed 10.")
SORT AND REVERSE
>>> li.sort()
>>> li.reverse()
>>> li.count(3)
>>> li = ["re"] * 3 (Repeating)
FUNCTIONS
def hello_world():  
    print('Hello, World!')
***************************************
def swap(x, y):
    return y, x
x = 1
y = 2
x, y = swap(x, y)  # => x = 2, y = 1
***************************************
def add(x, y=10):
    return x + y
add(5)      # => 15
add(5, 20)  # => 25
**Anonymous functions**
# => True
(lambda x: x > 2)(3)

# => 5
(lambda x, y: x ** 2 + y ** 2)(2, 1)
***************************************

Features of **NOSQL Database** (Hbase, Casandra, MongoDB): They have dynamic Schema, Auto Sharding, Replication, no Joins, Integrated caching, easily scalable, highly distributed,

store data with Columnar family and schema as namespace. To select the table use SCAN 'hrdata:employee' table.

sudo service hbase-master-restart
sudo service hbase-regionserver restart

*************************************
In **SPARK** dataframe, import HBASE jar files.
define def catalog_1=s"""{
"table":{"namespace":"hrdata","name":"employee"}
"rowkey":"rowkey",
"columns":{
"rowid":{"columnar_family cf":"rowkey","col"}

val df=spark.read.options(map hbasetablecatalog).load()
*************************************
**SQOOP** 
for moving data between Hadoop (HDFS) and RDBMS database. Sqoop uses MapReduce to import and export the data, which provides parallel operation as well as fault tolerance. The sqoop has two file formats: delimited text file format and sequence file format. Sqoop also includes a primitive SQL execution shell (the sqoop-eval tool).

$ sqoop import --connect jdbc:mysql://database.example.com/employees \
    --username venkatesh --password-file ${user.home}/.password

$ sqoop import \
  --query 'SELECT a.*, b.* FROM a JOIN b on (a.id == b.id) WHERE $CONDITIONS' \
  --split-by a.id --target-dir /user/foo/joinresults

You can also import mainframe records to Sequence, Avro, or Parquet files.


