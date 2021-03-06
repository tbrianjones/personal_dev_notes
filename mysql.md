MySQL
=====

Handy Queries
-------------
- simple dedup of a table
	- `ALTER IGNORE TABLE table_name ADD UNIQUE (location_id, datetime)`
	- source: http://stackoverflow.com/questions/2385921/deleting-duplicates-from-a-large-table
	- transfer from R3.L to R3.L runs at 1GB/150sec
	- "T" instances get throttled (IOPS, or MB/s or Network or something) (don't transfer large dbs with them)

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
- See RDS notes in the AWS folder.
