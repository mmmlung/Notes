# Preparing for SAS Base Programming for SAS 9 Exam

[TOC]



## Accessing Data. (Check)

### Use FORMATTED and LIST input to read raw data files.(Check)

- [x] Distinguish between character and numeric data, between standard and nonstandard numeric data:
  - [x] Character data contains a character or a sequence of characters. These characters can be letter, numbers or symbols. 
  - [x] A numeric data value represents numbers. A numerica data value can contain characters like “.”.  Numbers in scientific notation are also considered a numerica data type. 
  - [x] Standard data are either character or numerica data which can be read by list, column, named or formatted input. Numeric data value containing a “.” or leading “-” are considered to be standard.
  - [x] Nonstandard data type include numeric values that contain nonnumeric values like “$”, dates and times, packed decimal or binary.
- [x] Use INPUT statement with formatted input to read character, standard and nonstandard fixed-field data: INPUT variable format;
  - [x] See Section 1.1 in jupyter notebook “Preparing for SAS Base Programmer Certification”
- [x] Use INPUT statement with list input to read character, standard and nonstandard free-format data: INPUT variable <$>
  - [x] See Section 1.1 in jupyter notebook “Preparing for SAS Base Programmer Certification”

### Use INFILE statement options to control processing when reading raw data files. (Check)

- [x] Use the INFILE statement DLM= option to specify field delimiters.
- [x] Use the INFILE statement PAD= option to reading variable-length records
- [x] Use the INFILE statement DSD= option to read data when missing values occur
- [x] Use the INFILE statement option, FLOWOVER, MISSOVER, TRUNCOVER and STOPOVER, as appropriate to control reading records

### Use various components of an INPUT statement to process raw data files including column and line pointer controls, and trailing @controls. (Check)

- [x] Use pointer controls
- [x] Use line controls
- [x] Use trailing @ and double trailing @@.



### Combine SAS data sets. (Check)

- [x] Concatenate and interleave data sets.

  - [x] ```sas
    *concatenates data sets one and two into three;
    data three;
    set one two;
    
    *interleaves data sets one and two into three by year
    data three;
    set one two;
    by year;
    
    
    ```

- [x] Merge data sets one-to-one

  - [x] ```sas
    *merges data sets one and two into three
    *merging here means take two observations and produce one new
    data three;
    merge one two;
    ```

    

- [x] Merge data sets one-to-many

  - [x] ```sas
    data three;
    merge one two;
    by common;
    ```



### Access an Excel workbook (Check)

- [x] Use the SAS/ACCESS EXCEL or PICFILES engine to read an .xlsx file OR use the SAS/ACCESS XLSX engine to read an .xlsx file.
  - [x] See 2.18 in “The little SAS book”.



## Creating Data Structures (Check)

### Create temporary and permanent SAS data sets.(Check)

- [x] Use a DATA step to create a SAS data set from a text file. Example:
  - [x] DATA NewData; Infile “path.rawdata”; Input < pointer-control > variable format; run;
- [x] Use DATA step to create a SAS data set from an existing SAS data set. Example:
  - [x] DATA Mylib.NewData; Set Mylib.OldData; <other SAS statements>;run;

### Create and manipulate SAS date values. (Check)

- [x] Explain how SAS stores date and time values
- [x] Use SAS informats to read common date and time expressions
- [x] Explain the YearCutOff system option as used to interpret two digit year values
- [x] Use SAS date and time formats to specify how the values are displayed

### Export data to create standard and comma-delimited raw data files. (Check)

- [x] Create a simple text file using the DATA step with the file and put statements
- [x] Create a simple raw data file by using EXPORT procedure as an alternative to the DATA step
- [x] Use the DSD option to create a comma-delimited file that writes out values separated by a delimiter

### Control which observations and variables in a SAS data set are processed and output. (Check)

- [x]  Use the WHERE statement in the DATA step to select observations to be processed
- [x] Subset variables to be output by using the DROP and KEEP statements
- [x] Use the DROP= and KEEP= data set options to specify columns to be processed and/or output

## Managing Data. (Check)

### Investigate SAS data libraries using base SAS utility procedures (Check)

- [x] Use LIBNAME statement to assign a library reference name to a SAS library
- [x] Investigate a library programmatically using the CONTENTS and DATASET procedures

### Sort observations in a SAS data set (Check)

- [x] Use the SORT Procedure to re-order observations in place or output to an new dataset

### Conditionally execute SAS statements (Check)

- [x] Use IF-THEN/ELSE statements to process data conditionally.
- [x] Use DO and END statements to execute multiple statement conditionally.
- [x] Use SELECT/WHEN statements to process data conditionally.

### Use assigment statements in the DATA step (Check)

- [x] Create new variables and assign a value
- [x] Use LABEL and FORMAT statements to modify attributes in a DATA step
- [x] Define the lenght of a variables using the LENGTH statement.

### Accumulate sub-totals and totals using DATA step statements (Check)

- [x] Use the BY statement to aggregate by subgroups

### Use SAS functions to manipulate character data, numeric data and SAS date values (Check)

- [x] Use SAS functions such as SCAN, SUBSTR, TRIM and LOWCASE to perform tasks such as
  - [x] Replace the content of a character value.
  - [x] Trim trailing blanks from a character value
  - [x] Search a character value and extract a portion of the value
  - [x] Convert a character value to uppper and lowercase
- [x] Use SAS arithmetic, financial and probability functions to create or modify numeric values by using the INT or ROUND functions
- [x] Create SAS date values by using the functions MDY, TODAY, DATE AND TIME.
- [x] Extrat the month, year and interval from a SAS date value by using functions YEAR, QTR, MONTH and DAY:
- [x] Perform calculations with date and datetime values and time intervals by using the functions INTCK, INTNX, DATDIF and YRDIF.

### Use SAS functions to convert character data to numeric and vice versa. (Check)

- [x] Explain the automatic conversion that SAS uses to convert values between data types.
- [x] Use the INPUT function to explicitly convert character data values to numeric values.

### Process data using DO LOOPS (Check)

- [x] Explain how to iterative DO loops function.
- [x] Use DO loops to eliminate redundant code and to perform repetitive calculations.
- [x] Use conditional DO loops.
- [x] Use nested DO loops.

### Process data using one-dimensional SAS arrays. (Check) 

- [x] Explain the conepts of SAS arrays.
- [x] Use SAS arrays to perform repetetive calculations.
- [x] Define the elements in an array using the ARRAY statement
- [x] Use a DO loop to process an array.

### Validate and clean data. (Check)

- [x] Use PROC FREQ to list unique values, with the nlevel option to show the number of distinct values, with the order=freq to check for duplicate or missing values.
- [x] Use PROC PRINT with the WHERE statement to display observations with invalid values.
- [x] Use PROC MEAN to validate the range fo numeric variables
- [x] Use PROC UNIVARIATE to display extreme observations and missing values and with the ID statement to display the value of identifying variables(s).

## Generating Reports (check)

### Generate list reports using the PRINT procedure. (check)

- [x] Modify the default behaviour of PROC PRINT by adding statements and options such as
  - [x] use the VAR statement to select and order variables.
  - [x] calculate totals with a SUM statement.
  - [x] select observations with a WHERE statement.
  - [x] use the ID statement to identify observations.
  - [x] use the BY statement to process groups.

### Generate summary reports and frequency tables using base SAS procedures. (check)

- [x] Produce one-way and two-way frequency tables with FREQ procedure.
- [x] Enhance frequency tables with options.
- [x] Use PROC FREQ to validate data in a SAS data set
- [x] Calculate summary statistics and multilevel summaries using the MEANS procedure
- [x] Enhance summary tables with options
- [x] Identify extreme and missing values with the UNIVARIATE procedure

### Enhance reports through the use of user-defined formats, titles, footnotes and SAS reporting (check)

- [x] Use the LABEL statement to define descriptive column headings
- [x] Control the use of column headings with the LABEL and SPLIT= options in Proc Print output

### Generate reports using ODS statements (check)

- [x] Identify the Output Delivery System destinations.
- [x] Create HTML, PDF, RTF and LISTING files with the ODS statements.
- [x] Use the STYLE= option to specify a style template.
- [x] Create files that can be viewed in Microsoft Excel.



## Handling errors (check)

### Identify and resolve programming logic errors. (check)

- [x] Use the PUTLOG Statement in the Data step to help identify logic errors
- [x] Use PUTLOG to write the value of a variable, formatted values, or to write values of all variables.
- [x] Use PUTLOG with Conditional logic.
- [x] Use temporary variables N and ERROR to debug a DATA step.

### Recognize and correct syntax errors. (check)

- [x] Identify the characteristics of SAS statements
- [x] Define SAS syntax rules including typical types of syntax errors such as misspelled keywords, unmatchted quotation marks, missing semicolons, and invalid options.
- [x] Use the log to help diagnose syntax errors in a given programm.

### Examine and resolve data errors. (check)

- [x] Use the SAS system option, ERRORS=n, to specify the maximum number of observations for which SAS issues complete error messages written to the log.
- [x] Given a SAS program, use the log to determine the reason for a data error.





















