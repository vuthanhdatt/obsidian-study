## MySQL Data Types
### Character Data
- you should use the `char` type when all strings to be stored in the column are of the same length, and the `varchar` type when strings to be stored in the column are of varying lengths
### Character set
- chracter set using to support foreign language, such as utf-8, utf-16...
- To choose a character set other than the default when defining a column, simply name one of the supported character sets after the type definition, as in
`varchar(20) character set latin1`
- With MySQL, you may also set the default character set for your entire database: 
`create database european_sales character set latin1;`
- In prior versions of the MySQL server, the `latin1` character set was automatically chosen as the default character set, but version 8 defaults to `utf8mb4`
### Text Data
- If you need to store data that might exceed the 64 KB limit for varchar columns, you will need to use one of the text types.
![[Pasted image 20220616152509.png]]
- Now that MySQL allows up to 65,535 bytes for `varchar` columns (it was limited to 255 bytes in version 4), there isn’t any particular need to use the `tinytext` or `text` type.
- If the data being loaded into a text column exceeds the maximum size for that type, the data will be truncated.
### Numeric Data

![[Pasted image 20220616153235.png]]
![[Pasted image 20220616153242.png]]
### Temporal Data
![[Pasted image 20220616153534.png]]
- The timestamp type holds the same information as the datetime type (year, month, day, hour, minute, second), but a timestamp column will automatically be populated with the current date/time by the MySQL server when a row is added to a table or when a row is later modified.

### Design table
- When designing a table, you may specify which columns are allowed to be null (the default) and which columns are not allowed to be null (designated by adding the key‐ words `not null `after the type definition
- 