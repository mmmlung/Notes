# Preparing for SAS Base Programming for SAS 9 Exam

## Accessing Data

### Use FORMATTED and LIST input to read raw data files

- [ ] Distinguish between character and numeric data, between standard and nonstandard numeric data:
  - [ ] A:
- [ ] Use INPUT statement with formatted input to read character, standard and nonstandard fixed-field data: INPUT variable format;
  - [ ] A:
- [ ] Use INPUT statement with list input to read character, standard and nonstandard free-format data: INPUT variable <$>
  - [ ] A:

### Use INFILE statement options to control processing when reading raw data files.

- [ ] Use the INFILE statement DLM= option to specify field delimiters.
- [ ] Use the INFILE statement PAD= option to reading variable-length records
- [ ] Use the INFILE statement DSD= option to read data when missing values occur
- [ ] Use the INFILE statement option, FLOWOVER, MISSOVER, TRUNCOVER and STOPOVER, as appropriate to control reading records

### Use various components of an INPUT statement to process raw data files including column and line pointer controls, and trailing @controls.

- [ ] Use pointer controls
- [ ] Use line controls
- [ ] Use trailing @ and double trailing @@.



### Combine SAS data sets

- [ ] Concatenate and interleave data sets.
- [ ] Merge data sets one-to-one
- [ ] Merge data sets one-to-many



### Access an Excel workbook

- [ ] Use the SAS/ACCESS EXCEL or PICFILES engine to read an .xlsx file OR use the SAS/ACCESS XLSX engine to read an .xlsx file.



## Creating Data Structures

### Create temporary and permanent SAS data sets.

- [ ] Use a DATA step to create a SAS data set from a text file. Example:
  - [ ] DATA NewData; Infile “path.rawdata”; Input < pointer-control > variable format; run;
- [ ] Use DATA step to create a SAS data set from an existing SAS data set. Example:
  - [ ] DATA Mylib.NewData; Set Mylib.OldData; <other SAS statements>;run;

### Create and manipulate SAS date values

- [ ] Explain how SAS stores date and time values
- [ ] Use SAs informats to read common date and time expressions
- [ ] Explain the YearCutOff system option as used to interpret two digit year values
- [ ] Use SAS date and time formats to specify how the values are displayed

### Export data to create standard and comm-delimited raw data files.

- [ ] Create a simple text file using the DATA step with the file and put statements
- [ ] Create a simple raw data file by using EXPORT procedure as an alternative to the DATA step
- [ ] Use the DSD option to create a comma-delimited file that writes out values separated by a delimiter

### Control which observations and variables in a SAS data set are processed and output

- [ ]  Use the WHERE statement in the DATA step to select observations to be processed
- [ ] Subset variables to be output by using the DROP and KEEP statements
- [ ] Use the DROP= and KEEP= data set options to specify columns to be processed and/or output

## Managing Data

### Investigate SAS data libraries using base SAS utility procedures

- [ ] Use LIBNAME statement to assign a library reference name to a SAS library
- [ ] Investigate a library programmatically using the CONTENTS and DATASET procedures



### Sort observations in a SAS data set

## Generating Reports

## Handling errors

