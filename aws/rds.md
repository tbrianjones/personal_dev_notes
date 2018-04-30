RDS
===

### Export to CSV from RDS

WITH WRAPPING QUOTES
```
mysql -u USER -p -h HOST DB_NAME --batch -e "
    MYSQL
    QUERY
    CODE
" | sed 's/\t/","/g;s/^/"/;s/$/"/;s/\n//g' > FILE_NAME
```


NO WRAPPING QUOTES
```
mysql -u USER -p -h HOST DB_NAME --batch -e "
    MYSQL
    QUERY
    CODE
" | sed 's/\t//g;s/^//;s/$//;s/\n//g' > FILE_NAME
```

### Load a CSV to RDS
- use `LOAD DATA LOCAL INFILE` - example below
- column names should be the names of columns in the mysql table you're importing into, but they should be in order of how the data is layed out in the CSV file.
- This should be fast, eg. 3gb CSV loaded in 10min when the table doesn't have indexes
  - adding indexes after is WAY faster

```
mysql -u root -p -h aws-rds-host db_name --local-infile=1 -e "
load data local infile 'file_name.csv' into table table_to_import_into
fields terminated by ','
enclosed by ''
lines terminated by '\n'
(mysql,column,names);"
```

- If loading a csv/txt file with only a single column (like emails, or ids, or domains), you just need to create a table with a single column. Headers and other details don't matter. 16M rows imports in less than a minute.

ALTERNATE METHOD (more difficult)
- `mysqlimport` also works, but is a pain - see another example below that works sometimes
	- csv must have a header row with column names if there are more columns in the table than in the csv
	- `mysqlimport -v --local --compress --columns=id,company_id,url,tier --user=root --password --host=amazon.rds.host.address companies files.csv`
  	- don't use `--fields-terminated-by=` as this caused problems
  	- the last two parts of this are the database name and the file name
    	- mysql will automatically use the file name ( stripped of extension ) as the database to import to
	- some useful sites:
  		- http://aws.amazon.com/articles/2933?_encoding=UTF8&jiveRedirect=1
  - http://dev.mysql.com/doc/refman/5.0/en/mysqlimport.html

### sharing snapshot between accounts
- from console, 'manage snopshot permissions' and enter account id to share with
- https://aws.amazon.com/blogs/aws/amazon-rds-update-cross-account-snapshot-sharing/

### Copying a DB from one RDS to another
- THIS IS VERY SLOW AND MANY TABLES WILL FAIL (consider only using this for samll or simple tables)
- run this on an EC2 instance that has access to both RD instances
- this uses no drive space on the EC2 instance
- you have to create new-db-name on the new rds instance before triggering this

```
mysqldump -h old-db-host -u old-db-user -pold-db-pass old-db-name | mysql -h new-db-host -u new-db-user -pnew-db-pass new-db-name
```

- Same thing, but for a specific table

```
mysqldump -h old-db-host -u old-db-user -pold-db-pass old-db-name TABLE_NAME | mysql -h new-db-host -u new-db-user -pnew-db-pass new-db-name
```
