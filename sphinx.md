Setup Sphinx Search Server
==========================
*sphinx is the search server we first used on industrycortex.com*
*manual: http://sphinxsearch.com/docs/2.0.1/installing.html*


install sphinx
--------------
- install required linux utilities
	- sudo yum install gcc-c++ ( install c++ compiler )
	- sudo yum install make ( installs some more compiler stuff )
	- sudo yum install mysql-devel ( installs sphinx mysql dependencies )
- download .tar file from site http://sphinxsearch.com/downloads/beta/
- unzip tar ( tar xzvf sphinx-2.0.1-beta.tar.gz )
- cd to folder everything just unzipped into ( cd sphinx )
- configure the install utility ( sudo ./configure --prefix=/usr/local/sphinx )
	- rerun this after you fix any errors it encounters on the first try
- make sphinx ( sudo make )
	- execute this in the directory files just unzipped to ( should be in this directory already )
	- this must be run before make -j4 install
	- this outputs lots of gobbledygook, then runs with no output for a few minutes, then finishes.
- sudo make -j4 install
	- execute this in the directory files just unzipped to ( should be in this directory already )
	- this outputs lots of gobbledygook, then runs with no output for a few minutes, then finishes.
- everything should be working in /usr/local/sphinx


only launch sphinx as ec2-user ( don't sudo launches )
----------------------------------------
- change permissions so ec2-user can launch sphinx ( sudo chown -R ec2-user:ec2-user /data/sphinx )
- launch sphinx ( /data/sphinx/bin/searchd )
	

setup persistence on an ec2 server
----------------------------------
- move sphinx to /data where we typically attach EBS drives
		- sudo mv /usr/local/sphinx /data ( move /usr/local/sphinx to /data/sphinx )
		- sudo ln -s /data/sphinx /usr/local/sphinx ( create a symlink to the new location )
- move sphinx config data to www ( code must be checked out to /data/www for symlink to work )
	- create a symlink from /usr/local/sphinx/etc to /data/www/directory/configs/sphinx/etc/


troubleshooting sphinx
----------------------
*we've had lots of problems with the sphinx server running in production on industrycortex.com*
*TBJ has been unable to solve these issues*

###server keeps crashing with message 'FATAL: accept() failed: Too many open files' in searchd.log

###increase linux ulimits
- use `ulimit` to check max open files allowed ( eg. ulimit -a )
- increase this value ( eg. ulimit -n 4096 )
	- sometimes the system won't let me go past 2048 ... weird!!!
	- this has to be redone on a reboot.
	- this doesn't seem to do anything useful ( see the edit to /etc/security/limits.conf below )
- see here: http://www.cyberciti.biz/faq/linux-increase-the-maximum-number-of-open-files/

###increase max open files allowed
- sysctl -w fs.file-max=10000000
- sudo vim /etc/sysctl.conf
	- add the line - fs.file-max = 10000000
- sudo sysctl -p ( to reload the new configurations )
- sudo vim /etc/security/limits.conf
 	- how to: http://posidev.com/blog/2009/06/04/set-ulimit-parameters-on-ubuntu/
	- adding these and logging out without rebooting can cause the system to lock ec2-user out ( maybe the cause )
	- add these lines, then reboot:

```shell
ec2-user soft nofile 65536
ec2-user hard nofile 65536
```

### main problem was never resolved
* TBJ was unable to resolve the sphinx / linux issue that was causing sphinx to crash *

- so ... i setup a cron job to try and start sphinx every few minutes
	- edit cron jobs for ec2-user ( crontab -e )
	- add this line: `*/2 * * * * /data/sphinx/bin/searchd &>> /tmp/cron_output.txt`
	- make sure crond is running ( sudo /etc/init.d/crond restart )
	- crond does not need to be restarted when changes are made using crontab -e
	- the output from this cronjob is currently written to /tmp/cron_output.txt
		- this should be removed if this works
		- this should be replaced with `>/dev/null 2>&1` to discard the output
	
	
