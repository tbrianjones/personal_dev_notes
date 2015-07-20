Setup Systems to load on boot
=============================
*add the execution command to the /etc/rc.local file
- sudo vim /etc/rc.local

some utilities we commonly load on boot
---------------------------------------
- /etc/init.d/httpd start
- /etc/init.d/mysqld start
- /usr/bin/supervisord
- /etc/webmin/start/
- /data/sphinx/bin/searchd
