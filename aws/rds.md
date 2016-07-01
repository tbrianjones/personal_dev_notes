RDS
===

### Load a CSV to RDS
- use `LOAD DATA INFILE` - example below
- column names should be the names of columns in the mysql table you're importing into, but they should be in order of how the data is layed out in the CSV file.

```
mysql -u root -p -h aws-rds-host db_name --local-infile=1 -e "
load data local infile 'file_name.csv' into table table_to_import_into
fields terminated by ','
enclosed by ''
lines terminated by '\n'
(mysql,column,names);"
```

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
