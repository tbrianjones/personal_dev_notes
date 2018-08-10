**old notes - incomplete**

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


Setup Webgrind
--------------
*web based gui for xdebug*

- download from - https://github.com/jokkedk/webgrind/archive/master.zip
	- extract into the web directory (/var/www/html/)
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
