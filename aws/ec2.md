setup amazon ec2 instance
=========================
**MANY OF THESE NOTES ARE OLD AND COULD OUT OF DATE (as of 01/01/14)


initial ec2 setup
-----------------
- launch instance from dashboard
- login with keypair
    - copy command from dropdown in aws dashboard - instance actions >> connect
    - on local machine, change directory to location of aws keypair files ( try /aws )
    - paste command, and follow prompts

## Prior to logging into EC2 instance:
- If you do not have a keypair on your computer, create one. 
- Save the keypair file to your root directory
- Via terminal go to the root directory and run "sudo chmod 700 yourkey.pem"
- Optional:  (1) Use new Elastic IP, point DNS from a domain name to Elastic IP

## Login with keypair
- ssh -l ec2-user -i yourkeypair.pem yourDOMAIN OR ssh -i yourkeypair.pem ec2-user@yourDOMAIN

### allow ssh login with password rather than keypair
- sudo vim /etc/ssh/sshd_config
    - set 'PasswordAuthentication' to 'yes'
- sudo /etc/init.d/sshd reload ( apply new settings )
- sudo passwd ec2-user ( then enter a password at prompt )
- you can now ssh with 'ssh ec2-user@address.com'

### update installed linux applications
-  sudo yum update


file locations and symlinks changes (examples)
-----------------------------------

###file path changes
*certain systems are stored on the /data ebs drive for permanence and to prevent using too much space on the ephemeral storage provide by each ami*
- /usr/local/sphinx -> /data/sphinx
- /var/lin/mysql -> /data/mysql
- /home -> /data/home

###configuration files
*industrycortex configuration files for certain systems are stored in the www/directory/configs/ folder under version control.*
/usr/local/sphinx/etc -> /data/www/directory/configs/sphinx/etc
/etc/supervisord.conf -> /data/www/directory/configs/supervisor/supervisord.conf


Setup PHP
-----------

###install basic php language
- sudo yum install php

###install Multi-Byte String library
- this is compiled in the latest release of php that installs with `yum install php`
- sudo yum install php-mbstring

###Setup APC ( Alternative PHP Cache )
*a web cahcing utility for php*
- APC is installed and configured automatically when we install php with `sudo yum install php*`
- to monitor APC via a browser:
    - move the apc web app to the root web directory
    - eg. /usr/share/doc/php-pecl-apc-3.1.9/apc.php => /data/www/html/



System Monitoring Tools
=======================

Setup XDebug
------------
*debugging tool for php, also displays pretty html errors and arrays*
*this should probably be installed on every system we install php on*
- `sudo yum install php-pecl-xdebug`
- update `php.ini` so that `html_errors = On`
- restart httpd ( `sudo /etc/init.d/httpd restart` )
- logging
	- i have not used this in a while and don't know what to do here
	- log files written to /tmp ???
- to display more depth from a var_dump
	- update php.ini ( xdebug.var_display_max_depth = # ... like 5, default 3 )
- when not using codeigniter you have to explicitly tell a file to display errors
	- ini_set('display_errors',1);
	- error_reporting(E_ALL);


webgrind ( web based gui for xdebug )
--------
- download from - http://code.google.com/p/webgrind
	- extract into the web directory
	- make sure these lines are set in php.ini for xdebug ( webgrind will auto check the xdebug.profiler_output_dir for profiles )
- create /tmp/xdebug
	- give webserver ownership of /tmp/xdebug
- restart webserver
	- this is tested with apache and lighttpd

```shell
zend_extension=/usr/lib64/php/modules/xdebug.so
xdebug.profiler_enable = 1
xdebug.profiler_output_dir = /tmp/xdebug
```

iostat
------
*io monitoring tool*
- sudo yum install sysstat
- iostat -x 1 ( show drives and cpu usage at one second intervals )

iotop
-----
*io monitoring tool*
- sudo yum install iotop


Stuff We Generally Don't Use
============================

Setup Webmin
------------
*web based sys admin tools*
- download .tar from page: http://www.webmin.com/download.html
- install webmin from these instructions: http://www.webmin.com/tgz.html
    - use defaults for every question during install ( defaults will be presented in parenthesis )
    - use ec2-user as the username
    - use the same password for ec2-user on this instance
- make sure security group for this ec2 instance has port 10000 enabled ( see AWS Security Groups wiki page )
- webmin should be operational at www.domain.com:10000
- to start or stop webmin use the following commands
    - start: sudo /etc/webmin/start
    - stop: sudo /etc/webmin/stop

Setup Apachetop
---------------
*Apachetop is a benchmarking utility for apache*
- tutorial: http://www.howtogeek.com/howto/ubuntu/monitor-your-website-in-real-time-with-apachetop/
- *** make sure to configure the correct log file path ( eg. --with-logfile=/etc/httpd/logs/access_log )
- download the tar ( http://www.webta.org/apachetop/apachetop-0.12.6.tar.gz )
- untar the tar ( duh! )
- sudo yum install readline-devel
- sudo yum install ncurses-devel
- cd into the untarred folders ( eg. cd apachetop-0.12.6 )
- sudo ./configure --with-logfile=/etc/httpd/logs/access_log
- sudo make

Setup Systems to load on boot
-----------------------------
- add the execution command to the /etc/rc.local file
	- sudo vim /etc/rc.local

### some utilities we commonly load on boot

- /etc/init.d/httpd start
- /etc/init.d/mysqld start
- /usr/bin/supervisord
- /etc/webmin/start/
- /data/sphinx/bin/searchd
