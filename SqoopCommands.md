

### check connection for mysql database

sqoop import \
  --connect jdbc:mysql:### localhost:3306 \
  --username root \
  --password hortonworks1


### list databases available mysql database

sqoop list-databases \
  --connect jdbc:mysql:### localhost:3306 \
  --username root \
  --password hortonworks1


###  list-tables --- testdb is the database name

sqoop list-tables \
  --connect jdbc:mysql:### localhost:3306/testdb \
  --username root \
  --password hortonworks1
  
###  using password file instead of typing password. Need to give file location

sqoop list-tables \
  --connect jdbc:mysql:### localhost:3306/testdb \
  --username root \
  --password-file /tmp/pass-file
    

###  import data from Mysql to HDFS -- importing data from table employee present in testdb to HDFS location /tmp/sqooptbls/

sqoop import \
  --connect "jdbc:mysql:### localhost:3306/testdb" \
  --username=root \
  --password=hortonworks1 \
  --table employee \
  --target-dir '/tmp/sqooptbls/'
  

### Multi-Mapper Import  -- here each mapper writes to seperate files. 
If we use more than one mapper, we need have primary key for the table, else we need to use --split-by.
we can use -m or --num-mappers
By default Sqoop sets 4 mappers.

sqoop import \
  --connect jdbc:mysql:### localhost:3306/testdb \
  --username root \
  --password hortonworks1 \
  --table TBLS \ 
  --target-dir '/tmp/sqooptbls/' \
  --num-mappers 5
  
  sqoop import \
  --connect jdbc:mysql:### localhost:3306/testdb \
  --username root \
  --password hortonworks1 \
  --table TBLS \ 
  --target-dir '/tmp/sqooptbls/' \
  -m 5

### with split-by

sqoop import \
  --connect jdbc:mysql:### localhost:3306/testdb \
  --username root \
  --password hortonworks1 \
  --table TBLS \ 
  --split-by "id" \
  --target-dir '/tmp/sqooptbls/' \
  -m 5
  
### split based on text/string column --- need to set -Dorg.apache.sqoop.splitter.allow_text_splitter=true

sqoop import \
  -Dorg.apache.sqoop.splitter.allow_text_splitter=true
  --connect jdbc:mysql:### localhost:3306/testdb \
  --username root \
  --password hortonworks1 \
  --table TBLS \ 
  --split-by "id" \
  --target-dir '/tmp/sqooptbls/' \
  -m 5
  
### '--autoreset-to-one-mapper', it sets mappers as 1 by default when there is no primary key, it is not necessary to use split-by in this case

sqoop import \
  --connect jdbc:mysql:### localhost:3306/testdb \
  --username root \
  --password hortonworks1 \
  --table TBLS \ 
  --autoreset-to-one-mapper \
  --target-dir '/tmp/sqooptbls/' \
  -m 5
  

### Single-Mapper Import

sqoop import \
  --connect jdbc:mysql:### localhost:3306/testdb \
  --username root \
  --password hortonworks1 \
  --table TBLS \ 
  --target-dir '/tmp/sqooptbls/' \
  -m 1
  
  
### appending data to existing directory
  
  sqoop import \
  --connect jdbc:mysql:### localhost:3306/testdb \
  --username root \
  --password hortonworks1 \
  --table TBLS \ 
  --split-by "id" \
  --target-dir '/tmp/sqooptbls/' \
  --append \
  -m 5
  
### for overwriting data, we need to delete target directory and recreate it. We can use --delete-target-dir for this.
  
  sqoop import \
  --connect jdbc:mysql:### localhost:3306/testdb \
  --username root \
  --password hortonworks1 \
  --table TBLS \ 
  --split-by "id" \
  --target-dir '/tmp/sqooptbls/' \
  --delete-target-dir \
  -m 1
  

###  Storing data in different file formats.
By default data is stored in text files.

sqoop import \
  --connect jdbc:mysql:### localhost:3306/testdb \
  --username root \
  --password hortonworks1 \
  --table TBLS \ 
  --split-by "id" \
  --target-dir '/tmp/sqooptbls/' \
  --as-textfile \
  -m 1
  
sqoop import \
  --connect jdbc:mysql:### localhost:3306/testdb \
  --username root \
  --password hortonworks1 \
  --table TBLS \ 
  --split-by "id" \
  --target-dir '/tmp/sqooptbls/' \
  --as-parquetfile \
  -m 1
  
sqoop import \
  --connect jdbc:mysql:### localhost:3306/testdb \
  --username root \
  --password hortonworks1 \
  --table TBLS \ 
  --split-by "id" \
  --target-dir '/tmp/sqooptbls/' \
  --as-sequencefile \
  -m 1
  
### Boundary query

sqoop import \
  --connect jdbc:mysql:### localhost:3306/testdb \
  --username root \
  --password hortonworks1 \
  --table TBLS \   
  --target-dir '/tmp/sqooptbls/' \
  --boundary-query "select min(sno),max(sno) from TBLS" 
  
### hard code range

 sqoop import \
  --connect jdbc:mysql:### localhost:3306/testdb \
  --username root \
  --password hortonworks1 \
  --table TBLS \   
  --target-dir '/tmp/sqooptbls/' \
  --boundary-query "select 5,120" 
  
###  Importing only particular columns

sqoop import \
  --connect jdbc:mysql:### localhost:3306/testdb \
  --username root \
  --password hortonworks1 \
  --table TBLS \ 
  --columns name,id \
  --target-dir '/tmp/sqooptbls/'

###  we can use --query to perfom any operation on the table like fetch limited rows or joining tables..
we need to give '\$CONDITIONS' in where class when we use --query else it will throw an error.
we cannot use '--warehouse-dir' in this case, as we will not be mentioning any table name.

sqoop import \
  --connect jdbc:mysql:### localhost:3306/testdb \
  --username root \
  --password hortonworks1 \
  --query "select * from employee e,department d where e.deptid = d.id and and \$CONDITIONS" \
  --columns name,id \
  --target-dir '/tmp/sqooptbls/'
  --m 1
  
### '--where' --- to import data based on certain condititon

sqoop import \
  --connect jdbc:mysql:### localhost:3306/testdb \
  --username root \
  --password hortonworks1 \
  --table TBLS \ 
  --columns name,id \
  --where "start_date > '2000-01-01'"
  --target-dir '/tmp/sqooptbls/'
  
### using --split-by with --query
need to give the same column name as like in the query in --split-by
sqoop import \
  --connect jdbc:mysql:### localhost:3306/testdb \
  --username root \
  --password hortonworks1 \
  --query "select e.id, e.name, d.id, d.name from employee e,department d where e.deptid = d.id and and \$CONDITIONS" \
  --columns name,id \
  --split-by e.id
  --target-dir '/tmp/sqooptbls/'
  
 sqoop import \
  --connect jdbc:mysql:### localhost:3306/testdb \
  --username root \
  --password hortonworks1 \
  --query "select e.id as empId, e.name as empName, d.id as deptId, d.name as deptName from employee e,department d where e.deptid = d.id and and \$CONDITIONS" \
  --columns name,id \
  --split-by empId
  --target-dir '/tmp/sqooptbls/'
  
  
 ###  import all tables from the database
 Need to user 'warehouse-dir'. It creates sub directory under the mentioned directory for each table.
 Setting '--autoreset-to-one-mapper', takes care of executing sqoop job with one mapper if no primary key available for table.
 
sqoop import-all-tables \
  --connect "jdbc:mysql:### localhost:3306/testdb" \
  --username=root \
  --password=hortonworks1 \
  --autoreset-to-one-mapper
  --warehouse-dir '/sqoop/alltbls/'
  
###  Exclude few tables from import

sqoop import-all-tables \
  --connect "jdbc:mysql:### localhost:3306/testdb" \
  --username=root \
  --password=hortonworks1 \
  --autoreset-to-one-mapper
  --excluse-tables employee,salary
  --warehouse-dir '/sqoop/alltbls/'

 
  

  
  
  
  
