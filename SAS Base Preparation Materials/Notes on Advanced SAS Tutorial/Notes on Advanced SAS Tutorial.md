# Notes on SAS Tutorials

Following notes summarize the content of [this](https://jpsmonline.umd.edu/SASOnlineTutor/sot12/en/60477/paths.htm) and [this](https://www.tutorialspoint.com/sas/index.htm).

[TOC]

## A) Notes on Advanced SAS Tutorial

In this chapter I will take notes on the first tutorial (advanced level)

### 1. SQL Processing with SAS

#### 1.1. Performing queries using PROC SQL

[Goto TOC](#Notes on Advanced SAS Tutorial)

*PROC SQL* is an implementation of SQL.

*PROC SQL* uses following vocabulary:

| Data Processing | SAS         | SQL    |
| --------------- | ----------- | ------ |
| file            | SAS Dataset | table  |
| record          | observation | row    |
| field           | variable    | column |

 Example of typical *PROC SQL* query:

```SAS
proc sql;
	select empid, jobcode, salary,
		salary*.06 as bonus
	from sasuser.payrollmaster
	where salara < 32000
	order by jobcode
```

 

General structure of a *PROC SQL* query:

```SAS
proc sql;
	select column1, column2,...
	from table1, table2
	<where expressions>
    <group by> column1, column2,...
    <having expression>
    <Order by> column1, column2
	
```

To stop a running query use *quit;*

A *PROC SQL* query can have several outputs

| Type of output | PROC SQL statement      |
| -------------- | ----------------------- |
| table          | CREATE TABLE and SELECT |
| report         | SELECT                  |
| PROC SQL VIEW  | CREATE VIEW and SELECt  |

There are several built-in *Summary Functions*:

| Function Names | Description                                                  |
| -------------- | ------------------------------------------------------------ |
| AVG, MEAN      | mean/average of values                                       |
| COUNT, FREQ, N | number of nonmissing values                                  |
| CSS            | corrected sum of squares                                     |
| CV             | coefficient of variation (percent)                           |
| MAX            | largest value                                                |
| MIN            | smallest value                                               |
| NMISS          | number of missing values                                     |
| PRT            | probability of a greater absolute value                      |
| RANGE          | range of values                                              |
| STD            | standard deviation                                           |
| STDERR         | standard error of the mean                                   |
| SUM            | sum of values                                                |
| T              | students t value for testing the hypothesis that the population mean is zero |
| USS            | uncorrected sum of squares                                   |
| VAR            | variance                                                     |

The general from of creating a table is

```SAS
PROC SQL;
CREATE TABLE table_name AS 
	SELECT column1, column2,...
	FROM table1|View1,...
	WHERE expression
	GROUP BY columnd1,...
	ORDER BY column1...
```



#### 1.2. Performing Advanced queries

[Goto TOC](#Notes on Advanced SAS Tutorial)

There is a *Feedback* mode for queries in SAS:

```SAS
proc sql feedback;
	select *
	from sasuser.staffchanges
```

In thise mode SAS writes to a log which helps debugging statements.

There is a statement for *limiting* number of output rows:

```SAS
proc sql outobs = 10;
	select *
	from tablebla
```

You can use *labels*, *formats*, *titles* and *constant text* for displaying your result of queries better:

```SAS
proc sql outobs=15;
     title 'Current Bonus Information';
     title2 'Employees with Salaries > $75,000';
        select empid label='Employee ID',
               jobcode label='Job Code',
               salary,
               'bonus is:',
               salary * .10 format=dollar12.2
           from sasuser.payrollmaster
           where salary>75000
           order by salary desc;
```

You can have nested queries:

Example 1:

```SAS
proc sql;
        select jobcode, 
               avg(salary) as AvgSalary 
               format=dollar11.2
           from sasuser.payrollmaster
           group by jobcode
           having avg(salary) >
              (select avg(salary)
                 from sasuser.payrollmaster);
```

Example 2:

```SAS
proc sql;
   select flightnumber, date, delay
      from sasuser.flightdelays
      where destinationtype='International' and delay > all
         (select delay
            from sasuser.flightdelays
            where destinationtype='Domestic');
```

Example 3:

```SAS
proc sql;
title 'Flight Assignments for Level 3 Pilots';
   select empid, date, flightnumber, 
          destination
      from sasuser.flightschedule
      where empid in
         (select empid
            from sasuser.payrollmaster
            where payrollmaster.empid=
                  flightschedule.empid
                  and jobcode='PT3')
      order by empid, date, flightnumber;
```

Example 4:

```SAS
proc sql;
        select lastname, firstname
           from sasuser.flightattendants
           where not exists
              (select *
                 from sasuser.flightschedule
		         where flightattendants.empid=
		               flightschedule.empid);
```

You can validate the syntax of your query without running it by using *noexec* or *validate*

Example *noexec*:

```SAS
proc sql noexec;
        select empid, jobcode, salary
           from sasuser.payrollmaster
           where jobcode contains 'NA'
           order by salary;
```

Example *validate*:

```SAS
proc sql;
        validate
        select empid, jobcode, salary
           from sasuser.payrollmaster
           where jobcode contains 'NA'
           order by salary;
```

#### 1.3 Combining tables horizontally using PROC SQL

*PROC SQL* has different methods on combining tables horizontally:

Cartesian product (all possible combinations of rows in two tables):

```SAS
proc sql;
   select *
      from sales, 
      staffing;
```

Inner Joins (combinations of row of two tables where on share attribute is matching):

``` SAS
proc sql;
   select *
      from one, two
      where one.x = two.x;
```

One can eliminate duplicate columns when joining:

```SAS
proc sql;
        select one.*, b
           from one, two
           where one.x = two.x;

```

To make statements shorter and more readable one can use *Table aliases*:

```SAS
proc sql;
     title 'Employee Names and Job Codes';
        select s.empid, lastname, firstname, jobcode
           from sasuser.staffmaster as s, 
                sasuser.payrollmaster as p
           where s.empid=p.empid;
```

A third, this time not optional, Use Case of *Table aliases* are the following:

- a table is joined to itself:

```SAS 
from airline.staffmaster as s1, 
	  airline.staffmaster as s2
```



- One needs to reference columns from the same table in different libraries:

```SAS
from airline.flightdelays as af, 
     work.flightdelays as wf
where af.delay > wf.delay
```

The former join operations have been relatively simple. Let`s look at the general Grammar for queries with inner join statements to get an overview where these queries can get more complicated

```sql
SELECT column-1<,...column-n> 
       FROM table-1 | view-1, table-2 | view-2<,...table-n | view-n> 
       WHERE join-condition(s) 
              <AND other subsetting condition(s)> 
       <other clauses>;
```

Examples of more complicated inner join queries:

1. ```SAS
   proc sql outobs=15;
        title 'New York Employees';
           select substr(firstname,1,1) || '. ' || lastname 
                  as Name,
                  jobcode,
                  int((today() - dateofbirth)/365.25)
                  as Age
              from sasuser.payrollmaster as p,
                   sasuser.staffmaster as s
              where p.empid =
                    s.empid
                    and state='NY'
              order by 2, 3;
   ```

2. ```SAS
   proc sql outobs=15;
        title 'Avg Age of New York Employees';
           select jobcode,
                  count(p.empid) as Employees,
                  avg(int((today() - dateofbirth)/365.25))
                  format=4.1 as AvgAge
              from sasuser.payrollmaster as p,
                   sasuser.staffmaster as s
              where p.empid =
                    s.empid
                    and state='NY'
              group by jobcode
              order by jobcode;
   ```




Introducing Outer Joins (In Addition to a inner join, rows that do not match if one or both tables are added).

The general statement pattern is 

```SQL
SELECT column-1<,...column-n> 
       FROM table-1 | view-1 
                 LEFT JOIN | RIGHT JOIN | FULL JOIN 
                 table-2 | view-2 
                 ON join-condition(s) 
       <other clauses>;
```

Example (right join):

```SAS
proc sql;
   select *
      from one 
      right join 
      two
      on one.x=two.x;
```

Example (full join)

```SAS
proc sql;
   select *
      from one 
      full join
      two
      on one.x=two.x;
```



#### 1.4 Combining Tables Vertically

[Goto TOC](#Notes on Advanced SAS Tutorial)

Let`s start with the general pattern for combining tables vertically by using *set operations*:

```SAS
SELECT column-1<, ... column-n>
      FROM table-1 | view-1<, ... table-n | view-n>
      <optional query clauses>
set-operator <ALL> <CORR>
SELECT column-1<, ... column-n>
      FROM table-1 | view-1<, ... table-n | view-n>
      <optional query clauses>;
```

where

> - **SELECT** specifies the column(s) that will appear in the result
> - **FROM** specifies the table(s) or view(s) to be queried
> - *optional query clauses* are used to refine the query further and include the clauses WHERE, GROUP BY, HAVING, and ORDER BY
> - the *set-operator* is one of the following: **EXCEPT** | **INTERSECT** | **UNION** | **OUTER UNION**
> - the optional keywords **ALL** and **CORR** (**CORRESPONDING**) further modify the set operation.

#### 1.5 Creating and Managing Tables using PROC SQL

[Goto TOC](#Notes on Advanced SAS Tutorial)

This section will deal with creating, modifying and droping(deleting) tables quickly and efficiently

Let`s start by looking at different ways to create new tables

Creating:

Example 1: (empty table by defining columns)

```sas
proc sql;
   create table work.discount 
          (Destination char(3),
          BeginDate num Format=date9.,
          EndDate num format=date9.,
          Discount num);
```

Example 2: (empty table like an existing table)

```sas
proc sql;
   create table work.flightdelays2 
      like sasuser.flightdelays;
```

Example 3: (create populated table from query result)

```sas
proc sql;
   create table work.ticketagents as
      select lastname, firstname,
             jobcode, salary
         from sasuser.payrollmaster,
              sasuser.staffmaster
         where payrollmaster.empid
               = staffmaster.empid
         and jobcode contains 'TA';
```

When defining new columns one has to specify the corresponding data type. Following data types are supported:

| Specified Data Type | Example                       | SAS Data Type                             |
| ------------------- | ----------------------------- | ----------------------------------------- |
| CHARACTER (CHAR)    | ‘C’, ‘Char’ (fixed length)    | CHARACTER                                 |
| VARCHAR             | ‘C’, ‘Char’ (variable length) | CHARACTER                                 |
| NUMERIC (NUM)       | 0.34                          | NUMERIC                                   |
| DECIMAL             | 0.34                          | NUMERIC                                   |
| FLOAT               | 0.34                          | NUMERIC                                   |
| REAL                | 0.43                          | NUMERIC                                   |
| DOUBLE PRECISION    | 0.56                          | NUMERIC                                   |
| INTEGER (INT)       | 5                             | NUMERIC                                   |
| SMALLINT            | 6                             | NUMERIC                                   |
| DATE                | 02.07.2019                    | NUMERIC with a Date.7 informat and format |

Sometimes it is helpful display the structure of a table into the SAS log. For this you can use

Example: (Describe)

```SAS
 proc sql;
        describe table tabl.name;
```



When creating a table like another table but likes to leave out some columns then one can do this:

```SAS
proc sql;
        create table work.flightdelays3
               (drop=delaycategory destinationtype)
           like sasuser.flightdelays;
```

By Creating a table from a query result one can easily copy table

Example: (Copying by creating from query)

```SAS
proc sql;
        create table work.supervisors2 as
           select *
              from sasuser.supervisors;
```

Inserting single rows can be archived by

Example: (Inserting rows)

```sas
proc sql;
   insert into work.discount
      set destination='LHR',
          begindate='01MAR2000'd,
          enddate='05MAR2000'd,
          discount=.33
      set destination='CPH',
          begindate='03MAR2000'd,
          enddate='10MAR2000'd,
          discount=.15;
         
proc sql;
   insert into work.discount (destination, 
          begindate,enddate,discount)
      values ('LHR','01MAR2000'd, 
             '05MAR2000'd,.33)
      values ('CPH','03MAR2000'd,
             '10MAR2000'd,.15);
      
proc sql; 
   insert into payrollchanges2
      select empid,salary,dateofhire
         from sasuser.payrollmaster
         where empid in ('1919','1350','1401');
```



SQL PROC provides integrity constraints for tables. Let’s look at examples:

| Constraint Type | Action                                                       |
| --------------- | ------------------------------------------------------------ |
| CHECK           | Checks if column entries lie in a specified range of values  |
| NOT NULL        | Guarantees non-missing values in each row                    |
| UNIQUE          | Enforces uniqueness for the values of a column               |
| PRIMARY KEY     | For each row in the table the value of this columns specifies the row uniquely |
| FOREIGN KEY     |                                                              |

See these constrains in action:

Examples:

```sas
proc sql;
        create table work.employees
           (ID char (5) primary key,
           Name char(10),
           Gender char(1) not null check(gender in ('M','F')),
           HDate date label='Hire Date');
           
proc sql;
        create table work.discount3 
               (Destination char(3),
               BeginDate num Format=date9.,
               EndDate num format=date9.,
               Discount num,
               constraint ok_discount check (discount le .5),
               constraint notnull_dest not null(destination));
               
proc sql;
   create table work.departments
          (Dept char(15),
          Code num label='Dept Code',
          constraint code_nodupe unique(code));               
```



In contrast to adding new rows one can also update the values of existing rows:

```sas
proc sql;
   update work.payrollmaster_new
      set salary=salary*1.05
      where jobcode like '__1';
      
proc sql;
   update work.payrollmaster_new 
      set salary=salary*
         case when substr(jobcode,3,1)='1'
                 then 1.05
              when substr(jobcode,3,1)='2'
                 then 1.10
              when substr(jobcode,3,1)='3'
                 then 1.15
              else 1.08
         end;    
         
proc sql;
   update work.payrollmaster_new
      set salary=salary*1.05
         where substr(jobcode,3,1)='1';
   update work.payrollmaster_new
      set salary=salary*1.10
         where substr(jobcode,3,1)='2';
   update work.payrollmaster_new
      set salary=salary*1.15
         where substr(jobcode,3,1)='3';      
         
update work.insure_new
           set pctinsured=pctinsured*
              case 
                 when company='ACME' 
                      then 1.10
                 when company='RELIABLE' 
                      then 1.15
                 when company='HOMELIFE' 
                      then 1.25 
                 else 1 
              end;         
```



After Creating and Updating we now want to delete rows:

```sas
proc sql;
        delete from work.frequentflyers2
           where pointsearned-pointsused <= 0;
```



Next up is adding, dropping and modifiying of columns (in oposition to rows above)

```sas
proc sql;
        alter table work.payrollmaster4
           add Bonus num format=comma10.2,
               Level char(3);
               
 proc sql;
        alter table work.payrollmaster4
           drop bonus, level; 
           
proc sql;
        alter table work.payrollmaster4
           modify salary format=dollar11.2 label="Salary Amt";
           
proc sql;
        alter table work.payrollmaster4
           add Age num 
           modify dateofhire date format=mmddyy10.
           drop dateofbirth, gender;           
```



#### 1.6 Creating and Managing Indexes Using PROC SQL

[Goto TOC](#Notes on Advanced SAS Tutorial)

What is an *index* ? A.: An **index** is an auxiliary file that stores the physical location of values for one or more specified columns (**key columns**) in a table. In an index, each unique value of the key column(s) is paired with a location identifier for the row that contains that value. 

*Indices* can either be *simple* or *composite*:

- A **simple index** is based on one column that you specify. The indexed column can be either character or numeric. When you create a simple index by using PROC SQL, you must specify the name of the indexed column as the name of the index. 
- A **composite index** is based on two or more columns that you specify. The indexed columns can be character, numeric, or a combination of both. In the index, the values of the key columns are concatenated to form a single value 

In identfying queries that can be optimized using indices and how to do it, the following table gives rule of thumbs:

Query performance is optimized when the key column occurs in ... :

- a **Where clause expression** that contains:

  - a comparison operator

  - the TRIM or SUBSTR function

  - the CONTAINS operator

  - the LIKE function

    - Keycolumns: JobCode

    - ```sas
      proc sql;
         select empid, jobcode, salary
            from sasuser.payrollmaster
            where jobcode='FA3'
            order by empid;
      ```

- an **IN subquery**

  - Keycolumns: EmpID

  - ```sas
    proc sql;
       select empid, lastname, firstname, 
              city, state
          from sasuser.staffmaster
          where empid in
             (select empid
                from sasuser.payrollmaster
                where salary>40000);
    ```

- a **correlated subquery**, in which the column being compared with the correlated reference indexed

  - Keycolumns: Supervisors.EmpID

  - ```SAS
    proc sql;
       select lastname, firstname
          from sasuser.staffmaster
          where 'NA'=
             (select jobcategory
                from sasuser.supervisors
                where staffmaster.empid = 
                      supervisors.empid);
    ```

- a **join** in which:

  - the join expression contain the equals (=) operator (equijoin)

  - all the columns in the join expression are indexed in one of the tables being joined

    - Keycolumns: Payrollmaster.EmpID or Staffmaster.EmpID

    - ```sas
      proc sql;
         select *
            from sasuser.payrollmaster as p,
                 sasuser.staffmaster as s
            where p.empid =
                  s.empid
            order by jobcode;
      ```

Following *guidelines* on creating indexes are given in the tutorial:

 - Keep the number of indexes to a minimum to reduce disk storage and update costs.
 - Do not create an index for small tables. Sequential access is faster on small tables.
 - Do not create an index based on columns that have a very small number of distinct values (low cardinality), for example, a `Gender` column that contains only the two values *Male* and *Female*.
 - Use indexes for queries that retrieve a relatively small subset of rows—that is, less than 15%.
 - Do not create more than one index that is based on the same column as the primary key.

How on creates indices the following examples show:

Example:

```sas
proc sql;
        create unique index EmpID
           on work.payrollmaster(empid);
           
proc sql;
        create unique index daily
           on work.marchflights(flightnumber,date);           
```



#### 1.7. Creating and Managing Views Using PROC SQL

[Goto TOC](#Notes on Advanced SAS Tutorial)

A PROC SQL view is a stored query expression that reads data values from its underlying files, which can include SAS data files, DATA step views, other PROC SQL views, or DBMS data. 

Views are useful because they

- often save space (a view is usually quite small compared with the data that it accesses)
- prevent users from continually submitting queries to omit unwanted columns or rows
- ensure that input data sets are always current, because data is derived from tables at execution time
- shield sensitive or confidential columns from users while enabling the same users to view other columns in the same table
- hide complex joins or queries from users.



The following examples show how to create views:

Example:

```SAS
proc sql;
        create view sasuser.faview as
           select lastname, firstname, gender,
                  int((today()-dateofbirth)/365.25) as Age,
                  substr(jobcode,3,1) as Level,
                  salary
              from sasuser.payrollmaster,
                   sasuser.staffmaster
              where jobcode contains 'FA' and 
                    staffmaster.empid=
                    payrollmaster.empid;
```





Using views:

Example:

```SAS
 proc sql;
        select *
           from sasuser.faview;
           
proc tabulate data=sasuser.faview;
        class level; 
        var age; 
        table level*age*mean;
     run;           
```

Guidelines for using views:

- Avoid using an ORDER BY clause in a view definition, which causes the data to be sorted every time the view is executed. Users of the view might differ in how or whether they want the data to be sorted, so it is more efficient to specify an ORDER BY clause in a query that references the view.
- If the same data is used many times in one program or in multiple programs, it is more efficient to create a table rather than a view because the data must be accessed at each view reference. (This table can be a temporary table in the **Work** library.)
- Avoid creating views that are based on tables whose structure might change. A view is no longer valid when it references a nonexistent column.
- If a view resides in the same SAS data library as the contributing table(s), it is best to specify a one-level name in the FROM clause.

#### 1.8. Managing Processing using PROC SQL 

[Goto TOC](#Notes on Advanced SAS Tutorial)

### 2. SAS Macro Language

#### 2.1. Introducing Macro variables

[Goto TOC](#Notes on Advanced SAS Tutorial)



**SAS macro variables** enable you to substitute text in your SAS programs. Macro variables can supply a variety of information, including

- operating system information
- SAS session information
- text strings.

Example

```sas
%let year=2002;
    proc print data=sasuser.schedule;
       where year(begin_date)=&year;
       title "Scheduled Classes for &year";
    run;
    proc means data=sasuser.all sum;
       where year(begin_date)=&year;
       class location;
       var fee;
       title1 "Total Fees for &year Classes";
       title2 "by Training Center";
    run;
```



There are two types of macro variables:

- **automatic macro variables**, which are provided by SAS
- **user-defined macro variables**, whose values you create and define.



Some automatic macro variables have fixed values that are set when SAS is invoked:

| Name     | Value                                                        |
| -------- | ------------------------------------------------------------ |
| SYSDATE  | the date of the SAS invocation (DATE7.)                      |
| SYSDATE9 | the date of the SAS invocation (DATE9.)                      |
| SYSDAY   | the day of the week of the SAS invocation                    |
| SYSTIME  | the time of the SAS invocation                               |
| SYSENV   | FORE (interactive execution) or BACK (noninteractive or batch execution) |
| SYSSCP   | an abbreviation for the operating system that is being used, such as OpenVMS, WIN, HP 300 |
| SYSVER   | the release of SAS that is being used                        |
| SYSJOBID | an identifier for the current SAS session or for the current batch job (the user ID or job name for mainframe systems, the process ID (PID) for other systems) |
| SYSLAST  | Stores the name of the most recently created dataset         |
|          |                                                              |

The simplest way to define your own macro variables is to use a **%LET statement**. The %LET statement enables you to define a macro variable and to assign a value to it.

 ```sas
%let time=afternoon;
 ```

When you use the %LET statement to define macro variables, you should keep in mind the following rules:

- All values are stored as character strings.
- Mathematical expressions are **not** evaluated.
- The case of the value is preserved.
- Quotation marks that enclose literals are stored as part of the value.
- Leading and trailing blanks are removed from the value before the assignment is made. 

Example

```sas
%let site=DALLAS;
    title "REVENUES FOR &site TRAINING CENTER";
    proc tabulate data=sasuser.all(keep=location
                             course_title fee);
       where upcase(location)="&site";
       class course_title;
       var fee;
       table course_title=' ' all='TOTALS',
             fee=' '*(n*f=3. sum*f=dollar10.)
             / rts=30 box='COURSE';
    run;
```



**SAS Processing**:
A SAS program can be any combination of:

- DATA steps and PROC steps
- global statements
- SAS Component Language (SCL) code
- Structured Query Language (SQL) code
- SAS macro language code.

SAS is a compiled language

The **%PUT Statement**:

Another way of verifying the values of macro variables is to write your own messages to the SAS log. The %PUT statement writes text to the SAS log. 

```sas
%put The value of the macro variable CITY is: &city;
```

**The %STR Function**:

```sas
%let prog=%str(data new; x=1; run;);

%let prog=data new%str(;) x=1%str(;)run%str(;);

%let s=%str(;);
    %let prog=data new&s x=1&s run&s;

```

**The %NRSTR Function**:

The %NRSTR function performs the same quoting function as %STR, except it also masks macro triggers (& and %) 

```sas
%let Period=%str(May&Jun);
    %put Period resolves to: .
    %let Period=%nrstr(May&Jun);
    %put Period resolves to: .
```



**Macro Character Functions**:
With macro character functions, you can

- change lowercase letters to uppercase
- produce a substring of a character string
- extract a word from a character string
- determine the length of a character string, and more.

Examples:

```sas
%let a=begin;
    %let b=%nrstr(&a);

    %put UPCASE produces: %upcase(&b);
    %put QUPCASE produces: %qupcase(&b)
    
proc print data=sasuser.schedule;
       where begin_date between
             "01%substr(&sysdate9,3)"d and
             "&sysdate9"d;
       title "All Courses Held So Far This Month";
       title2 "(as of &sysdate9)";
    run;
   
%let a=one;
     %let b=two;
     %let c=%nrstr(&a &b);

     %put C: &c;
     %put With SUBSTR: %substr(&c,1,2);
     %put With QSUBSTR: %qsubstr(&c,1,2);
     
 %let a=a very long value;
     %let b=%index(&a,v);
     %put V appears at position &b.;
     
data work.thisyear;
       set sasuser.schedule;
       where year(begin_date) =
             year("&sysdate9"d);
    run;

    %let libref=%scan(&syslast,1,.);
    %let dsname=%scan(&syslast,2,.);
    proc datasets lib=&libref nolist;
    title "Contents of the Data Set &syslast";
       contents data=&dsname;
    run;
    quit;
    
     %let a=one;
    %let b=two;
    %let c=%nrstr(&a*&b);

    %put C: &c;
    %put With SCAN: %scan(&c,1,*);
    %put With QSCAN: %qscan(&c,1,*);
```

#### 2.2. Processing Macro Variables at Execution Time

[Goto TOC](#Notes on Advanced SAS Tutorial)

**Introduction**

Because the macro facility performs its tasks before SAS programs execute, the information that the macro facility supplies does not depend on values that are accessed or computed during the execution of a SAS program. However, sometimes it is necessary to access or create macro variables during the execution of a SAS program. There are several methods that enable the macro facility to create or access macro variables **at execution time**. In this lesson, you learn to use macro variables during execution of

- a DATA step
- a PROC SQL step
- an SCL program. 

You can use the SYMPUT routine to create a macro variable and to assign to that variable any value that is available in the DATA step. 

Examples:

```sas
options symbolgen pagesize=30;
    %let crsnum=3;
    data revenue;
       set sasuser.all end=final;
       where course_number=&crsnum;
       total+1;
       if paid='Y' then paidup+1;
       if final then do;
          if paidup<total then do;
             call symput('foot','Some Fees Are Unpaid');
          end;
          else do;
             call symput('foot','All Students Have Paid');
          end;
       end;
    run;
 
    proc print data=revenue;
       var student_name student_company paid;
       title "Payment Status for Course &crsnum";
       footnote "&foot";
    run;
```



**The SYMGET Function**

Now, suppose you want to obtain the value of a macro variable **during** DATA step execution. You can obtain a macro variable's value during DATA step execution by using the SYMGET function. The SYMGET function returns the value of an existing macro variable.



#### 2.3. Creating and Using Macro Programs

[Goto TOC](#Notes on Advanced SAS Tutorial)

**Introduction**:

Like macro variables, macro programs (also known as macros) enable you to **substitute text** into your SAS programs. Macros are different from macro variables because they can use conditional logic to make decisions about the text that you substitute into your programs. Using macros can help make your SAS programs more dynamic and reusable. 

**Defining a macro**:

```sas
 %macro prtlast;
       proc print data=&syslast (obs=5);
       title "Listing of &syslast data set";
       run;
    %mend;
   
%macro printdsn(dsn,vars);
        proc print data=&dsn;
           var &vars;
        title "Listing of %upcase(&dsn) data set";
        run;
     %mend;
     
 %macro printdsn(dsn, vars=course_title course_code days);
        proc print data=&dsn;
           var &vars;
        title "Listing of %upcase(&dsn) data set";
        run;
     %mend;
     
 %macro printz/parmbuff;
        %put Syspbuff contains: &syspbuff;
        %let num=1;
        %let dsname=%scan(&syspbuff,&num);
        %do %while(&dsname ne);
           proc print data=sasuser.&dsname;
           run;
           %let num=%eval(&num+1);
           %let dsname=%scan(&syspbuff,&num);
        %end;
     %mend printz;
    
```

**Calling macro**

```sas
%prtlast

proc sort data=sasuser.courses out=courses;
       by course_code;
    run;

    %prtlast
    
    proc sort data=sasuser.schedule out=schedule;
       by begin_date;
    run;

    %prtlast
    
    proc sort data=sasuser.students out=students;
       by student_name;
    run;

    %prtlast
```



#### 2.4. Storing Macro Programs

[Goto TOC](#Notes on Advanced SAS Tutorial)

You might want to store your macros **permanently** so that you can reuse them in later SAS sessions or share them with others. There are several ways of storing your macro programs permanently and of making them accessible during a SAS session. The methods that you will learn in this lesson are

- the %INCLUDE statement
- the autocall macro facility
- permanently stored compiled macros.



```sas
%macro prtlast;
       %if &syslast ne _NULL_ %then %do;
          proc print data=&syslast(obs=5);
             title "Listing of &syslast data set";
          run;
       %end;
       %else
          %put No data set has been created yet.;
    %mend;
    
 %include 'c:\sasfiles\prtlast.sas' /source2;

    proc sort data=sasuser.courses out=bydays;
       by days;
    run;

    %prtlast   
 
```



**The CATALOG Procedure:**

You can also use the CATALOG procedure to list the contents of a SAS catalog. The CONTENTS statement of the CATALOG procedure lists the contents of a catalog in the procedure output.

```sas
proc catalog cat=work.sasmacr;
       contents;
       title "Default Storage of SAS Macros";
    quit;
```





### 3. Advanced SAS Programming Techniques

#### 3.1. Creating Sampling and Indexes

[Goto TOC](#Notes on Advanced SAS Tutorial)

This lesson discusses two types of representative samples:

- **systematic samples**
- **random samples**.

**Creating systematic samples**:

Example

```sas
do pickit=1 to 142 by 10;
        set sasuser.sale2000 point=pickit;
        output;
end;
```

The **Stop statement** is necessary in direct-access read mode to prevent the Data stept from looping continuously.

Example:

```sas
data sasuser.subset;
        do pickit=1 to 142 by 15;
           set sasuser.revenue point=pickit;
           output;
        end;
        stop;
     run;
     
     proc print data=sasuser.subset;
     run;
```

Situation: **unknown number of observations**. Use the **NOBS= variable** option:

```sas
data sasuser.subset;
        do pickit=1 to totobs by 10;
           set sasuser.revenue point=pickit nobs=totobs;
           output;
        end;
        stop;
     run;
```



**Creating random samples**:

Use the **RANUNI**(seed)-function: It generates (pseudo) random numbers between 0 and 1.

````sas
data random2;
        do i=1 to 10 by 1;
           varone=ranuni(10);
           output;
        end;
run;
````

To round a non-integer random to next bigger integer you can use **ceil**-function:

```SAS
ceil(ranuni(0)*50)
```

A full example of **creating a random sample with Replacement**:

```sas
  data work.rsubset (drop=i sampsize);
        sampsize=10;
        do i=1 to sampsize;
           pickit=ceil(ranuni(0)*totobs);
           set sasuser.revenue point=pickit nobs=totobs;
           output;
        end;
        stop;
     run;
     
     proc print data=work.rsubset label;
        title 'A Random Sample with Replacement';
     run;
```

An alternative to sample with Replace ist to sample **without replacement**. It enforces the uniqueness of each observation:

```sas
data work.rsubset(drop=obsleft sampsize);
        sampsize=10;
        obsleft=totobs;
        do while(sampsize>0);
           pickit+1;
           if ranuni(0)<sampsize/obsleft then do;
              set sasuser.revenue point=pickit 
                  nobs=totobs;
              output;
              sampsize=sampsize-1;
           end;
           obsleft=obsleft-1;
        end;
        stop;
     run;

     proc print data=work.rsubset heading=h label;
        title 'A Random Sample without Replacement';
     run;
```



**Using Indexes**:

An **index** can help you quickly locate one or more particular observations that you want to read. An index is an optional file that you can create for a SAS data set in order to specify the location of observations based on values of one or more **key variables**. Indexes can provide direct access to observations in SAS data sets to

- yield faster access to small subsets of observations for WHERE processing
- return observations in sorted order for BY processing
- perform table lookup operationsjoin observations
- modify observations. 

Like in *PROC SQL* case indexes can be either *simple* or *composite*.

Examples:

```sas
data simple (index=(division));
        set sasuser.empdata;
     run;
    
 data simple2 (index=(division empid/unique));
        set sasuser.empdata;
     run;
     
data composite (index=(Empdiv=(division empid)));
        set sasuser.empdata;
     run;
```



**Managing Indexes with PROC DATASETS**

General form, PROC DATASETS to create and delete an index:

> - **PROC DATASETS LIBRARY=***libref* **<NOLIST>;**
> -        **MODIFY** *SAS-data-set-name***;**
> -        **INDEX DELETE** *index-name***;**
> -        **INDEX CREATE** *index-specification***;**
> - 
> - **QUIT;**

where

- *libref* points to the SAS library that contains *SAS-data-set-name*
- the **NOLIST** option suppresses the printing of the directory of SAS files in the SAS log and as ODS output
- *index-name* is the name of an existing index to be deleted
- *index-specification* for a simple index is the name of the key variable
- *index-specification* for a composite index is *index-name=(variable-1...variable-n)*.

Examples:

```sas
proc datasets library=sasuser nolist;
        modify sale2000;
        index create origin;
     quit;
     
proc datasets library=sasuser nolist;
        modify sale2000;
        index delete origin;
        index create flightid;
        index create Fromto=(origin dest);
     quit;
```



#### 3.2. Combining Data Vertically

#### 3.3. Combining Data Horizontally

#### 3.4. Using Look-Up Tables to Match Data

#### 3.5. Formatting Data

#### 3.6. Modifiying SAS Data Sets and Tracking Changes

### 4. Optimizing SAS Programs

#### 4.1. Introduction to Efficient SAS Programming

#### 4.2 Controlling Memory Usage

#### 4.3 Controlling Data Storage Space

#### 4.4 Utilizing Best Practices

#### 4.5 Selecting Efficient Sorting Strategies

#### 4.6 Querying Data Efficiently

## B) Notes on SAS Basics Tutorial

### Overview

### Environment

### User Interface

### Programm structure

The **DATA** step: 

General syntax:

```SAS
DATA data_set_name;		#Name the data set.
INPUT var1,var2,var3;   #Define the variables in this data set.
NEW_VAR;			    #Create new variables.
LABEL;			      	#Assign labels to variables.
DATALINES;		      	#Enter the data.
RUN;
```

Example:

```sas
DATA TEMP;
INPUT ID $ NAME $ SALARY DEPARTMENT $;
comm = SALARY*0.25;
LABEL ID = 'Employee ID' comm = 'COMMISION';
DATALINES;
1 Rick 623.3 IT
2 Dan 515.2 Operations
3 Michelle 611 IT
4 Ryan 729 HR
5 Gary 843.25 Finance
6 Nina 578 IT
7 Simon 632.8 Operations
8 Guru 722.5 Finance
;
RUN;
```



The **Proc** step:

General syntax

```SAS
PROC procedure_name options; #The name of the proc.
RUN;
```

Example

```sas
PROC MEANS;
RUN;
```



The **Output** step:

General syntax

```sas
PROC PRINT DATA = data_set;
OPTIONS;
RUN;
```

Example:

```sas
PROC PRINT DATA=TEMP;
WHERE SALARY > 700;
RUN;
```



### Basic syntax

The syntax of SAS is built up on three components

1. SAS Statements
   1. Can start and end anywhere
   2. Are ended by a semicolon
   3. Many statemens ending with a semicolon can be on the same line
   4. Spaces can be used to separate components of a statement
   5. Every SAS program must end with a RUN statement
2. SAS Variables
   1. Represent columns of a data set
   2. Names of variables:
      1. Can be 32 chars long max
      2. Cannot include blanks
      3. Must start with a letter (not cas sensitive) or an underscore _
      4. Can include numbers but not as first char
      5. Variable names are not case sensitive
3. SAS Data Set
   1. The **DATA** statement marks the creation of a new SAS data set
   2. A single word after a DATA statement indicates a temporary data set. This means that the data set gets erased at the end of the sesssion
   3. The data set name can be prefix with a library name to make it permanent.
   4. If no name is supplied SAS creates a temporary data set with default names DATA1, DATA2 ect.

### Data Sets

tba

### Variables

Variables in SAS can be of three type:

- Numeric Variables
- Character Variables
- Date Variables



### Strings

### Arrays

### Numeric Formats

### Operators

### Loops

### Decision making

### Functions

### Input Methods

### Macros

### Dates & Times







