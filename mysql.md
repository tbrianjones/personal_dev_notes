MySQL
=====


Setup MySQL Server
------------------
*mysql is the relational database server we use for most project*
*some projects are using Amazon's RDS mysql database system instead of running mysql locally*
- sudo yum install mysql
- sudo yum install mysql-server
- if using php, install the php mysql library ( sudo yum install php-mysql )
- start mysql for the first time ( sudo /etc/init.d/mysqld start )
- set a root password ( mysqladmin -u root password 'password' )
- add users ( via the command line, webmin, sqlpro, phpmyadmin, etc. )
	- `CREATE USER 'newuser'@'localhost' IDENTIFIED BY 'password';`
	- `GRANT ALL PRIVILEGES ON DATABASE_NAME.TABLE_NAME TO 'newuser'@'localhost';`
		- use DATABASE_NAME.* to give permission on all tables
		- use % instead of localhost to give permission from remote servers
	- `FLUSH PRIVILEGES;` - reloads users


Reset Root Password
-------------------
- http://www.rackspace.com/knowledge_center/article/mysql-resetting-a-lost-mysql-root-password


Profiling with MysqlTuner
-------------------------

- http://mysqltuner.com/
- Put the `mysqltuner.pl` file on the local machine that has access to the db
- perl mysqltuner.pl --host database_on_rds.rds.amazonaws.com --user root_user --pass password --forcemem 32000
  - where 32000 is the ram in MB on that mysql server


Amazon RDS
----------

### Importing a CSV to an RDS Instance
- csv must have a header row with column names if there are more columns in the table than in the csv
- `mysqlimport -v --local --compress --columns=id,company_id,url,tier --user=root --password --host=amazon.rds.host.address companies files.csv`
  - don't use `--fields-terminated-by=` as this caused problems
  - the last two parts of this are the database name and the file name
    - mysql will automatically use the file name ( stripped of extension ) as the database to import to
- some useful sites:
  - http://aws.amazon.com/articles/2933?_encoding=UTF8&jiveRedirect=1
  - http://dev.mysql.com/doc/refman/5.0/en/mysqlimport.html
